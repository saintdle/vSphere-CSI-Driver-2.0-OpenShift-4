# Container Storage Interface (CSI) driver for vSphere 6.x/7.x and OpenShift Container Platform 4.x
This repository provides scripts for deploying the vSphere CSI provider on an OCP environment. 

> This repo was updated in February 2022 to follow the latest manifests. VMware released official support for OpenShift in their public repository with the [CSI Driver version 2.1.0](https://github.com/kubernetes-sigs/vsphere-csi-driver/blob/master/docs/book/releases/v2.1.0.md), so my instructions now reference their manifests. 

Thank you to [Myles Gray](https://github.com/mylesagray), [Sandeep Pissay](https://github.com/SandeepPissay) and the [VMware team](https://github.com/kubernetes-sigs/vsphere-csi-driver) for working on this support. 
   
# Installation

Please read the prerequisites from the [official documentation](https://docs.vmware.com/en/VMware-vSphere-Container-Storage-Plug-in/index.html).
 * Requires OCP virtual machines to be virtual hardware v15 or above.

To create the vCenter roles you can use this script;

    https://github.com/saintdle/PowerCLI/blob/master/Create_CSI_Driver_vCenter_Roles.ps1 

1. Edit csi-vsphere.conf + vsphere.conf files with your environment details.
2. Create a Kubernetes secret + configmap that will contain configuration details to connect to vSphere for both the CSI Driver and the Cloud Provider Interface.
````
   oc create secret generic vsphere-config-secret --from-file=csi-vsphere.conf --namespace=kube-system
   oc create configmap cloud-config --from-file=vsphere.conf --namespace=kube-system
```` 
3. Taint all OpenShift nodes
```
   Command: kubectl taint nodes --all 'node.cloudprovider.kubernetes.io/uninitialized=true:NoSchedule'
```
4. Install the CPI (RBAC, Bindings and DaemonSet)

````
   oc apply -f https://raw.githubusercontent.com/kubernetes/cloud-provider-vsphere/master/manifests/controller-manager/cloud-controller-manager-roles.yaml
   oc apply -f https://raw.githubusercontent.com/kubernetes/cloud-provider-vsphere/master/manifests/controller-manager/cloud-controller-manager-role-bindings.yaml
   oc apply -f https://github.com/kubernetes/cloud-provider-vsphere/raw/master/manifests/controller-manager/vsphere-cloud-controller-manager-ds.yaml
````
5. Install the CSI
> The below installation is based on the CSI version 2.4.0. Please see the official repository for alternative versions for further Kubernetes version support.

````sh
# Taint the Control Plane nodes
kubectl taint nodes <k8s-primary-name> node-role.kubernetes.io/master=:NoSchedule

# Create the namespace
oc apply -f https://github.com/kubernetes-sigs/vsphere-csi-driver/blob/release-2.4/manifests/vanilla/namespace.yaml

# Install the CSI
oc apply -f https://raw.githubusercontent.com/kubernetes-sigs/vsphere-csi-driver/v2.4.0/manifests/vanilla/vsphere-csi-driver.yaml
````

# Documentation

Official documentation for vSphere CSI Driver is available here:

    https://vsphere-csi-driver.sigs.k8s.io
    
All install manifests referenced can be found here:

    https://github.com/kubernetes-sigs/vsphere-csi-driver

# Older Resources

You can read my [legacy] complete blogs here, please use with caution as some items might have changed!!!: 

  * [Installing the CSI driver in Openshift 4.x and creating a block PVC](https://veducate.co.uk/how-to-install-vsphere-csi-driver-openshift/)
   
  * [Using the CSI Driver in Openshift 4.x with VSAN Native File Services](https://veducate.co.uk/vsphere-csi-driver-openshift-vsan-file-services/)
