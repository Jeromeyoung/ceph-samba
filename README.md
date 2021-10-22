# Export SMB with RHEL/Samba and cephfs as backend
# Summary
- [Introduction](#Introduction)
- [LAB Requirements](#Requirements)
- [RHCS deployment ](#RHCS-deployment)
   - [Install and verify RHCS pre-requisites](#Install-and-check-prerequisites)
   - [Deply RHCS from ceph-adm orchestrator](#RHCS-deployment-from-bootstrap-node)
- [OpenShift Data Foundation v4.7](#OpenShift-Data-Foundation-v47)
   -  [Order your lab environment from RHPDS](#Order-your-lab-environment-from-RHPDS)
   -  [Deploy 4 OSD nodes in  2 AZs with 3rd AZ and an arbiter node](#Deploy-4-OSD-nodes-in-2-AZs-with-3rd-AZ-with-an-arbiter-node)
   -  [Deploy ODF v4.7 Stretched Cluster](#Deploy-ODF-v47-Stretched-Cluster)

## Introduction
This is a testing demo about how to export cephfs volume from 2 RHEL Samba gateways.

## Requirements
- Ceph cluster, in my lab I'm running RHCS v5 based on Pacific
- 2 x RHEL Red Hat Enterprise Linux release 8.4 (Ootpa)
- ctdb v4.13.3
- samba v4.13.3
- Networking: 1 x NIC for cephfs public network, 1 x NIC for CTP Service IP 

## RHCS deployment  
### Install and check prerequisites
1. Pre-reqs for RHEL bootstrap node: 
   - Enable the RHCS repos
   - Create ansible hosts inventory
   - Configure ssh public key authentication from bootstrap node
```
# ansible all -m command -a 'subscription-manager register --username=ctorres-redhat --password="PUT_YOUR_PASSWORD_HERE"'
```
```
# ansible all -m command -a 'subscription-manager attach --pool=8a85f9997acf22f9017b4e4153d762e7'
```
```
# ansible all -m command -a 'subscription-manager repos --enable=rhel-8-for-x86_64-baseos-rpms --enable=rhel-8-for-x86_64-appstream-rpms --enable=rhceph-5-tools-for-rhel-8-x86_64-rpms --enable=ansible-2.9-for-rhel-8-x86_64-rpms'
```
```
dnf install cephadm-ansible -y
```
```
# cd  /usr/share/cephadm-ansible
```
Check your ansible.cfg file in /usr/share/cephadm-ansible
```
# grep inventory ansible.cfg
inventory = ./inventory/staging/hosts
```
Check the inventory groups admin and clients
```
# cat ./inventory/staging/hosts
[admin]
ceph-osd-1

[clients]
ceph-osd-[1:6]

```
```
# ansible all -m ping
ceph-osd-1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
ceph-osd-4 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
ceph-osd-5 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
ceph-osd-3 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
ceph-osd-6 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
ceph-osd-2 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

2. Instal cephadm-ansible by following RH documentation [link](https://access.redhat.com/documentation/en/red_hat_ceph_storage/5/html-single/installation_guide/index#registering-the-red-hat-ceph-storage-nodes-to-the-cdn-and-attaching-subscriptions_install)
```
ansible-playbook -i /usr/share/cephadm-ansible/inventory/staging/hosts cephadm-preflight.yml --extra-vars "ceph_origin=rhcs"
```
Run cephadm-preflight.yml playbook
```
ansible-playbook -i /usr/share/cephadm-ansible/inventory/staging/hosts cephadm-preflight.yml --extra-vars "ceph_origin=rhcs"
```

### RHCS deployment from bootstrap node
1. Bootstrap a storage cluster
```
# cephadm bootstrap --mon-ip 192.168.1.101 --registry-json ./mylogin.json
```
```
# cephadm bootstrap --mon-ip 192.168.1.101 --registry-json ./mylogin.json
Verifying podman|docker is present...
Verifying lvm2 is present...
Verifying time synchronization is in place...
Unit chronyd.service is enabled and running
Repeating the final host check...
podman|docker (/usr/bin/podman) is present
...
Ceph Dashboard is now available at:

	     URL: https://ceph-osd-1.carlos-lab.com:8443/
	    User: admin
	Password: 5o2ex18qmu

You can access the Ceph CLI with:

	sudo /usr/sbin/cephadm shell --fsid c0bf2ee0-338b-11ec-af56-527267c4d4c1 -c /etc/ceph/ceph.conf -k /etc/ceph/ceph.client.admin.keyring
...
```