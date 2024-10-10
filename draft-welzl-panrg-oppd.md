---
title: "On-Path Proxy Discovery"
category: info

docname: draft-welzl-panrg-oppd-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: "IRTF"
workgroup: "Path Aware Networking RG"
keyword:
 - next generation
 - unicorn
 - sparkling distributed ledger
venue:
  group: "Path Aware Networking RG"
  type: "Research Group"
  mail: "panrg@irtf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/panrg"
  github: "mwelzl/oppd"
  latest: "https://mwelzl.github.io/oppd/draft-welzl-panrg-oppd.html"

author:
 -
    fullname: "Michael Welzl"
    organization: University of Oslo
    email: "michawe@ifi.uio.no"

normative:

informative:
  Sidekick:
      title: "Sidekick: In-Network Assistance for Secure End-to-End Transport Protocols"
      seriesinfo: Usenix NSDI 2024
      authors:
        -
          ins: G. Yuan
          name: Gina Yuan
        -
          ins: M. Sotoudeh
          name: Matthew Sotoudeh
        -
          ins: D. Zhang
          name: David K. Zhang
        -
          ins: M. Welzl
          name: Michael Welzl
        -
          ins: D. Mazieres
          name: David Mazieres
        -
          ins: K. Winstein
          name: Keith Winstein
      date: 2024
  SMAQ:
      title: "Secure Middlebox-Assisted QUIC"
      seriesinfo: IFIP NETWORKING 2023
      authors:
        -
          ins: M. Kosek
          name: Mike Kosek
        -
          ins: B. Spiess
          name: Benedikt Spiess
        -
          ins: J. Ott
          name: Joerg Ott
      date: 2023
  I-D.kuehlewind-quic-proxy-discovery:
  I-D.ietf-tsvwg-udp-options:


--- abstract

[ Please add something to this draft, and then add your name to the author list. Also, consider the draft title: it now indicates that I'm the primary dude here (is that really justified?), and that we target PANRG. Should it carry a different name? ]

This document surveys possibilities for On-Path Proxy Discovery (OPPD). It is meant to help the conversation in a planned side meeting at IETF-121 in Dublin.


--- middle

# Introduction

Proxies can carry out functions that improve the performance of an end-to-end connection. These function can be quite diverse, ranging from minimal help (e.g. just offering information) to more significant interference, e.g. splitting an end-to-end connection in half, for reliability, congestion control or both.

It is commonly desirable for such proxies to be located on the path(s) that a connection already traverses, rather than using a tunneling or forwarding approach to enforce a path. This is naturally the case for transparent "Performance Enhancing Proxies" (PEPs) that have been implemented for TCP, but the transparent nature of such proxies has caused a number of known problems in the past. Non-transparent proxies leave the choice of utilizing and configuring a performance enhancing function to end systems -- and such a choice requires a means to detect the proxy and explicitly communicate with it. With QUIC, the encryption of packet headers necessitates using non-transparent instead of transparent proxies, and research works such as the Sidekick {{Sidekick}} and Secure Middlebox-Assisted QUIC (SMAQ) {{SMAQ}} have shown that this is both possible and beneficial.

There are various ways in which On-Path Proxy Discovery (OPPD) can work, and they differ from the ways in which end systems learn about proxies that are not necessarily on-path.

This document surveys some possibilities that are available for OPPD.


# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Terminology

* Base connection: an end-to-end connection between two endpoints on which an on-path proxy is expect to carry out a performance-improving function.

* Endpoint: an entity that communicates with one or more other endpoints using a specific transport protocol. It is locally identified by the 5-tuple of IP address pair, protocol and port numbers.


# General assumptions

* On-path proxy devices are expected to carry out functions in relation to a base connection. Thus, they must be on the same path, which means that communication with them must use the same 5-tuple as the base connection.

* Endpoint initiation: OPPD must be initiated by an endpoint. First, in the presence of NATs, this is the only way to ensure that communication with the proxy use the same IP addresses and port numbers as the base connection. Second, in this way, endpoints can execute some kind of flow control to avoid the reception of many unsolicited announcements.

* Proxies must somehow prove that they are on-path, perhaps akin to the way it is done with several ICMP messages, by including the first 64 bits of the original packet that evoked them {{?RFC792}}. This establishes a certain minimal level of trust, since on-path devices are in the position to do whatever they want with a connection's packets anyway -- for example discard, rate-limit or duplicate them.

# A survey of possibilities

Please insert your ideas below -- or add a description of a scheme that already exists.

## Endpoint to proxy

### Special packet of base connection

Sidekick {{Sidekick}} endpoints signal proxy support by sending a distinguished packet containing a 128-byte sidekick-request marker. Such inline signaling could confuse receiving endpoints, but sidekicks target protocols such as QUIC that discard cryptographically unauthenticated data anyway.

Secure Middlebox-Assisted QUIC {{SMAQ}} leaves the design of a proxy discovery solution as future work, but also suggests to multiplex the necessary signaling over the same 5-tuple as the base connection. The paper mentions that, in this case, "an open problem still to overcome is the possible collision of Connection IDs".

### Header options

An endpoint could use a newly defined TCP option or a UDP option
{{?I-D.ietf-tsvwg-udp-options}} to signal proxy support. Such an option
could be defined to be ignored by the receiving endpoint, and receiving
endpoints that are not upgraded to support the option should ignore
it anyway. In case of QUIC, for example, the QUIC implementation at the
receiving endpoint would not even be informed about the message in the
option. This approach might have the advantage of not possibly confusing
the receiving endpoint, and it does not require the endhost to send an
additional packet.


## Proxy to endpoint

### Special packet of base connection

A sidekick proxy replies to a sidekick-request packet by sending a special packet from the receiver's IP address and port number back to the endpoint {{Sidekick}}. This packet contains a sidekick-reply marker, an opaque session ID, and an IP address and port number for communicating with the proxy. Upon receiving the sidekick-reply packet, the sender begins communicating directly with the proxy from a different UDP port. It initially sends back the session ID and configuration parameters to start receiving quACKs (special ACKs crafted by a Sidekick proxy).

### Header options

A proxy could use insert a newly defined TCP option or a UDP option
{{?I-D.ietf-tsvwg-udp-options}} in a returning packet (e.g., an ACK)
to answer back to the endpoint that originally announced its proxy support.
This approach does not require the proxy to send an additional packet.



# A survey of open issues

Just an idea, having a separate list of common problems to be considered might be helpful. For example:

* How to handle multiple proxies on a path?
* How to deal with multi-path?
  * Suggestion (Michael): we ignore it, we just apply the discovery per path. Endpoints are expected to initiate the discovery process for every path at which they want to make use of a proxy should a proxy be available.

This list will become longer as we add mechanisms to the preceding section.


# Examined material that was not included

{{?I-D.kuehlewind-quic-proxy-discovery}} lists several possibilities for proxy discovery, but the proxies in question need not be on-path. One notable possibility mentioned in {{?I-D.kuehlewind-quic-proxy-discovery}} document is the use of PCP; this is, in some sense, an on-path discovery method since NATs are necessarily on-path. However, there is no reason to limit the discovery process described in the present document to scenarios with NATs only.


# Security Considerations

TODO.


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
