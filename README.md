# Overview

The SP (Service Provider) integration library for CIP (Cloud Integration Platform) .

https://mvnrepository.com/artifact/com.soterianetworks

# Integration Ways

There are three ways to integrate the SP to CIP.

Way To Integration |  Description
---|---
| [CIP Managed](./cipped.md) | Everything is managed by CIP  (db, cache, mq ...)
| | And deployed as member in CIP 
[SP Managed](./sped.md)  | Everything is managed by SP (db, cache, mq ...)
| | The user authn & authz are delegated to CIP
| | The frontend is integrated into CIP in the way of OAuth2 IMPLICIT grant_type. 
| | * The micro service provided by SP will register to CIP's registry
| | * The frontend accesses the micro service through CIP gateway
|[Standalone](./standalone.md) | Everything is managed by SP (db, cache, mq ...)
| | The user authn & authz are delegated to CIP
| | The frontend is integrated into CIP in the way of OAuth2 IMPLICIT grant_type. 
| | * SP manages the micro service by itself
| | * The frontend accesses the micro service in its own way

