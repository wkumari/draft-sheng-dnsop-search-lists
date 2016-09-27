**Important:** Read CONTRIBUTING.md before submitting feedback or contributing
```




Network Working Group                                           S. Sheng
Internet-Draft                                             A. McConachie
Intended status: Best Current Practice                             ICANN
Expires: December 24, 2016                                     W. Kumari
                                                                  Google
                                                           June 22, 2016


            Considerations for Deprecating DNS search lists
                       draft-sheng-search-list-00

Abstract

   A Domain Name System (DNS) "search list" (hereafter, simply "search
   list") is conceptually implemented as an ordered list of domain
   names.  When the user enters a name, the domain names in the search
   list are used as suffixes to the user-supplied name, one by one,
   until either a name is successfully resolved or the search list is
   exhausted.

   Processing search lists was weakly standardized in early Requests For
   Comments (RFCs) and implemented in most operating systems.  However,
   as the Internet has grown, search list behavior has diversified.
   Applications (e.g., web browsers and mail clients) and DNS resolvers
   process search lists differently.  In addition, some of these
   behaviors present security and privacy issues to end systems, can
   lead to performance problems for the Internet, and might cause
   collision with names provisioned under the newly delegated top-level
   domains.

   In this document, we suggested several options to deprecate the use
   of search lists.

   [ Ed note (remove): This document is being developed in github:
   https://github.com/stevesheng/id-search-list/draft-sheng-search-list
   .  ]

Status of This Memo

   This Internet-Draft is submitted in full conformance with the
   provisions of BCP 78 and BCP 79.

   Internet-Drafts are working documents of the Internet Engineering
   Task Force (IETF).  Note that other groups may also distribute
   working documents as Internet-Drafts.  The list of current Internet-
   Drafts is at http://datatracker.ietf.org/drafts/current/.





Sheng, et al.           Expires December 24, 2016               [Page 1]

Internet-Draft        Deprecating DNS Search Lists             June 2016


   Internet-Drafts are draft documents valid for a maximum of six months
   and may be updated, replaced, or obsoleted by other documents at any
   time.  It is inappropriate to use Internet-Drafts as reference
   material or to cite them other than as "work in progress."

   This Internet-Draft will expire on December 24, 2016.

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
   2.  Proposal  . . . . . . . . . . . . . . . . . . . . . . . . . .   3
   3.  Negative Consequences For the Change  . . . . . . . . . . . .   4
   4.  IANA Considerations . . . . . . . . . . . . . . . . . . . . .   4
   5.  Security Considerations . . . . . . . . . . . . . . . . . . .   4
   6.  Acknowledgements  . . . . . . . . . . . . . . . . . . . . . .   4
   7.  Normative References  . . . . . . . . . . . . . . . . . . . .   4
   Appendix A.  Changes / Author Notes.  . . . . . . . . . . . . . .   5
   Authors' Addresses  . . . . . . . . . . . . . . . . . . . . . . .   5

1.  Introduction

   Many organizations create subdomains under their primary domain(s) to
   delegate or distribute management of the namespace, reduce the load
   on the authoritative DNS servers, and more easily distinguish a
   host's organizational and/or geographical affiliations.

   As a convenience to users, many operating systems implement search
   list processing, a feature that allows a user to enter a partial name
   in an application, with the operating system expanding the name
   through entries in a search list.  For example, if a user has a
   search list of "corp.example.com;berlin.example.com;example.com" and
   she types "system" into her browser's address box, the operating



Sheng, et al.           Expires December 24, 2016               [Page 2]

Internet-Draft        Deprecating DNS Search Lists             June 2016


   system would try "system.corp.example.com",
   "system.berlin.example.com", "system.example.com", and perhaps
   "system." in some order.

   Search list processing, including order of operations for search list
   processing, was loosely specified in [RFC1123](specifically, section
   6.1.4.3 (2)), [RFC1535], and [RFC1536] and has been implemented in
   most operating systems.  However, as the Internet has grown, search
   list behavior has diversified.  Applications (e.g., web browser and
   mail clients) and DNS resolvers process search list suffixes
   differently.  Some of these behaviors also present security and
   privacy issues to end systems, and performance problems both for the
   end system and the Internet.

   In this document, we suggested several options to deprecate the use
   of search lists.

1.1.  Requirements notation

   The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT",
   "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this
   document are to be interpreted as described in [RFC2119].

2.  Proposal

   No automatically generated search lists.  [TODO: What is an
   automatically generated search list?]  Administrators (including DHCP
   server administrators) should configure the search list explicitly,
   and must not use implicit search lists (as defined in Section 2).
   Where DNS parameters such as the domain search list have been
   manually configured, these parameters should not be overridden by
   DHCP.  These are suggested default processing rules.  Operating
   system / resolve vendors may provide configuration options to
   override these.

   Unqualified Single-Label Domain Names Are Never Queried Directly.
   When a user enters a single label name, that name may be subject to
   search list processing if a search list is specified, but must never
   be queried in the DNS in its original single-label form.

   Unqualified Multi-label Domain Names Never Use Search Lists.  When a
   user queries a hostname that contain two or more labels separated by
   dots, such as www.server, applications and resolvers must query the
   DNS directly.  Search lists must not be applied even if such names do
   not resolve.






Sheng, et al.           Expires December 24, 2016               [Page 3]

Internet-Draft        Deprecating DNS Search Lists             June 2016


3.  Negative Consequences For the Change

   There are administrators that today rely on both automatic generation
   of search lists, and the automatic propagation of search lists to
   clients via DHCP.  The proposed change would require reconfiguration
   of systems.

   There are users, and links in web pages, that use partially qualified
   names (such as www.corp) instead of either just a single token or a
   fully qualified domain name.

   Changing search list behavior of unqualified multi-label domain names
   would reduce the utility of these names.

   Finally, not all applications currently in use treat these categories
   of domain names in the same way.  Incompatibilities and operational
   problems, specifically in BYOD (Bring Your Own Device) environments,
   already exist.

4.  IANA Considerations

   TBD

5.  Security Considerations

   TBD

6.  Acknowledgements

   Thanks to the ICANN Security and Stability Advisory Committee.

7.  Normative References

   [RFC1034]  Mockapetris, P., "Domain names - concepts and facilities",
              STD 13, RFC 1034, DOI 10.17487/RFC1034, November 1987,
              <http://www.rfc-editor.org/info/rfc1034>.

   [RFC1123]  Braden, R., Ed., "Requirements for Internet Hosts -
              Application and Support", STD 3, RFC 1123, DOI 10.17487/
              RFC1123, October 1989,
              <http://www.rfc-editor.org/info/rfc1123>.

   [RFC1535]  Gavron, E., "A Security Problem and Proposed Correction
              With Widely Deployed DNS Software", RFC 1535, DOI
              10.17487/RFC1535, October 1993,
              <http://www.rfc-editor.org/info/rfc1535>.





Sheng, et al.           Expires December 24, 2016               [Page 4]

Internet-Draft        Deprecating DNS Search Lists             June 2016


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

   [RFC3646]  Droms, R., Ed., "DNS Configuration options for Dynamic
              Host Configuration Protocol for IPv6 (DHCPv6)", RFC 3646,
              DOI 10.17487/RFC3646, December 2003,
              <http://www.rfc-editor.org/info/rfc3646>.

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
   Email: andrew.ccconachie@icann.org



Sheng, et al.           Expires December 24, 2016               [Page 5]

Internet-Draft        Deprecating DNS Search Lists             June 2016


   Warren Kumari
   Google
   1600 Amphitheatre Parkway
   Mountain View, CA  94043
   US

   Email: warren@kumari.net












































Sheng, et al.           Expires December 24, 2016               [Page 6]
```
