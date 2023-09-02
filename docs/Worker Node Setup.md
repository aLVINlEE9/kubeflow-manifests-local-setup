## Worker Node Setup

Setting up a Worker Node is the process of adding nodes to your Kubernetes cluster. Part of this setup is similar to the Master Node configuration.

### 1. Start on the Worker Node.

All commands are executed in the terminal.

### 2. Follow up to step 15 from Master Node setup

Complete the basic configurations, such as installing the `kubeadm`, `kubelet`, and `kubectl` packages, up to step 15 of the Master Node setup.

### 3. Execute the `kubeadm join` command

Run the `kubeadm join` command that was provided as a result of executing `kubeadm init` on the Master Node. This command is displayed on the Master Node and is used to join from the Worker Node.

```bash
sudo kubeadm join 192.168.122.105:6443 --token ujgg0d.wdyv7k0ljl1bjmhe \\
--discovery-token-ca-cert-hash sha256:a31655bee1b0d2baf2538137bc670df7686029fe5901854cbf5cb989f0bd9f2b
```

Executing this command will add the Worker Node to the cluster.

### 4. Check the cluster status

SSH into the Master Node and run `kubectl get nodes` to verify the status of the cluster.

```bash
kubectl get nodes
```

The output should be:

```bash
NAME          STATUS   ROLES           AGE    VERSION
k8s-2         Ready    <none>          108s   v1.28.1
k8s-control   Ready    control-plane   127m   v1.28.1
```

If you see the output as above, the Worker Node has successfully joined the cluster.