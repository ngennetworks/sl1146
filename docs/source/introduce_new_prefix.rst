.. toctree::
   :maxdepth: 4
   :caption: Contents:


.. _introduce-new-prefix:

Introduce a New Prefix
----------------------

In this exercise, we will be adding the documentation prefix "192.0.2.0/24" to a router interface, and adjust routing policy statements to annouce the IP space to the upstream IP Transit provider. We will assign a /30 address on interface "ae0.100"; this is a LAG interface with a VLAN tag of 100.

NOTE: 192.0.2.0/24 is only an example and should never be announced, nor used in any production network.

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
5) Use "show | compare" to view the result of your "set" command. This is equivalent to the "diff" command on POSIX systems.::

    [edit policy prefix-list LAX1-OUT-v4]
    user@mx# show | compare
    ...
    4.2.2.0/24; ## Existing network
    + 192.0.2.0/24;
    8.8.8.8/24; ## Existing network
    ...
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

7) Next, we will configure 192.0.2.0/24 on ae0.100::

    [edit]
    user@mx# edit interface ae0 unit 100
    [edit interface ae0 unit 100]
    user@mx# show
    description "LAG to switch A";
    vlan-id 100;
    family inet;
    [edit interface ae0 unit 100]
    user@mx\# set family inet address 192.0.2.1/30
    [edit interface ae0 unit 100]
    user@mx\# show | compare
    [edit interface ae0 unit 100 family inet address]
    + 192.0.2.1/30;
    user@mx\# top

8)
