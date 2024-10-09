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
  I-D.kuehlewind-quic-proxy-discovery:


--- abstract

[ Please add something to this draft, and then add your name to the author list. Also, consider the draft title: it now indicates that I'm the primary dude here (is that really justified?), and that we target PANRG. Should it carry a different name? ]

This document surveys possibilities for on-path proxy discovery. It is meant to help the conversation in a planned side meeting at IETF-121 in Dublin.


--- middle

# Introduction

Proxies can carry out functions that improve the performance of an end-to-end connection. These function can be quite diverse, ranging from minimal help (e.g. just offering information) to more significant interference, e.g. splitting an end-to-end connection in half, for reliability, congestion control or both.

It is commonly desirable for such proxies to be located on the path(s) that a connection already traverses, rather than using a tunneling method to enforce a path. This is naturally the case for transparent "Performance Enhancing Proxies" (PEPs) that have been implemented for TCP, but the transparent nature of such proxies has caused a number of known problems in the past. Non-transparent proxies leave the choice of utilizing and configuring a performance enhancing function to end systems -- and such a choice requires a means to detect the proxy and explicitly communicate with it.

There are various ways in which on-path proxies can be discovered, and they differ from the ways in which end systems learn about proxies that are not necessarily on-path.

This document surveys some possibilities that are available for on-path proxy discovery.


# Conventions and Definitions

{::boilerplate bcp14-tagged}


# A survey of possibilities

TODO. The idea is to describe the method in our two research papers (see README), and others we know of.


# A survey of open issues

Just an idea, having a separate list of common problems to be considered might be helpful. For example:

* How to handle multiple proxies on a path?
* How to deal with multi-path?

This list will become longer as we add mechanisms to the preceding section.


# Examined material that was not included

{{?I-D.kuehlewind-quic-proxy-discovery}} contains various ideas on proxy discovery, but the proxies in question need not be on-path. One notable possibility mentioned in {{?I-D.kuehlewind-quic-proxy-discovery}} document is the use of PCP; this is, in some sense, an on-path discovery method since NATs are necessarily on-path. However, there is no reason to limit the discovery process described in the present document to scenarios with NATs only.

# Security Considerations

TODO Security


# IANA Considerations

This document has no IANA actions.


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
