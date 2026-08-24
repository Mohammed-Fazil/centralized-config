# Centralized Configuration

This repository contains centralized Spring Boot configuration properties for multiple services.

## Repository Structure

```text
centralized-config/
│
├── global/
│   ├── application.properties
│   ├── application-dev.properties
│   ├── application-qa.properties
│   └── application-prod.properties
│
├── order-service/
│   ├── order-service.properties
│   ├── order-service-dev.properties
│   ├── order-service-qa.properties
│   └── order-service-prod.properties
│
└── payment-service/
    ├── payment-service.properties
    ├── payment-service-dev.properties
    ├── payment-service-qa.properties
    └── payment-service-prod.properties
```

## Global Configuration

The `global` folder contains properties shared across services.

### Default

```text
global/application.properties
```

Contains common default properties.

### Environment-Specific

```text
global/application-dev.properties
global/application-qa.properties
global/application-prod.properties
```

Contain properties specific to each environment.

## Service Configuration

Each service has its own folder.

For example:

```text
order-service/
```

contains:

```text
order-service.properties
order-service-dev.properties
order-service-qa.properties
order-service-prod.properties
```

### Service Default

```text
order-service/order-service.properties
```

Contains properties common to Order Service across environments.

### Service Profiles

```text
order-service/order-service-dev.properties
order-service/order-service-qa.properties
order-service/order-service-prod.properties
```

Contain environment-specific properties and overrides.

The same structure is used for `payment-service`.

## Profiles

The supported profiles are:

```text
dev
qa
prod
```

The active Spring profile determines which environment-specific properties are loaded.

For example:

```text
order-service + qa
```

uses the configuration from:

```text
global/application.properties
global/application-qa.properties
order-service/order-service.properties
order-service/order-service-qa.properties
```

## Configuration Precedence

Configuration is resolved from general to specific.

For `order-service` with the `qa` profile:

```text
global/application.properties
        ↓
order-service/order-service.properties
        ↓
global/application-qa.properties
        ↓
order-service/order-service-qa.properties
```

The most specific property source has the highest precedence.

Therefore, if the same property is defined in multiple files, the service-specific profile configuration takes priority.

### Example

```properties
# global/application.properties
message=This is global default
```

```properties
# global/application-qa.properties
message=This is global qa
```

```properties
# order-service/order-service.properties
message=This is order-service default
```

```properties
# order-service/order-service-qa.properties
message=This is order-service qa
```

For `order-service` with the `qa` profile, the final value is:

```properties
message=This is order-service qa
```

## Global vs Service Configuration

Use the `global` folder for properties that are shared by multiple services.

Example:

```properties
environment=QA
```

Use the service folder for properties that belong specifically to one service.

Example:

```properties
order.timeout=5000
order.owner=Order QA
```

This keeps shared configuration separate from service-specific configuration.

## Configuration Refresh

Configuration is normally loaded when a service starts.

For refreshable configuration, Spring Cloud supports:

```text
@RefreshScope
```

A service can manually request updated configuration through:

```text
POST /actuator/refresh
```

After the refresh, refreshable beans can use the latest configuration obtained through the Config Server.

## Spring Cloud Bus Refresh

When there are multiple services, refreshing every service individually is inconvenient.

Spring Cloud Bus allows a refresh event to be distributed to multiple services through a message broker such as RabbitMQ.

The concept is:

```text
Configuration Repository
          │
          ▼
    Config Server
          │
          ▼
     Bus Refresh
          │
          ▼
       RabbitMQ
       /            ▼        ▼
 Order Service  Payment Service
      │              │
    Refresh        Refresh
```

A single bus refresh event can notify the connected services so they can refresh their configuration.

This is useful when the same centralized configuration change needs to be applied across multiple running services.

## Configuration Flow

```text
                Centralized Config
                      Repository
                          │
                          ▼
                   Config Server
                          │
              ┌───────────┴───────────┐
              ▼                       ▼
        Order Service           Payment Service
              │                       │
              ▼                       ▼
       Resolved Properties      Resolved Properties
```

With Bus Refresh:

```text
                 Config Server
                      │
                      ▼
                Spring Cloud Bus
                      │
                      ▼
                   RabbitMQ
                 /                          ▼            ▼
         Order Service   Payment Service
              │               │
           Refresh         Refresh
```

## Purpose

This repository provides:

- Global default properties
- Global environment-specific properties
- Service-specific default properties
- Service-specific environment properties
- Profile-based configuration
- Configuration precedence
- Centralized configuration
- Runtime configuration refresh
- Spring Cloud Bus based refresh for multiple services
