.. toctree::
   :maxdepth: 4
   :caption: Contents:


.. _introduce-new-prefix:

Introduce a New Prefix
----------------------

In this exercise, we will be adding the documentation prefix "192.0.2.0/24" to a router interface, and adjust routing policy statements to annouce the IP space to the upstream IP Transit provider. We will assign a /30 address on interface "ae0.100"; this is a LAG interface with a VLAN tag of 100.

**NOTE: 192.0.2.0/24 is only an example and should never be announced, nor used in any production network.**

1) Connect to OAM VPN
2) Login to router using IDAM credentials
3) Enter "configure" mode::

    user@mx> configure private

4) Transition to policy stanza of configuration and add prefix to prefix-list "LAX1-OUT-v4"::

    [edit]
    user@mx# edit policy prefix-list LAX1-OUT-v4
    [edit policy prefix-list LAX1-OUT-v4]
    user@mx# set 192.0.2.0/24
    [edit policy prefix-list LAX1-OUT-v4]
    user@mx# show
    4.2.2.0/24; ## Existing network
    192.0.2.0/24;
    8.8.8.8/24; ## Existing network
    [edit policy prefix-list LAX1-OUT-v4]
    user@mx#

5) Use "show | compare" to view the result of the "set" command. This is equivalent to the "diff" command on POSIX systems.::

    [edit policy prefix-list LAX1-OUT-v4]
    user@mx# show | compare
    [edit policy prefix-list LAX1-OUT-v4]
    + 192.0.2.0/24;
    [edit policy prefix-list LAX1-OUT-v4]
    user@mx#

6) Transition to the routing configuration stanza by first moving to the top level of the configuration, then editing "routing-options rib inet.0 aggregate". Implementing an aggregation statement; this ensures that the specified prefix length (/24) is consistently presented to the upstream peer, even if the block is broken into smaller subnets within the network.::

    [edit policy prefix-list LAX1-OUT-v4]
    user@mx# top
    [edit]
    user@mx# edit routing-options rib inet.0 aggregate
    [edit routing-options rib inet.0 aggregate]
    user@mx# set route 192.0.2.0/24
    [edit routing-options rib inet.0 aggregate]
    user@mx# show | compare
    [edit routing-options rib inet.0 aggregate]
    + 192.0.2.0/24;
    [edit routing-options rib inet.0 aggregate]
    user@mx# top
    [edit]
    user@mx#

7) Next, configure 192.0.2.0/24 on ae0.100::

    [edit]
    user@mx# edit interface ae0 unit 100
    [edit interface ae0 unit 100]
    user@mx# show
    description "LAG to switch A";
    vlan-id 100;
    family inet;
    [edit interface ae0 unit 100]
    user@mx# set family inet address 192.0.2.1/30
    [edit interface ae0 unit 100]
    user@mx# show | compare
    [edit interface ae0 unit 100 family inet address]
    + 192.0.2.1/30;
    user@mx# top

8) Review all changes from the top level of the configuration::

    [edit]
    user@mx# show | compare
    [edit interface ae0 unit 100 family inet address]
    + 192.0.2.1/30;
    [edit routing-options rib inet.0 aggregate]
    + 192.0.2.0/24;
    [edit policy prefix-list LAX1-OUT-v4]
    + 192.0.2.0/24;
    [edit]
    user@mx#

**You should see only the changes you applied using these steps. If you see anything additional, do not "commit". There is likely configuration from another user's session which has become merged with your change.**

9) Once you are satisfied with your configuration additions, execute "commit check" to validate your syntax. If no error is returned, you may continue.

10) Once "commit check" clears, run "commit" to implement the change in the running configuration, as well as the persistent configuration on the system.

11) After the change is committed, validate that the desired prefix is being announced to upstream peer(s)::

      user@mx> show bgp neighbor
      Peer: <BGP NEIGHBOR IP>+179 AS <PEER ASN> Local: <LOCAL EXTERNAL IP>+50287 AS <LOCAL ASN>
      Group: <BGP NEIGHBOR GROUP> Routing-Instance: master
      Forwarding routing-instance: master
      Type: External    State: Established    Flags: <Sync>
      Last State: OpenConfirm   Last Event: RecvKeepAlive
      ... snip ...

      user@mx> show route advertising-protocol bgp <BGP NEIGHBOR IP>

      inet.0: 656663 destinations, 656674 routes (27 active, 0 holddown, 656647 hidden)
      Prefix		  Nexthop	       MED     Lclpref    AS path
      ... other prefixes ...
      * 192.0.2.0/24          Self                                    I
      ... other prefixes ...

      user@mx>

12) Once this validation passes, connect to one of the following Looking Glass sites to validate routing:

  * Level3_
  * Zayo_
  * Sprint_
  * HE.net_

.. _Level3: http://lg.level3.net/bgp/lg_bgp_main.php
.. _Zayo: https://www.zayo.com/ca/services/ip-services/looking-glass/
.. _Sprint: https://www.sprint.net/lg/
.. _HE.net: https://lg.he.net/

**For more information on validating BGP routing, visit ":ref:`bgp-validation`".

Example from Level3 (for 8.8.8.0/24)::

    Report generated from: ear1.atl2

    Route results for 8.8.8.0/24 from Atlanta, GA


    BGP Routing table entry for 8.8.8.0/24
    Paths: (2 available, best #1)

      15169
      AS-path translation: 8.8.8.0/24
       edge1.Atlanta4 (metric 0)
        Community: North_America Backbone_2 Lclprf_86 United_States Level3_Peer Atlanta
        Origin: IGP, metric 0, localpref 86, Used  Valid  Best  IGP  Group-Best
        Originator: edge1.Atlanta4

      15169
      AS-path translation: 8.8.8.0/24
       edge1.Atlanta4 (metric 0)
        Community: North_America Backbone_2 Lclprf_86 United_States Level3_Peer Atlanta
        Origin: IGP, metric 0, localpref 86, Valid  IGP
        Originator: edge1.Atlanta4
