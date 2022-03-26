# lens_setup_with_rke
# Rancher Kubernetes Engine 
# Download rke from the below link.

 ```https://rancher.com/products/rke
    https://github.com/rancher/rke 
  ```
# Downlaod latest released version and type according to your system 

``` 
https://github.com/rancher/rke/releases/tag/v1.3.5
```

## For macbook user setup ssh key to server.
## most cases path is same for mac users.
## change your ip accordingly.
## This will copy your ssh keys to server and path of host ssh -->> to avoid etcd host error.
```cat ~/.ssh/id_ecdsa.pub | ssh ubuntu@192.168.5.67 "umask 077; test -d ~/.ssh || mkdir ~/.ssh ; cat >> ~/.ssh/authorized_keys"
 ```

##For install docker

```curl https://releases.rancher.com/install-docker/20.10.sh | sh
 ```
## provide docker usermod permission 
```sudo usermod -aG docker $USER
 ```

## go to folder where you download rke 

```rke config
 ```

#check your ssh path and change your ssh address according to server ip.

#Cluster Level SSH Private Key Path [~/.ssh/id_rsa]: 
#[+] Number of Hosts [1]: 
#[+] SSH Address of host (1) [none]: 192.168.5.67

## SSH Port of host (1) [22]: 
[+] SSH Private Key Path of host (192.168.5.67) [none]: 
[-] You have entered empty SSH key path, trying fetch from SSH key parameter
[+] SSH Private Key of host (192.168.5.67) [none]: 
[-] You have entered empty SSH key, defaulting to cluster level SSH key: ~/.ssh/id_rsa
[+] SSH User of host (192.168.5.67) [ubuntu]: 
[+] Is host (192.168.5.67) a Control Plane host (y/n)? [y]: 
[+] Is host (192.168.5.67) a Worker host (y/n)? [n]: y
[+] Is host (192.168.5.67) an etcd host (y/n)? [n]: y
[+] Override Hostname of host (192.168.5.67) [none]: 
[+] Internal IP of host (192.168.5.67) [none]: 
[+] Docker socket path on host (192.168.5.67) [/var/run/docker.sock]: 
[+] Network Plugin Type (flannel, calico, weave, canal, aci) [canal]: 
[+] Authentication Strategy [x509]: 
[+] Authorization Mode (rbac, none) [rbac]: 
[+] Kubernetes Docker image [rancher/hyperkube:v1.21.6-rancher1]: 
[+] Cluster domain [cluster.local]: 
[+] Service Cluster IP Range [10.43.0.0/16]: 
[+] Enable PodSecurityPolicy [n]: 
[+] Cluster Network CIDR [10.42.0.0/16]: 
[+] Cluster DNS Service IP [10.43.0.10]: 
[+] Add addon manifest URLs or YAML files [no]:



## config is ready 

## Go to the folder and run the engine
```rke up
 ```
## Now open lens and add this config to the workspace by + button
## open up terminal and use following command
```helm repo add openebs https://openebs.github.io/charts
   helm repo update
 ```

## Install openebs with chart name as openebs:
```helm upgrade -i openebs openebs/openebs \
  --create-namespace \
  --namespace openebs \
 ```

## set openebs-hostpath as default storage class

```kubectl patch storageclass openebs-hostpath \
  --patch='{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
 ```
## Install Metal LB:  

```kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.11.0/manifests/namespace.yaml
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.11.0/manifests/metallb.yaml
 ```
## Setup IP range. Giving only 1 IP which is of the node IP

```kubectl create -f - << EOF
apiVersion: v1
kind: ConfigMap
metadata:
  namespace: metallb-system
  name: config
data:
  config: |
    address-pools:
    - name: address-pool-1
      protocol: layer2
      addresses:
      - 192.168.5.67/32
EOF
```


