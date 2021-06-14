# Guide for provisioning a Proxy and DHCP server on RHEL8 / Centos8

**Background**

Deploying OpenShift 4.6 through the assisted installer method requires dynamic IP address assignment through a DHCP server.

In this design, we will keep the cluster nodes in a private network exposing only the API anbd ingress endpoints.  To provide the cluster with access to the public domain, we will also require a **proxy server**

**Before getting started:**
1. Provision a private subnet on the IBM Cloud; this will provide the installer with a pool of IP addresses to assign to each node


As a best practice, you should configure your DHCP and Proxy servers as two separate servers.  In this guidance document, I will be configuring both services on a single server.

## Provisioning a RHEL8 / Centos8 server

**Server Specs**

- **CPU:** 2 vCPU
- **Memmory:** 4GB RAM
- **Storage:** 100GB
- 
### Manually provisioning your RHEL 8 server

If you are provisioning your proxy server as a RHEL8 / Centos8 machine on existing VMWare infrastructure with public network connectivity:

**Note:** The installation of RHEL 8 requires that the machine be registered during the installation process. To ensure connectivity to the public network during the installation process, only configure the ethernet adapter that was has connectivity to the public distributed virtual switch

**Server with GUI**
 - Debugging Tools
 - Performance Tools
 - System Tools
 - Security Tools
 - Graphical Administration Tools

1. Configure a regular user account on the system, even if you remove it later. The root user can only log in via the console. To log remotely into a new system, you'll need a user account set up as an administrator that can issue the sudo command or su to root

### Placing an order on the IBM Cloud

You may also provision a RHEL 8 server through the IBM Cloud: https://cloud.ibm.com/gen1/infrastructure/provision/vs


## Configuring the server

Using either method, I follow the following procedure when the server first boots up:

1. Enable the firewall if it is not already enabled, and create separate firewall zones for my public (WAN) and private (LAN) network interfaces:
*Note:* find the names of your network interfaces: `ifconfig`

```console
systemctl enable firewalld --now;
firewall-cmd --change-interface=eth1 --zone=external --permanent;
firewall-cmd --change-interface=eth0 --zone=internal --permanent;
firewall-cmd --set-default-zone=internal;
systemctl restart firewalld;
```

1. To manage the machine through my browser (this includes a browser-based shell window!) I also like to activate the cockpit web console:

```console
dnf install cockpit -y;
firewall-cmd --add-service=cockpit --permanent --zone=external;
systemctl enable --now cockpit.socket;
```

Once enabled, the cockpit user interface can be accessed at https://PROV_NODE_IP:9090/

## Configuring ISC DHCP

1. Install the DHCP service

```console
dnf -y install dhcp-server;
```

1. Use the dhcp.conf.template file as a refence to update service's the dhcpd.conf file:

```console
vi /etc/dhcp/dhcpd.conf
```

**Note:** [See the dhcpd.conf man page for the full list of configuration options](https://linux.die.net/man/5/dhcpd.conf)

Updatete the firewall and enable the service
```console
systemctl enable --now dhcpd
firewall-cmd --add-service=dhcp --zone=internal --permanent
firewall-cmd --reload
```