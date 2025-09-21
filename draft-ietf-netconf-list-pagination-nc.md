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
   defined in [I-D.ietf-netconf-list-pagination] to NETCONF [RFC6241].

   This document updates [RFC6241], to augment the &lt;get&gt; and &lt;get-
   config> "rpc" statements, and [RFC8526], to augment the &lt;get-data&gt;
   "rpc" statement, to define input parameters necessary for list
   pagination.

--- middle

# Introduction

   This document defines a mapping of the list pagination mechanism
   defined in [I-D.ietf-netconf-list-pagination] to NETCONF [RFC6241].

   This document updates [RFC6241] and [RFC8526], as described in
   Section 2.

   While the pagination mechanism defined in this document is designed
   for the NETCONF protocol [RFC6241], the augmented RPCs MAY be used by
   the RESTCONF protocol [RFC8040] if the RESTCONF server implements the
   "ietf-list-pagination-nc" module.
   The YANG data model in this document conforms to the Network
   Management Datastore Architecture defined in [RFC8342]

##  Terminology

   The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT",
   "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and
   "OPTIONAL" in this document are to be interpreted as described in BCP
   14 [RFC2119] [RFC8174] when, and only when, they appear in all
   capitals, as shown here.

##  Conventions

   Various examples in this document use "BASE64VALUE=" as a placeholder
   value for binary data that has been base64 encoded (per Section 9.8
   of [RFC7950]).  This placeholder value is used because real base64
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

   In order for NETCONF to support [I-D.ietf-netconf-list-pagination],
   this document extends the operations &lt;get&gt;, &lt;get-config&gt; and &lt;get-
   data&gt; to include additional input parameters and output annotations.

   The updated operations accept a content filter parameter, similar to
   the "filter" parameter of &lt;get-config&gt;, but includes nodes for "list"
   and "leaf-list" filtering.

   The content filter parameter is used to specify the YANG list or
   leaf-list that is to be retrieved.  This must be a path expression
   used to represent a list or leaf-list data node.

   The following tree diagram [RFC8340] illustrates the "ietf-netconf-
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
      defined in [RFC6241].  The "get-data" augment is against the YANG
      module defined in [RFC8526].

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


# Acknowledgments
{:numbered="false"}

   This work has benefited from the discussions of RESTCONF resource
   collection over the years, in particular,
   [I-D.ietf-netconf-restconf-collection] which provides enhanced
   filtering features for the retrieval of data nodes with the GET
   method and [I-D.zheng-netconf-fragmentation] which document large
   size data handling challenge.  The authors would like to thank the
   following for lively discussions on list (ordered by first name):
   Andy Bierman, Martin Björklund, Robert Varga, and Robert Wilton.
