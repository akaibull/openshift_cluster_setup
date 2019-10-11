# opennshit_cluster_setup

# Ports required for Openshift Cluster

Open ports on the network which will be used to setup Openshift Cluster.
```
22 			TCP
53 or 8053		TCP/UDP
80 or 443		TCP
1936			TCP
4001			TCP
2379 and 2380		TCP
4789			UDP
8443			TCP
10250			TCP
```
To get more details understanding on ports for Openshift, refer this link https://docs.openshift.com/container-platform/3.11/install/prerequisites.html


# Install Openshift 3.11 on CentOS

**On All Nodes, Create a user for installation to be used in Ansible and also grant root privileges to him.**

```
setenforce 0

sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config  

systemctl enable firewalld

systemctl start firewalld

firewall-cmd --add-port=0-65535/tcp --permanent 

firewall-cmd --add-port=0-65535/udp --permanent 

firewall-cmd --reload

swapoff -a

sed -e '/swap/ s/^#*/#/' -i /etc/fstab

modprobe br_netfilter

echo  net.bridge.bridge-nf-call-ip6tables = 1 >>/etc/sysctl.conf && echo net.bridge.bridge-nf-call-iptables = 1 >>/etc/sysctl.conf

sysctl -p 

```

```
useradd origin

passwd origin

echo -e 'Defaults:origin !requiretty\norigin ALL = (root) NOPASSWD:ALL' | tee /etc/sudoers.d/openshift 

chmod 440 /etc/sudoers.d/openshift 

firewall-cmd --add-service=ssh --permanent && firewall-cmd --reload 
```

**On All Nodes, install OpenShift Origin 3.11 repository and Docker and so on.**
```
yum -y install centos-release-openshift-origin311 epel-release docker git pyOpenSSL 

systemctl enable --now docker 

```

**On Master Node, login with a user created above and set SSH keypair with no pass-phrase.**
```
$ ssh-keygen -q -N ""
#Enter file in which to save the key (/home/origin/.ssh/id_rsa):

$ vi ~/.ssh/config
# create new ( define each node )

Host ctrl
    Hostname ctrl.srv.world
    User origin
Host node01
    Hostname node01.srv.world
    User origin
Host node02
    Hostname node02.srv.world
    User origin

$ chmod 600 ~/.ssh/config 

$ ssh-copy-id ctrl
```
