
# Online Course Management Platform (B2B SaaS)

Summary: This is Multi-Tenant application which can be used by schools, colleges, and training institutes. Each of the Tenant will have its own branding, users and courses.

---
Based on functional requirement, I'm thinking below would be the core microservices, we need to implement:
## 🧩 Core Microservices

- **Tenant Service** – to on board clients, brand and their other configs. 
- **Auth Service** – to Authenticate the system users like Tenant and its users.  
- **User Service** – to manage the system Users and to grant Role & Access based on user type.  
- **Course Service** – to create courses, its versioning, draft, publish - only Teacher/Admin role can have create/update/remove & publish access.
- **Assignment Service** – to create assignments & quizz and evaluation.
- **Streaming Service** – to stream live classes or courses videos from cloud storage.
- **Forum Service** – to process and store discussions, comments, feedback or query.
- **Certification Service** – to generate certificate of completion courses.
- **Reporting Service** – progress report of User and other analytics of Tenant & Users like how many reviews/ratings of a course.
- **Notification Service** – to notify the Users or Tenants via SMS/Email.
- **Audit Service** – Tracks user actions for GDPR compliance via logs.

---

## 🗃️ Database Design

### SQL Tables

- tenants  
- users  
- courses  
- course_content  
- enrollments  
- assignments  
- submissions  
- live_classes  
- course_progress  
- certificates  

### NoSQL Collections

- app_logs  
- forum_discussion  

**ER Diagram:** [ER Diagram on dbdiagram.io](https://dbdiagram.io/d/682818081227bdcb4eb7c9bf)

---

## 👥 System Roles

- **SuperAdmin** – Manages tenants and system users.  
- **TenantAdmin** – Manages branding, users and courses.
- **Tutor** – Creates & manages courses, assignments, live sessions
- **Trainee** – Enrolls in courses, submits work, attends sessions

---

## 🧱 Tech Stack

- **Springboot** – to implement core microservices 
- **AWS Lambda** – to send application notification to the end user.
- **Kafka** – to enable async processing like course upload, audit logs, certificate generation, triggers aws lambda for notification.  
- **SQL DB** – PostgreSQL
- **NoSQL DB** – DynamoDB
- **Redis Cache** – Cache course metadata, user sessions, live class URLs to reduce hit on database.

---

## ☁️ Infrastructure Setup

- **Route 53 (DNS)** – ensures global DNS resolution and supports subdomain. 
- **CloudFront (CDN)** – low latency delivery of static assets logos & videos and API responses.
- **API Gateway** – authenticates requests, routes to microservices, subdomain routing, rate limiting.  
- **VPC** – restricts external access for all core components like services, DB, Kafka, Redis Server.
- **WAF** – to implement on CloudFront & API Gateway to prevent attacks.
- **EKS Cluster** – Runs Spring Boot microservices with scalability and tenant isolation based on namespace.
- **RDS** – to store SQL data and need to implement partition tables, read replicas. it should be shard by tenant_id.  
- **DynamoDB** – to store NoSQL data like logs, errors, likes, reviews, ratings.it should be shard by tenant_id.
- **AWS S3** – store video lectures, live recording and its relevant document. Tenant static assests like logo, images.
- **External APIs** – third-party api integration for live class Zoom & Google Meet.

---

## 🔒 Security & GDPR Compliance

- **Tenant Isolation** - PostgreSQL RLS checks for each tenant. EKS namespaces to limit resource per tenant.
- **Authentication** - Auth0 with OAuth 2.0 anf JWT with tenant_id, role.
- **Authorization** - Role based access of resources.
- **Audit Logging** - app_logs via Kafka.
- **Network** - VPC for EKS, RDS and Kafka. WAF on CloudFront/API Gateway.
- **AWS Secrets Manager** - to store API keys also we can rotate keys regularly.

---

## ⚙️ Scalability & Availability

- **Multi Region** - we should deploy app based on users region.
- **RDS** - master-slave with read replicas and tenant-based sharding. Indexing.
- **Deployment** - Blue-Green deployment strategy  
- **Backups** - Daily automated DB snapshots.

---

## 📊 Monitoring & Observability

- **Logging:** CloudWatch for AWS, ELK Stack for Spring Boot logs.
- **Tracing:** AWS X-Ray for microservice requests.
- **Alerting:** PagerDuty for critical issues.
- **Metrics:** Prometheus + Grafana  
  - Alert on latency > 100ms  
  - Alert on errors > 1%

---

## 📌 Appendix

- **ER Diagram:** [https://dbdiagram.io/d/682818081227bdcb4eb7c9bf](https://dbdiagram.io/d/682818081227bdcb4eb7c9bf)  
- **Architecture Diagram:** Refer Draw.io ([https://www.eraser.io/ai/architecture-diagram-generator](https://viewer.diagrams.net/?tags=%7B%7D&lightbox=1&highlight=EEEEF7&edit=_blank&layers=1&nav=1&dark=auto#R%3Cmxfile%3E%3Cdiagram%20name%3D%22Page-1%22%20id%3D%22oSqO3GrDE7ehOkx5gg81%22%3E7Vxbd5s4EP41eawPIK6PvsTdtmlPtunutk85spGxNhhxQL7111cCgUFSYjeOTS5NzkmsQegy33yj0SB8AYaLzfsMpvPPJETxhWWEmwswurAsExiA%2FeOSbSnxKkGU4VBU2glu8E8khIaQLnGI8lZFSkhMcdoWTkmSoCltyWCWkXW72ozE7V5TGCFFcDOFsSr9D4d0Xkp9x9jJ%2F0I4mlc9m4a4soBVZSHI5zAk64YIXF6AYUYILT8tNkMUc%2BVVeinvG99ztR5YhhJ6yA3Lj7dr48und9%2BYnv92P%2F7Eo2TyzvHE4Oi2mjEKmQJEkWR0TiKSwPhyJx1kZJmEiDdrsNKuzhUhKROaTPg%2FonQr0IRLSphoThexuFr2yTu6dy5ClJNlNkUPTcASRgGzCNGHKjq1zpmxIrJANNuyGzMUQ4pX7ZFAYTVRXW%2BnWPZB6PZ39CyGuYLxUnSlKJ4saYwTNKwtmWs3hPm8VvUKZRQz27yCExRfkxxTTBJ2bUIoJYtGhX6MI36BckAGUJSmTLsoa0PBrDLl%2FS82ESdwD65z0GMAU3TrAHZ9huN4SGKSFWMEY8d3gMvkrHaIWXvVtYQkqEaWDwNtHsZWRULcAARfhMMwA1Fe7%2BjnGT2nFM4b3POdSvr08NkvCb5pTJbhLCNc3x0AaLpmC0FbA6CrAzAAJ4IvCF66l3MO9HKe3amXc14STWCKbyNI0RpuVZ6Mgr5nm2flCeieKI67H8D9zGkoHiVhn0dhHL6YTO%2B4KJ4U5QpFDg%2Bza1rVEwpmd45xXANIM3KHGvg4tjNwbYWmnUQW93CuAWMdFR7MQ9HcNcFs3LXJBO21kYWb7RbKSYmbmrGg1I7pSIusZ7QbKietNFQYVT3Fx9uZaZiPsKujPTLaYPqd385IVZZ%2BiMb459GmWdg%2BvUV59qEW5XfqxX3FCdwwh%2BPGPJwI8Yp9jGihmVI0yWQJ67ZV79kuAbkmxL00HMsGpw1xZfqpjr%2Bq0XT77sncfqAgPtzFkDJ8bN60rVtF7zI8CxyGJYVRjn%2FCSdEUBzrlHqaYjjO4cEa8LcbavCSw6vgFBE3EnnA9doKe5bQdrAqMrwEGGCcCxlW3jf3rD0zwXsQtbwcbW8bGBio4wVnBAQo4X%2FnGmYkc8GaQAfVevHJmrm7Dfk5c7E5ii4SN%2FXsVQfDCj12kwYu78KIobZula5RhNnluBU8exr6MoMNTMyz%2FXg8VINuWP2AmNDQ4A6whLxUuoiWQy15bYKol3kZbIJe9tsCUmzel%2Fk15gA2BUmo1b0j9G40BFrTXRlL3xC0NW13PMUU3KSwMZ83CIe4d2FIvjNu0qrJQPG91ysqQ9ZWJNgokUHa5QiUgZZ04hmmOd54qQ9NlljPr%2BYqEezL0sZjdixgF02L4H6ZFsKdevV2lU0u3G%2FSH9ng81vu4h0PFGM24W86ZMnASXRWlETCEAhpd9PsDb%2BDLgeoTOFDHkIJBp85jNjyoHWjyAHYVHzx9xszvwoce4PYedB77d2DHPgTQb6fleN4%2F727aU9Nu%2FSUzHMuFC27rySRPy91Zq3yDshVm6pKBbsOo8xUKZHsI8FupStGM3Y4qgKtQwtRRAtinYsRzfVJ2JCO80zDCkpyanKk6NSU8hRLfUAJ5Zy%2BaFJYUbAd217RwXyct3PPQorLTc9FCze%2F%2Fk6PslZFCtwM9Myuc18mKY7eIh7HCss%2FMCjXhPWSqyNEr44WlyZidlxeemmju5znbiS3Qq1uagdG1tn01e%2FyFUDxju%2BHieYplXMHFJISKRl9urvIxyAFfAs7RAAfOmcH01cyygtHzeYgWF0Z0O1sm07IH7Zkj%2B%2F7M1Ok4abYXFuCryLqBCqx3qrSKf8A5svwO0elcyx6R79TmPHV5T23uU81%2FtqoVGUlND7JQJ%2FNUoalWq5KYqlAn02Vs5btNzd2mdPf9%2BVIp0cd%2BxxxS2YqHgeXoz6LMip8z0G9fjhbmaTmtGd7wcegTrcxDF4FemWblDluXcM3C%2FKTUNNw2NdX8ThWWtZjpn4qZag7t6%2Bjmja%2BMthSYqxjpjimcblk84HTaH%2B%2F5x3s%2BA%2B8ZbhO4IOHknC7U1EQ35%2FWhatJ1VOhhpJ4Be1uOFFQn0OtnfB0fLPLVlIcCkUatMkMegq9%2B30nZmMjbg7HbN4z7YFJIGOMJnMDb4iWH3h2c3cFTIieduLU1J8Ict%2BcGzR9XBdI%2B2Y5Czah8KnTytglnS7ABq9vQJVAPhL2GhHDFjr2Hi%2Fxjn6ccp31DDe5RyhSKk4iJX1pi0en8WXg3p%2FfPfMLuOGpUK%2Bx%2BaoBOqfEmDkse%2BdgrOBTKTt%2B%2BM43nusgcelbVNKwDNd3te471OB8Kn%2FGi%2BGoBNWhOG%2FbLOsNpjhom%2FdiYur0Nty3PGwyYPOb75wGc3kUF0FWVEM3gsnhXR94Vi5OQ3wr0%2BWswYh4jvGBr5ZhF3%2Bwvniz432SWQRbqLad0mSGuSZJgSjJ2%2B21Mooj97%2BWr6NDFcc8bAIbXjumA5WrOSZqWJqyrhSewBDWjf1VO%2FYJ3Kp5gln%2BZ5HOtoVcUoO8Dzm0DZwVa4OynicdZcfedGuUJgd03k4DLXw%3D%3D%3C%2Fdiagram%3E%3C%2Fmxfile%3E))
