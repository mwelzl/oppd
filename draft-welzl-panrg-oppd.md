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
      seriesinfo:
          IFIP NETWORKING 2023
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


--- abstract

[ Please add something to this draft, and then add your name to the author list. Also, consider the draft title: it now indicates that I'm the primary dude here (is that really justified?), and that we target PANRG. Should it carry a different name? ]

This document surveys possibilities for On-Path Proxy Discovery (OPPD). It is meant to help the conversation in a planned side meeting at IETF-121 in Dublin.


--- middle

# Introduction

Proxies can carry out functions that improve the performance of an end-to-end connection. These function can be quite diverse, ranging from minimal help (e.g. just offering information) to more significant interference, e.g. splitting an end-to-end connection in half, for reliability, congestion control or both.

It is commonly desirable for such proxies to be located on the path(s) that a connection already traverses, rather than using a tunneling method to enforce a path. This is naturally the case for transparent "Performance Enhancing Proxies" (PEPs) that have been implemented for TCP, but the transparent nature of such proxies has caused a number of known problems in the past. Non-transparent proxies leave the choice of utilizing and configuring a performance enhancing function to end systems -- and such a choice requires a means to detect the proxy and explicitly communicate with it.

There are various ways in which On-Path Proxy Discovery (OPPD) can work, and they differ from the ways in which end systems learn about proxies that are not necessarily on-path.

This document surveys some possibilities that are available for OPPD.


# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Terminology

* Base connection: an end-to-end connection between two endpoints on which an on-path proxy is expect to carry out a performance-improving function.

* Endpoint: an entity that communicates with one or more other endpoints using a specific transport protocol. It is locally identified by the 5-tuple of IP address pair, protocol and port numbers.

# General assumptions

* On-path proxy devices are expected to carry out functions in relation to a base connection. Thus, they must be on the same path, which means that communication with them must use the same 5-tuple.

* Endpoint initiation: OPPD must be initiated by an endpoint. First, in the presence of NATs, this is the only way to ensure that communication with the proxy use the same IP addresses and port numbers as the base connection. Second, in this way, endpoints can execute some kind of flow control to avoid the reception of many unsolicited announcements.

# A survey of possibilities

## Sidekick

In {{?Sidekick}}, endpoints signal proxy support by sending a distinguished packet containing a 128-byte sidekick-request marker. Such inline signaling could confuse receivers, but sidekicks target protocols such as QUIC that discard cryptographically unauthenticated data anyway.

The proxy replies to a sidekick-request packet by sending a special packet from the receiver's IP address and port number back to the endpoint. This packet contains a sidekick-reply marker, an opaque session ID, and an IP address and port number for communicating with the proxy. Upon receiving the sidekick-reply packet, the sender begins communicating directly with the proxy from a different UDP port. It initially sends back the session ID and configuration parameters to start receiving quACKs (special ACKs crafted by a Sidekick proxy).

## SMAQ

In {{?SMAQ}}, ...TODO.

## UDP options

TODO. Describe the possibility (if there is one?).

## Please insert your idea here

... or something that already exists. And give a brief description of how it works.


# A survey of open issues

Just an idea, having a separate list of common problems to be considered might be helpful. For example:

* How to handle multiple proxies on a path?
* How to deal with multi-path?
  * Suggestion (Michael): we ignore it, we just apply the discovery per path. Endpoints are expected to initiate the discovery process for every path at which they want to make use of a proxy should a proxy be available.

This list will become longer as we add mechanisms to the preceding section.


# Examined material that was not included

{{?I-D.kuehlewind-quic-proxy-discovery}} lists several possibilities for proxy discovery, but the proxies in question need not be on-path. One notable possibility mentioned in {{?I-D.kuehlewind-quic-proxy-discovery}} document is the use of PCP; this is, in some sense, an on-path discovery method since NATs are necessarily on-path. However, there is no reason to limit the discovery process described in the present document to scenarios with NATs only.


# Security Considerations

TODO Security: for now this is copy+pasted text from the NSDI paper.

A malicious third-party could execute a reflection amplification attack that generates a large amount of traffic while hiding its source. This is possible because the sender requests quACKs to a different port and (for some carrier-grade NATs) IP address from the underlying session. To mitigate this, each quACK contains a quota, initially 1, of remaining quACKs the proxy will send as well as an updated session ID. The quota and session ID ensure only the sender can increase the quota or otherwise reconfigure the session.

An adversarial PEP could send misleading information to the sender. Note that only on-path PEPs can send credible information, since they refer to unique packet identifiers. To mitigate this, the sender can consider PEP feedback along with end-to-end metrics to determine whether to keep using the PEP. The sender can always opt out of the PEP, and the PEP cannot actively manipulate traffic any more than outside a sidekick setting.


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
