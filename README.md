# Vagrant - Kubeadm

Vagrant Konfiguration zur erstellen eines Kubernetes Clusters,
basierend auf `kubeadm` 

# Umgebung

* Windows 10 + WSL (Ubuntu)
* Virtualbox 6.x.x
* Vagrant in der WSL

## Vagrant in der WSL
64-bit Debian Package von Vagrant (https://www.vagrantup.com/downloads.html)
In der WSL installieren:
```
sudo dpkg -i vagrant_t2.x.x_x86_64.deb
```
Umgebungsvariablen exportieren:
```
export VAGRANT_WSL_ENABLE_WINDOWS_ACCESS="1"
export PATH="$PATH:/mnt/c/Program\ Files/Oracle/VirtualBox:"       
```
Notwendigs Plugin installiert
```
vagrant plugin install vagrant-vbguest
```
Windows HyperV deaktivieren
```
bcdedit /set hypervisorlaunchtype off
```

# Kubernetes Cluster mittels `kubeadm` erstellen

## Virtuelle Images - master, node1 und node2 hochfahren
* vagrant up + Basis Provisionierung

## Master Node customizing

```
scp -rp -P 2222 /home/rpri265/bin /home/rpri265/.bashrc /home/rpri265/.vim vagrant@localhost:~/.
```

## Master Node provisionieren

* kubeadm init (root)
```
sudo kubeadm init --apiserver-advertise-address 192.168.26.10 --pod-network-cidr 192.168.0.0/16
```
* User - Konfig laut Anweisung des vorhergehenden Befehls übernehmen
```
  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
* Pod Network Plugin installieren
In diesem Fall Weave:
```
kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
```

Überprüfen:
```
[vagrant@master ~]$ kubectl get pods --all-namespaces
NAMESPACE     NAME                             READY   STATUS    RESTARTS   AGE
kube-system   coredns-66bff467f8-d8tvl         1/1     Running   0          7m51s
kube-system   coredns-66bff467f8-h4sh7         1/1     Running   0          7m51s
kube-system   etcd-master                      1/1     Running   0          7m56s
kube-system   kube-apiserver-master            1/1     Running   0          7m56s
kube-system   kube-controller-manager-master   1/1     Running   0          7m56s
kube-system   kube-proxy-n7xpn                 1/1     Running   0          7m50s
kube-system   kube-scheduler-master            1/1     Running   0          7m56s
kube-system   weave-net-bc559                  2/2     Running   0          61s
[vagrant@master ~]$ kubectl get nodes
NAME     STATUS   ROLES    AGE     VERSION
master   Ready    master   8m12s   v1.18.3
```
## Worker Nodes joinen
Beispiel:
```
sudo kubeadm join 192.168.26.10:6443 --token ty5p.1bsnr770u0mra \
    --discovery-token-ca-cert-hash sha256:cfd2226fbcc1b4d85be2a899d4eaf395769b1da065a2
```
# Source
https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/
https://github.com/sandervanvugt/cka
https://medium.com/@joatmon08/playing-with-kubeadm-in-vagrant-machines-36598b5e8408

