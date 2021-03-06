**Important:** Read CONTRIBUTING.md before submitting feedback or contributing
```




DNS Operations Working Group                                    S. Sheng
Internet-Draft                                             A. McConachie
Updates: 1535,1536 (if approved)                                   ICANN
Intended status: Best Current Practice                         W. Kumari
Expires: May 20, 2017                                             Google
                                                       November 16, 2016


                 DNS Search List Best Current Practices
                       draft-sheng-search-list-00

Abstract

   A Domain Name System (DNS) "search list" (hereafter, simply "search
   list") is an ordered list of domain names.  When an application needs
   to lookup a name on an internet connected host, the domain names in
   the search list are used as suffixes to the application supplied
   name, one by one, until a domain name with the desired associated
   data is found or the search list is exhausted.[Ed note: This
   definintion is lifted from RFC 1123, then edited.]

   Processing search lists was weakly standardized in early Requests For
   Comments (RFCs) [RFC1535] [RFC1536] [Ed note: need more refs here]
   and implemented in most operating systems.  However, as the Internet
   has grown, search list behavior has diversified.  Applications (e.g.,
   web browsers, mail clients) and DNS resolvers process search lists
   differently.  In addition, some of these behaviors present security
   and privacy issues to end systems, can lead to performance problems
   for the Internet, and can cause collisions with names provisioned
   under delegated top-level domains.

   In this document, we make six proposals regarding when and how to use
   DNS search lists.

   [ Ed note (remove): This document is being developed in github:
   https://github.com/wkumari/draft-sheng-dnsop-search-lists ]

Status of This Memo

   This Internet-Draft is submitted in full conformance with the
   provisions of BCP 78 and BCP 79.

   Internet-Drafts are working documents of the Internet Engineering
   Task Force (IETF).  Note that other groups may also distribute
   working documents as Internet-Drafts.  The list of current Internet-
   Drafts is at http://datatracker.ietf.org/drafts/current/.





Sheng, et al.             Expires May 20, 2017                  [Page 1]

Internet-Draft   DNS Search List Best Current Practices    November 2016


   Internet-Drafts are draft documents valid for a maximum of six months
   and may be updated, replaced, or obsoleted by other documents at any
   time.  It is inappropriate to use Internet-Drafts as reference
   material or to cite them other than as "work in progress."

   This Internet-Draft will expire on May 20, 2017.

Copyright Notice

   Copyright (c) 2016 IETF Trust and the persons identified as the
   document authors.  All rights reserved.

   This document is subject to BCP 78 and the IETF Trust's Legal
   Provisions Relating to IETF Documents
   (http://trustee.ietf.org/license-info) in effect on the date of
   publication of this document.  Please review these documents
   carefully, as they describe your rights and restrictions with respect
   to this document.  Code Components extracted from this document must
   include Simplified BSD License text as described in Section 4.e of
   the Trust Legal Provisions and are provided without warranty as
   described in the Simplified BSD License.

Table of Contents

   1.  Introduction  . . . . . . . . . . . . . . . . . . . . . . . .   2
     1.1.  Requirements notation . . . . . . . . . . . . . . . . . .   3
   2.  Search List Processing  . . . . . . . . . . . . . . . . . . .   3
     2.1.  Deprecate search lists  . . . . . . . . . . . . . . . . .   3
     2.2.  No implicit search lists  . . . . . . . . . . . . . . . .   3
     2.3.  No overriding manually configured search lists  . . . . .   4
     2.4.  No applying search lists to multi-label domain names  . .   4
     2.5.  No querying of single-label domain names  . . . . . . . .   4
     2.6.  No search lists on recursive resolvers  . . . . . . . . .   4
   3.  Potential Negative Consequences . . . . . . . . . . . . . . .   4
   4.  IANA Considerations . . . . . . . . . . . . . . . . . . . . .   5
   5.  Security Considerations . . . . . . . . . . . . . . . . . . .   5
   6.  Acknowledgements  . . . . . . . . . . . . . . . . . . . . . .   5
   7.  References  . . . . . . . . . . . . . . . . . . . . . . . . .   5
     7.1.  Normative References  . . . . . . . . . . . . . . . . . .   5
     7.2.  Informative References  . . . . . . . . . . . . . . . . .   6
   Appendix A.  Changes / Author Notes.  . . . . . . . . . . . . . .   6
   Authors' Addresses  . . . . . . . . . . . . . . . . . . . . . . .   6

1.  Introduction

   Many organizations create subdomains under their primary domain(s) to
   delegate or distribute management of their namespace, reduce the load




Sheng, et al.             Expires May 20, 2017                  [Page 2]

Internet-Draft   DNS Search List Best Current Practices    November 2016


   on their authoritative DNS servers, and more easily distinguish a
   host's organizational and/or geographical affiliations.

   As a convenience to users, many operating systems implement search
   list processing, a feature that allows a user to enter a partial name
   in an application, with the operating system expanding the name
   through entries in a search list.  For example, if a user has a
   search list of "corp.example.com;berlin.example.com;example.com" and
   she types "system" into her browser's address box, the operating
   system would attempt resolving "system.corp.example.com",
   "system.berlin.example.com", "system.example.com", and perhaps
   "system." in some order.

   Search list processing, including order of operations for search list
   processing, was loosely specified in [RFC1123] (specifically, section
   6.1.4.3 (2)), [RFC1535], and [RFC1536] and has been implemented in
   most operating systems.  Processing of search lists received via DHCP
   is standardized in [RFC3397] and [RFC3646].  Processing of search
   lists received via IPv6 Router Advertisements (RA) is standardized in
   [RFC6106].  As the Internet has grown, search list behavior has
   diversified.  Applications (e.g., web browser and mail clients) and
   DNS resolvers process search list suffixes differently.  Some of
   these behaviors present security and privacy issues to end systems
   [SAC064] [RFC3397], and performance problems both for the hosts and
   the Internet.

1.1.  Requirements notation

   The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT",
   "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this
   document are to be interpreted as described in [RFC2119].

2.  Search List Processing

2.1.  Deprecate search lists

   Search lists SHOULD NOT be used.  The security, stability and user
   expectation issues outweigh the benefit of using search lists in most
   circumstances.  Recognizing that there will still be environments
   where search list usage cannot be avoided, the following five
   sections provide guidance on these situations.

2.2.  No implicit search lists

   If search lists are to be used, administrators SHOULD configure the
   search list explicitly, and MUST NOT use implicit search lists.  An
   explicitly configured search list is either manually configured on a
   host or learned by the host via DHCP or IPv6 RAs.



Sheng, et al.             Expires May 20, 2017                  [Page 3]

Internet-Draft   DNS Search List Best Current Practices    November 2016


   An implicit search list is a search list derived by a host from some
   part of its Fully Qualified Domain Name (FQDN).  The most common
   implicit search list is a list with a single entry composed only of
   the host's immediate parent domain.  For example, a host with FQDN
   a.example.org might have an implicit search list of example.org.

   This behavior updates the advice given in [RFC1536] section 6.  This
   proposal is to not apply an implicit search list even after failing
   to resolve a name with a search list applied.

2.3.  No overriding manually configured search lists

   A search list configured manually on a host by an operator MUST NOT
   be overridden by DHCP or IPv6 Router Advertisements (RA).

   For a discussion of how hosts should process DNS search lists learned
   via DHCP or IPv6 RAs see [RFC6106] section 5.3.1.

2.4.  No applying search lists to multi-label domain names

   Multi-label domain names MUST NOT use search lists.  When a user
   queries a hostname that contains two or more labels separated by
   dots, such as www.example, applications MUST query the DNS directly.
   Search lists MUST NOT be applied even if such names do not resolve.

2.5.  No querying of single-label domain names

   Single label domain names, aka dotless-domains, MUST NOT be sent by
   applications for resolution.  However, if an application sends a
   single label name, that name MAY be subject to search list processing
   if a search list is specified, but MUST NOT be queried in the DNS in
   its original single-label form.

2.6.  No search lists on recursive resolvers

   DNS Recursive resolvers MUST NOT use search lists.

3.  Potential Negative Consequences

   Some administrators today rely on a combination of both implicit
   search lists and the automatic propagation of search lists via DHCP
   or IPv6 RA to form a complete search list on hosts.  This proposal
   requires these administrators to include their local parent domain(s)
   in the distribution of their DNS search lists.

   There are users, and links in web pages, that use partially qualified
   names (e.g., www.example) instead of either just a single token or a
   fully qualified domain name.  Changing search list behavior of



Sheng, et al.             Expires May 20, 2017                  [Page 4]

Internet-Draft   DNS Search List Best Current Practices    November 2016


   unqualified multi-label domain names could potentially reduce the
   utility of these names and may alter the expected behavior of end-
   user applications.

4.  IANA Considerations

   None

5.  Security Considerations

   TBD

6.  Acknowledgements

   Thanks to the ICANN Security and Stability Advisory Committee.

7.  References

7.1.  Normative References

   [RFC1123]  Braden, R., Ed., "Requirements for Internet Hosts -
              Application and Support", STD 3, RFC 1123, DOI 10.17487/
              RFC1123, October 1989,
              <http://www.rfc-editor.org/info/rfc1123>.

   [RFC1535]  Gavron, E., "A Security Problem and Proposed Correction
              With Widely Deployed DNS Software", RFC 1535, DOI
              10.17487/RFC1535, October 1993,
              <http://www.rfc-editor.org/info/rfc1535>.

   [RFC1536]  Kumar, A., Postel, J., Neuman, C., Danzig, P., and S.
              Miller, "Common DNS Implementation Errors and Suggested
              Fixes", RFC 1536, DOI 10.17487/RFC1536, October 1993,
              <http://www.rfc-editor.org/info/rfc1536>.

   [RFC2119]  Bradner, S., "Key words for use in RFCs to Indicate
              Requirement Levels", BCP 14, RFC 2119, DOI 10.17487/
              RFC2119, March 1997,
              <http://www.rfc-editor.org/info/rfc2119>.

   [RFC3397]  Aboba, B. and S. Cheshire, "Dynamic Host Configuration
              Protocol (DHCP) Domain Search Option", RFC 3397, DOI
              10.17487/RFC3397, November 2002,
              <http://www.rfc-editor.org/info/rfc3397>.







Sheng, et al.             Expires May 20, 2017                  [Page 5]

Internet-Draft   DNS Search List Best Current Practices    November 2016


   [RFC3646]  Droms, R., Ed., "DNS Configuration options for Dynamic
              Host Configuration Protocol for IPv6 (DHCPv6)", RFC 3646,
              DOI 10.17487/RFC3646, December 2003,
              <http://www.rfc-editor.org/info/rfc3646>.

   [RFC6106]  Jeong, J., Park, S., Beloeil, L., and S. Madanapalli,
              "IPv6 Router Advertisement Options for DNS Configuration",
              RFC 6106, DOI 10.17487/RFC6106, November 2010,
              <http://www.rfc-editor.org/info/rfc6106>.

7.2.  Informative References

   [SAC064]   "SSAC Advisory on DNS "Search List" Processing", SAC 64,
              February 2014,
              <https://www.icann.org/en/system/files/files/sac-
              064-en.pdf>.

Appendix A.  Changes / Author Notes.

   [RFC Editor: Please remove this section before publication ]

   From initial to -00.

   o  Nothing changed in the template!

Authors' Addresses

   Steve Sheng
   Internet Corporation for Assigned Names and Numbers
   12025 Waterfront Drive, Suite 300
   Los Angeles  90094
   United States of America

   Phone: +1.310.301.5800
   Email: steve.sheng@icann.org


   Andrew McConachie
   Internet Corporation for Assigned Names and Numbers
   12025 Waterfront Drive, Suite 300
   Los Angeles  90094
   United States of America

   Phone: +1.310.301.5800
   Email: andrew.mccconachie@icann.org






Sheng, et al.             Expires May 20, 2017                  [Page 6]

Internet-Draft   DNS Search List Best Current Practices    November 2016


   Warren Kumari
   Google
   1600 Amphitheatre Parkway
   Mountain View, CA  94043
   US

   Email: warren@kumari.net












































Sheng, et al.             Expires May 20, 2017                  [Page 7]
```
