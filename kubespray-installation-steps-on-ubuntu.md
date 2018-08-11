# Kubespray installation steps on Ubuntu

Installing OS updates and ansible

```
sudo apt-get update
sudo apt-get install software-properties-common
sudo apt-add-repository ppa:ansible/ansible
sudo apt-get update
sudo apt-get install ansible
```

Configuring ansible technical user

```
useradd -m -s /bin/bash kubeadmin
mkdir /home/kubeadmin/.ssh;chmod 700 /home/kubeadmin/.ssh;
chown -R kubeadmin:kubeadmin /home/kubeadmin
sed -i  /kubeadmin/d /etc/sudoers; echo "kubeadmin ALL=(ALL)      NOPASSWD: ALL"  >> /etc/sudoers
passwd kubeadmin
```

Generating ssh key and copying on each host

```
su - kubeadmin
ssh-keygen

ssh-copy-id <hostname>
```

Running ansible playbook to install Kubernetes cluster

```
su - kubeadmin
git clone https://github.com/kubernetes-incubator/kubespray.git
cd kubespray
cp -rfp inventory/sample inventory/mycluster

# configure hosts and all yamls

declare -a IPS=(195.201.37.52 195.201.126.6)
CONFIG_FILE=inventory/mycluster/hosts.ini python3 contrib/inventory_builder/inventory.py ${IPS[@]}

ansible-playbook -i inventory/mycluster/hosts.ini cluster.yml -b -v \
  --private-key=~/.ssh/id_rsa
```