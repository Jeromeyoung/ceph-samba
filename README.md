# ceph-discovery
# Summary
- [Introduction](#Introduction)
- [LAB Requirements](#Requirements)
- [Performance testing](#performance-testing)
- [OpenShift Data Foundation v4.7](#OpenShift-Data-Foundation-v47)
   -  [Order your lab environment from RHPDS](#Order-your-lab-environment-from-RHPDS)
   -  [Deploy 4 OSD nodes in  2 AZs with 3rd AZ and an arbiter node](#Deploy-4-OSD-nodes-in-2-AZs-with-3rd-AZ-with-an-arbiter-node)
   -  [Deploy ODF v4.7 Stretched Cluster](#Deploy-ODF-v47-Stretched-Cluster)

## Introduction
This is a testing demo about how to export cephfs volume from 2 RHEL Samba gateways.

## Requirements
- A ceph cluster, in my lab I'm running RHCS v5 based on Pacific
```bash
ceph versions
```
```
ceph versions
{
    "mon": {
        "ceph version 16.2.0-117.el8cp (0e34bb74700060ebfaa22d99b7d2cdc037b28a57) pacific (stable)": 3
    },
    "mgr": {
        "ceph version 16.2.0-117.el8cp (0e34bb74700060ebfaa22d99b7d2cdc037b28a57) pacific (stable)": 3
    },
    "osd": {
        "ceph version 16.2.0-117.el8cp (0e34bb74700060ebfaa22d99b7d2cdc037b28a57) pacific (stable)": 24
    },
    "mds": {
        "ceph version 16.2.0-117.el8cp (0e34bb74700060ebfaa22d99b7d2cdc037b28a57) pacific (stable)": 2
    },
    "overall": {
        "ceph version 16.2.0-117.el8cp (0e34bb74700060ebfaa22d99b7d2cdc037b28a57) pacific (stable)": 32
    }
}
```