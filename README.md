# Container Storage Interface (CSI) driver for vSphere 6.x/7.x and OpenShift Container Platform 4.x
This repository provides scripts for deploying the vSphere CSI provider on an OCP environment. 

> This repo was updated in February 2021 to correct a mistake which omitted configuring the vSphere Cloud Provider Interface. Which is mandatory to using the CSI Driver. If you have deployed the CSI driver to your OpenShift environment without the CPI configuration, please review your configuration. Furthermore the VMware team released official support for OpenShift in their public repository with the [CSI Driver version 2.1.0](https://github.com/kubernetes-sigs/vsphere-csi-driver/blob/master/docs/book/releases/v2.1.0.md), so my instructions now reference their manifests. 

You can read my complete blogs here: 

  * [Installing the CSI driver in Openshift 4.x and creating a block PVC](https://veducate.co.uk/how-to-install-vsphere-csi-driver-openshift/)
   
  * [Using the CSI Driver in Openshift 4.x with VSAN Native File Services](https://veducate.co.uk/vsphere-csi-driver-openshift-vsan-file-services/)

Thank you to [Myles Gray](https://github.com/mylesagray), [Sandeep Pissay](https://github.com/SandeepPissay) and the [VMware team](https://github.com/kubernetes-sigs/vsphere-csi-driver) for working on this support. 
   
# Installation

Please read the prerequisites from the [official documentation](https://vsphere-csi-driver.sigs.k8s.io).

To create the vCenter roles you can use this script;

    https://github.com/saintdle/PowerCLI/blob/master/Create_CSI_Driver_vCenter_Roles.ps1 

1. Edit csi-vsphere.conf + vsphere.conf files with your environment details.
2. Create a Kubernetes secret + configmap that will contain configuration details to connect to vSphere for both the CSI Driver and the Cloud Provider Interface.
```
   Command: oc create secret generic vsphere-config-secret --from-file=csi-vsphere.conf --namespace=kube-system
   Command: oc create configmap cloud-config --from-file=vsphere.conf --namespace=kube-system
```  
3. Taint all OpenShift nodes
```
   Command: kubectl taint nodes --all 'node.cloudprovider.kubernetes.io/uninitialized=true:NoSchedule'
```
4. Install the CPI (RBAC, Bindings and DaemonSet)

```
   Command: oc apply -f https://raw.githubusercontent.com/kubernetes/cloud-provider-vsphere/master/manifests/controller-manager/cloud-controller-manager-roles.yaml
   Command: oc apply -f https://raw.githubusercontent.com/kubernetes/cloud-provider-vsphere/master/manifests/controller-manager/cloud-controller-manager-role-bindings.yaml
   Command: oc apply -f https://github.com/kubernetes/cloud-provider-vsphere/raw/master/manifests/controller-manager/vsphere-cloud-controller-manager-ds.yaml
```
5. Create the CSI (RBAC, Bindings, Deployment and DaemonSet)
> Use the correct vSphere version manifests as per this link https://github.com/kubernetes-sigs/vsphere-csi-driver/tree/master/manifests/v2.1.0
> This example shows the newer driver manifests for vSphere 7.0 U1.

For K8s >=1.17 (>=OpenShift 4.4)

```
   Command: oc apply -f https://raw.githubusercontent.com/kubernetes-sigs/vsphere-csi-driver/master/manifests/v2.1.0/vsphere-7.0u1/rbac/vsphere-csi-controller-rbac.yaml
   Command: oc apply -f https://raw.githubusercontent.com/kubernetes-sigs/vsphere-csi-driver/master/manifests/v2.1.0/vsphere-7.0u1/deploy/vsphere-csi-node-ds.yaml
   Command: oc apply -f https://raw.githubusercontent.com/kubernetes-sigs/vsphere-csi-driver/master/manifests/v2.1.0/vsphere-7.0u1/deploy/vsphere-csi-controller-deployment.yaml

```

For K8s <=1.16 (<=OpenShift 4.3)

```
   Command: oc apply -f https://raw.githubusercontent.com/kubernetes-sigs/vsphere-csi-driver/master/manifests/v1.0.3/rbac/vsphere-csi-controller-rbac.yaml
   Command: oc apply -f https://raw.githubusercontent.com/kubernetes-sigs/vsphere-csi-driver/master/manifests/v1.0.3/deploy/vsphere-csi-node-ds.yaml
   Command: oc apply -f https://raw.githubusercontent.com/kubernetes-sigs/vsphere-csi-driver/master/manifests/v1.0.3/deploy/vsphere-csi-controller-ss.yaml

```
I've personally tested with vSphere 6.7 u3 and vSphere 7.0, requires the OCP Node Virtual Machines to be VM Hardware 15 or higher. 

# Documentation

Official documentation for vSphere CSI Driver is available here:

    https://vsphere-csi-driver.sigs.k8s.io
    
All install manifests referenced can be found here:

    https://github.com/kubernetes-sigs/vsphere-csi-driver
