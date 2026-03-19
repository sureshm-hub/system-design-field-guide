# Summary of Techniques:
  - **Polling:**
  - **Long Polling:** Client-driven requests.
  - **SSE (Server-Sent Events):** Unidirectional server-to-client push.
  - **WebSockets:** Bidirectional, persistent communication.
  - **Webhooks:** Event-driven HTTP POST requests

## 1. Polling
- Client asks the server every few seconds: “Any new data?”

### Flow:
  - Client sends repeated GET requests on a timer
  - Server responds immediately, even if nothing changed

  ``` javascript
      setInterval(async () => {
          const res = await fetch("/api/notifications");
            
          if (res.status === 200) {
          const data = await res.json();
          console.log("Updates:", data);
          } else if (res.status === 204) {
          console.log("No new updates");
          }
      }, 5000);
  ```
  ``` java/spring boot
      @RestController
      @RequestMapping("/api")
      public class NotificationController {
        
          @GetMapping("/notifications")
          public ResponseEntity<List<String>> getNotifications() {
              List<String> updates = List.of(); // fetch new updates
        
              if (updates.isEmpty()) {
                  return ResponseEntity.noContent().build(); // 204
              }
              return ResponseEntity.ok(updates); // 200
          }
      }
  ```

### When to use:
  * Simple dashboards
  * Low-frequency updates
  * Easy to implement, but wasteful



## 2. Long Polling
- Client sends a request, and the server holds it open until  data is available
or timeout happens.

### Flow:
  * Client sends GET /updates
  * Server waits
  * When event happens, server returns response
  * Client **immediately sends** the next request

   ```javascript
   async function longPoll() {
     try {
       const res = await fetch("/api/long-poll");
    
       if (res.status === 200) {
         const data = await res.json();
         console.log("Received:", data);
       } else if (res.status === 204) {
         console.log("Timeout, retrying...");
       }
     } catch (e) {
       console.error("Polling error:", e);
     }
    
     longPoll(); // immediately reconnect
   }
    
   longPoll();
   ```
   ```java
        @RestController
        @RequestMapping("/api")
        public class LongPollingController {
        
            @GetMapping("/long-poll")
            public DeferredResult<ResponseEntity<String>> longPoll() {
                DeferredResult<ResponseEntity<String>> output = new DeferredResult<>(30000L); // Time out for the long  polling
        
                output.onTimeout(() ->
                    output.setResult(ResponseEntity.noContent().build()) // 204
                );
        
                // Simulate async event arrival
                CompletableFuture.runAsync(() -> {
                    try {
                        Thread.sleep(5000); // wait for event
                        output.setResult(ResponseEntity.ok("New event arrived")); // 200
                    } catch (Exception e) {
                        output.setErrorResult(
                            ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body("Error")
                        );
                    }
                });
        
                return output;
            }
        }
   ```

### When to use
  - Better than polling when updates are infrequent
  - Useful before WebSocket/SSE
  - Still HTTP request heavy at scale


## 3. SSE (Server-Sent Events)
* Server keeps a single HTTP connection open and continuously pushes events to
the client. This is one-way: server → client.

### Flow
  - Client opens EventSource
  - Server responds with text/event-stream
  - Server keeps streaming events over same connection

```JavaScript client
    const source = new EventSource("/api/sse");
    
    source.onmessage = function(event) {
    console.log("Message:", event.data);
    };
    
    source.onerror = function(err) {
    console.error("SSE error:", err);
    };
```
``` Java Spring Boot server
    @RestController
    @RequestMapping("/api")
    public class SseController {
    
        @GetMapping(value = "/sse", produces = MediaType.TEXT_EVENT_STREAM_VALUE)
        public SseEmitter streamEvents() {
            SseEmitter emitter = new SseEmitter();
    
            Executors.newSingleThreadExecutor().submit(() -> {
                try {
                    emitter.send("Event 1");
                    Thread.sleep(2000);
                    emitter.send("Event 2");
                    Thread.sleep(2000);
                    emitter.complete();
                } catch (Exception e) {
                    emitter.completeWithError(e);
                }
            });
    
            return emitter;
        }
    }
``` 
``` HTTP
    Raw HTTP response headers
    HTTP/1.1 200 OK
    Content-Type: text/event-stream
    Cache-Control: no-cache
    Connection: keep-alive
``` 

### When to use
  - Live notifications
  - Monitoring dashboards
  - Stock ticker style updates
  - Easier than WebSockets for one-way streaming
  - CricInfo/NBA Score updates on server

## 4. WebSockets

* WebSockets provide a full-duplex persistent connection between client and server.
Unlike SSE, both client and server can send messages anytime after the connection is established.

### Flow
- Client sends an HTTP request with Upgrade: websocket
- Server responds with protocol upgrade
- Connection switches from HTTP to WebSocket
- Both sides exchange messages over the same long-lived connection


```JavaScript client
    const socket = new WebSocket("ws://localhost:8080/ws");
    
    socket.onopen = () => {
    console.log("Connected");
    socket.send("Hello from client");
    };
    
    socket.onmessage = (event) => {
    console.log("From server:", event.data);
    };
    
    socket.onclose = () => {
    console.log("Disconnected");
    };
    
    socket.onerror = (err) => {
    console.error("WebSocket error:", err);
    };
```
```Java Spring Boot server
    import org.springframework.context.annotation.Configuration;
    import org.springframework.web.socket.*;
    import org.springframework.web.socket.config.annotation.*;
    
    @Configuration
    @EnableWebSocket
    public class WebSocketConfig implements WebSocketConfigurer {
    @Override
    public void registerWebSocketHandlers(WebSocketHandlerRegistry registry) {
    registry.addHandler(new MyWebSocketHandler(), "/ws").setAllowedOrigins("*");
    }
    }
    
    class MyWebSocketHandler extends TextWebSocketHandler {
    @Override
    public void afterConnectionEstablished(WebSocketSession session) throws Exception {
    session.sendMessage(new TextMessage("Hello from server"));
    }
    
        @Override
        protected void handleTextMessage(WebSocketSession session, TextMessage message) throws Exception {
            System.out.println("Client says: " + message.getPayload());
            session.sendMessage(new TextMessage("Echo: " + message.getPayload()));
        }
    }
```
``` HTTP
    WebSocket handshake example
    GET /ws HTTP/1.1
    Host: localhost:8080
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: x3JJHMbDL1EzLkh9GBhXDw==
    Sec-WebSocket-Version: 13
    Server response
    HTTP/1.1 101 Switching Protocols
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Accept: HSmrc0sMlYUkAGmm5OPpG2HaGWk=
    Common close codes
``` 

### When to use
  - Chat applications
  - Multiplayer games
  - Collaborative editing
  - Trading dashboards
  - Bidirectional low-latency systems

#### Tradeoffs
  - More efficient than repeated polling
  - Supports real-time two-way messaging
  - More complex than SSE/polling
  - Requires connection/state management, heartbeats, reconnect logic, scaling strategy
  - Quick positioning vs SSE
  - SSE → server to client only
  - WebSocket → client and server both send anytime

### Typical HTTP codes
  - 200 OK → response returned successfully, with data or empty result
  - 204 No Content → valid request, but no new data
  - 400 Bad Request → invalid query params
  - 401 Unauthorized / 403 Forbidden → auth issues
  - 429 Too Many Requests → client polling too aggressively
  - 500 Internal Server Error → server failure
  - #### From Long Polling perspective
  - 408 Request Timeout → sometimes used, though 204 is often cleaner  # long polling
  - 499 → client closed request early (common in proxies/nginx, not standard HTTP)
  - #### From webhook sender’s perspective
  - 202 Accepted → receiver accepted for async processing  # webhooks
  - 410 Gone → webhook endpoint retired  # webhooks
  - #### Typical HTTP codes during handshake
  - 101 Switching Protocols → successful WebSocket upgrade
  - 426 Upgrade Required → server expects WebSocket upgrade
  - #### These are WebSocket close codes, not HTTP status codes:
  - 1000 → normal closure
  - 1001 → endpoint going away
  - 1002 → protocol error
  - 1003 → unsupported data
  - 1006 → abnormal closure
  - 1008 → policy violation
  - 1011 → internal server error

### Other HTTP upgrade examples like WebSocket (related upgrade/tunneling patterns):
  - HTTP → WebSocket via 101 Switching Protocols
  - HTTP CONNECT for creating a tunnel, often for HTTPS via proxy
  - HTTP/2 cleartext upgrade (h2c) exists, but uncommon on the public web
  - TLS ALPN negotiation chooses protocol like HTTP/1.1 vs HTTP/2, but 
  that is not the same as HTTP Upgrade

### TCP 3 way handshake
  - The TCP 3-way handshake (SYN, SYN-ACK, ACK) establishes a reliable 
    connection before HTTP data is transmitted
  - SYNC
  - SYNC/ACK
  - ACK

## 5. Webhooks: Server to Server calling
* Instead of client repeatedly asking, the server calls another server when an
  event happens.
* Cross‑system automation without polling or messaging

### Flow
- Consumer registers callback URL
- Producer stores it
- On event, producer sends HTTP POST to callback URL

```JavaScript sender example
    async function sendWebhook() {
        const payload = {
        event: "order.created",
        orderId: 123
        };
        
        const res = await fetch("https://client.example.com/webhook", {
            method: "POST",
            headers: {
            "Content-Type": "application/json",
            "X-Signature": "abc123"
            },
            body: JSON.stringify(payload)
        });
    
        console.log("Webhook status:", res.status);
    }
```
```Java Spring Boot receiver
        @RestController
        public class WebhookController {
        
            @PostMapping("/webhook")
            public ResponseEntity<String> receiveWebhook(@RequestBody Map<String, Object> payload,
                                                         @RequestHeader(value = "X-Signature", required = false) String signature) {
                if (signature == null || !signature.equals("abc123")) {
                    return ResponseEntity.status(HttpStatus.UNAUTHORIZED).body("Invalid signature"); // 401
                }
        
                System.out.println("Received webhook: " + payload);
                return ResponseEntity.ok("Received"); // 200
            }
        }
```
### When to use
- Payment notifications
- GitHub/Jenkins triggers
- Stripe/Slack/Shopify style integrations
- Efficient for server-to-server async events

### How it works
- Webhook post → how browser knows: the browser usually does not know
  directly about the webhook call. The webhook hits your backend, then your
  backend updates the UI via:
    - SSE to push fresh data to browser
    - WebSocket to push instantly
    - polling / refetch from browser
    - sometimes DB/cache update + browser refresh/API call
- GitHub/Jenkins triggers:
    - GitHub sends webhook on:
        - push
        - pull_request
        - release
    - Jenkins receives it and can:
        - start CI build
        - run tests
        - deploy
        - post build result
- Third‑party -> your system callbacks