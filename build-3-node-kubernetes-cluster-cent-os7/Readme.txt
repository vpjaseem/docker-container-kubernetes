How to install Kubernetes on CentOS 7.

Step 1: Remoce Docker and Start New
- If you have already installed Docker, this step is optional. 
- I have installed Docker n my 3 CentOS Machines, I'm removing it and then installing new docker.

// Removes the Docker Package
[root@node-01 ~]# yum remove docker-ce docker-ce-cli containerd.io -y
//

// Deletes the Docker Images
[root@node-01 ~]# rm -rf /var/lib/docker
//

## Master Node Configuration

Step 2: Set Hostname and Update ETC host Names for the worker Nodes
- 
//
hostnamectl set-hostname master-node-01

cat <<EOF>> /etc/hosts
192.168.39.21 master-node-01
192.168.39.22 worker-node-01
192.168.39.23 worker-node-02
EOF
//

Step 2: Disable SElinux and Reboot

//
setenforce 0
sed -i --follow-symlinks 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
reboot
//

Step 3: Update Firewall Rules

//
firewall-cmd --permanent --add-port=6443/tcp
firewall-cmd --permanent --add-port=2379-2380/tcp
firewall-cmd --permanent --add-port=10250/tcp
firewall-cmd --permanent --add-port=10251/tcp
firewall-cmd --permanent --add-port=10252/tcp
firewall-cmd --permanent --add-port=10255/tcp
//

Step 4: Letting iptables see bridged traffic
- As a requirement for your Linux Node's iptables to correctly see bridged traffic, you should ensure 'bridge-nf-call-iptables' is set to 1
//
modprobe br_netfilter
echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables
//

Step 5: Setup the Kubernetes Repo

//
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF
//

Step 6: Install Kubeadm and Docker

//
yum update -y
yum install kubeadm docker -y

systemctl enable kubelet
sudo service kubelet start
sudo chkconfig kubelet on

systemctl enable docker
sudo service docker start
sudo chkconfig docker on
//

Step 7: Initialize Kubernetes Master and Setup Default User
Now we are ready to initialize kubernetes master, but before that you need to disable swap in order to run “kubeadm init“ command.

//
swapoff -a
kubeadm init
//

//
Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 192.168.39.21:6443 --token t86k11.tuco6eni9dwdy6rf \
    --discovery-token-ca-cert-hash sha256:e9c81edbdbad997250a7eb844c90e2ae39af1d24f4d07689142f5067338ee3d1 
[root@master-node-01 ~]# 
//

Step 8: 
//
mkdir -p $HOME/.kube
cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
chown $(id -u):$(id -g) $HOME/.kube/config


To use a sudo enabled user, run:

mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
//













































