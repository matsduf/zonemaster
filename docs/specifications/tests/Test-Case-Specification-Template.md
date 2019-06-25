# LEVEL02: Short name of the test case

## Test case identifier
**LEVEL02**

## Objective

<Description of what the test case tests and supporting information
and references to standard documents that supports the decisions
that the test case takes about what is good and what is bad.>

## Inputs

* "Child Zone" - The domain name to be tested.

<More input can be given, always in this format, initial capital
letter i each word.>

## Ordered description of steps to be taken to execute the test case

<The steps are explicitly ordered with numbers. Any message that
the implementation is expected to output (use "output").>

1. Created an SOA query for the *Child Zone*.

<Refer to input in *Italic* and in the same way as the input was named
in the input section.>

<When the implementation is to output messages, they should be explicitly
stated her. Use the word "output". The messages should be shown in italic
and being a link to the outcome section.>

(...)

<Create new units to refer to by defining them and giving them a name with
initial capitals within quotemarks in parenthesis.>

3. Obtain the set of name server IP addresses using [Method4] and [Method5]
   ("Name Server IP").

<Use explicit loops when suitable.>

4. For each name server in *Name Server IP* do:

<Indent and explicitly order the steps within the loop.>

   1. Send the SOA query and collect the response.
   2. If there is no DNS response, then output *[NO_RESPONSE]*

## Outcome(s)

<More or less fixed text start here.>

The outcome of this Test Case is "fail" if there is at least one message
with the severity level *ERROR* or *CRITICAL*.

The outcome of this Test Case is "warning" if there is at least one message
with the severity level *WARNING*, but no message with severity level
*ERROR* or *CRITICAL*.

The outcome of this Test case is "pass" in all other cases.

<End of fixed text.>

<A table with all messages and their default severity level.>

Message                           | Default severity level
:---------------------------------|:-----------------------------------
NO_RESPONSE                       | WARNING
NS_ERROR                          | WARNING

## Special procedural requirements	

<If needed.>

If either IPv4 or IPv6 transport is disabled, ignore the evaluation of the
result of any test using this transport protocol and log a message reporting
the ignored result.

## Intercase dependencies

<If needed.>

None

<Links not displayed when rendered as markdown. Used above.>

[Method4]: ../Methods.md#method-4-obtain-glue-address-records-from-parent
[Method5]: ../Methods.md#method-5-obtain-the-name-server-address-records-from-child
[NO_RESPONSE]: #outcomes
[NS_ERROR]: #outcomes
[RFC 1035]: https://tools.ietf.org/html/rfc1035
