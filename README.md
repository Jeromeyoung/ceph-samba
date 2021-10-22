# Export SMB with RHEL/Samba and cephfs as backend
# Summary
- [Introduction](#Introduction)
- [LAB Requirements](#Requirements)
- [RHCS deployment ](#RHCS-deployment)
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
1. Create inventory and verify ssh public key authentication
```
# pwd
/usr/share/cephadm-ansible
# grep inventory ansible.cfg
inventory = ./inventory/staging/hosts
# cat ./inventory/staging/hosts
[admin]
ceph-osd-1

[clients]
ceph-osd-[1:6]

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