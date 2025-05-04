# tiny url service

# Scope
- Features:
  - user can paste & go a tiny url in browser & hit go, the tinyurl service will redirect to long url
  - service to create tiny url's
    - long url -> tiny url
  - delete or update tiney url requirements?
  - auto delete any stale urls?
  - user initiated deletes
  - update urls
      - why not create a new tiny url?
      - new users receive the updated url in redirects

- NFR's:
  - scalable: handle millions of requests per day
  - store billions of url's
  - HA: many 9's
  - low latency - ms response in redirects
  - observability/usage analytics

- Assumptions:
  - 1 million full urls:
    - 4KB full url's ~ 2048 chars
    - 1 tiny url < 100 char's  ~ 200 Bytes
  - 100 K DAU's per day
  - Growth:
    - 1 million full urls to 100 million full urls (100X)
    - 100K DAU's to 10 million DAU's  (100X)

# High Level Design

- service to create a tiny url
- web server to host the tiny url application
  - issue redirects
  - HA: Horizontal auto scaling
- Back End:
  - Cache: for fast look up of url's
    - new url creation hits cache and backend
  - Dynamo DB in preference to Relational like Oracle
    - prefer availability in case of partitions & support eventually consistency 

```mermaid
flowchart LR
    
    browser -->|tiny_url| fetch_tiny_url_service <--> cache_service
    
    user --> |full_url| create_tiny_url_service --> DynamoDB & cache_service

```
``` mermaid
 
flowchart LR

  browser -->|GET /short_url| CloudFront -->|Cache Miss| API_Gateway --> Web_Server
  Web_Server -->|Check Cache| ElastiCache
  Web_Server -->|Cache Miss| DynamoDB
  Web_Server -->|Log Click| Kinesis --> S3

  user -->|POST /tinyUrl| API_Gateway -->|Authenticate| Cognito --> Web_Server
  Web_Server -->|Write| DynamoDB
  Web_Server -->|Update Cache| ElastiCache

```

# Deep Dive

- API flow

```http

POST /tiny_url {full_url}  
response tiny.url/tiny_url

GET /tiny_url
Response:
    Status: 302
    Location: <full_url>
    
DELETE /tiny_url

PUT /tiny_url {updated_full_url}
```

- Service
  - create tiny url
    - uniqueId generator for each full url 
      - simple to call the increment uniqueId generator 
      - store the next value for restarts/ share or scale with a prefix for each server
    - Convert unique Id to hexadecimal
      - serves as a partition key for Dynamo DB
      - no additional sort key is required
    - Save to Dynamo DB as
      - partition key
      - attributes of record are  full_url, created_by, created_timestamp
      
  - fetch full url
    - check cache for tiny url
      - return full url if found
      - if not found query dynamo DB attribute Full_Url, save in cache and return
    - issue redirect
    - update usage/observability metrics
    
 - Dynamo DB model

| Partition | Full_Url     | created_by | created_timestamp        | expires_at | active |
|-----------|--------------|------------|--------------------------|------------|--------|
| abc123    | http://url1  | user       | 2025-01-12 20:21:12.123  | 2026-01-12 | Y      |

- Storage
  - 1 million full urls ~ 4KB * 1 million  ~4 GB  (1 partition has typically 10 GB)
  - Usage 100 K DAU's
    - cache frequent tiny urls to avoid RCUs on Dynamo DB & reduce latency
    - 1 request per sec (assuming uniform load)
    - 1 web server can handle this load
- Observability (Analytics Table)
  - tiny1, user agent/browser details, access time, location details

| partition | user_agent   | accessed_timestamp      | location  | user_info  |
|-----------|--------------|-------------------------|-----------|------------|
| abc123    | chrome/120   | 2025-04-05 20:21:15.123 | US        | user1      |

# Wrap Up
- Growth
    - 1 million full urls to 100 million full urls (100X)  --> 400 GB  ~ 40 partitions (easily handled by DynamoDB)
    - 100K DAU's to 10 million DAU's  (100X)  --> 100 req per sec  --> can split load easily across 2 to 3 http/nginx servers
    - Dynamo DB read write/failure handling
    - Cache Eviction or stale entries
    - Unique ID generation can run into scaling issues, couple of options:
      - MD5 or SHA-1 hash of full url truncated to  6-8 chars with collision handling
      - distributed ID generator
      - Use DynamoDB for resource counter storing
      - Ideal solution Unique ID Generation:
        - Use a base62-encoded hash (e.g., MD5(long_url)[:6]) for the short key.
        - On collision, append a suffix (e.g., abc123_1) and retry.
        - Store in DynamoDB with conditional writes to ensure uniqueness.
    - DynamoDB Hot Partition Handling with potential sort key
    - URL Validation for long urls
    - Duplicate Long Url check
- CDN and cloud front for low latency
- Observability to Usage Reports for Clients
  - S3 logs with Kinesis
- Rate Limiting on Create tiny url to prevent spamming
- AuthN/AuthZ for created_by and permissions to create tiny url