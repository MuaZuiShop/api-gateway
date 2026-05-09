# API Gateway - Vietnam Restaurants

## Overview

This module implements the **API Gateway** utilizing Spring Cloud Gateway. It acts as the single, unified entry point for all incoming client requests (Web, Mobile) directed towards the microservices ecosystem.

**Core Responsibilities:**

* **Dynamic Routing:** Intercepts incoming HTTP requests and forwards them to the appropriate downstream microservice based on predefined URL path configurations.
* **Service Discovery Integration:** Integrates seamlessly with the Eureka Service Registry to resolve logical service names to physical IP addresses, enabling dynamic client-side load balancing via the `lb://` scheme.
* **Cross-Cutting Concerns:** Centralizes infrastructure-level policies including Authentication/Authorization validation, Cross-Origin Resource Sharing (CORS) configurations, Rate Limiting, and unified logging mechanisms.

## Configuring Routes for New APIs

When a new microservice is deployed and successfully registered with Eureka, the API Gateway must be updated with specific routing rules to expose its endpoints to external clients.

### Configuration Guide

Routing configurations are defined within the `application.yml` (or `application.properties`) file under the `spring.cloud.gateway.routes` node.

**Implementation Example:** Assuming the deployment of a new service named `booking-service` (defined by its `spring.application.name`), the following configuration routes all traffic matching `/api/v1/bookings/**` to this specific instance.

```yaml
spring:
  cloud:
    gateway:
      routes:
        # Existing configuration example
        - id: restaurant-service
          uri: lb://restaurant-service
          predicates:
            - Path=/api/restaurants/**
            
        # NEW SERVICE CONFIGURATION BLOCK
        - id: booking-service # 1. Route Identifier (Conventionally matches the service name)
          uri: lb://BOOKING-SERVICE # 2. Target URI leveraging Eureka for discovery and load balancing
          predicates:
            - Path=/api/bookings/** # 3. Path predicate determining route matching criteria
```

### Property Definitions:

* `id`: A unique identifier for the specific route definition.
* `uri`: The destination address. Utilizing the `lb://<service-name>` syntax instructs the Gateway to query Eureka for available instances and apply load balancing algorithms.
* `predicates`: A collection of criteria that determine whether a route matches an incoming request. The `Path` predicate is standard for RESTful APIs.

**Note:** The API Gateway requires a restart (or a refresh via Spring Cloud Config/Actuator) to apply routing configuration changes.