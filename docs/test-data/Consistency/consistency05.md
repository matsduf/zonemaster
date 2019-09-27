# Specification of test data for CONSISTENCY05

## Test Case
This document specifies available test data for test case [CONSISTENCY05].

## Base domain for test zones

The base domain name for the test zones in this document is
`consistency05.data.zonemaster.se.`. All test zones are child zones the base domain name.

## Messages
The messages are defined in the test case ([CONSISTENCY05]).

* "T" = the message _MUST_ be outputted
* "F" = the message must _NOT_ be outputted
* "-" = the presence or non-presence is ignored.

Message \ Scenario group          | A | B | D | E | F | G | H | I
:---------------------------------|:--|:--|:--|:--|:--|:--|:--|:--
CHILD_NS_FAILED                   | F | T | T | T | F | F | F | F
NO_RESPONSE                       | F | T | T | F | T | F | F | F
CHILD_ZONE_LAME                   | F | F | T | T | T | F | F | F
IN_BAILIWICK_ADDR_MISMATCH        | F | F | F | F | F | T | F | F
OUT_OF_BAILIWICK_ADDR_MISMATCH    | F | F | F | F | F | F | T | F
EXTRA_ADDRESS_CHILD               | F | F | F | F | F | - | F | T
ADDRESSES_MATCH                   | T | F | F | F | F | F | F | F

A scenario group, e.g. "A", will give the given values of True (message
is present) or False (message is absent) of the messages in the table.
Each scenario group has one or several zone files that matches that
scenario. The zone files are numbered, e.g. `A00`, `A01` etc. In the
table below each zone file is briefly described, and its default
outcome is given. The default outcome is derived from the outcome section
[CONSISTENCY05].

Zone file|Default outcome|Description of zone
:--------|:--------------|:----------------------------------------------------------------------------------------------
A00      |Pass           |Two name server names in delegation and both are in-bailiwick names. Correctly defined.
A01      |Pass           |Two name server names in delegation and both are out-of-bailiwick names, but with glue in delegation. Correctly defined.
A02      |Pass           |Two name server names in delegation and both are out-of-bailiwick names and not included in delegation as glue. Correctly defined.
B00      |Warning        |Two name server names in delegation. One responds without NOERROR/NXDOMAIN, one gives no response on port 53 and one responds correctly.
B01      |Warning        |Two name server names in delegation. One responds with AA unset, one gives no response on port 53 and one responds correctly.
D00      |Fail           |Two name server names in delegation. One responds without NOERROR/NXDOMAIN, and one gives no response on port 53.
D01      |Fail           |Two name server names in delegation. One responds with AA unset, and one gives no response on port 53.
E00      |Fail           |Two name server names in delegation and both respond with AA unset.
E01      |Fail           |Two name server names in delegation and both respond without NOERROR/NXDOMAIN.
F00      |Fail           |Two name server names in delegation and both give no response on port 53.
G00      |Fail           |Two name server names in delegation with one in-bailiwick name with glue record that mismatches records in zone.
G01      |Fail           |Two name server names in delegation with one in-bailiwick name with glue record that is absent in zone.
H00      |Fail           |Two name server names in delegation with one out-of-bailiwich name with glue record that mismatches record in authoritative zone.
I00      |Pass           |Two name server names in delegation with two in-bailiwich names with glue records where records in zone matches but also has valid extra addresses.

The child zone name is derived from the tables above. In the first table, each combination
defined combination of messages from the test case is given a capital letter, e.g. `A`. In
the second table the zone file name prefix is defined, e.g. `A00`. The prefix is is prepended
the base domain name, e.g. `A00.consistency05.data.zonemaster.se` and then a general prefix
`zone` is added to the final domain name, e.g. `zone.A00.consistency05.data.zonemaster.se`.


[CONSISTENCY05]:                  ../../specifications/tests/Consistency-TP/consistency05.md
