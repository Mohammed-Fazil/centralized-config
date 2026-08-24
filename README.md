# Centralized Config Repository

This repository contains the centralized configuration for the `order-service` and `payment-service` applications.

The configuration is managed separately from the application source code and is consumed by a Spring Cloud Config Server.

## Repository Structure

```text
centralized-config-repo/
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

## Configuration Levels

### Global Configuration

The `global` folder contains configuration shared across services.

### Order Service Configuration

The `order-service` folder contains default and environment-specific configuration for Order Service.

### Payment Service Configuration

The `payment-service` folder contains default and environment-specific configuration for Payment Service.

## Environments

The following environments are supported:

- `default` — Base configuration
- `dev` — Development environment
- `qa` — QA environment
- `prod` — Production environment

## Configuration Flow

```text
                    GitHub
                      │
                      ▼
          centralized-config-repo
                      │
                      ▼
              Config Server
                   :8888
                      │
             ┌────────┴────────┐
             │                 │
             ▼                 ▼
       Order Service     Payment Service
```

The services do not store their environment-specific configuration locally.

Instead, they request their configuration from the Spring Cloud Config Server.

## Example

If `order-service` starts with:

```properties
spring.application.name=order-service
spring.profiles.active=dev
```

the Config Server retrieves the configuration associated with:

```text
order-service + dev
```

Similarly, if `payment-service` starts with:

```properties
spring.application.name=payment-service
spring.profiles.active=qa
```

it retrieves the QA configuration for Payment Service.

## Purpose

This repository is used to practice:

- Spring Cloud Config
- Centralized configuration
- Git-backed configuration
- Environment-specific configuration
- Service-specific configuration
- Configuration profiles
- Configuration precedence
- Externalized application configuration
- Configuration refresh

## Important

Do not store sensitive information such as:

- Passwords
- API keys
- JWT secrets
- Database credentials
- Private tokens

in this repository.

For this practice project, configuration values are intentionally kept simple so that configuration loading and precedence can be easily tested.
