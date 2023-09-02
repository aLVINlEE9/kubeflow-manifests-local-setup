# ****Setting up VMs for Kubernetes****

Two virtual machines (VMs) are set up for running Kubernetes. The setup process is conducted using a graphical user interface (GUI) of Ubuntu's Virtual Manager with KVM.

## Prerequisites

- **Virtualization Software**: Ubuntu Virtual Manager with KVM (GUI version)

## VM Specifications

### 1. Master Node (k8s-control)

- **OS**: Ubuntu 22.04.3 LTS. Download the ISO file [here](https://releases.ubuntu.com/jammy/ubuntu-22.04.3-live-server-amd64.iso).
- **Storage**: 200GB
- **Memory**: 24GB
- **CPU**: 4 cores

### 2. Worker Node (k8s-2)

- **OS**: Ubuntu 22.04.3 LTS. Download the ISO file [here](https://releases.ubuntu.com/jammy/ubuntu-22.04.3-live-server-amd64.iso).
- **Storage**: 200GB
- **Memory**: 32GB
- **CPU**: 16 cores

## Setting up the VMs (Using GUI)

1. Click on `Create`.
2. Under "How would you like to install the operating system?", select `Local Install media (ISO image or CDROM)`.
3. Browse and select the downloaded `.iso` file.
4. Set memory and CPU:
    - **k8s-control**: 24GB (24576MB) memory and 4-core CPU
    - **k8s-2**: 32GB (32768MB) memory and 16-core CPU
5. Ensure "Enable storage for this virtual machine" is checked.
    - Create disk image for:
        - **k8s-control**: 200GB
        - **k8s-2**: 200GB
6. Name the VMs as `k8s-control` and `k8s-2` respectively. For network selection, choose `NAT` (default).
7. Click on the `Finish` button.
8. Begin the Ubuntu installation. During the setup, in the "FILE SYSTEM SUMMARY", ensure the size at location `/` is edited to 190GB to maximize usable space.
9. Complete the installation.