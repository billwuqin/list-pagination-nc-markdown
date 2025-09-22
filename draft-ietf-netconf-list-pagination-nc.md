---
title: "NETCONF Extensions to Support List Pagination"
abbrev: "NETCONF Pagination Support"
category: std

docname: draft-ietf-netconf-list-pagination-nc-latest
submissiontype: IETF
number:
date:
consensus: true
v: 3
area: "Operations and Management"
workgroup: "Network Configuration"
keyword:
 - list pagination
 - yang data model
 - netconf

author:
 -
    fullname:  Kent Watsen
    organization: Watsen Networks
    email: kent+ietf@watsen.net
 -
    fullname:  Qin Wu
    organization: Huawei
    email: bill.wu@huawei.com
 -
    fullname:  Per Andersson
    organization: Cisco
    email: per.ietf@ionio.se
 -
    fullname:  Olof Hagsand
    organization: SUNET
    email: olof@hagsand.se
 -
    fullname:  Hongwei Li
    organization: HP
    email: flycoolman@gmail.com

normative:

informative:

...

--- abstract

   This document defines a mapping of the list pagination mechanism
   defined in {{?I-D.ietf-netconf-list-pagination}} to NETCONF {{!RFC6241}}.

   This document updates {{!RFC6241}}, to augment the &lt;get&gt; and &lt;get-
   config&gt; "rpc" statements, and {{!RFC8526}}, to augment the &lt;get-data&gt;
   "rpc" statement, to define input parameters necessary for list
   pagination.

--- middle

# Introduction

   This document defines a mapping of the list pagination mechanism
   defined in {{?I-D.ietf-netconf-list-pagination}} to NETCONF {{!RFC6241}}.

   This document updates {{!RFC6241}} and {{!RFC8526}}, as described in
   Section 2.

   While the pagination mechanism defined in this document is designed
   for the NETCONF protocol {{!RFC6241}}, the augmented RPCs MAY be used by
   the RESTCONF protocol {{!RFC8040}} if the RESTCONF server implements the
   "ietf-list-pagination-nc" module.
   The YANG data model in this document conforms to the Network
   Management Datastore Architecture defined in {{!RFC8342}}

##  Terminology

   The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT",
   "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and
   "OPTIONAL" in this document are to be interpreted as described in BCP
   14 {{!RFC2119}} {{!RFC8174}} when, and only when, they appear in all
   capitals, as shown here.

##  Conventions
{::boilerplate bcp14-tagged}

   Various examples in this document use "BASE64VALUE=" as a placeholder
   value for binary data that has been base64 encoded (per Section 9.8
   of {{!RFC7950}}).  This placeholder value is used because real base64
   encoded structures are often many lines long and hence distracting to
   the example being presented.

#  Updates to NETCONF operations

##  Updates to RFC 6241

   The &lt;get&gt; and &lt;get-config&gt; rpc statements are augmented to accept
   additional input parameters, as described in Section 3.


##  Updates to RFC 8526

   The &lt;get-data&gt; rpc statement is augmented to accept additional input
   parameters, as described in in Section 3.

#  List Pagination for NETCONF

   In order for NETCONF to support {{?I-D.ietf-netconf-list-pagination}},
   this document extends the operations &lt;get&gt;, &lt;get-config&gt; and &lt;get-
   data&gt; to include additional input parameters and output annotations.

   The updated operations accept a content filter parameter, similar to
   the "filter" parameter of &lt;get-config&gt;, but includes nodes for "list"
   and "leaf-list" filtering.

   The content filter parameter is used to specify the YANG list or
   leaf-list that is to be retrieved.  This must be a path expression
   used to represent a list or leaf-list data node.

   The following tree diagram {{!RFC8340}} illustrates the "ietf-netconf-
   list-pagination" module:

~~~~
   module: ietf-list-pagination-nc

     augment /nc:get/nc:input:
       +---w list-pagination
          +---w where?           union
          +---w locale?          string {sort}?
          +---w sort-by?         union {sort}?
          +---w direction?       enumeration
          +---w cursor?          string
          +---w offset?          uint32
          +---w limit?           union
          +---w sublist-limit?   union
     augment /nc:get-config/nc:input:
       +---w list-pagination
          +---w where?           union
          +---w locale?          string {sort}?
          +---w sort-by?         union {sort}?
          +---w direction?       enumeration
          +---w cursor?          string
          +---w offset?          uint32
          +---w limit?           union
          +---w sublist-limit?   union
     augment /ncds:get-data/ncds:input:
       +---w list-pagination
          +---w where?           union
          +---w locale?          string {sort}?
          +---w sort-by?         union {sort}?
          +---w direction?       enumeration
          +---w cursor?          string
          +---w offset?          uint32
          +---w limit?           union
          +---w sublist-limit?   union
~~~~
   Comments:

   *  This module augments three NETCONF "rpc" statements: get, get-
      config, and get-data.

   *  The "get" and "get-config" augments are against the YANG module
      defined in {{!RFC6241}}.  The "get-data" augment is against the YANG
      module defined in {{!RFC8526}}.

#  Error Reporting

   When an input query parameter is supplied with an erroneous value, an
   &lt;rpc-error&gt; MUST be returned containing the error-type value
   "application", the error-tag value "invalid-value", and MAY include
   the error-severity value "error".  Additionally the error-app-tag
   SHOULD be set containing query parameter specific error value.

##  The "offset" Query Parameter

   If the "offset" query parameter value supplied is larger than the
   number of instances in the working result-set, the &lt;rpc-error&gt; MUST
   contain error-app-tag with value "offset-out-of-range".

#  YANG Module for List Pagination in NETCONF

   The "ietf-netconf-list-pagination-nc" module defines conceptual
   definitions within groupings, which are not meant to be implemented
   as datastore contents by a server.

   This module has normative references to {{!RFC6241}}, {{!RFC6243}},
   {{!RFC6991}}, and {{!RFC8342}}.

~~~~
   <CODE BEGINS> file "ietf-list-pagination-nc@2025-04-03.yang"
{::include-fold ./yang/ietf-list-pagination-nc.yang}
   <CODE ENDS>
~~~~

#  IANA Considerations

##  The "IETF XML" Registry

   This document registers one URI in the "ns" subregistry of the IETF
   XML Registry {{!RFC3688}} maintained at
   https://www.iana.org/assignments/xml-registry/xml-registry.xhtml#ns.
   Following the format in {{!RFC3688}}, the following registration is
   requested:

         URI: urn:ietf:params:xml:ns:yang:ietf-list-pagination-nc
         Registrant Contact: The IESG.
         XML: N/A, the requested URI is an XML namespace.

##  The "YANG Module Names" Registry

   This document registers one YANG module in the YANG Module Names
   registry {{!RFC6020}} maintained at https://www.iana.org/assignments/
   yang-parameters/yang-parameters.xhtml.  Following the format defined
   in {{!RFC6020}}, the below registration is requested:

      name: ietf-list-pagination-nc
      namespace: urn:ietf:params:xml:ns:yang:ietf-list-pagination-nc
      prefix: pgnc
      RFC: XXXX

#  Security Considerations

##  The "ietf-netconf-list-pagination" YANG Module

   This section follows the template defined in Section 3.7.1 of
   {{!RFC8407}}.

   The YANG module specified in this document defines a schema for data
   that is designed to be accessed via network management protocols such
   as NETCONF {{!RFC6241}} or RESTCONF {{!RFC8040}}.  The lowest NETCONF layer
   is the secure transport layer, and the mandatory-to-implement secure
   transport is Secure Shell (SSH) {{!RFC6242}}.  The lowest RESTCONF layer
   is HTTPS, and the mandatory-to-implement secure transport is TLS
   {{!RFC8446}}.

   The Network Configuration Access Control Model (NACM) {{!RFC8341}}
   provides the means to restrict access for particular NETCONF users to
   a preconfigured subset of all available NETCONF protocol operations
   and content.

   The security considerations for the base NETCONF protocol operations
   (see Section 9 of {{!RFC6241}} and Section 6 of {{!RFC8526}}) apply to the
   extension of operations &lt;get&gt;, &lt;get-config&gt;, and &lt;get-data&gt; defined
   in this document.

--- back

# Appendix A.  Example YANG Module

   The examples within this document use the "example-social" YANG
   module defined in Appendix A.1 of {{?I-D.ietf-netconf-list-pagination}}.

# Appendix B.  Example Data Set

   The Example Data Set used by the examples is defined in Appendix A.2
   of {{?I-D.ietf-netconf-list-pagination}}.

# Appendix C.  Example Queries

## C.1.  List pagination with all query parameters

   This example mimics that Appendix A.3.9 of
   {{?I-D.ietf-netconf-list-pagination}}.

~~~~
   <rpc xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" message-id="42">
     <get>
       <source>
         <running/>
       </source>
       <filter type="xpath" select="/es:members/es:member"
         xmlns:es="https://example.com/ns/example-social"/>
       <list-pagination
         xmlns="urn:ietf:params:xml:ns:yang:ietf-list-pagination-nc">
         <where>//stats[starts-with(joined,'2020')]</where>
         <sort-by>joined</sort-by>
         <direction>backwards</direction>
         <offset>2</offset>
         <limit>2</limit>
         <sublist-limit>1</sublist-limit>
         <locale>sv_US</locale>
       </list-pagination>
     </get>
   </rpc>
~~~~

   Response from the NETCONF server:

~~~~
   <rpc-reply message-id="101"
        xmlns="urn:ietf:params:xml:ns:netconf:base:1.0">
     <data>
       <members
         xmlns="https://example.com/ns/example-social"
         xmlns:lp="urn:ietf:params:xml:ns:yang:ietf-list-pagination">
         <member lp:remaining="1" lp:locale="en_US">
           <member-id>eric</member-id>
           <email-address>eric@example.com</email-address>
           <password>$0$1543</password>
           <avatar>BASE64VALUE=</avatar>
           <tagline>Go to bed with dreams; wake up with purpose.</tagline>
           <following>alice</following>
           <posts>
             <post>
               <timestamp>2020-09-17T18:02:04Z</timestamp>
               <title>Son, brother, husband, father</title>
               <body>What's your story?</body>
             </post>
           </posts>
           <favorites>
             <bits lp:remaining="2" lp:locale="en_US">two</bits>
           </favorites>
           <stats>
             <joined>2020-09-17T19:38:32Z</joined>
             <membership-level>pro</membership-level>
             <last-activity>2020-09-17T18:02:04Z</last-activity>
           </stats>
         </member>
         <member lp:remaining="1" lp:locale="en_US">
           <member-id>bob</member-id>
           <email-address>bob@example.com</email-address>
           <password>$0$1543</password>
           <avatar>BASE64VALUE=</avatar>
           <tagline>Here and now, like never before.</tagline>
           <posts>
             <post lp:remaining="2" lp:locale="en_US">
               <timestamp>2020-08-14T03:32:25Z</timestamp>
               <body>Just got in.</body>
             </post>
           </posts>
           <favorites>
             <decimal64-numbers lp:remaining="1" lp:locale="en_US">3.14159</decimal64-numbers>
           </favorites>
           <stats>
             <joined>2020-08-14T03:30:00Z</joined>
             <membership-level>standard</membership-level>
             <last-activity>2020-08-14T03:34:30Z</last-activity>
           </stats>
         </member>
       </members>
     </data>
   </rpc-reply>
~~~~

# Acknowledgments
{:numbered="false"}

   This work has benefited from the discussions of RESTCONF resource
   collection over the years, in particular,
   {{?I-D.ietf-netconf-restconf-collection}} which provides enhanced
   filtering features for the retrieval of data nodes with the GET
   method and {{?I-D.zheng-netconf-fragmentation}} which document large
   size data handling challenge.  The authors would like to thank the
   following for lively discussions on list (ordered by first name):
   Andy Bierman, Martin Björklund, Robert Varga, and Robert Wilton.
