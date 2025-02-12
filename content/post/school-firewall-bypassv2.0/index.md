---
title: Firewall Bypass V2.0 (WIP)
description: How to bypass almost any firewall. Using a raspberry pi and a phone/sim card.
slug: firewall-bypass-2
date: 2025-02-12 00:00:00+0000
---


***This is for education purposes only, use at your own risk***

### This guide will speak about how to bypass a firewall, using wireguard tunneling, a raspberry pi and an external wifi source. This guide is an alternative version to the first guide, using a more complicated and less straightforward approach. This usecase is if you would have no access to an exposed/unmonitored port.

For this to work you will need:

1. A linux-based SBC (Single Board Connector)
2. An access to the wifi of the organisation/firewalled network
3. A simcard adapter for the external internet access (or a phone acting as a hotspot, although this is more expensive and requires two wifi adapters)
4. A power source for the SBC
5. Linux knowledge
6. Common sense

General outline of this guide.
We will use nmcli to configure the SBC to connect to the wifi of the organisation, then we will have to setup a wireguard server to let our devices connect to the SBC. After this, we will need to setup a route from our wifi interface to our sim interface to use it as our primary internet source (gateway).

