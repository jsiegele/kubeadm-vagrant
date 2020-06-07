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

# Source
https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/
https://github.com/sandervanvugt/cka

