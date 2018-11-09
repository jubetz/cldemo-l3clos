# Cumulus Layer 3 Clos Demo

This demo sets up BGP Unnumbered to build a basic layer 3 Clos fabric using the Cumulus [reference topology](https://github.com/cumulusnetworks/cldemo-vagrant).  Please visit the reference topology github page for detailed instructions on using Cumulus Vx with Vagrant if you want to run this demo locally.

This is a basic layer 3 forwarding fabric that would simulate two racks.  Each rack contains a pair of leaf/ToR switches and two dual attached servers using MLAG.  BGP Unnumbered is running between the spine and leaf switches.  Each leaf advertises its directly connected subnet into the fabric to provide full reachability between all servers.

![topology](https://raw.githubusercontent.com/jubetz/cldemo-l3clos/master/images/l3-clos.png)

_Don't want to run it locally? You can also run this demo in [Cumulus In the Cloud](https://cumulusnetworks.com/try-for-free/)_


Table of Contents
=================
* [Prerequisites](#prerequisites)
* [If Using Libvirt KVM](#using-libvirtkvm)
* [If Using Cumulus in the Cloud](#using-cumulus-in-the-cloud)
* [Running the Demo](#running-the-demo)
* [Troubleshooting + FAQ](#troubleshooting--faq)


Prerequisites For Running Locally
------------------------
* Running this simulation roughly 10G of RAM.
* Internet connectivity is required from the hypervisor. Multiple packages are installed on both the switches and servers when the lab is created.
* Download this repository locally with `git clone https://github.com/CumulusNetworks/cldemo-l3clos.git` or if you do not have Git installed, [Download the zip file](https://github.com/CumulusNetworks/cldemo-l3clos/archive/master.zip)
* Install [Vagrant](https://releases.hashicorp.com/vagrant/).  Vagrant 2.0.1+ is needed to support VirtualBox 5.2.x
* Install [Virtualbox](https://www.virtualbox.org/wiki/VirtualBox) or [Libvirt+KVM](https://libvirt.org/drvqemu.html) hypervisors.


If Using Libvirt+KVM
------------------------
* Rename `Vagrantfile-kvm` to `Vagrantfile` replacing the existing Vagrantfile that is used for Virtualbox.


If Using Cumulus in the Cloud
------------------------
Request a "Blank Workbench" on [Cumulus in the Cloud](https://cumulusnetworks.com/try-for-free/). When you receive notice that it is provisioned, you'll be automatically connected to the *oob-mgmt-server*

Once connected run  
`git clone -b citc https://github.com/CumulusNetworks/cldemo-l3clos`

This will set the groundwork to copy the rest of the demo to your workbench.

Next  
`cd cldemo-netq`  
`ansible-playbook setup.yml`

After Ansible finishes, a new directory gets created named, 'l3-clos'

Running the Demo (Locally)
--------------------------
* `cd cldemo-l3clos`
* `vagrant up oob-mgmt-server oob-mgmt-switch`
* `vagrant up` (bringing up the oob-mgmt-server and switch first prevent DHCP issues)
* `vagrant ssh oob-mgmt-server`

Just as described in the topology diagram above each server is configured with a /32 loopback IP and BGP ASN.

To Provision This demo
-----------------------
Once at the oob-mgmt-server
* `cd l3-clos`
* `ansible-playbook run_demo.yml`

Once the anisble playbook finishes, ssh to server01.  Ping all of the other servers that are in the network
* ping 10.1.3.102
* ping 10.2.4.103
* ping 10.2.4.104

Traceroute to the other rack:
* traceroute 10.2.4.103

Exit back to *oob-mgmt-server* then ssh into leaf01

On leaf01, look around at your own interfaces:
ip link show
ip add show

Notice that our front panel ports, named 'swpX' aren't assigned any interfaces.  This is unnumbered interfaces and BGP Unnumbered in action.

Use NCLU (network command line utility) to peek at the status of BGP:
* net show bgp
* net show bgp sum

Also take a look at our Netq demo: https://github.com/CumulusNetworks/cldemo-netq
Netq provides next level visibility into the status, health and history of the forwarding fabric.
