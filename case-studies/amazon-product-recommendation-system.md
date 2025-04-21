# Design Amazon's Product Recommendation System

# Design Scope
- web vs mobile (both)
- features:
  - user product suggestions
  - frequently bought  together
  - search based product recommendations
  - user upload of product reviews: (video/images/text)
  - feedback on reviews 
  - upvote/down vote and relevancy of reviews -> feed back to recommendation engine
- NFR's:
  - latency
  - scale (millions of products)
  - high availability
  - multi device support
- Assumptions:
  - 10 million DAU (scale 10x)
  - 100K products (scale to a million products)
- Integration
  - shopping cart
  - checkout?

# High Level Design
- client app/web
  - search for product
  - push notifications
- load balancer
- gateway
  - rate limiting
  - autheN
  - routing logic
- search service
  - search
- recommendation engine
- review service
  - post comments
  - up vote/ down vote
- storage
  - video
  - image
  - comments/text

```mermaid
flowchart TD

    subgraph review
    user2 --> review-service --> storage-service & recommendation-engine
    end
    
    subgraph user-search
    user  --> loadbalancer --> gateway <--> search-service <--> recommendation-engine & product-search
    end

```

# Design Deep Dive
  - http/rest vs graphQL
    - graphQL for mobile to give flexibility to client app
    - http/rest for web
  - response model
      ```json
      {
        "search": "keyword",
        "search-metadata" : {},
        "results" : [
          {
            "product_key": 123,
            "product_name": "..",
            "quantity": 12,
            "product_details": "",
            "pics" : [
              "img1", "img2"
            ],
            "reviews" : [
              {"reviewer": "..",
                "rating": "3/5",
                "review": "",
                "comments": [
                
                ],
                "up_votes": 5,
                "down_votes": 1
              }
            ]
                  
                  
          }
        ]
      }
      ```
  - Scalability:
    - elastic search can scale horizontally with products
    - document db like mongo db for products, reviews & comments (allows flexible schema)
    - postgresSQL (relational) for inventory
    - rate limiting at load balancer

  - Availability:
    - elastic search
      - shard allocation & replication
      - cluster health monitoring
      - cross cluster replication
    - mongo db
      - replica set 
      - election to promote new primary in case of failure
    - push notifications
      - FCM & APN
    
# End to End flow for product search & recommendations

1. User types keyword and hits search
2. search goes through load balancer and is forwarded to search-service
3. the search service calls recommendation engine
4. recommendation engine (elastic search) retrieve a list of products
5. search service calls product-search (mongo db) for the recommended products and forms a result response
6. result response is returned to the user
7. images, videos are CDN hosted for reducing latency
8. user follows recommendation and hits upvotes/downvotes for the reviews
9. up votes/down votes fire and forget commands that first update memcached/redis and synched with the db in the background


# End to End flow for user adding a review 

1. user submits a review (text), along with images and videos
2. review service calls blob service to store images and videos -> returns the s3 urls
3. review service updates the elastic search index with user review text

# Summary of 100K products and 10 million DAU
  - A Product is 10 KB document, 5 - 20 images, 2-5 video reviews
  - image & video in blob storage (S3) with links in product
  - 100K products ~ 1 TB & with replication factor of 3 ~ 3 TB
  - images:
    - avg 10 images ~ 1 million images (hash based path to avoid s3 rate limiting and hot url)
  - videos:
    - avg 3 videos ~ 300K videos
  - mongo db 3 node replica set for 1 TB with replication and fault tolerance
  - elastic search
    - 1 shard can support 10 - 50GB
    - 1 TB ~ 20 to 100 shards  
  - redis/memcached
    - improve recommendation latency
    - hold inventory
    - 100K products * 10K key per product ~ 1 gig
  - edge caching
    - product images
    - videos
        
| Component          | Estimate             |
|--------------------|----------------------|
| 1 Product          | 10 KB                |
| Mongo db           | 3 node replica set   |
| elastic search     | ~ 50 shards          |
| Replication        | Factor = 3           |
| S3                 | 1 million images     |
| S3                 | 300K videos          |
| redis  (inventory) | 1 Gig (1 to 2 nodes) |

# Wrap Up

- scale system by 10X 

| Component          | Estimate    (100K products & 10 million users) | Estimate (1 million products & 100 million users) |
|--------------------|------------------------------------------------|---------------------------------------------------|
| 1 Product          | 10 KB                                          | no change                                         |
| Mongo db           | 1 shard(3 node replica set)                    | 8 shards                                          |                                       
| elastic search     | ~ 50 shards                                    | 500 shards                                        |                                        
| Replication        | Factor = 3                                     | no change                                         |
| S3                 | 1 million images                               | 10 miion images                                   |                                   
| S3                 | 300K videos                                    | 3 million videos                                  |
| redis  (inventory) | 1 Gig (1 to 2 nodes)                           | 20 nodes                                          |

- scaling to 100 million users means 
  - support for  multi region 
  - Async everywhere
  - multi-tenant 
  - Observability 
  - Failure & Error handling