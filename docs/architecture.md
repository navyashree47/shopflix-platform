# ShopFlix Architecture

## 1. Project Overview

ShopFlix is a production-style e-commerce and streaming platform designed to provide hands-on experience with modern DevOps practices.

The platform will initially focus on e-commerce functionality such as products and orders. Streaming functionality will be introduced later as an extension of the platform.

The main goal of this project is to build and operate the platform using real-world DevOps practices, including:

* Git and GitHub
* CI/CD
* Docker
* AWS
* Terraform
* Kubernetes
* Monitoring and logging
* Security
* Incident response

The platform will be developed incrementally instead of implementing all services at once.

---

## 2. Architecture Goals

The main goals of the ShopFlix architecture are:

1. Build the platform incrementally.
2. Keep services independently deployable where practical.
3. Make the platform scalable.
4. Provide reliable communication between services.
5. Separate development, staging, and production environments.
6. Make the system observable through metrics and logs.
7. Automate infrastructure and deployments.
8. Apply security best practices.
9. Make the platform suitable for troubleshooting and incident-response exercises.

The architecture should avoid unnecessary complexity. New technologies should be introduced when there is a clear requirement for them.

---

## 3. Services

The platform will eventually contain several services.

### Initial services

The first version of the platform will focus on:

### Product Service

Responsible for:

* Product information
* Product categories
* Product availability
* Product search

### Order Service

Responsible for:

* Creating orders
* Updating order status
* Retrieving orders
* Managing the order lifecycle

### Future services

The following services will be introduced as the platform grows:

* User Service
* Payment Service
* Notification Service
* Catalog Service
* Streaming Service
* Analytics Service

The services will be introduced gradually so that the platform does not become unnecessarily complex during the early stages.

---

## 4. Service Communication

ShopFlix will use a combination of synchronous and asynchronous communication.

### Synchronous communication

REST/HTTP will be used when a service needs an immediate response.

For example:

```text
Customer
   |
   v
API Gateway
   |
   v
Product Service
   |
   v
PostgreSQL
```

Another example is an order request that requires an immediate response from the Order Service.

### Asynchronous communication

Kafka will be used for events and background processing where an immediate response is not required.

For example:

```text
Order Service
      |
      | OrderCreated event
      v
    Kafka
      |
      +----------> Notification Service
      |
      +----------> Analytics Service
      |
      +----------> Payment Service
```

This approach allows services to process events independently and reduces direct dependencies between services.

---

## 5. Database Strategy

PostgreSQL will be the primary database technology for the initial version of ShopFlix.

It will be used for structured transactional data such as:

* Products
* Orders
* Users
* Payments

The project will initially avoid introducing multiple database technologies unless there is a clear technical requirement.

Using one primary database technology reduces operational complexity while the platform is being developed.

As the platform grows, individual services may receive separate database instances or schemas depending on their requirements.

The database strategy will prioritize:

* Data consistency
* Backups
* Security
* Monitoring
* Recovery
* Scalability

---

## 6. Caching

Redis will be used as the caching layer.

The purpose of Redis is to reduce unnecessary database queries and improve application response times.

Potential cache candidates include:

* Frequently accessed products
* Popular products
* Product catalog information
* Session information
* Frequently accessed metadata

Not all data should be cached.

Sensitive or highly transactional data such as payment transactions and critical order state will require careful consideration before being cached.

The initial caching architecture will be:

```text
Application
     |
     v
   Redis
     |
     | Cache miss
     v
PostgreSQL
```

---

## 7. Object Storage

Amazon S3 will be used for object storage.

S3 will eventually store large files such as:

* Product images
* Video files
* Static assets
* Application-generated files

Video content will not be stored directly inside PostgreSQL because object storage is more appropriate for large files.

The future streaming architecture will use S3 together with a CDN such as CloudFront.

```text
Streaming Service
       |
       v
      S3
       |
       v
   CloudFront
       |
       v
     User
```

---

## 8. Environments

ShopFlix will have three main environments.

### Development

The development environment will be used by developers for:

* Developing new features
* Testing changes
* Debugging
* Experimenting with new functionality

Changes can be frequent in this environment.

### Staging

The staging environment will be as close as practical to production.

It will be used for:

* Integration testing
* Deployment validation
* Release candidate testing
* Testing infrastructure changes
* Finding problems before production

### Production

Production will be the environment used by real users.

It will require stronger controls around:

* Security
* Availability
* Monitoring
* Backups
* Access control
* Deployments
* Incident response

The deployment flow will eventually be:

```text
Development
     |
     v
   Staging
     |
     v
 Production
```

---

## 9. V1 Architecture

The first version of ShopFlix will focus on the Product and Order functionality.

The initial architecture will be:

```text
                         INTERNET
                            |
                            v
                    +---------------+
                    | Load Balancer |
                    +-------+-------+
                            |
                            v
                    +---------------+
                    | API Gateway   |
                    +-------+-------+
                            |
                    +-------+-------+
                    |               |
                    v               v
             +-------------+  +-------------+
             |   Product   |  |    Order    |
             |   Service   |  |   Service   |
             +------+------+  +------+------+
                    |                |
                    v                v
                 +-----+        +-----------+
                 |Redis|        |PostgreSQL |
                 +-----+        +-----------+
                                      |
                                      v
                                   +------+
                                   |Kafka |
                                   +------+
                                      |
                         +------------+------------+
                         |            |            |
                         v            v            v
                   Notification   Analytics    Payment
                     Service       Service      Service

```

Some services shown in the diagram, such as Payment, Notification, and Analytics, will be implemented in later stages.

---

## 10. Future Architecture

As ShopFlix grows, the platform will evolve to include additional services and infrastructure.

The future platform may include:

* User Service
* Product Service
* Order Service
* Payment Service
* Notification Service
* Catalog Service
* Streaming Service
* Analytics Service
* Kafka
* Redis
* PostgreSQL
* Amazon S3
* CloudFront
* Kubernetes
* AWS infrastructure

The streaming portion will eventually provide a Netflix-style experience using object storage and CDN infrastructure.

---

## 11. DevOps and Infrastructure

The infrastructure will eventually be managed using Terraform.

Applications will be containerized using Docker.

Kubernetes will be used to orchestrate containers.

The expected deployment architecture will eventually look like:

```text
GitHub
   |
   v
GitHub Actions
   |
   v
Docker Build
   |
   v
Security Scan
   |
   v
Container Registry
   |
   v
Kubernetes
   |
   v
ShopFlix Services
```

Infrastructure will be separated into development, staging, and production environments.

---

## 12. Observability

The platform will include centralized monitoring and logging.

The planned observability stack is:

* Prometheus for metrics
* Grafana for dashboards
* Loki for logs

The platform will monitor metrics such as:

* Request rate
* Error rate
* HTTP 5xx responses
* Request latency
* CPU usage
* Memory usage
* Pod health
* Database connections
* Kafka queue/event activity

The goal is to make it possible to identify and troubleshoot production problems.

---

## 13. Security

Security will be considered throughout the project.

The platform will eventually implement:

* IAM
* Least-privilege access
* Secrets management
* Container image scanning
* Dependency scanning
* Network security
* Kubernetes RBAC
* TLS
* Secure handling of credentials
* Audit logging

Secrets and credentials must not be committed to Git.

---

## 14. Architecture Principles

The following principles will guide the project:

1. Keep the initial architecture simple.
2. Introduce complexity only when required.
3. Automate repetitive operations.
4. Treat infrastructure as code.
5. Keep environments reproducible.
6. Do not store secrets in Git.
7. Design for observability.
8. Test changes before production.
9. Document important operational decisions.
10. Practice failure recovery and incident response.

---

## 15. Project Evolution

ShopFlix will be developed in stages.

### Stage 1

Product Service + PostgreSQL

### Stage 2

Order Service + PostgreSQL

### Stage 3

Redis caching

### Stage 4

Docker containerization

### Stage 5

GitHub Actions CI/CD

### Stage 6

AWS infrastructure with Terraform

### Stage 7

Kubernetes deployment

### Stage 8

Kafka and asynchronous services

### Stage 9

Monitoring and centralized logging

### Stage 10

Streaming platform using S3 and CloudFront

### Stage 11

Production incidents, troubleshooting, scaling, security, and disaster-recovery exercises
