
![1698231190316](image/4-KubernetesArchitectureExplanationandClusterSetup/1698231190316.png)

**Master:**

1. API-Server :- Communicate With Cluster
2. Scheduler:- See the status of container(pods)
3. etcd:- Database Administrator - Information store in Documentation or Key:value Store
4. Controller Manager :- Check the Node satus working properlly or not
5. Kubectl:-

**Node / Worker Node:**

1. Kubelet :- Report to API Server, which work is currently running on Worker Node
2. Service Proxy:- We want to give the Access to outer User so that time use of service proxy(Communication Between Outer User to Master Node)

**CNI Network :(Container Network interface)**

* Communication Between Master Node with Worker Node

# 1) Both Master & Worker Node

# using 'sudo su' is not a good practice.

```
sudo apt update
```

```
sudo apt-get install -y apt-transport-https ca-certificates curl
```

```
sudo apt install docker.io -y
```

```
sudo systemctl enable --now docker # enable and start in single command.
```

# Adding GPG keys.

```
curl -fsSL "https://packages.cloud.google.com/apt/doc/apt-key.gpg" | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/kubernetes-archive-keyring.gpg
```

# Add the repository to the sourcelist.

echo 'deb https://packages.cloud.google.com/apt kubernetes-xenial main' | sudo tee /etc/apt/sources.list.d/kubernetes.list

```
sudo apt update
```

```
 sudo apt install kubeadm=1.20.0-00 kubectl=1.20.0-00 kubelet=1.20.0-00 -y
```

* Kubeadm - Cluster setup It is tool we can use to inisitally a control plane & Join our worker node to control plane
* kubectl - Give the work (Head)
* kubelet - Go to node and check the status

## Master Node

1. Initialize the Kubernetes master node. & Create Control Panel(Master Node)

   ```
   kubeadm init --ignore-preflight-errors=Swap,SystemVerification
   swapoff -a
   sudo systemctl daemon-reload
   sudo systemctl restart docker
   kubeadm init --v=5

   ```

   2. Set up local kubeconfig (both for root user and normal user):

   To start using your cluster, you need to run the following as a regular user:

```
mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown (id -u):(id−u):(id -g) $HOME/.kube/config
```

3. Apply Weave network for the the use of communication between Master to Node (CNI):

```
kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml
```

4. Generate a token for worker nodes to join:

```
sudo kubeadm token create --print-join-command
```
