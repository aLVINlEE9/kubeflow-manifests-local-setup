## Master Node Setup

### 1. Start with the master node `k8s-control`:

All of the following commands are executed in the terminal.

### 2. Check the IP address of your host:

```bash
virsh net-dhcp-leases default
```

Output: 

```yaml
Expiry Time           MAC address         Protocol   IP address           Hostname      Client ID or DUID
---------------------------------------------------------------------------------------------------------------------------------------------------
 2023-09-02 08:52:18                       ipv4       192.168.122.105/24   k8s-control   
 2023-09-02 09:02:41                       ipv4       192.168.122.49/24    k8s-2         
```

This command provides a list of DHCP leases on the `default` network. Look for the IP address associated with the hostname `k8s-control`.

### 3. Connect to `k8s-control`:

Use the found IP address to SSH into `k8s-control` using the user account you created during setup.

### 4. Update system packages:

```bash
sudo apt update && sudo apt upgrade
```

This ensures all packages are up to date.

### 5. Switch to the root account:

```bash
sudo -s
```

Some of the upcoming tasks require root privileges.

### 6. Update the `/etc/hosts` file:

Using the output from `virsh net-dhcp-leases default`, append the IP addresses and hostnames to the hosts file:

```bash
printf "\\n192.168.122.105 k8s-control\\n192.168.122.49 k8s-2\\n\\n" >> /etc/hosts
```

### 7. Confirm the updates:

```bash
cat /etc/hosts
```

Ensure that the added entries appear correctly.

### 8. Update kernel parameters for Kubernetes:

```bash
printf "overlay\\nbr_netfilter\\n" >> /etc/modules-load.d/containerd.conf
printf "net.bridge.bridge-nf-call-iptables = 1\\nnet.ipv4.ip_forward = 1\\nnet.bridge.bridge-nf-call-ip6tables = 1\\n" >> /etc/sysctl.d/99-kubernetes-cri.conf
```

The commands you provided update the kernel parameters for Kubernetes networking. This is done by appending configuration options to two files: **`/etc/modules-load.d/containerd.conf`** and **`/etc/sysctl.d/99-kubernetes-cri.conf`**.

The first command appends the **`overlay`** and **`br_netfilter`** modules to the **`/etc/modules-load.d/containerd.conf`** file. This ensures that these modules are loaded at boot time, which is necessary for Kubernetes networking to function properly.

The second command appends several options to the **`/etc/sysctl.d/99-kubernetes-cri.conf`** file. These options configure the system to forward network packets and allow iptables to see bridged traffic. This is necessary for Kubernetes services to communicate with each other and with the outside world.

### 9. Apply the new kernel parameters:

```bash
sysctl --system
```

### 10. Download and install containerd:

Containerd is a lightweight container runtime that is used to manage containers. The following steps show how to download and install it on your system.

```bash
wget https://github.com/containerd/containerd/releases/download/v1.6.23/containerd-1.6.23-linux-amd64.tar.gz -P /tmp/
tar Cxzvf /usr/local /tmp/containerd-1.6.23-linux-amd64.tar.gz
wget https://raw.githubusercontent.com/containerd/containerd/main/containerd.service -P /etc/systemd/system/
```

First, the **`wget`** command is used to download the containerd release archive from GitHub. The **`-P`** option specifies the directory where the file should be saved, in this case **`/tmp/`**.

Next, the **`tar`** command is used to extract the contents of the archive to the **`/usr/local`** directory.

After that, another **`wget`** command is used to download the **`containerd.service`** file from GitHub. This file contains the systemd service definition for containerd, which allows it to be managed using systemd. The **`-P`** option specifies that the file should be saved in the **`/etc/systemd/system/`** directory.

Reload the systemd manager configuration:

```bash
systemctl daemon-reload
```

This ensures that systemd is aware of the new service definition.

Start and enable containerd:

```bash
systemctl enable --now containerd
```

This starts the containerd service and configures it to start automatically at boot time.

### 12. Install required utilities:

The commands you provided show how to fetch and install **`runc`** and CNI (Container Network Interface) plugins, which are required utilities for setting up a Kubernetes cluster.

```bash
wget https://github.com/opencontainers/runc/releases/download/v1.1.9/runc.amd64 -P /tmp/
install -m 755 /tmp/runc.amd64 /usr/local/sbin/runc
wget https://github.com/containernetworking/plugins/releases/download/v1.3.0/cni-plugins-linux-amd64-v1.3.0.tgz -P /tmp/
```

First, the **`wget`** command is used to download the **`runc`** binary from GitHub.

Next, the **`install`** command is used to copy the **`runc`** binary to the **`/usr/local/sbin`** directory and set its permissions to **`755`**. This makes it executable by all users on the system.

After that, another **`wget`** command is used to download the CNI plugins archive from GitHub. The **`-P`** option specifies that the file should be saved in the **`/tmp/`** directory.

Once these steps are completed, you need to extract the CNI plugins using the **`tar`** command.

```bash
mkdir -p /opt/cni/bin
tar Cxzvf /opt/cni/bin /tmp/cni-plugins-linux-amd64-v1.3.0.tgz
```

In this case, the files are extracted to the **`/opt/cni/bin`** directory.

### 13. Configure containerd:

Generate a default configuration and adjust the `SystemdCgroup` setting:

```bash
mkdir -p /etc/containerd
containerd config default | tee /etc/containerd/config.toml
vi /etc/containerd/config.toml
```

First, the **`mkdir`** command is used to create the **`/etc/containerd`** directory if it does not already exist. This is where the containerd configuration file will be stored.

Next, the **`containerd config default`** command is used to generate a default configuration for containerd. The output of this command is piped to the **`tee`** command, which writes it to the **`/etc/containerd/config.toml`** file.

```yaml
[plugins."io.containerd.grpc.v1.cri".containerd.runtimes]

        [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc]
          base_runtime_spec = ""
          cni_conf_dir = ""
          cni_max_conf_num = 0
          container_annotations = []
          pod_annotations = []
          privileged_without_host_devices = false
          runtime_engine = ""
          runtime_path = ""
          runtime_root = ""
          runtime_type = "io.containerd.runc.v2"

          [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
            BinaryName = ""
            CriuImagePath = ""
            CriuPath = ""
            CriuWorkPath = ""
            IoGid = 0
            IoUid = 0
            NoNewKeyring = false
            NoPivotRoot = false
            Root = ""
            ShimCgroup = ""
            SystemdCgroup = true # here

      [plugins."io.containerd.grpc.v1.cri".containerd.untrusted_workload_runtime]
        base_runtime_spec = ""
        cni_conf_dir = ""
        cni_max_conf_num = 0
        container_annotations = []
        pod_annotations = []
        privileged_without_host_devices = false
        runtime_engine = ""
        runtime_path = ""
        runtime_root = ""
        runtime_type = ""

        [plugins."io.containerd.grpc.v1.cri".containerd.untrusted_workload_runtime.options]
```

After that, the **`vi`** command is used to open the **`config.toml`** file in a text editor. Within the opened file, you need to locate the **`SystemdCgroup`** setting and change its value from **`false`** to **`true`**. This tells containerd to use systemd cgroups for managing containers. Once you have made this change, you can save and exit the file (by typing **`:wq`** in **`vi`**).

```bash
systemctl restart containerd
```

Once these steps are completed, you need to reload the containerd configuration using the **`systemctl restart containerd`** command. This restarts the containerd service and applies the new configuration.

### 14. Disable Swap:

Kubernetes recommends disabling swap for performance and reliability reasons. The commands you provided show how to do this on your system.

```bash
vi /etc/fstab
```

First, the **`vi`** command is used to open the **`/etc/fstab`** file in a text editor. This file contains the configuration for the filesystems that are mounted at boot time.

Within the opened file, you need to locate the swap entry and comment it out by prepending it with a **`#`** character. This prevents the swap partition from being mounted at boot time. Once you have made this change, you can save and exit the file (by typing **`:wq`** in **`vi`**).

```bash
swapoff -a
```

After that, you can use the **`swapoff -a`** command to disable swap immediately. This command turns off all swap devices and files on the system.

### 15. Install Kubernetes components:

The commands you provided show how to add the Kubernetes APT repository and install the required components on your system.

```bash
apt-get update
apt-get install -y apt-transport-https ca-certificates curl
curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-archive-keyring.gpg
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
apt update
```

First, the **`apt-get update`** command is used to update the package index. This ensures that your system is aware of the latest available packages.

Next, the **`apt-get install`** command is used to install several packages that are required to add the Kubernetes APT repository. These include **`apt-transport-https`**, **`ca-certificates`**, and **`curl`**.

After that, the **`curl`** command is used to download the Kubernetes APT repository’s GPG key. The **`-fsSL`** options tell **`curl`** to follow redirects, be silent, show errors, and output the content to stdout. The output of this command is piped to the **`gpg`** command, which is used to verify the key and save it to the **`/etc/apt/keyrings/kubernetes-archive-keyring.gpg`** file.

Once these steps are completed, you can add the Kubernetes APT repository to your system by echoing its definition to the **`/etc/apt/sources.list.d/kubernetes.list`** file. This tells your system where to find the Kubernetes packages.

After adding the repository, you need to update the package index again using the **`apt update`** command. This ensures that your system is aware of the new repository and its packages.

Once these steps are completed, you can reboot your system using the **`reboot`** command.

```bash
reboot
```

This applies all changes and ensures that your system is in a clean state.

```bash
sudo -s
apt-get install -y kubelet kubeadm kubectl
apt-mark hold kubelet kubeadm kubectl
```

After rebooting, you can SSH back into the master node and install Kubernetes using the **`apt-get install`** command.

This installs several packages, including **`kubelet`**, **`kubeadm`**, and **`kubectl`**. These are the core components of a Kubernetes cluster.

Finally, you can use the **`apt-mark hold`** command to prevent these packages from being automatically updated. This ensures that your Kubernetes cluster remains stable and consistent.

### 16. Initialize the Kubernetes master node:

The **`kubeadm init`** command is used to initialize the Kubernetes master node. This sets up the control plane components and prepares the node to manage a Kubernetes cluster.

```bash
kubeadm init --pod-network-cidr 10.10.0.0/16 --kubernetes-version 1.28.1 --node-name k8s-control
```

The **`--pod-network-cidr`** option specifies the IP address range for the pod network. In this case, it is set to **`10.10.0.0/16`**, which means that pods will be assigned IP addresses from this range.

The **`--kubernetes-version`** option specifies the version of Kubernetes to use. In this case, it is set to **`1.28.1`**, which means that version 1.28.1 of Kubernetes will be installed.

The **`--node-name`** option specifies the name of the node. In this case, it is set to **`k8s-control`**, which means that the node will be named **`k8s-control`**.

After running this command, you should follow the printed instructions to start using your cluster. This typically involves copying the **`kubeconfig`** file to your home directory and deploying a pod network.

After exiting from sudo, run the following commands:

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

Later, when joining a worker node, enter this: (NOTE: not in master node!!)

```bash
kubeadm join 192.168.122.105:6443 --token ujgg0d.wdyv7k0ljl1bjmhe \
--discovery-token-ca-cert-hash sha256:a31655bee1b0d2baf2538137bc670df7686029fe5901854cbf5cb989f0bd9f2b
```

### 17. Setup the pod network:

Kubernetes requires a pod network to enable communication between pods. In this case, we will use Calico as the pod network.

```bash
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/tigera-operator.yaml
wget https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/custom-resources.yaml
```

First, the **`kubectl create`** command is used to create the necessary resources for Calico by applying the **`tigera-operator.yaml`** manifest file from the Calico GitHub repository.

Next, the **`wget`** command is used to download the **`custom-resources.yaml`** file from the Calico GitHub repository. This file contains the configuration for Calico, including the IP address range for the pod network.

Adjust the `cidr` setting:

```bash
vi custom-resources.yaml
```

After downloading the file, you need to adjust the **`cidr`** setting by opening the **`custom-resources.yaml`** file in a text editor using the **`vi`** command. Within the opened file, locate the **`cidr`** entry and change its value to **`10.10.0.0/16`**. This specifies that pods will be assigned IP addresses from this range. Once you have made this change, save and exit the file (by typing **`:wq`** in **`vi`**).

Apply the Calico configuration:

```bash
kubectl create -f custom-resources.yaml
```

Finally, you can apply the Calico configuration by running the **`kubectl create -f custom-resources.yaml`** command. This creates the necessary resources for Calico and configures it to use the specified IP address range.