.. toctree::
   :maxdepth: 4
   :caption: Contents:


.. _route-prefix-to-host:

Route Prefix to Host
====================
* :ref:`route-prefix-to-host-v4`
* :ref:`route-prefix-to-host-v6`


.. _route-prefix-to-host-v4:

Route Prefix to Host - IPv4
---------------------------
In this exercise, we will be routing prefix "192.0.2.0/24" to host "172.16.0.2".

In the context of this exercise, it is assumed that the router (172.16.0.1) and the target host (172.16.0.2) are directly connected on a layer 2 segment and are capable of exchanging packets across router interface ae0.100.

It is furthermore assumed that 192.0.2.0/24 is already announced to upstream peers.

**NOTE: 192.0.2.0/24 is only an example for documentation and should never be announced, nor used in any production network.**

1) Connect to OAM VPN
2) Login to router using IDAM credentials
3) Enter "configure private" mode. This typically ensures that changes don't conflict with each other with multiple users on the system::

    user@mx> configure private

3) Next, set a static route within the IPv4 routing table (RIB)::

    [edit]
    user@mx# set routing-options rib inet.0 static route 192.0.2.0/24 next-hop 172.16.0.2
    [edit]
    user@mx#

4) Validate the configuration change::

  [edit]
  user@mx# show | compare
  [routing-options rib inet.0 static]
  + route 192.0.2.0/24 next-hop 172.16.0.2;
  [edit]
  user@mx#

**You should see only the changes you applied using these steps. If you see anything additional, do not "commit". There is likely configuration from another user's session which has become merged with your change.**

5) Once you are satisfied with your configuration additions, execute "commit check" to validate your syntax. If no error is returned, you may continue.

6) After "commit check" clears with no errors, run "commit and-quit" to implement the change in the running configuration, as well as the persistent configuration on the system::

    user@mx# commit and-quit
    commit complete

    user@mx>

7) Validate that the static route is now installed in the RIB::

    user@mx> show route 192.0.2.0/24

    inet.0: 657000 destinations, 657011 routes (27 active, 0 holddown, 656984 hidden)
    + = Active Route, - = Last Active, * = Both

    192.0.2.0/24    *[Static/5] 1m 15:55:11
                        > to 172.16.0.2 via ae0.100
                        .. _route-prefix-to-host-v4:


.. _route-prefix-to-host-v6:

Route Prefix to Host - IPv6
---------------------------
In this exercise, we will be routing prefix "2001:DB8:100::/48" to host "2001:DB8::2".

In the context of this exercise, it is assumed that the router (2001:DB8::1) and the target host (2001:DB8::2) are directly connected on a layer 2 segment and are capable of exchanging packets across router interface ae0.100.

It is furthermore assumed that 2001:DB8:100::/48 is already announced to upstream peers.

**NOTE: 2001:DB8::/32 (and its constituent networks) are only an example for documentation and should never be announced, nor used in any production network.**

1) Connect to OAM VPN
2) Login to router using IDAM credentials
3) Enter "configure private" mode. This typically ensures that changes don't conflict with each other with multiple users on the system::

    user@mx> configure private

3) Next, set a static route within the IPv4 routing table (RIB)::

    [edit]
    user@mx# set routing-options rib inet6.0 static route 2001:DB8:100::/48 next-hop 2001:DB8::2
    [edit]
    user@mx#

4) Validate the configuration change::

  [edit]
  user@mx# show | compare
  [routing-options rib inet6.0 static]
  + route 2001:DB8:100::/48 next-hop 2001:DB8::2;
  [edit]
  user@mx#

**You should see only the changes you applied using these steps. If you see anything additional, do not "commit". There is likely configuration from another user's session which has become merged with your change.**

5) Once you are satisfied with your configuration additions, execute "commit check" to validate your syntax. If no error is returned, you may continue.

6) After "commit check" clears with no errors, run "commit and-quit" to implement the change in the running configuration, as well as the persistent configuration on the system::

    user@mx# commit and-quit
    commit complete

    user@mx>

7) Validate that the static route is now installed in the RIB::

    user@mx> show route 2001:DB8:100::/48

    inet6.0: 41960 destinations, 41961 routes (28 active, 0 holddown, 41932 hidden)
    + = Active Route, - = Last Active, * = Both

    2001:DB8:100::/48    *[Static/5] 1m 15:55:11
                        > to 2001:DB8::2 via ae0.100
