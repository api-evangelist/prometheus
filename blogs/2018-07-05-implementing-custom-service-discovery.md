---
title: "Implementing Custom Service Discovery"
url: "https://prometheus.io/blog/2018/07/05/implementing-custom-sd/"
date: "2018-07-05T00:00:00.000Z"
author: "Callum Styan"
feed_url: "https://prometheus.io/blog/feed.xml"
---
Prometheus contains built in integrations for many service discovery (SD) systems such as Consul, Kubernetes, and public cloud providers such as Azure. However, we can’t provide integration implementations for every service discovery option out there. The Prometheus team is already stretched thin supporting the current set of SD integrations, so maintaining an integration for every possible SD option isn’t feasible.
