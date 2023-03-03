# Keycloak Event Metrics

Provides metrics for Keycloak user/admin events.

[![Apache License, Version 2.0, January 2004](https://img.shields.io/github/license/kokuwaio/keycloak-event-metrics.svg?label=License)](http://www.apache.org/licenses/)
[![Maven Central](https://img.shields.io/maven-central/v/io.kokuwa.keycloak/keycloak-event-metrics.svg?label=Maven%20Central)](https://central.sonatype.com/search?namespace=io.kokuwa.keycloak&q=keycloak-event-metrics)
[![CI](https://img.shields.io/github/actions/workflow/status/kokuwaio/keycloak-event-metrics/ci.yaml?branch=main&label=CI)](https://github.com/kokuwaio/keycloak-event-metrics/actions/workflows/ci.yaml?query=branch%3Amain)

## What?

Resuses micrometer from Quarkus distribution to add metrics for Keycloak for events.

### User Events

User events are added with key `keycloak_event_user_total` and tags:

* `type`: [EventType](https://github.com/keycloak/keycloak/blob/main/server-spi-private/src/main/java/org/keycloak/events/EventType.java#L27) from [Event#type](https://github.com/keycloak/keycloak/blob/main/server-spi-private/src/main/java/org/keycloak/events/Event.java#L44)
* `realm`: realm id from [Event#realmId](https://github.com/keycloak/keycloak/blob/main/server-spi-private/src/main/java/org/keycloak/events/Event.java#L46)
* `client`: client id from [Event#clientId](https://github.com/keycloak/keycloak/blob/main/server-spi-private/src/main/java/org/keycloak/events/Event.java#L48)
* `error`: error from [Event#error](https://github.com/keycloak/keycloak/blob/main/server-spi-private/src/main/java/org/keycloak/events/Event.java#L56), only present for error types

Examples:

```txt
keycloak_event_user_total{client="test",realm="9039a0b5-e8c9-437a-a02e-9d91b04548a4",type="LOGIN",error="",} 2.0
keycloak_event_user_total{client="test",realm="1fdb3465-1675-49e8-88ad-292e2f42ee72",type="LOGIN",error="",} 1.0
keycloak_event_user_total{client="test",realm="1fdb3465-1675-49e8-88ad-292e2f42ee72",type="LOGIN_ERROR",error="invalid_user_credentials",} 1.0
```

### Admin Events

Admin events are added with key `keycloak_event_admin_total` and tags:

* `realm`: realm id from [AdminEvent#realmId](https://github.com/keycloak/keycloak/blob/main/server-spi-private/src/main/java/org/keycloak/events/admin/AdminEvent.java#L44)
* `operation`: [OperationType](https://github.com/keycloak/keycloak/blob/main/server-spi-private/src/main/java/org/keycloak/events/admin/OperationType.java#L27) from [AdminEvent#operationType](https://github.com/keycloak/keycloak/blob/main/server-spi-private/src/main/java/org/keycloak/events/admin/AdminEvent.java#L53)
* `resource`: [ResourceType](https://github.com/keycloak/keycloak/blob/main/server-spi-private/src/main/java/org/keycloak/events/admin/ResourceType.java#L24) from [AdminEvent#resourceType](https://github.com/keycloak/keycloak/blob/main/server-spi-private/src/main/java/org/keycloak/events/admin/AdminEvent.java#L51)
* `error`: error from [AdminEvent#error](https://github.com/keycloak/keycloak/blob/main/server-spi-private/src/main/java/org/keycloak/events/admin/AdminEvent.java#L59), only present for error types

Examples:

```txt
keycloak_event_admin_total{error="",operation="CREATE",realm="1fdb3465-1675-49e8-88ad-292e2f42ee72",resource="USER",} 1.0
keycloak_event_admin_total{error="",operation="CREATE",realm="9039a0b5-e8c9-437a-a02e-9d91b04548a4",resource="USER",} 1.0
```

## Installation

### Testcontainers

For usage in [Testcontainers](https://www.testcontainers.org/) see [KeycloakExtension.java](src/test/java/io/kokuwa/keycloak/metrics/junit/KeycloakExtension.java#L57-L68)

### Docker

Dockerfile:

```Dockerfile
FROM quay.io/keycloak/keycloak:21.0.1

ENV KEYCLOAK_ADMIN=admin
ENV KEYCLOAK_ADMIN_PASSWORD=password
ENV KC_HEALTH_ENABLED=true
ENV KC_METRICS_ENABLED=true
ENV KC_LOG_CONSOLE_COLOR=true

ADD target/keycloak-event-metrics-0.0.1-SNAPSHOT.jar /opt/keycloak/providers
RUN /opt/keycloak/bin/kc.sh build
```

Run:

```sh
docker build . --tag keycloak:metrics
docker run --rm -p8080 keycloak:metrics start-dev
```
