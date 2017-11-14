.. toctree::
   :maxdepth: 4
   :caption: Contents:


.. _add-prefix-vlan:

Add Prefix to a VLAN Interface
------------------------------

In this exercise, we will be adding the documentation prefix "192.0.2.0/24" to VLAN interface "ae0.100"; this is a LAG interface with a VLAN tag of 100.

**NOTE: 192.0.2.0/24 is only an example and should never be announced, nor used in any production network.**

1) Connect to OAM VPN
2) Login to router using IDAM credentials
3) Enter "configure private" mode. This typically ensures that changes don't conflict with each other with multiple users on the system::

    user@mx> configure private

3) Next, transition to configuration for ae0.100 and add 192.0.2.1/24::

    [edit]
    user@mx# edit interface ae0 unit 100
    [edit interface ae0 unit 100]
    user@mx# show
    description "LAG to switch A";
    vlan-id 100;
    family inet;
    [edit interface ae0 unit 100]
    user@mx# set family inet address 192.0.2.1/24
    [edit interface ae0 unit 100]
    user@mx# show | compare
    [edit interface ae0 unit 100 family inet address]
    + 192.0.2.1/24;
    user@mx# top

4) Review all changes from the top level of the configuration::

    [edit]
    user@mx# show | compare
    [edit interface ae0 unit 100 family inet address]
    + 192.0.2.1/24;
    [edit]
    user@mx#

**You should see only the changes you applied using these steps. If you see anything additional, do not "commit". There is likely configuration from another user's session which has become merged with your change.**

5) Once you are satisfied with your configuration additions, execute "commit check" to validate your syntax. If no error is returned, you may continue.

6) After "commit check" clears with no errors, run "commit and-quit" to implement the change in the running configuration, as well as the persistent configuration on the system::

    user@mx# commit and-quit
    commit complete

    user@mx>

7) Validate that the IP address is now present on interface ae0.100::

    user@mx> show interfaces terse ae0.100
    Interface               Admin Link Proto    Local                 Remote
    ae0.10050               up    up   inet     192.0.2.1/24
                                     multiservice

    user@mx>
