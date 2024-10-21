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
  I-D.trammell-plus-spec:
  I-D.ietf-tsvwg-udp-options:


--- abstract

This document surveys possibilities for On-Path Proxy Discovery (OPPD). It is meant to help the conversation in a planned side meeting at IETF-121 in Dublin (see the github page linked under "About This Document" for coordinates).

The draft title indicates PANRG as a target of this document just because we thought that PANRG should be informed. What a suitable target is, and if this is even the right type of document, should all be discussed at the side meeting.

--- middle

# Introduction

Proxies can carry out functions that improve the performance of an end-to-end connection. These function can be quite diverse, ranging from minimal help (e.g. just offering information) to more significant interference, e.g. splitting an end-to-end connection in half, for reliability, congestion control or both.

It is commonly desirable for such proxies to be located on the path(s) that a connection already traverses, rather than using a tunneling or forwarding approach to enforce a path. This is naturally the case for transparent "Performance Enhancing Proxies" (PEPs) that have been implemented for TCP, but the transparent nature of such proxies has caused a number of problems in the past. Non-transparent proxies leave the choice of utilizing and configuring a performance enhancing function to end systems -- and such a choice requires a means to detect the proxy and explicitly communicate with it. With QUIC, the encryption of packet headers necessitates using non-transparent instead of transparent proxies, and research works such as the Sidekick {{Sidekick}} and Secure Middlebox-Assisted QUIC (SMAQ) {{SMAQ}} have shown that this is both possible and beneficial.

There are various ways in which On-Path Proxy Discovery (OPPD) can work, and they differ from the ways in which end systems learn about proxies that are not necessarily on-path.

This document surveys some possibilities that are available for OPPD.


# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Terminology

* Base connection: an end-to-end connection between two endpoints on which an on-path proxy is expected to carry out a performance-improving function.

* Endpoint: an entity that communicates with one or more other endpoints using a specific base connection. It is locally identified by the base connection's 5-tuple of IP address pair, protocol and port numbers.


# General assumptions {#general}

* On-path proxies are expected to carry out functions in relation to a base connection. Thus, they must be on the same path, which, given the prevalence of functions such as Equal-Cost Multi-Path routing (ECMP) {{?RFC2992}}, means that communication with them must use the same 5-tuple as the base connection.

* Endpoint initiation: OPPD must be initiated by an endpoint. First, in the presence of Network Address Translators (NATs) {{?RFC2663}}, this is the only way to ensure that communication with the proxy uses the same IP addresses and port numbers as the base connection. Second, in this way, endpoints can execute some kind of flow control to avoid the reception of many unsolicited announcements.

* Proxies must somehow prove that they are on-path, akin to the way it is done with several ICMP messages that include the first 64 bits of the original packet that evoked them {{?RFC792}}. Naturally, the part of the original packet that is to be returned must not be too easy to guess--e.g. a nonce of a certain minimum length. This establishes a certain minimal level of trust, since on-path devices are in the position to do whatever they want with a connection's packets anyway -- for example discard, rate-limit or duplicate them.

# A survey of possibilities

Please insert your ideas below -- or add a description of a scheme that already exists.

## Endpoint to proxy

### Special packet of base connection

Sidekick {{Sidekick}} endpoints signal proxy support by sending a distinguished packet containing a 128-byte sidekick-request marker over the base connection's 5-tuple. Such inline signaling could confuse receiving endpoints, but sidekicks target protocols such as QUIC that discard cryptographically unauthenticated data anyway.

Secure Middlebox-Assisted QUIC {{SMAQ}} leaves the design of a proxy discovery solution as future work, but also suggests to multiplex the necessary signaling over the same 5-tuple as the base connection.

### Header options

An endpoint could use a newly defined TCP option or UDP option
{{?I-D.ietf-tsvwg-udp-options}} to signal proxy support. Such an option
could be specified to be ignored by the receiving endpoint, and receiving
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

A proxy could insert a newly defined TCP option or UDP option
{{?I-D.ietf-tsvwg-udp-options}} in a returning packet (e.g., an ACK)
to answer back to the endpoint that originally announced its proxy support.
This approach does not require the proxy to send an additional packet.



# A survey of open issues

Just an idea, having a separate list of common problems to be considered might be helpful. For example:

* How to handle multiple proxies on a path?
  * Suggestion: Each proxy could send a special packet back to the endpoint, or add its information to a header option as long as there is enough space.
* How to deal with multi-path?
  * Suggestion: we ignore it, we just apply the discovery per path. Endpoints are expected to initiate the discovery process for every path at which they want to make use of a proxy should a proxy be available.

This list will become longer as we add mechanisms to the preceding section.


# Examined material that was not included

{{?I-D.kuehlewind-quic-proxy-discovery}} lists several possibilities for proxy discovery, but the proxies in question need not be on-path. One notable possibility mentioned in {{?I-D.kuehlewind-quic-proxy-discovery}} is the use of Port Control Protocol (PCP) options; this is, in some sense, an on-path discovery method since PCP talks to NATs, which are necessarily on-path. However, there is no reason to limit the discovery process described in the present document to scenarios with NATs only.

OPPD shares the on-path communication constraint with Path Layer UDP Substrate (PLUS) {{?I-D.trammell-plus-spec}}. As such, there are commonalities between PLUS and OPPD such as the potential sharing of ports. However, the PLUS wire image in {{?I-D.trammell-plus-spec}} is designed for the endpoint-to-network direction of signaling, which eliminates the need for an on-path proxy to prove that it has seen a packet.


# Security Considerations

The idea of OPPD is only to discover on-path proxies. These devices must make it reasonably credible that they are indeed on-path; see the last item in {{notation}}.

Further security considerations will depend on the use case.


# IANA Considerations

This document has no IANA actions.


--- back

<!--# Acknowledgments
{:numbered="false"}

TODO acknowledge.-->
