.. toctree::
   :maxdepth: 4
   :caption: Contents:


.. _new-switch-vlan:

Add New VLAN to Switch Fabric
=============================
In this exercise, we will be adding VLAN 42 (for "client # 42") to the following switches:

* Core Aggregation Switch (AGG)
* Top of Rack Switch (TOR)

These two switches are connected by an aggregated ethernet interface (LAG) called ae10.

The AGG switch is connected to the upstream router on an aggregated ethernet interface (LAG) called ae0.

++++++++++++++++++++++++

Configure the AGG Switch
------------------------

1) Connect to OAM VPN
2) Login to the AGG switch using IDAM credentials
3) Enter "configure private" mode. This typically ensures that changes don't conflict with each other with multiple users on the system::

    user@AGG> configure private

4) Next, transition to VLAN configuration stanza::

    [edit]
    user@AGG# edit vlans
    [edit vlans]
    user@AGG# edit vlan42
    [edit vlans vlan42]
    user@AGG# set description "CLIENT-42"
    [edit vlans vlan42]
    user@AGG# set vlan-id 42
    [edit vlans vlan42]
    user@AGG# top

5) Review all changes from the top level of the configuration::

    [edit]
    user@AGG# show | compare
    [edit vlans vlan42]
    + description "CLIENT-42";
    + vlan-id 42;
    [edit]
    user@AGG#

**You should see only the changes you applied using these steps. If you see anything additional, do not "commit". There is likely configuration from another user's session which has become merged with your change.**

6) Review the configuration for ae0 and ae10 to ensure that this VLAN is trunked across these LAGs::

    [edit]
    user@AGG# show interfaces ae10 unit 0 family ethernet-switching
    description "Trunk to TOR, ae10"
    port-mode trunk
    vlan members [ all ]
    [edit]
    user@AGG# show interfaces ae0 unit 0 family ethernet-switching
    description "Trunk to Router, ae0"
    port-mode trunk
    vlan members [ all ]
    [edit]
    user@AGG#

**Because this interface is configured in "trunk" mode, frames egress with 802.1q tags (tagged frames). Additionally, because "vlan members" includes "all", all VLANs defined on this switch (everything under the "vlans" stanza in the configuration) are permitted ingress and egress on this interface.**

7) Once you are satisfied with your configuration additions, execute "commit check" to validate your syntax. If no error is returned, you may continue.

8) After "commit check" clears with no errors, run "commit and-quit" to implement the change in the running configuration, as well as the persistent configuration on the system::

    user@AGG# commit and-quit
    commit complete

    user@AGG>

9) Validate that VLAN 42 is now trunking across ae10::

    user@AGG>
    Name           Tag     Interfaces
    vlan42         42
                           ae0.0*, ae10.0*

    {master:0}
    user@AGG>
++++++++++++++++++++++++

Configure the TOR Switch
------------------------

1) Connect to OAM VPN
2) Login to the TOR switch using IDAM credentials
3) Enter "configure private" mode. This typically ensures that changes don't conflict with each other with multiple users on the system::

    user@TOR> configure private

4) Next, transition to VLAN configuration stanza::

    [edit]
    user@TOR# edit vlans
    [edit vlans]
    user@TOR# edit vlan42
    [edit vlans vlan42]
    user@TOR# set description "CLIENT-42"
    [edit vlans vlan42]
    user@TOR# set vlan-id 42
    [edit vlans vlan42]
    user@TOR# top

5) Review all changes from the top level of the configuration::

    [edit]
    user@TOR# show | compare
    [edit vlans vlan42]
    + description "CLIENT-42";
    + vlan-id 42;
    [edit]
    user@TOR#

**You should see only the changes you applied using these steps. If you see anything additional, do not "commit". There is likely configuration from another user's session which has become merged with your change.**

6) Review the configuration for ae10 to ensure that this VLAN is trunked across this LAG::

    [edit]
    user@TOR# show interfaces ae10 unit 0 family ethernet-switching
    description "Trunk to AGG, ae10"
    port-mode trunk
    vlan members [ all ]
    [edit]
    user@TOR#

**Because this interface is configured in "trunk" mode, frames egress with 802.1q tags (tagged frames). Additionally, because "vlan members" includes "all", all VLANs defined on this switch (everything under the "vlans" stanza in the configuration) are permitted ingress and egress on this interface.**

7) Once you are satisfied with your configuration additions, execute "commit check" to validate your syntax. If no error is returned, you may continue.

8) After "commit check" clears with no errors, run "commit and-quit" to implement the change in the running configuration, as well as the persistent configuration on the system::

    user@TOR# commit and-quit
    commit complete

    user@TOR>

9) Validate that VLAN 42 is now trunking across ae10::

    user@TOR>
    Name           Tag     Interfaces
    vlan42         42
                           ae10.0*

    {master:0}
    user@TOR>
