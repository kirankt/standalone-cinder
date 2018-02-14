# All-in-one Ceph Deployment

## Prereqs
A fully functioning OCP cluster

## As an admin of the OCP cluster, label a node to host Ceph:
oc label node <hostname> "controller"="true"

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
oc create sa openstack
oc create sa openstack-priv

## Create a project called 'openstack'. All the pods reside here.
oc new-project openstack

## As cluster admin, add scc to the service accounts:
oadm policy add-scc-to-user anyuid -n openstack -z openstack
oadm policy add-scc-to-user privileged -n openstack -z openstack-priv

## Create Cinder installation
oc create -f cinder-xtremio.yml

# Cinder with Ceph Backend
TODO
