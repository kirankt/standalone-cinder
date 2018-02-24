# All-in-one Ceph Deployment

## Prereqs
A fully functioning OCP cluster

## As an admin of the OCP cluster, label a node to host Ceph:
oc label node some-host "controller"="true"

## Deploy Ceph
oc create -f ceph-demo.yml

## Teardown
oc delete deployment ceph-demo

On the host (the host that was labeled above) where ceph created directories:

rm -rf /etc/ceph /var/lib/ceph

# Standalone Cinder
## Caveats
1. MariaDB doesn't use persistent storage
2. Cinder auth strategy is 'noauth'

## Prerequisites
A fully working OpenShift/Kubernetes cluster. This template has been tested on
OCP 3.6 and newer

## Create two service accounts:
oc create sa cinder-anyuid

oc create sa cinder-privileged

## Create a project called 'openstack'. All the pods reside here.
oc new-project openstack

## As cluster admin, add scc to the service accounts:
oadm policy add-scc-to-user anyuid -n openstack -z cinder-anyuid

oadm policy add-scc-to-user privileged -n openstack -z cinder-privileged

## Create Cinder installation
oc create -f cinder-xtremio.yml

# Cinder with Ceph Backend
Gather info of your Ceph cluster. Or deploy a dummy on your OCP cluster.

Create a cinder client in the Ceph cluster. Fetch the fsid, ceph.client.cinder.keyring,
ceph.conf and the fsid. We will create a secret out of these.

'''bash
oc create secret generic ceph-secrets --from-literal fsid='cdd4641e-f769-410b-b7c5-61ad19708145' --from-file=ceph.conf --from-file=ceph.client.cinder.keyring
'''

Deploy Cinder with Ceph backend.

'''bash
oc create -f cinder-ceph.yml
'''
