# **Kubernetes VM Setup Guide with Kubeflow on Ubuntu**

**Description of this document:**
The objective of this guide is to install Kubeflow using local manifests on Ubuntu. To achieve this, Kubernetes needs to be set up first, and for that, virtual machines (VMs) are required. We'll utilize KVM and the Virtual Manager for this process. The VMs will house the Kubernetes master and worker nodes. Once Kubernetes is fully set up, we'll proceed with installing Kubeflow. This document will walk you through each step in detail.

# **Table of Contents**
1. [**Setting up VMs for Kubernetes**](https://github.com/aLVINlEE9/kubeflow-manifests-local-setup/blob/2220d6cc47493227992a73ba205c6436d87245e5/docs/Setting%20up%20VMs%20for%20Kubernetes.md)
    
    Two virtual machines (VMs) are set up for running Kubernetes. The setup process is conducted using a graphical user interface (GUI) of Ubuntu's Virtual Manager with KVM.
2. HOW TO SETUP KUBERNETES

    This guide will walk you through setting up Kubernetes on a master node named k8s-control and a worker node named k8s-2. 
    1. [**Master Node Setup**](https://github.com/aLVINlEE9/kubeflow-manifests-local-setup/blob/2220d6cc47493227992a73ba205c6436d87245e5/docs/Master%20Node%20Setup.md)
    2. [**Worker Node Setup**](https://github.com/aLVINlEE9/kubeflow-manifests-local-setup/blob/2220d6cc47493227992a73ba205c6436d87245e5/docs/Worker%20Node%20Setup.md)

3. [**Installing Kubeflow**](https://github.com/aLVINlEE9/kubeflow-manifests-local-setup/blob/2220d6cc47493227992a73ba205c6436d87245e5/docs/Installing%20Kubeflow.md)

4. [**Troubleshooting**](https://github.com/aLVINlEE9/kubeflow-manifests-local-setup/blob/2220d6cc47493227992a73ba205c6436d87245e5/docs/Troubleshooting.md)

5. [**References**](https://github.com/aLVINlEE9/kubeflow-manifests-local-setup/blob/2220d6cc47493227992a73ba205c6436d87245e5/docs/References.md)
