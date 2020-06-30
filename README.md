Net-ISP-Balance
===============

This Linux application enables you to load balance a home or small
business Internet connection across two or more ISPs. You gain
additional bandwidth and reliability; if one ISP fails, the other(s)
will take over automatically.

See <a
href="http://lstein.github.io/Net-ISP-Balance/">http://lstein.github.io/Net-ISP-Balance/</a>
for installation and configuration instructions.

License
=======

Perl Artistic License version 2.0
(http://www.perlfoundation.org/artistic_license_2_0).

Credits
=======

This package contains a slightly-modified version of Mika Ilmaranta's
&lt;ilmis at nullnet.fi&gt; Link Status Monitor (lsm) package. The original
source code can be fond at http://lsm.foobar.fi/.


Author
======

Lincoln D. Stein (lincoln.stein@gmail.com).

Senior Principal Investigator, Ontario Institute for Cancer Research

Notes from StackExchange
========================

\#!/bin/sh

PATH=/usr/sbin:/sbin:/bin:/usr/bin

\# Primary Connection - 2085426230
   IF1=eth0
   IP1=172.16.0.2
    P1=172.16.0.1
P1_NET=172.16.0.0

\# Secondary Connection - 2085420213
   IF2=eth1
   IP2=172.16.1.2
    P2=172.16.1.1
P2_NET=172.16.1.0

\# Local Network
   IF3=eth2
   IP3=172.16.2.2
    P3=172.16.2.1
P3_NET=172.16.2.0

T1=WAN1
T2=WAN2

\# delete all existing rules.
iptables -F
iptables -t nat -F
iptables -t mangle -F
iptables -X

\# Always accept loopback and WAN traffic
iptables -A INPUT -i lo -j ACCEPT
iptables -A INPUT -i ${IF1} -j ACCEPT

\# Allow established connections, and those not coming from the outside
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
iptables -A FORWARD -i ${IF1} -o ${IF3} -m state --state ESTABLISHED,RELATED -j ACCEPT

\# Allow outgoing connections from the LAN side.
iptables -A FORWARD -i ${IF3} -o ${IF1} -j ACCEPT

\# Masquerade.
iptables -t nat -A POSTROUTING -o ${IF1} -j MASQUERADE

\# Enable routing.
echo 1 > /proc/sys/net/ipv4/ip_forward
