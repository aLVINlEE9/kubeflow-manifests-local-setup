# **Kubernetes VM Setup Guide with Kubeflow on Ubuntu**

**Description of this document:**
The objective of this guide is to install Kubeflow using local manifests on Ubuntu. To achieve this, Kubernetes needs to be set up first, and for that, virtual machines (VMs) are required. We'll utilize KVM and the Virtual Manager for this process. The VMs will house the Kubernetes master and worker nodes. Once Kubernetes is fully set up, we'll proceed with installing Kubeflow. This document will walk you through each step in detail.

# **Table of Contents**
1. [**Setting up VMs for Kubernetes**](#prerequisites)
    
    Two virtual machines (VMs) are set up for running Kubernetes. The setup process is conducted using a graphical user interface (GUI) of Ubuntu's Virtual Manager with KVM.
2. HOW TO SETUP KUBERNETES

    This guide will walk you through setting up Kubernetes on a master node named k8s-control and a worker node named k8s-2. 
    1. [**Master Node Setup**](#prerequisites)
    2. [**Worker Node Setup**](#vm-specifications)

3. [**Installing Kubeflow**](#prerequisites)

4. [**Troubleshooting**](#prerequisites)

5. [**References**](#prerequisites)
