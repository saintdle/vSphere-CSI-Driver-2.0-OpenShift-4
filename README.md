# Container Storage Interface (CSI) driver for vSphere 7.x and OpenShift Container Platform
This repository provides scripts for deploying the vSphere CSI provider on an OCP environment. The driver in these files is 2.0 

# Installation

# Documentation

Official documentation for vSphere CSI Driver is available here:

    https://vsphere-csi-driver.sigs.k8s.io

# vSphere CSI Driver Images

v2.0.0

    gcr.io/cloud-provider-vsphere/csi/release/driver:v2.0.0
    gcr.io/cloud-provider-vsphere/csi/release/syncer:v2.0.0

# Source Manifests
The YAMLs in this repository are based on the manifests from the following locations;

https://github.com/kubernetes-sigs/vsphere-csi-driver/tree/master/manifests/v2.0.0/vsphere-7.0/vanilla

https://github.com/dav1x/ocp-vsphere-csi
