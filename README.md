### Spine and Leaf

Leaf_01

br-ctlplane  Provisioning : eth0 1/2/3/4--->spine_01--> swp2
br-ex        External 	 : eth1 5/6/7/8--->spine_01--> swp3
br-vlan      Isolated_Vlan: eth2 9/10/11--->spine_01--> swp4

Leaf_02 -  

br-ctlplane  Provisioning  : eth0 5--->spine_01-->5
br-ex        External 	   : eth1 6--->spine_01-->6
br-vlan      Isolated_Vlan : eth2 7--->spine_01-->7


Spine_01 - 1st leg

Provisioning : swp2-->Leaf_01-->swp4
External 	 : swp3-->Leaf_01-->swp8
vlan 		 : swp4-->Leaf_01-->swp11

Spine_01 - 2nd leg

Provisioning : swp5-->Leaf_01-->swp1
External 	 : swp6-->Leaf_01-->swp2
Vlan 		 : swp7-->Leaf_01-->swp3


===================================================================================================

Configuration: 

### Leaf_01

net add bridge br-ctlplane port swp1-4
net add bridge br-ex port swp58
net add bridge br-vlan port swp9-11

net commit

vi /etc/network/interfaces

bridge-stp off
bridge-pvid 1
bridge-vlan-aware yes/no

ifreload -a 

### Spine_01

net add bridge br-ctlplane port swp1-4
net add bridge br-ex port swp58
net add bridge br-vlan port swp9-11

net commit

vi /etc/network/interfaces

bridge-stp off
bridge-pvid 1
bridge-vlan-aware yes/no

ifreload -a 


### Leaf_02

net add bridge br-ctlplane port swp1-4
net add bridge br-ex port swp5-8
net add bridge br-vlan port swp9-11

net commit

vi /etc/network/interfaces

bridge-stp off
bridge-pvid 1
## or 
bridge-vids 710-750
bridge-vlan-aware yes/no

ifreload -a 




### FOR LEAF_01

auto vlan20
iface vlan20
    address 172.18.20.1/24
    vlan-id 20
    vlan-raw-device bridge

auto vlan40
iface vlan40
    address 172.16.40.1/24
    vlan-id 40
    vlan-raw-device bridge

auto vlan50
iface vlan50
    address 172.17.50.1/24
    vlan-id 50
    vlan-raw-device bridge

auto vlan60
iface vlan60
    address 172.19.60.1/24
    vlan-id 60
    vlan-raw-device bridge




### FOR LEAF_02


auto vlan120
iface vlan120
    address 172.118.120.1/24
    vlan-id 120
    vlan-raw-device bridge

auto vlan140
iface vlan140
    address 172.116.140.1/24
    vlan-id 140
    vlan-raw-device bridge

auto vlan150
iface vlan150
    address 172.117.150.1/24
    vlan-id 150
    vlan-raw-device bridge

auto vlan160
iface vlan160
    address 172.119.160.1/24
    vlan-id 160
    vlan-raw-device bridge




#### Configuration 


root@spine_01:~# net show interface 
State  Name     Spd  MTU    Mode          LLDP             Summary
-----  -------  ---  -----  ------------  ---------------  --------------------------
UP     lo       N/A  65536  Loopback                       IP: 127.0.0.1/8
       lo                                                  IP: ::1/128
UP     eth0     1G   1500   Mgmt                           IP: 10.74.130.184/22(DHCP)
UP     swp1     1G   1500   Access/L2                      Master: br-ex(UP)
UP     swp2     1G   1500   Interface/L3  leaf_01 (swp4)   IP: 192.168.24.250/24
UP     swp3     1G   1500   Access/L2     leaf_01 (swp8)   Master: br-ex(UP)
UP     swp4     1G   1500   Trunk/L2      leaf_01 (swp11)  Master: br-vlan(UP)
UP     swp5     1G   1500   Interface/L3  leaf_02 (swp1)   IP: 192.168.34.250/24
UP     swp6     1G   1500   Access/L2     leaf_02 (swp2)   Master: br-ex(UP)
UP     swp7     1G   1500   Trunk/L2      leaf_02 (swp3)   Master: br-vlan(UP)
UP     br-ex    N/A  1500   Bridge/L2
UP     br-vlan  N/A  1500   Bridge/L2
UP     vlan20   N/A  1500   Interface/L3                   IP: 172.18.20.1/24
UP     vlan40   N/A  1500   Interface/L3                   IP: 172.16.40.1/24
UP     vlan50   N/A  1500   Interface/L3                   IP: 172.17.50.1/24
UP     vlan60   N/A  1500   Interface/L3                   IP: 172.19.60.1/24
UP     vlan120  N/A  1500   Interface/L3                   IP: 172.118.120.1/24
UP     vlan140  N/A  1500   Interface/L3                   IP: 172.116.140.1/24
UP     vlan150  N/A  1500   Interface/L3                   IP: 172.117.150.1/24
UP     vlan160  N/A  1500   Interface/L3                   IP: 172.119.160.1/24

root@spine_01:~# 

root@spine_01:~# cat /etc/network/interfaces
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

source /etc/network/interfaces.d/*.intf

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
auto eth0
iface eth0 inet dhcp

auto br-ex
iface br-ex
    bridge-ports swp1 swp3 swp6
    bridge-vlan-aware no
    bridge-stp off
    bridge-pvid 1


auto swp2
iface swp2
	address 192.168.24.250/24
auto swp5
iface swp5
	address 192.168.34.250/24


auto br-vlan
iface br-vlan
    bridge-ports swp4 swp7
    bridge-vlan-aware yes
    bridge-stp off
    bridge-vids 10-160

### FOR LEAF_01

auto vlan20
iface vlan20
    address 172.18.20.1/24
    vlan-id 20
    vlan-raw-device br-vlan

auto vlan40
iface vlan40
    address 172.16.40.1/24
    vlan-id 40
    vlan-raw-device br-vlan

auto vlan50
iface vlan50
    address 172.17.50.1/24
    vlan-id 50
    vlan-raw-device br-vlan

auto vlan60
iface vlan60
    address 172.19.60.1/24
    vlan-id 60
    vlan-raw-device br-vlan




### FOR LEAF_02


auto vlan120
iface vlan120
    address 172.118.120.1/24
    vlan-id 120
    vlan-raw-device br-vlan

auto vlan140
iface vlan140
    address 172.116.140.1/24
    vlan-id 140
    vlan-raw-device br-vlan

auto vlan150
iface vlan150
    address 172.117.150.1/24
    vlan-id 150
    vlan-raw-device br-vlan

auto vlan160
iface vlan160
    address 172.119.160.1/24
    vlan-id 160
    vlan-raw-device br-vlan
root@spine_01:~# 


root@spine_01:~# net show route

show ip route
=============
Codes: K - kernel route, C - connected, S - static, R - RIP,
       O - OSPF, I - IS-IS, B - BGP, E - EIGRP, N - NHRP,
       T - Table, v - VNC, V - VNC-Direct, A - Babel, D - SHARP,
       F - PBR,
       > - selected route, * - FIB route

K>* 0.0.0.0/0 [0/0] via 10.74.131.254, eth0, 00:02:34
C>* 10.74.128.0/22 is directly connected, eth0, 00:02:34
C>* 172.16.40.0/24 is directly connected, vlan40, 00:02:34
C>* 172.17.50.0/24 is directly connected, vlan50, 00:02:34
C>* 172.18.20.0/24 is directly connected, vlan20, 00:02:34
C>* 172.19.60.0/24 is directly connected, vlan60, 00:02:34
C>* 172.116.140.0/24 is directly connected, vlan140, 00:02:34
C>* 172.117.150.0/24 is directly connected, vlan150, 00:02:34
C>* 172.118.120.0/24 is directly connected, vlan120, 00:02:34
C>* 172.119.160.0/24 is directly connected, vlan160, 00:02:34
C>* 192.168.24.0/24 is directly connected, swp2, 00:02:34
C>* 192.168.34.0/24 is directly connected, swp5, 00:02:34


root@spine_01:~# cat /etc/default/isc-dhcp-relay
SERVERS="192.168.24.1 192.168.24.10"
INTF_CMF="-i swp2 -i swp5"
OPTIONS=""
root@spine_01:~# 







### RHOSP-13 DCN

yum install wget git tmux -y
yum update -y 
reboot


hostnamectl set-hostname undercloud-0-spine-leaf.example.com
hostnamectl set-hostname --transient undercloud-0-spine-leaf.example.com
echo "127.0.0.1 undercloud-0-spine-leaf.example.com undercloud-0-spine-leaf" >> /etc/hosts


useradd stack
echo stack | passwd --stdin stack
echo "stack ALL=(root) NOPASSWD:ALL" | tee -a /etc/sudoers.d/stack
chmod 0440 /etc/sudoers.d/stack

su - stack 


### Configuring Undercloud

~~~
cat install_undercloud.sh
~~~

#!/bin/bash

sudo yum install -y python-tripleoclient

echo -e "[DEFAULT]
local_interface = eth0
local_ip = 192.168.24.1/24
undercloud_public_vip = 192.168.24.2
undercloud_admin_vip = 192.168.24.3
undercloud_ntp_servers = clock.corp.redhat.com
overcloud_domain_name = example.com
local_subnet = leaf0
subnets = leaf0,leaf1
enable_routed_networks = true

[leaf0]
cidr = 192.168.24.0/24
dhcp_start = 192.168.24.10
dhcp_end = 192.168.24.40
gateway = 192.168.24.1
inspection_iprange = 192.168.24.50,192.168.24.100
masquerade = False

[leaf1]

cidr = 192.168.34.0/24
dhcp_start = 192.168.34.5
dhcp_end = 192.168.34.20
gateway = 192.168.34.1
inspection_iprange = 192.168.34.50,192.168.34.100
masquerade = False

#TODO(skatlapa): add param to override masq" > ~/undercloud.conf

source /home/stack/stackrc
openstack undercloud install
openstack network list



~~~
cat image_upload.sh
~~~

#!/bin/bash 

mkdir ~/images
mkdir ~/templates
cd  ~/images
curl -O https://images.rdoproject.org/queens/rdo_trunk/current-tripleo-rdo/ironic-python-agent.tar
curl -O https://images.rdoproject.org/queens/rdo_trunk/current-tripleo-rdo/overcloud-full.tar

tar xvf ironic-python-agent.tar
tar xvf overcloud-full.tar


openstack overcloud image upload --image-path /home/stack/images/

touch /home/stack/overcloud_images.yaml
touch /home/stack/local_registry_images.yaml


~~~
cat container_prepare.sh
~~~

#!/bin/bash

sudo openstack overcloud container image prepare \
         --namespace docker.io/tripleoqueens \
         --tag current-tripleo-rdo \
         --tag-from-label rdo_version \
         --output-env-file=~/overcloud_images.yaml
         tag=`grep "docker.io/tripleoqueens" /home/stack/overcloud_images.yaml |tail -1 |awk -F":" '{print $3}'`

sudo openstack overcloud container image prepare \
         --namespace docker.io/tripleoqueens \
         --tag current-tripleo-rdo \
         --push-destination 192.168.24.1:8787 \
         --output-env-file=/home/stack/overcloud_images.yaml \
--output-images-file=/home/stack/local_registry_images.yaml

sudo openstack overcloud container image upload --config-file /home/stack/local_registry_images.yaml --verbose

sudo sed -i '/enabled_drivers/s/$/,fake_pxe/' /etc/ironic/ironic.conf
sudo systemctl restart openstack-ironic-api openstack-ironic-conductor
touch /home/stack/instackenv.json



vi /home/stack/instackenv.json

~~~
{
"nodes":[
{
"mac":["00:50:00:00:08:00"
],
"name":"controller-0",
"capabilities" : "node:ctrl-0,boot_option:local",
"pm_type":"fake_pxe"
},

{
"mac":["00:50:00:00:09:00"
],
"name":"compute-0",
"capabilities" : "node:cmpt-0,boot_option:local",
"pm_type":"fake_pxe"
},

{
"mac":["00:50:00:00:0a:00"
],
"name":"compute-1",
"capabilities" : "node:ctrl-1,boot_option:local",
"pm_type":"fake_pxe"
}


]
}
~~~


$ openstack overcloud node import /home/stack/instackenv.json



(undercloud) [stack@undercloud-0 ~]$ openstack baremetal port list
+--------------------------------------+-------------------+
| UUID                                 | Address           |
+--------------------------------------+-------------------+
| 2e61d8f7-3d96-4ec9-87b1-8d04e5af8dc3 | 00:50:00:00:08:00 |
| 9321f7fc-45a1-4cef-929c-0474ab43667f | 00:50:00:00:09:00 |
| 67093042-d322-490c-81b4-861689df4647 | 00:50:00:00:0a:00 |
+--------------------------------------+-------------------+
(undercloud) [stack@undercloud-0 ~]$


(undercloud) [stack@undercloud-0 ~]$ openstack baremetal port set --physical-network leaf1 67093042-d322-490c-81b4-861689df4647                                              
(undercloud) [stack@undercloud-0 ~]$ openstack baremetal port set --physical-network leaf0 9321f7fc-45a1-4cef-929c-0474ab43667f 


(undercloud) [stack@undercloud-0 ~]$ openstack baremetal port show 9321f7fc-45a1-4cef-929c-0474ab43667f
+-----------------------+--------------------------------------+
| Field                 | Value                                |
+-----------------------+--------------------------------------+
| address               | 00:50:00:00:09:00                    |
| created_at            | 2019-12-26T06:07:40+00:00            |
| extra                 | {}                                   |
| internal_info         | {}                                   |
| local_link_connection | {}                                   |
| node_uuid             | 3eccb5f4-9625-4136-9b18-22ddecd027ef |
| physical_network      | leaf0                                |
| portgroup_uuid        | None                                 |
| pxe_enabled           | True                                 |
| updated_at            | 2019-12-26T06:12:20+00:00            |
| uuid                  | 9321f7fc-45a1-4cef-929c-0474ab43667f |
+-----------------------+--------------------------------------+
(undercloud) [stack@undercloud-0 ~]$ openstack baremetal port show 67093042-d322-490c-81b4-861689df4647
+-----------------------+--------------------------------------+
| Field                 | Value                                |
+-----------------------+--------------------------------------+
| address               | 00:50:00:00:0a:00                    |
| created_at            | 2019-12-26T06:07:41+00:00            |
| extra                 | {}                                   |
| internal_info         | {}                                   |
| local_link_connection | {}                                   |
| node_uuid             | ce2b55c9-c48e-4054-a9b7-e63e20ce886b |
| physical_network      | leaf1                                |
| portgroup_uuid        | None                                 |
| pxe_enabled           | True                                 |
| updated_at            | 2019-12-26T06:12:09+00:00            |
| uuid                  | 67093042-d322-490c-81b4-861689df4647 |
+-----------------------+--------------------------------------+
(undercloud) [stack@undercloud-0 ~]$ 




#!/bin/bash
time openstack overcloud deploy --templates /home/stack/templates_new/openstack-tripleo-heat-templates/ \
-e /home/stack/network_data_spine_leaf.yaml \
-e /home/stack/roles_data_spine_leaf.yaml \
-e /home/stack/templates_new/openstack-tripleo-heat-templates/environments/network-isolation.yaml \
-e /home/stack/templates_new/openstack-tripleo-heat-templates/environments/network-environment.yaml \
-e /home/stack/templates_new/openstack-tripleo-heat-templates/environments/disable-telemetry.yaml \
-e /home/stack/overcloud_images.yaml \
-e /home/stack/templates_new/scheduler_hints_env.yaml \
-e /home/stack/templates_new/node-info.yaml \
-e /home/stack/templates_new/openstack-tripleo-heat-templates/environments/docker-ha.yaml \
--libvirt-type qemu --timeout 100
