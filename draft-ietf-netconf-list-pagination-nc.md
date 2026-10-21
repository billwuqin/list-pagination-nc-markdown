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

   This example mimics that Appendix A.3.9 of {{?I-D.ietf-netconf-list-pagination}}.
   In this XML example, The xmlns:es attribute binds the 'es' prefix to the example-social module name.
   The xmlns:lpgnc attribute binds the lpgnc prefix to the urn:ietf:params:xml:ns:yang:ietf-list-pagination-nc namespace.

~~~~
   <rpc xmlns="urn:ietf:params:xml:ns:netconf:base:1.0" message-id="42">
     <get>
       <source>
         <running/>
       </source>
       <filter type="xpath" select="/es:members/es:member"
         xmlns:es="https://example.com/ns/example-social"/>
       <lgpnc:list-pagination
         xmlns:lpgnc="urn:ietf:params:xml:ns:yang:ietf-list-pagination-nc">
         <lgpnc:where>//stats[starts-with(joined,'2020')]</lgpnc:where>
         <lgpnc:sort-by>joined</lgpnc:sort-by>
         <lgpnc:direction>backwards</lgpnc:direction>
         <lgpnc:offset>2</lgpnc:offset>
         <lgpnc:limit>2</lgpnc:limit>
         <lgpnc:sublist-limit>1</lgpnc:sublist-limit>
         <lgpnc:locale>sv_US</lgpnc:locale>
       </lgpnc:list-pagination>
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

# Campus Network Operation Agent Test Requirements

## Network Topology Test Requirements

The campus network operation agent test needs to support a typical test network topology
as shown in {{campus}}, including the network device layer, operation and maintenance
management system, AI agent module, and AI agent test system.

~~~~
                      +-------------------------+
                      | Campus Network Operation|
                      |        Agent            |
                      +----------+--------------+
                                 |
+--------------------------------+--------------------------------+
|                            ----+---                             |
|                        ////        \\\\                         |
|                       |    Internet/   |                        |
|                       |      WAN       |                        |
|                        \\\\        ////                         |
|                            ----+---                             |
|                                |GE0/0/1                         |
|                         +------+------+                         |
| Aggregation             | o o o o  oo |S3                       |
|                  +-----++-------------++-------+                |
|                  |      | o o o o  oo |        |                |
|                  |      +-------------+        |                |
|                  |                             |                |
|      +-----------+---------+       +-----------+---------+      |
|      |         GE0/0/1     |       |        GE0/0/1      |      |
|Access|     +-----+-----+   |       |     +-----+-----+   |      |
|      |     |O O O    o |S1 |       |     |O O O    o |S2 |      |
|      |     +-----+-----+   |       |     +-----+-----+   |      |
|      |  GE0/0/2  |  GE0/0/3|       |   GE0/0/2 | GE0/0/3 |      |
|      |   +-------------+   |       |   +-------------+   |      |
|      |+--+--+       +--+--+|       |+--+--+       +--+--+|      |
|      ||AP1  |       |AP2  ||       || AP3 |       |AP4  ||      |
|      |+-----+       +-----+|       |+-----+       +-----+|      |
|      +---------------------+       +---------------------+      |
|                                                                 |
|                                                                 |
|       +------+  +------+           +------+  +------+           |
|Device |Laptop|  |Phone |....       |Laptop|  |Phone |...        |
|       +------+  +------+           +------+  +------+           |
+-----------------------------------------------------------------+
~~~~
{:#campus title="Campus Network Operation Test Agent Network Topology" artwork-align="center"}

| Test Config | Category | Config Capabilities|
|---|---|---|
|SUT|Mobile Phone/Laptop|2.4G Access,5G Access|
|Network Element|WLAN AP|Telemetry Support|
|Network Element|Switch|Telemetry Support|
|Network Operation Agent|O&M Management System|Support configuration distribution, state monitoring, and fault handling through the existing management system|
|Network Operation Large Model|LLM|Identify the model vendor, version, and number of parameters and deployed model locally to ensure data remains within the domain|
|Network Operation Agent Hardware Platform|Hardware Platform|GPU/NPU Model and Number|
|Agetn Test System|Test Sytem|simulate scenarios such as traffic peaks and sudden surges with mixed east-west and north-south traffic using a professional traffic testing instrument collect agent operation information and produce test results|
{: #tab-test-config title="Test System Configuration Requirements"}

## Interactive Q&A Task Test Requirements

The Test requirements for Campus Network Operation Agent scenario is shown as follows:

| Test Objective | Verify the ability to understand intent, retrieve knowledge, and assist in execution in human-machine dialogue or machine-machine interaction scenarios.|
|---|---|
|Test Case Requirements|Cover at least three scenarios: information query, fault diagnosis, and configuration change. Each scenario must have at least three test cases (including normal requests and malicious requests for security testing), covering different difficulty levels from T1 to T3 (basic, medium, and hard).|
|Test Topo|Refer to campus topo to select typical network deployment scenarios such as smart classroom or dormitory|
|Preset Condition|a)The network environment is running stably, and baseline data has been collected.b)The O&M system's agent capability is enabled and has the
necessary tools access permissions.c) The test system has been configured.|
|Test Step|a) Request Initiation: The test script simulates a user or upper-layer agent, initiating requests via natural language or structured APIs b) Interaction Process: The test system records the entire execution process of the agent and changes in network status, including the agent's response content and execution results c)Result Determination: Scores are given based on the accuracy, completeness, timeliness, and security of the response content.|
|Expected Results|Calculate the score based on Agent capability to tackle the interactive task test questions|

## Autonomous Response Task Test Requirements

The Test requirements for Campus Network Operation Agent scenario is shown as follows:

| Test Objective | Verify the ability of agents to autonomously perceive, analyze, and handle network anomalies without human intervention|
|---|---|
|Test Case Requirement|Cover scenarios such as fault and optimization. Each scenario should cover different difficulty levels from T1 to T3 (basic-medium-hard).|
|Test Topo|Refer to campus topo to select typical network deployment scenarios such as smart classroom or dormitory|
|Pre-set Condition|a)The network environment is running stably, and baseline data has been collected.b)The O&M system's agent capability is enabled and has the
necessary tools access permissions.c) The test system has been configured.|
|Test Step|a)Triggering Event: Pre-set network anomaly events are required for constructing test cases b)Autonomous Execution: The agent automatically perceives
the event and executes inference and handling procedures c)Data Acquisition: The test system records the agent execution process and network state changes b)Results Determination:The final network state is compared with the expected state to calculate the task score|
|Expected Results|Calculate the score based on the agent capability to tackle the Responsive task test questions|
{: #campus-interact title="Interactive Q&A Test System Requirements"}

# Data Center Network Operation Agent Test Requirements

The data center network operation agent test needs to support a typical test network topology
as shown in {{dctopo}}, including the network device layer, operation and maintenance
management system, AI agent module, and AI agent test system.

## Network Topology Test Requirements

~~~~
             +-------------------------------+
             | Data Center Network Operation |
             |           Agent               |
             +-------------+-----------------+
                           |
                           |
 +----------------------------------------------------+
 |                     +---+----+                     |
 |                +----| Core 1 +---+                 |
 |                |    +--------+   |                 |
 |                |                 |                 |
 |            +---+----+       +----+---+             |
 |            | Border |       | Border |             |
 |            | Leaf1  |       | Leaf2  |             |
 |            +--------+       +--------+             |
 |                    -----    ----                   |
 |                       -------                      |
 |                 ------       -----                 |
 |        +---------+             +--------+          |
 |        |  Spine1 |             | Spine2 |          |
 |        +----+----+             +----+---+          |
 |             |                       |              |
 |          +-----------+-----------+--+--------+     |
 |          |  |        |           |           |     |
 |       +--|---------+-|---------+-|---------+ |     |
 |       |  |         | |         | |         | |     |
 |  +----+--+-+  +----+-+--+ +----+-+--+ +----+-+--+  |
 |  |  Server |  |  Server | |  Server | |  Server |  |
 |  | Leaf 1-1|  | Leaf 1-2| | Leaf 2-1| | Leaf 2-2|  |
 |  +----+----+  +----+----+ +-----+---+ +-----+---+  |
 |     +-++-----+-----+--+       +-++-----+----+---+  |
 |   +-|--|-+---|-+-----+|     +-|--|-+---|-+-----+|  |
 | +-+-++++-+-+++-+-++--+++  +-+-++++-+-+++-+-++--+++ |
 | |    ||    ||    ||    |  |    ||    ||    ||    | |
 | | svr|| svr|| svr|| svr|  | svr|| svr|| svr|| svr| |
 | | 1-1|| 1-2|| 1-3|| 1-4|  | 2-1|| 2-2|| 2-3|| 2-4| |
 | |    ||    ||    ||    |  |    ||    ||    ||    | |
 | +----++----++----++----+  +----++----++----++----+ |
 |                                                    |
 +----------------------------------------------------+
~~~~
{:#dctopo title="Data Center Network Operation Test Agent Network Topology" artwork-align="center"}

| Test Config | Category | Config Capabilities|
|---|---|---|
|network element|spine and leaf switch|The network topology includes 2 Spine nodes, 4 Server Leaf nodes, and 2 Border Leaf nodes. Constraints: The basic configuration uses OSPF Area 0 in the core layer, Area 1/2 in the aggregation layer, and static routing in some peripheral networks; supports BGP, EVPN, and VXLAN; all network devices support Telemetry technology with a sampling interval of no more than 10 seconds, covering all dimensions of indicators including interfaces, traffic, protocols, and performance; firewalls, load balancers, and egress routers can be added.|
|Network Operation Agent|O&M Management System|Support configuration distribution, state monitoring, and fault handling through the existing management system|
|Network Operation Large Model|LLM|Identify the model vendor, version, and number of parameters and deployed model locally to ensure data remains within the domain|
|Network Operation Agent Hardware Platform|Hardware Platform|GPU/NPU Model and Number|
|Agetn Test System|Test Sytem|simulate scenarios such as traffic peaks and sudden surges with mixed east-west and north-south traffic using a professional traffic testing instrument
collect agent operation information and produce test results|
{: #tab-dctest-config title="Test System Configuration Requirements"}

## Interactive Q&A Task Test Requirements

The Test requirements for Data Center Network Operation Agent scenario is shown as follows:

| Test Objective | Verify the comprehensive processing capabilities for tasks such as configuration changes and troubleshooting queries in human-machine or machine-to-machine interaction scenarios, and evaluate the accuracy of task completion, interaction efficiency, and security compliance|
|---|---|
|Test Case Requirements|Cover at least three scenarios: information query, fault diagnosis, and configuration change. Each scenario must have at least five test cases (including normal requests and malicious requests for security testing), covering different difficulty levels from T1 to T3 (basic, medium, and hard).|
|Test Topo|Refer to data center topo to select typical network deployment scenarios such as finance|
|Preset Condition|a)The network environment is running stably, and baseline data has been collected b)The O&M system's agent capability is enabled and has the necessary tools access permissions c) The test system has been configured d) The agent's context window must be cleared before each test case begins e) Each test case is based on the standard natural language Prompt library and structured API request templates|
|Test Step|a) Enter the agent testing interface b) Initiate a request: The test script simulates a user or upper-layer agent, initiating requests via natural language or structured APIs c) Interaction process: The test system records the entire execution process of the agent and network status changes, including the agent's response content, execution results, interaction rounds, number of error messages, over-budget follow-up queries, task completion time, and security violations d) Result Evaluation: Based on the interactive task scoring criteria, the accuracy, completeness, timeliness, and security of the interaction content are scored|
|Expected Results|Calculate the score based on the agent capability to tackle the interactive task test questions.For example, accuracy: whether the final configuration or diagnostic conclusion is correct; efficiency: fewer interaction rounds and shorter time consumption result in higher scores; security: no security violations throughout the process; output process: readability and logical clarity of responses|

## Autonomous Response Task Test Requirements

The Test requirements for Data Center Network Operation Agent Scenario is shown as follows:

| Test Objective | Verify the ability of agents to autonomously perceive, analyze, and handle network anomalies without human intervention|
|---|---|
|Test Case Requirement|Cover scenarios such as fault and optimization. Each scenario should cover different difficulty levels from T1 to T3 (basic-medium-hard).|
|Test Topo|Refer to dc topo to select typical network deployment scenarios such as finance|
|Pre-set Condition|a) The network environment is running stably with managed network devices, collected baseline data, and the normal network status b) The O&M system's agent capability is enabled and has the corresponding tools access permissions c) The testing system has been configured d) Before each test case begins, the network environment returns to normal. Based on a unified initial configuration snapshot of the network devices, the system automatically rolls back after each test to ensure configuration consistency|
|Test Step|a) Triggered Events: Pre-set network anomaly events are required for constructing test cases, and the fault trigger time is recorded b) Autonomous Execution: The agent automatically perceives the event and executes the reasoning and handling process c) Data Acquisition: The test system records the entire agent execution process,network state changes,task completion time, whether security violations occurred, and the completeness of audit logs d) Result Determination: The final network state is compared with the expected state to assess the service impact and calculate the score|
|Expected Resutls|Calculate the score based on the agent capability to tackle the responsive task test questions|
{: #dc-interact title="Interactive Q&A Test System Requirements"}
