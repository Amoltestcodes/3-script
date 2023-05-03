Configure Centralized rsyslog Server in CENT OS:
----------------------------------------------------------------------

rsyslog is responsible for log processing in RHEL. rsyslog is abbreviation of ‘Rocket Fast System for Log processing’. rsyslog offers high-performance, great security features and modular design. It can accept input from wide variety of sources, transform it and output the result to diverse destinations.

In this article, we will configure a central logging server using rsyslog on RHEL and then we will configure RHEL clients to submit their local logs to this rsyslog based central logging server.

Environment Specification:
We are using two virtual machines, one as the rsyslog server and the other as the rsyslog client.
SERVER IP Address: 10.42.0.85 (centos 7.9)
CLIENT IP Address: 10.42.0.42 (centos 7.9)

Configuring rsyslog Server on centos 7.9:
---------------------------------------------------
1. rsyslog is by default installed on most of the Linux distros including RHEL/CentOS.
Connect to rsyslog server and check status of rsyslog.service, start it if it is not running. (Install the package from repository if there is no such service present)

[root@ansible_node_1 ~]# rpm -qi rsyslog
[root@ansible_node_1 ~]# systemctl start rsyslog.service
[root@ansible_node_1 ~]# systemctl enable rsyslog.service
[root@ansible_node_1 ~]# systemctl status rsyslog.service

2. Now we are configuring rsyslog settings to accept input from other machines.
[root@ansible_node_1 ~]# vim /etc/rsyslog.conf
Find and uncomment following two directives.
$ModLoad imtcp
$InputTCPServerRun 514





Save settings.

3. Now restart the rsyslog.service.
[root@ansible_node_1 ~]# systemctl restart rsyslog.service

4. Allow rsyslog service port in Linux firewall and reload the firewall.
[root@ansible_node_1 ~]# firewall-cmd --permanent --add-port=514/tcp
[root@ansible_node_1 ~]# firewall-cmd --reload

Our rsyslog server has been configured to received input from other log sources via port 514/tcp.


Configuring rsyslog Client on centos 7.9:
---------------------------------------------------
1. Connect to rsyslogclient.nehraclasses and check status of rsyslog.service, start & enable it if not running.
[root@ansible_node_1 ~]# rpm -qi rsyslog
[root@ansible_node_1 ~]# systemctl start rsyslog.service
[root@ansible_node_1 ~]# systemctl enable rsyslog.service
[root@ansible_node_1 ~]# systemctl start rsyslog.service

2. Now configure rsyslog client to transmit its log to our rsyslog server by adding the following directives in /etc/rsyslog.conf
[root@ansible_node_1 ~]# vim /etc/rsyslog.conf
# . 10.42.0.42:514






3. Restart the rsyslog.service to apply changes.
[root@ansible_node_1 ~]# systemctl restart rsyslog.service

Our rsyslog client has been configured.
=========================================

Now connect to our rsyslog server and check /var/log/messages
[root@ansible_node_1 ~]# tail /var/log/messages

We can see that client is forwarding its logs to server.

We have successfully configure a central login server using rsyslog on CentOS 7.

==============================================================================





