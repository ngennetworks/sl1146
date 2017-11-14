.. toctree::
   :maxdepth: 4
   :caption: Contents:


.. _introduce-new-prefix:

Introduce a New Prefix
----------------------

In this exercise, we will be adding the documentation prefix "192.0.2.0/24" to a router interface, and adjust routing policy statements to annouce the IP space to the upstream IP Transit provider.

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
    4.2.2.0/24 ## Existing network
    192.0.2.0/24
    8.8.8.8/24 ## Existing network
    [edit policy prefix-list LAX1-OUT-v4]
    user@mx#
5) Use "show | compare" to view the result of your "set" command::

    [edit policy prefix-list LAX1-OUT-v4]
    user@mx# show | compare
    ...
    4.2.2.0/24 ## Existing network
    + 192.0.2.0/24
    8.8.8.8/24 ## Existing network
    ...
    [edit policy prefix-list LAX1-OUT-v4]
    user@mx#
