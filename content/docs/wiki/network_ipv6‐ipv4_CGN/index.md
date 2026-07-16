---
date: '2026-07-16T21:01:37+02:00'
draft: false
title: 'Network_ipv6‐ipv4_CGN'
---
# Network
Assuming a network where the host running Nextcloud snap acquires a static local IPv4 address from DHCP/router and the required ports 80 and 443 are enabled and internet facing for that IPv4 address, the routers public IPv4 address must be available via DNS (**D**omain **N**ame **S**ystem) request.

- [x] static local IPv4 address from DHCP/router
- [x] internet facing Ports 80 and 443 for local IPv4 address 
- [X] DNS entry pointing to routers public IPv4 address

> [!NOTE]
> [**CG-NAT**](https://en.wikipedia.org/wiki/Carrier-grade_NAT) (**C**arrier **G**rade **N**etwork **A**ddress **T**ranslation) commonly known as [**DSLite**](https://en.wikipedia.org/wiki/Dual-Stack_Lite) (**D**ual **S**tack **L**ite) will not make a public IPv4 address available and is not supported! 
>
> Contact your *service provider* and request a public IPv4 address (comp. **F**ull **D**ual **S**tack) or do some research for alternative [IPv6 to IPv4](https://en.wikipedia.org/wiki/6in4) tunneling.