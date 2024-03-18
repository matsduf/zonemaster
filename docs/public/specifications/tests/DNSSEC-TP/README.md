# DNSSEC Test Plan

These are the DNSSEC tests for a domain.

This document uses the terminology defined in the [Master Test Plan].


## Default DNS query flags for all DNSSEC tests

* Transport: UDP
* Bufsize: EDNS0 buffer size (512)
* Flags -- query flags
    * do -- DNSSEC ok (1)
    * cd -- Checking Disabled (1)
    * rd -- Recursion Desired (0)
    * ad -- Authenticated Data (0)

See section 3.2 of [RFC 4035]
for a description of the flags used by a recursive name server.

## Key, hash and signature algorithms

There are many algorithms defined for doing DNSSEC, not all of them are
mandatory to implement. This test case should strive not only to implement
all mandatory algorithms, but also most of those that are in use on the
internet today as well.

If any algorithm in a DNSSEC record type is not recognized by the test
system, the test system should emit a notice about this.


[Master Test Plan]:             ../MasterTestPlan.md
[RFC 4035]:                     https://datatracker.ietf.org/doc/html/rfc4035#section-3.2
[Test Case README]:             ../README.md

<!-- Content until EOF generated by script updateTestPlanReadme.pl from Zonemaster/Zonemaster utils directory -->

## Test cases list

|Test Case |Test Case Description|
|:---------|:--------------------|
|[DNSSEC01](dnssec01.md)|Legal values for the DS hash digest algorithm|
|[DNSSEC02](dnssec02.md)|DS must match a valid DNSKEY in the child zone|
|[DNSSEC03](dnssec03.md)|Verify NSEC3 parameters|
|[DNSSEC04](dnssec04.md)|Check for too short or too long RRSIG lifetimes|
|[DNSSEC05](dnssec05.md)|Check for invalid DNSKEY algorithms|
|[DNSSEC06](dnssec06.md)|Verify DNSSEC additional processing|
|[DNSSEC07](dnssec07.md)|If DNSKEY at child, parent should have DS|
|[DNSSEC08](dnssec08.md)|Valid RRSIG for DNSKEY|
|[DNSSEC09](dnssec09.md)|RRSIG(SOA) must be valid and created by a valid DNSKEY|
|[DNSSEC10](dnssec10.md)|Zone contains NSEC or NSEC3 records|
|[DNSSEC11](dnssec11.md)|DS in delegation requires signed zone|
|[DNSSEC12](dnssec12.md)|Test for DNSSEC Algorithm Completeness|
|[DNSSEC13](dnssec13.md)|All DNSKEY algorithms used to sign the zone|
|[DNSSEC14](dnssec14.md)|Check for valid RSA DNSKEY key size|
|[DNSSEC15](dnssec15.md)|Existence of CDS and CDNSKEY|
|[DNSSEC16](dnssec16.md)|Validate CDS|
|[DNSSEC17](dnssec17.md)|Validate CDNSKEY|
|[DNSSEC18](dnssec18.md)|Validate trust from DS to CDS and CDNSKEY|