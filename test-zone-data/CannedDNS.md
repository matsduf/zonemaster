# Wishes for a CannedDNS server
Mats Dufberg


## Background

The context for these wishes is the creation and maintenance of test zones for
Zonemaster. For those we have full control of the DNS queries, and we have full
control of what responses we want to see coming back, but we have sometimes a
hard time creating those. On the other hand, we do not necessarily need a
name server that know any details of the DNS protocol besides reading a query
and creating a response.

This document have specifications of what is required for such a name server,
CannedDNS server. That could be integrated in an existing tool or it could
be created as a stand-alone tool.


## Multiple IP addresses

When setting up the test zones it is an absolute requirement that a daemon
can listen to multiple IPv4 and IPv6 addresses. Since it must be able to
co-exist with other name servers it cannot listen to all addresses, just
selected ones. Even though it could be possible to start the daemon several
times (once for each address) it would probably be unmanageable.

I assume that the daemon must be able to listen to multiple IP addresses
in one instance, but at the same time being able to separate those from
each other.

## Configuration

Test scearios and test zones are created per Zonemaster test case. To retain a
workable structure, each test case is given its own folder for all files and
configuration for all scenarios for that test case. Configuration common to
multiple tets cases are kept in separate folders for such data.

By putting all configuration for a test case in a dedicated folder, the risk
of `Git` conflicts is reduced.

To have a workable solution the CannedDNS server it must support "include"
function. I.e. the top level configuration file must support some mechanism
to include other files in other folders with the specific configuration.

## Actions

Three actions are forseen for the daemon

* Canned response
* Forward to another name server
* Drop

Action should be selected based on what pattern that the query matches.


## Query matching

It must be possible to match the query against the following items:

* Listening address: one address from a list of addresses
* Query name: one name from a list of names given in display format
* Query class: (default: IN)
* Query type: one type from a list of types
* EDNS exist: boolean
* EDNS version: integer
* EDNS option code: list or range of integers

Query name labels can be assumed to be limited to "A-Za-z0-9_/" exept the root
label (empty). Names are assumed to be FQDN even if final dot is missing. The
list of query names should be assumed to be a simple OR statement, not a
complex regular expression.

Query types could be either the common name such as "A" or "SOA" for the
common query types, or "TYPE731" for less common types.

Absent fields in the matching specification matches anything.

If a pattern is match, the action is executed. If several patterns match
a query, the first match will be used and the remaining ignored.


## Canned response

In the specification of the response all values in a DNS response must be
specifiable. All fields have default values that are used if the field is
absent (below in parenthesis for each field). Empty field means empty value,
which is only possible for DNS records. "int" is here a non-negative interger of
the size that matches the field in the DNS packet or EDNS record, respectively.

* ID: int (from query)
* QR: int (1)
* OPCODE: int (0)
* AA flag: 1/0 (1)
* TC flag: 1/0 (0)
* RD flag: 1/0 (from query)
* RA flag: 1/0 (0)
* BIT 9: 1/0 (0)
* AD flag: 1/0 (0)
* CD flag: 1/0 (0)
* RCODE: RCODE name/int (NOERROR)
* QDCOUNT: int/auto (auto)
* ANCOUNT: int/auto (auto)
* NSCOUNT: int/auto (auto)
* ARCOUNT: int/auto (auto)
* Question Section: DNS record (from query)
* Answer Section: DNS record ()
* Authority Section: DNS record ()
* Additional Section: DNS record ()
* EDNS: yes/no/auto (auto)
* EDNS Version: int (0) 
* EDNS UDP Size: int (1232)
* EDNS Flags: int for the flag bits (0) 
* EDNS OPT: int:value (none)

The server must support the commonly used  RR types in the DNS records referred
to below (A, NS, CNAME, SOA, PTR, MX, TXT, AAAA, DNAME, DS, RRSIG, NSEC, DNSKEY,
NSEC3, NSEC3PARAM, CDS, CDNSKEY, CSYNC, ZONEMD, SPF, URI, CAA). It should be
possible to specify other RR types with the "TYPE731" format.

*Question Section* must support a DNS record in display format where query name
is assumed to be FQDN even if final dot is missing. Here no TTL nor RDATA must
be included.

*Answer Section*, *Authority Section* and *Additional Section* must support
a DNS record in display format where query name is assumed to be FQDN
even if final dot is missing. Here TTL and RDATA must be included. RDATA must
match RR type unless generic format is used (which should always be possible).

*Question Section*, *Answer Section*, *Authority Section* and
*Additional Section* can be repeated to give multiple DNS records for
respective section. The order must be kept. A single, emply field will give
an empty section.

In *EDNS* the value "yes" creates an OPT record with default values. If any
*EDNS Version*, *EDNS UDP Size*, *EDNS Flags* or *EDNS OPT* field is present, it
will set another value than default.

With "no" in the *EDNS* field the response will have no OPT record and any
*EDNS Version*, *EDNS UDP Size*, *EDNS Flags* or *EDNS OPT* fields are ignored.

With "auto" in the *ENDS* field the OPT from the query is used, possibly
with modifications by the *EDNS Version*, *EDNS UDP Size*, *EDNS Flags* or
*EDNS OPT* fields. If the query has no OPT record, it will function as if the
value was "no".

The *EDNS OPT* field can be repeated to add multiple options. The order must
be retained.

An OPT record is created, if applicable, from the EDNS fields. That OPT record is
placed as the last record in the additional section.

The value "auto" in the *QDCOUNT*, *ANCOUNT*, *NSCOUNT* and *ARCOUNT* will set
the counter to the actual number of records in respective section. Note that any
OPT record in the additional section is included in that count.


## Forward to another name server

The incoming query without any change is forwarded to another name server by
protocol (UDP/TCP) of incoming query to specified IP address and port. The
response from the remote server is forwarded back without any change to the
original client. If no response nothing is sent if UDP, and a reset if TCP.


## Drop

No response is sent to the original client. If the protocol is TCP, a reset
is sent.

