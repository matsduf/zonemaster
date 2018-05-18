# Methods common to Test Case Specifications

This is a list of generic methods used in many Test Case specifications. The
Test Cases that makes use of any of these methods refer directly to
this text.

## Method 1: Parent zone

The method has been removed. The function is integrated in [BASIC01] instead.


## Method 2: Delegation name servers

### Method identifier

**Method2**

### Objective

Obtain the name servers names (extracted from the NS records) for 
the given zone (child zone) as defined in the delegation from the parent zone.

### Inputs

 * *Child zone* - The name of the zone to test.
 * *Parent NS* - The set of IP addresses of the name servers of the parent zone of the *child zone*.

### Preconditions

 * *Child zone* exists.
 * *Child zone* is delegated.

### Ordered description of steps to be taken to execute the method

 1. Create an empty mutable set of *results*.
 2. Create an *SOA query* for the *child zone* with the RD bit unset.
 3. For each *NS IP* in the *parent NS* set:
    1. Send the *SOA query* to the *NS IP* and collect any response *packet*.
    2. If no *packet* was collected, continue with the next *NS IP*.
    3. If the *packet* has the AA bit set, continue with the next *NS IP*.
    4. For each *NS record* in the authority section of the *packet*:
       1. If the owner name of the *NS record* is not *child zone*, continue with the next *NS record*.
       2. Add the *NSDNAME* field from the *NS record* to the set of *results*.
 4. If the set of *results* is empty:
    1. Create an *NS query* for the *child zone* with the RD bit unset.
    2. For each *NS IP* in the *parent NS* set:
       1. Send the *NS query* to *NS IP* and collect any response *packet*.
       2. If no *packet* was collected, continue with the next *NS IP*.
       3. If the *packet* has the AA bit unset, continue with the next *NS IP*.
       4. For each *NS record* in the answer section of the *packet*:
          1. If the owner name of the *NS record* is not *child zone*, continue with the next *NS record*.
          2. Add the *NSDNAME* field from the *NS record* to the set of *results*.
 5. Return the set of *results*.

### Outcome(s)

A set of name servers.

### Special procedural requirements

 * The response to a given query from a given server may be cached in order to limit the number of requests.
 * The *results* set preserves neither duplicates nor order among its elements.
 * The *results* set compares its elements case-insensitively.

### Dependencies

None


## Method 3: In-zone name servers

### Method identifier
**Method3** In-zone name servers


### Objective
Obtain the names of the authoritative name servers for the given zone 
(child zone) as defined in the NS records in the zone itself.

### Inputs

* The name of the child zone ("child zone").
* The addresses to the name servers of the child zone, as given by 
  [Method4] ("name server IPs").

### Ordered description of steps to be taken to execute the method

1. Create an NS query for apex of the child zone with the RD flag 
   unset and send that to the *name server IPs*.
2. Ignore response unless AA flag is set.
3. Collect all the unique NS records in the answer sections of the
   responses and extract the name server names.
4. Create a set of name servers (name server names) as collected
   from the zone.
5. If the set is empty, then return ERROR.

### Outcome(s)

Unless ERROR was returned in the steps, outcome is the set of name 
servers.

### Special procedural requirements

None.

### Dependencies

Test Case [BASIC01] and [Method4] must have been run first.



## Method 4: Delegation name server addresses

### Method identifier
**Method4** Delegation name server addresses

### Objective

Obtain the addresses of the name servers for the given
zone (child zone) as defined in the delegation from the parent 
zone ([in-bailiwick] name servers) and on public DNS 
([out-of-bailiwick] name servers).

### Inputs

* Unless *undelegated test*, the name servers to the parent zone as found in 
  [BASIC01] ("parent NS").
* The name of the child zone ("child zone").
* The fact if the child zone exists from [BASIC01].
* The test type, "undelegate test" or "normal test".
* If *undelegated test*, the submitted name server data.

### Ordered description of steps to be taken to execute the method

1. *Normal test*.

   1. If *child zone* does not exists then return ERROR.
   2. These steps assume that the servers of *parent NS* behave the
      same way as when [BASIC01] was run.
   3. Create an NS query for the *child zone* and send that to a server
      of *parent NS* with RD flag unset.
   4. If the response response contains a referral to the child zone:
      1. Extract the name server names from the RDATA in the NS record in
         the authority section.
      2. Extract all A and AAAA records from the additional section.
   5. If the response is authoritative (AA bit set) and the answer section
      contains the NS record of the child zone:
      1. Repeat the query with the next server in *parent NS* until a server 
         has responded
         with a referral or no more servers are available.
      2. If a referral was found go to step 4.
      3. If no referral was found, extract the name server names from the
         RDATA of the NS records in the answer section.
      4. For each [in-bailiwick] name server, query the name server for A and
         AAAA and extract the record in the answer or additional section.
	 Follow any referral to sub-zone if needed.
   6. For each [out-of-bailiwick] name servers do a normal recursive lookup 
      for those to a resolver by querying for A and AAAA.
   7. Collect all matching A and AAAA records from the responses.
   8. If no IP addresses have been collected, return with ERROR.

2. *Undelegated test*.

   1. For every [in-bailiwick] name server in input data, collect IPv4 and
      IPv6 address for the server name.
   2. For all [out-of-bailiwick] name servers in input data, if it has 
      neither IPv4 nor IPv6 data, do a normal recursive lookup for 
      the address to a resolver by querying for A and AAAA. 
   3. If a name server has either IPv4 or IPv6 data, do not query for
      any data.
   4. If no IP addresses have been collected, return with ERROR.


### Outcome(s)

Unless ERROR was returned in the steps, outcome is a set of IP addresses.

### Special procedural requirements

If a name server name points at a CNAME or DNAME follow that to extract
an IP address, if possible. It is, however, not permitted for a NS record
to point at a name that has a CNAME or DNAME, but that test i covered by
Test Case [DELEGATION05].

### Dependencies

Test Case [BASIC01] must have been run.



## Method 5: In-zone addresses records of name servers

### Method identifier
**Method5** In-zone addresses records of name servers


### Objective

Obtain the addresses of the [in-bailiwick] name servers, if any, for 
the child zone as defined in the child zone itself. This method will
ignore any addresses of [out-of-bailiwick] name servers.


### Inputs

* The name of the child zone ("child zone").
* The fact if the child zone exists from [BASIC01].
* The test type, "undelegate test" or "normal test".
* The addresses to the name servers of the child zone, as given by 
  [Method4] ("name server IPs").
* The name server names defined in the zone as defined by [Method2]
  ("child zone name server names").

### Ordered description of steps to be taken to execute the method

1. If the child zone does not exist, end with an ERROR unless the
   test is *undelegated test*.
2. For each *child zone name server name* that is an [in-bailiwick]
   name server:
   1. Send an A and an AAAA query to all servers in *name server IPs*.
   2. If a delegation (referral) to a sub-zone of child zone is returned, 
      follow that delegation, possibly in several steps, by repeating the
      A and AAAA queries.
   3. Ignore non-referral responses unless AA flag is set. Cached data
      is not accepted.
3. Create a list of unique IPv4 addreses and unique IPv6 addresses,
   respectively, found in the answer sections of the responses for
   the *child name server names*.


### Outcome(s)

Unless ERROR was returned in the steps, outcome is a set of IP addresses.


### Special procedural requirements

If a name server name points at a CNAME or DNAME follow that to extract
an IP address, if possible. It is, however, not permitted for a NS record
to point at a name that has a CNAME or DNAME, but that test i covered by
Test Case [DELEGATION05].


### Dependencies

Test Cases [BASIC01], [Method4] and [Method2] must have been run first.

## Terminology

The terms "in-bailiwick" and "out-of-bailiwick" are used as defined
in [RFC 7719], section 6, page 15.



[RFC 7719]: https://tools.ietf.org/html/rfc7719

[BASIC01]: Basic-TP/basic01.md

[DELEGATION05]: Delegation-TP/delegation05.md

[Method2]: #method-2-delegation-name-servers

[Method3]: #method-3-in-zone-name-servers

[Method4]: #method-4-delegation-name-server-addresses

[Method5]: #method-5-in-zone-addresses-records-of-name-servers

[in-bailiwick]:     #terminology
[out-of-bailiwick]: #terminology
