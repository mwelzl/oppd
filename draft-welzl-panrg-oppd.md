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
    organization: Your Organization Here
    email: "michawe@ifi.uio.no"

normative:

informative:


--- abstract

TODO Abstract


--- middle

# Introduction

Proxy systems can carry out functions that improve the performance of an end-to-end connection; this includes to just offer information. To do this, it is usually desirable for such systems to be located on the path(s) that the connection traverses. This is naturally the case for transparent "Performance Enhancing Proxies" (PEPs) that have been implemented for TCP, but transparent nature of such proxies has caused a number of known problems in the past. Non-transparent proxies leave the choice of utilizing and configuring a performance enhancing function to end systems - and such choice requires a signaling channel to detect the proxy and communicate with it.

For protocols such as QUIC, where encryption prevents proxies from transparently interfering with traffic, or when performance enhancing proxy functions are desired, chosen and configured by an end system,


There are various ways in which on-path proxies can be discovered, and they differ from the ways in which end systems learn about proxies that are not necessarily on-path.

This document is meant  to serve as a starting point towards the design of approaches to discover on-path proxies by surveying some possibilities that are available.


Remember to check and, if it makes sense, cite:
draft-kuehlewind-quic-proxy-discovery-01


# Conventions and Definitions

{::boilerplate bcp14-tagged}


# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
