# Creating Cluster

## Create cluster with `kubeadm`

1. Check for ip address of your machine

```bash
# Get default ip route
ip route show default | awk '/default/ { print $3 }'

# Or you can manually check for the line that starts with "default via"
ip route show
```

> **NOTICE**: If two or more default routes are found, kubernetes will use the first one.

2. Initialize control plane node

```bash
sudo kubeadm init --pod-network-cidr=10.244.0.0/16 --apiserver-advertise-address=<control-plane-node-ip>
```

> **NOTICE**: Replace `<control-plane-node-ip>` with the ip address of your control plane node (the default ip route found in the previous step).
> The `--pod-network-cidr` flag is used to specify the IP address range for the pod network. This is necessary for the pod network plugin to work - 10.244.0.0/16 is the default for Flannel (We will discuss more about this in the next section).

3. Set config file to use kubectl

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

4. Taint control plane node

> **IMPORTANT**: If you don't do this, you will not be able to schedule any pods on the control plane node. (Included coreDNS, metrics-server, etc)

```bash
kubectl taint nodes --all node-role.kubernetes.io/control-plane-
```

5. Install a pod network plugin

First, check for coreDNS

```bash
kubectl get po -n kube-system -l k8s-app=kube-dns

# It will show 2 pods pending because you don't have a pod network plugin installed
NAME                                   READY   STATUS    RESTARTS   AGE
coredns-674b8bbfcf-csht9               0/1     Pending   0          3m55s
coredns-674b8bbfcf-rq6nd               0/1     Pending   0          3m55s
```

Then, install a pod network plugin. You can select any pod network [plugin](https://kubernetes.io/docs/concepts/cluster-administration/addons/#networking-and-network-policy) you want, but we will use Flannel in this tutorial.

```bash
kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml
```

After that, check for coreDNS again

```bash
kubectl get po -n kube-system -l k8s-app=kube-dns

# It will show 2 pods running
NAME                                   READY   STATUS    RESTARTS   AGE
coredns-674b8bbfcf-csht9               1/1     Running   0          7m59s
coredns-674b8bbfcf-rq6nd               1/1     Running   0          7m59s
```

## Troubleshooting

1. Your machine doesn't have static ip. This is a common issue when you are using wifi with dhcp or using forward port on router. To fix this, you can create a bridge interface with static ip.

```bash
# Add this in netplan
# /etc/netplan/01-netcfg.yaml
network:
  version: 2
  renderer: NetworkManager
  ethernets:
    # This is my wifi interface
    wlp4s0:
      dhcp4: true

  bridges:
    # This is my bridge interface
    k8s-br:
      dhcp4: false
      addresses:
        - 172.16.100.1/24
      parameters:
        stp: false
        forward-delay: 0

# Apply netplan
sudo netplan apply
```

Then you can create a new cluster by using `kubeadm init` again with bridge interface ip address

```bash
sudo kubeadm init --pod-network-cidr=10.244.0.0/16 --apiserver-advertise-address=172.16.100.1
```

2. Cgroups driver conflict

First, check for cgroup driver in both kubelet and containerd

```bash
# Check for cgroup driver in kubelet
sudo cat /var/lib/kubelet/config.yaml | grep cgroupDriver

# Check for cgroup driver in containerd
sudo cat /etc/containerd/config.toml | grep SystemdCgroup
```

If they are different, you need to set cgroup driver to systemd in both kubelet and containerd

```bash
# Set cgroup driver to systemd in kubelet
sudo sed -i 's/cgroupDriver: .*/cgroupDriver: systemd/' /var/lib/kubelet/config.yaml

# Set cgroup driver to systemd in containerd
sudo sed -i 's/SystemdCgroup = .*/SystemdCgroup = true/' /etc/containerd/config.toml

# Restart containerd and kubelet
sudo systemctl restart containerd
sudo systemctl restart kubelet
```

3. Pod CIDR not set or conflict

Assume that we have a scenario: You have already created a cluster without setting `--pod-network-cidr` flag, and now you want to set pod network CIDR without reinitializing the cluster with `kubeadm reset` and `kubeadm init`. This is how you can do it:

* First, edit the kubeadm ConfigMap:

```bash
kubectl -n kube-system edit configmap kubeadm-config

# Set the field networking.podSubnet to your desired pod network CIDR, in my case it is 10.244.0.0/16 (Flannel)
# Then it will look like this:
data:
  ClusterConfiguration: |
    ...
    networking:
      podSubnet: 10.244.0.0/16
```

* Second, edit the static pod manifest file for `kube-controller-manager`

```bash
sudo vim /etc/kubernetes/manifests/kube-controller-manager.yaml

# Add the following line to the command section
--cluster-cidr=10.244.0.0/16
```

* Third, edit the node configuration (if you have more than 1 node, you will need to do this for all nodes)

```bash
kubectl edit node <node-name>

# Add the podCIDR field to the node spec and it will look like this:
spec:
  ...
  podCIDR: 10.244.0.0/16
```

* And finally, delete all CNI pods to restart the pod network plugin

```bash
kubectl delete pod -n kube-flannel -l app=flannel
```

> After you have done all of this, now the pod network plugin will be restarted, coreDNS will be reloaded and the cluster will be ready to use. (Check if all pods are running)

## Completely remove kubernetes

```bash
# Reset kubeadm
yes | sudo kubeadm reset

# Remove cni and k8s configure
sudo rm -rf /etc/cni/net.d
rm $HOME/.kube/config

# Remove ip link interface
# If you are using Flannel or any other pod network plugin, you need to remove the ip link interface
sudo ip link del flannel.1
sudo ip link del cni0

# Kill port 6443 (k8s)
sudo fuser -k 6443/tcp
# Kill port 10259 (health check)
sudo fuser -k 10259/tcp
# Kill port 10257 (liveness check)
sudo fuser -k 10257/tcp

# Delete iptables related to k8s
sudo ipvsadm --clear

# Backup iptables
sudo iptables-save > /tmp/iptables-backup-$(date +%Y%m%d-%H%M%S%z)

# Flush all k8s iptable rules and chains
for table in filter nat mangle; do
  sudo iptables-save -t $table | grep -v KUBE- | sudo iptables-restore -T $table
done
