# Overview

The SP (Service Provider) base library for integration with Soteria Networks CIP (Cloud Integration Platform) .

https://mvnrepository.com/artifact/com.soterianetworks

# Integration Modes

There are three ways to integrate a SP to the CIP.

Integration Mode|  Description
---|---
| [CIP Managed](./cipped.md) | Everything is managed by CIP  (db, cache, messaging ...)
| | Deployed as part of the CIP, usually a platform service. 
[SP Managed](./sped.md)  | Everything is managed by SP (db, cache, messaging ...)
| | The user authn & authz are delegated to CIP
| | The frontend is integrated with CIP by way of OAuth2 IMPLICIT grant_type. 
| | * The micro service provided by SP will register to CIP's registry
| | * The frontend accesses the micro service through CIP gateway
|[Standalone](./standalone.md) | Everything is managed by SP (db, cache, messaging ...)
| | The user authn & authz are delegated to CIP
| | The frontend is integrated with CIP by way of OAuth2 IMPLICIT grant_type. 
| | * SP manages the backend micro service by itself
| | * The frontend accesses the backend micro service directly

