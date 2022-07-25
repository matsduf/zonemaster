# CONSISTENCY07: Delegation consistency

## Test case identifier
**CONSISTENCY07**


## Table of contents

* [Objective](#Objective)
* [Scope](#Scope)
* [Inputs](#Inputs)
* [Summary](#Summary)
* [Test procedure](#Test-procedure)
* [Outcome(s)](#Outcomes)
* [Special procedural requirements](#Special-procedural-requirements)
* [Intercase dependencies](#Intercase-dependencies)
* [Terminology](#terminology)

## Objective

Every DNS zone, except for the root zone ("."), is dependent on the delegation
from its parent zone. With the delegation the parent zone point out its child
zone's name server, not authoritively, but it acts as a boot strap to find the
child zone, and its name servers.

The parent zone is hosted on the parent name servers. Usually on multiple
servers. It is obvious that it can create issues if the different parent name
servers do not have the same delegation of the child zone. Two DNS resolvers
doing lookup of the child zone might end up on different child name servers
with different versions of the child zone.

This test case verifies that all parent name servers returns the same delegation
to the child zone.

## Scope



## Inputs

> > Input data to the test case. Always include the Child zone, but it can also
> > be other data units, such as current time, if that is relevant.
> >
> > The input data name is put in quote marks '""', then a hyphen '-' as a
> > separator, and finally a description.

* "Child Zone" - The domain name to be tested.

## Summary

> > First we can have bullets, if applicable, that state notable things about
> > the execution or the messages. E.g.
* If no CDS record is found, the test case will terminate early
  with no message tag outputted.
* If a CDS record is of "delete" type, then it can by definition not
  match or point at any DNSKEY record.

> > Here is a table of all message tags referred to in the steps. The table has
> > the following columns:
> > 1. The message tag outputted in the test procedure below.
> > 2. The default severity level.
> > 3. The arguments (if any) to be included in the message ID. Argument name
> >    must be selected from the defined names (see link below).
> > 4. Message ID to be outputted by the implementation with any arguments
> >    included (see example below).
> >
> > Always use the same table set-up, but with the correct tags. Keep the table
> > sorted by message tag. Here is an example:

Message Tag                   | Level   | Arguments            | Message ID for message tag
:-----------------------------|:--------|:---------------------|:--------------------------------------------
T01_ALGO_NOT_SUPPORTED_BY_ZM  | NOTICE  | algo_descr, algo_num | The algorithm used, {algo_descr} ({also_num}) is not supported by the Zonemaster implementation.
T01_BROKEN_DNSSEC             | ERROR   | ns_ip_list           | Replies do not follow the standard. Returned from name servers "{ns_ip_list}".
T01_HAS_NSEC                  | INFO    |                      | The *Child Zone* uses NSEC.
T01_HAS_NSEC3                 | INFO    |                      | The *Child Zone* uses NSEC3.
T01_INCONSISTENT_DNSSEC       | ERROR   | keytag               | The keytag "{keytag}" of the zone is inconsistent with respect to DNSSEC.

The value in the Level column is the default severity level of the message. The
severity level can be changed in the [Zonemaster-Engine profile]. Also see the
[Severity Level Definitions] document.

The argument names in the Arguments column lists the arguments used in the
message. The argument names are defined in the [argument list].


> > The message tags should be formed following the [Message Tag Specification].
> >
> > The default severity level is selected from the [Severity Level Definitions].

## Test procedure

> > This section contains the detailed steps to execute the test case. The steps
> > should be as explicit as possible to avoid that different implementations or
> > executions make different interpretations or assumptions.
> >
> > If the test procedure uses DNS queries (most test cases do), then add the
> > following paragraph (types of DNS quries that do no apply for the test case
> > can be removed):

In this section and unless otherwise specified below, the terms "[DNS Query]",
"[EDNS Query]" and "[DNSSEC Query]" follow the specification for DNS queries
as specified in [DNS Query and Response Defaults]. The handling of the DNS
responses on the DNS queries follow, unless otherwise specified below, what is
specified for [DNS Response], [EDNS Response], and [DNSSEC Response],
respectively, in the same specification.

> > How the term "[DNSSEC Query]" is used can be seen in the next example below.
> >
> > The steps should be written in such a way that it is reasonably possible to
> > use them to execute the test case manually using tools such as [`dig`][dig].
> > It can be assumed that the reader of the text has good understanding of DNS.
> >
> > The steps should state what messages (message tags) to be outputted when.
> > Only messages with default severity level DEBUG or higher can be included.
> >
> > All messages with level INFO or higher must be included. I.e. the
> > implementation should not include messages with default level INFO or higher
> > unless included in the specification.
> >
> > Messages DEBUG or lower can be added in the implementation as needed without
> > being included in the specification.
> >
> > Also include statement on what other information to be accompanied
> > with the message (included as parameter to the message tag). Example of such
> > information is IP adresses to name servers.
> >
> > When refering to the data defined in **Inputs** then use the name in
> > *italic*, e.g.:

2. Create a [DNSSEC Query] with query type DNSKEY and query name *Child Zone*
   ("DNSKEY Query").

> > If data sets are created, then defined them in quote marks. E.g.:

4. Create three empty sets, "NSEC-Zone", "NSEC3-Zone", and
   "No-DNSSEC-Zone".

> > When used (referred to) in the steps, make the name italic. E.g.:

6. Add the name server IP to the *NSEC-Zone* set (*NSEC3-Zone*
         set).

> > When a message is outputted in the steps, it is done by outputting
> > the message tag, "T01_HAS_NSEC3" in the example below. Make the tag as a link
> > name in italic. What the link name points at is to be defined at the bottom
> > of the document. E.g.:

8. If the NSEC (NSEC3) records do not "cover" the
   *Non-Existent Query Name*, then output *[T01_HAS_NSEC3]*

> > When referring to RCODE, such as "NoError", use the term [RCODE Name]
> > with a link to the IANA page. Also use the name forms on that page.



## Outcome(s)
> > First we have standard text that should normally be the same in all
> > test case specifications.

The outcome of this Test Case is "fail" if there is at least one message
with the severity level *[ERROR]* or *[CRITICAL]*.

The outcome of this Test Case is "warning" if there is at least one message
with the severity level *[WARNING]*, but no message with severity level
*ERROR* or *CRITICAL*.

In other cases, no message or only messages with severity level
*[INFO]* or *[NOTICE]*, the outcome of this Test Case is "pass".

> > Then there could be statements about data being outputted from the test case
> > for use as input data for other test cases.

## Special procedural requirements

> > First we have standard text that should normally be the same in all
> > test case specifications.

If either IPv4 or IPv6 transport is disabled, skip sending queries over that
transport protocol. A message will be outputted reporting that the transport
protocol has been skipped.

> > Then there could be some other limitations or specialties of how or when
> > this test case is run. E.g.:

The test case is only performed if some DNSKEY record is found in the
*Child Zone*.

> > This is a standard limitation for all test cases:

The *Child Zone* must be a valid name meeting
"[Requirements and normalization of domain names in input]".

## Intercase dependencies

> > Either the following text if there is no formal dependencies on other test
> > cases...

None.

> > ...or specification on the outcome that this test case depend on, e.g.:

Example of formal dependency to be added.

> > Also see the text under **Objective** about test cases that are assumed to be
> > run, but that this test case does not depend on. I.e. not dependencies in a
> > formal sense.


## Terminology

> > If there is no terminology to be explained, then this section should contain
> > the following text:

No special terminology for this test case.

> > Following are examples of terminology that are candidates to be included
> > in the section.

The terms "in-bailiwick", "out-of-bailiwick" and "glue record" are defined in
[RFC 8499][RFC 8499#section-7], section 7, pages 24-25. In this document, the
term "in-bailiwick" is limited to the meaning "in domain" in
[RFC 8499][RFC 8499#section-7]. The term "out-of-bailiwick" means what is not
"in-bailiwick, in domain", in this document.


The terms "in-bailiwick" and "out-of-bailiwick" are used as defined
in [RFC 8499], section 7, page 25.

The term "glue records" is defined in [RFC 8499][RFC 8499#page-24], section 7,
page 24.

When the term "using Method" is used, names and IP addresses are fetched
using the defined [Methods].

The term "send" (to an IP address) is used when a DNS query is sent to
a specific name server.

The term "DNS Lookup" is used when a recursive lookup is used, though
any changes to the DNS tree introduced by an [undelegated test] must be
respected.

> > ----
> > The links listed below are not visible when rendered by Github. In the
> > specification the different parts below should be combined into one link
> > collection and sorted. Always start the link label (the left side) with an
> > upper case letter (if it starts with a letter). The reference can from the
> > text is case independent.
> >
> > All link names are listed below to the left with the link target to the
> > right. They are only visible when viewing the source of this document.
> >
> > All message tags are linked to section **Summary**

[T01_ALGO_NOT_SUPPORTED_BY_ZM]:         #summary
[T01_HAS_NSEC]:                         #summary
[T01_HAS_NSEC3]:                        #summary
[T01_INCONSISTENT_DNSSEC]:              #summary

> > All links in the template are absolute except for links within this template
> > or to other documents in the template tree. All absolute links to documents
> > in the zonemaster/zonemaster directory are to the develop branch. In the
> > test case specification all links should be relative if the link target is
> > in the zonemaster/zonemaster repository.
> >
> > A few relative links in this template:

[Message Tag Specification]:            MessageTagSpecification.md
[Test Case Identifier Specification]:   TestCaseIdentifierSpecification.md

> > Absolute links to be converted to relative links in the test case
> > specification. Here grouped to be easier to copy, but sort them
> > them always in the specification.

> > Links to pages on Zonemaster/Zonemaster:

[Severity Level Definitions]:                                     https://github.com/zonemaster/zonemaster/blob/develop/docs/specifications/tests/SeverityLevelDefinitions.md
[DEBUG]:                                                          https://github.com/zonemaster/zonemaster/blob/develop/docs/specifications/tests/SeverityLevelDefinitions.md#notice
[INFO]:                                                           https://github.com/zonemaster/zonemaster/blob/develop/docs/specifications/tests/SeverityLevelDefinitions.md#info
[NOTICE]:                                                         https://github.com/zonemaster/zonemaster/blob/develop/docs/specifications/tests/SeverityLevelDefinitions.md#notice
[WARNING]:                                                        https://github.com/zonemaster/zonemaster/blob/develop/docs/specifications/tests/SeverityLevelDefinitions.md#warning
[ERROR]:                                                          https://github.com/zonemaster/zonemaster/blob/develop/docs/specifications/tests/SeverityLevelDefinitions.md#error
[CRITICAL]:                                                       https://github.com/zonemaster/zonemaster/blob/develop/docs/specifications/tests/SeverityLevelDefinitions.md#critical

[Basic04]:                                                        https://github.com/zonemaster/zonemaster/blob/develop/docs/specifications/tests/Basic-TP/basic04.md

[DNS Query and Response Defaults]:                                https://github.com/zonemaster/zonemaster/blob/develop/docs/specifications/tests/DNSQueryAndResponseDefaults.md
[DNS Query]:                                                      https://github.com/zonemaster/zonemaster/blob/develop/docs/specifications/tests/DNSQueryAndResponseDefaults.md#default-setting-in-dns-query
[DNS Response]:                                                   https://github.com/zonemaster/zonemaster/blob/develop/docs/specifications/tests/DNSQueryAndResponseDefaults.md#default-handling-of-a-dns-response
[DNSSEC Query]:                                                   https://github.com/zonemaster/zonemaster/blob/develop/docs/specifications/tests/DNSQueryAndResponseDefaults.md#default-setting-in-dnssec-query
[DNSSEC Response]:                                                https://github.com/zonemaster/zonemaster/blob/develop/docs/specifications/tests/DNSQueryAndResponseDefaults.md#default-handling-of-a-dnssec-response
[EDNS Query]:                                                     https://github.com/zonemaster/zonemaster/blob/develop/docs/specifications/tests/DNSQueryAndResponseDefaults.md#default-setting-in-edns-query
[EDNS Response]:                                                  https://github.com/zonemaster/zonemaster/blob/develop/docs/specifications/tests/DNSQueryAndResponseDefaults.md#default-handling-of-an-edns-response

[Methods]:                                                        https://github.com/zonemaster/zonemaster/blob/develop/docs/specifications/tests/Methods.md
[MethodsV2]:                                                      https://github.com/zonemaster/zonemaster/blob/develop/docs/specifications/tests/MethodsV2.md
[Undelegated test]:                                               https://github.com/zonemaster/zonemaster/blob/develop/docs/specifications/test-types/undelegated-test.md

[Requirements and normalization of domain names in input]:        https://github.com/zonemaster/zonemaster/blob/develop/docs/specifications/tests/RequirementsAndNormalizationOfDomainNames.md

> > Links to other repositories:

[Argument list]:                                                  https://github.com/zonemaster/zonemaster-engine/blob/master/docs/logentry_args.md
[Zonemaster-Engine profile]:                                      https://github.com/zonemaster/zonemaster-engine/blob/master/docs/Profiles.md

> > External links:

[Dig]:                                                            https://en.wikipedia.org/wiki/Dig_(command)
[RCODE Name]:                                                     https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml#dns-parameters-6
[RFC 4035#section-3.1.3]:                                         https://tools.ietf.org/html/rfc4035#section-3.1.3
[RFC 8499#section-7]:                                             https://datatracker.ietf.org/doc/html/rfc8499#section-7


> > Keep all links sorted, and make a straight column of the link targets in
> > the test case specification.


