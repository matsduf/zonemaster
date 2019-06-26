# Specification of test data for CONSISTENCY05

## Test Case
This document specifies available test data for test case [CONSISTENCY05].

## Base domain for test zones

The base domain name for the test zones in this document is 
`consistency05.data.zonemaster.se.`. All test zones are child zones the base domain name.

## Messages
The messages are defined in the test case ([CONSISTENCY05]).

* T = the message _MUST_ be present
* F = the message must _NOT_ be present
* If the cell is empty, then the presence or non-presence is ignored.

Message                           | A | B | D | E | F | G | H | I
:---------------------------------|:--|:--|:--|:--|:--|:--|:--|:--
CHILD_NS_FAILED                   | F | T | T | T | F | F | F | F 
NO_RESPONSE                       | F | T | T | F | T | F | F | F 
CHILD_ZONE_LAME                   | F | F | T | T | T | F | F | F 
IN_BAILIWICK_ADDR_MISMATCH        | F | F | F | F | F | T | F | F 
OUT_OF_BAILIWICK_ADDR_MISMATCH    | F | F | F | F | F | F | T | F 
EXTRA_ADDRESS_CHILD               | F | F | F | F | F | F | F | T 
ADDRESSES_MATCH                   | T | F | F | F | F | F | F | F 


In some cases the same combination of messages is tested with multiple test zones. 
If so, that is given in the second table below and the alternatives are 
then `X1`, `X2` etc.

Scenario | Default outcome (*)| Description of 
:--------|:-------------------|:----------------------------------------------------------------------------------------------
A        | Pass               | All name servers are in-bailiwick names. Correctly defined.
A1       | Pass               | All name server names are out-of-bailiwick names, but with glue in delegation. Correctly defined.
A2       | Pass               | All name server names are out-of-bailiwick names and not included in delegation as glue. Correctly defined.
B        | Warning            | One name server responds without NOERROR/NXDOMAIN, one gives no response on port 53 and at least one responds correctly.
B1       | Warning            | One name server responds with AA unset, one gives no response on port 53 and at least one responds correctly.
D        | Fail               | One name server responds without NOERROR/NXDOMAIN. The rest gives no response on port 53.
D1       | Fail               | One name server responds with AA unset. The rest gives no response on port 53.
E        | Fail               | All name servers respond with AA unset.
E1       | Fail               | All name servers respond without NOERROR/NXDOMAIN.
F        | Fail               | All name servers give no response on port 53.
G        | Fail               | The delegation includes at least one in-bailiwick name server named and one IPv4 and one IPv6 address for that

(*) The default outcome is 

The child zone name is derived from the tables below. In the first table, each combination
defined combination of messages from the test case is given a capital letter, e.g. `X`. That
letter is prepended the base domain name, e.g. `X.consistency05.data.zonemaster.se.`.




[CONSISTENCY05]:                  ../../specifications/tests/Consistency-TP/consistency05.md
