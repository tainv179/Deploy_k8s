# k8s with kubespray
1 master(ubuntu 20.04)
2 worker(ubuntu 20.04)
12/03/2024


#setup vm
#in vm master
sudo apt-get update -y
sudo apt install software-properties-common
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt-get update -y
sudo apt-get install git pip python3.9 -y

sudo -i
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
python3.9 get-pip.py
ssh-keygen -t rsa
ssh-copy-id {ip_addr}

#in both master and worker
sudo passwd root
....
sudo apt-get install openssh-server
sudo nano /etc/ssh/sshd_config
Add: PermitRootLogin yes
/etc/init.d/ssh restart

#Clone kubespray
git clone https://github.com/kubernetes-sigs/kubespray.git
cd kubespray/
pip3.9 install -r requirements.txt

# Copy ``inventory/sample`` as ``inventory/mycluster``
cp -rfp inventory/sample inventory/mycluster

# Update Ansible inventory file with inventory builder
declare -a IPS=({ip_addr1} {ip_addr2}......)
CONFIG_FILE=inventory/mycluster/hosts.yaml python3.9 contrib/inventory_builder/inventory.py ${IPS[@]}
sudo nano inventory/mycluster/hosts.yaml (to edit cluster)

#Create cluster
ansible-playbook -i inventory/mycluster/hosts.yaml  --become --become-user=root cluster.yml


#addons.yml
sudo nano inventory/sample/group_vars/k8s-cluster/addons.yml
  dashboard_enabled true
  metrics_server_enabled true
  ingress_nginx_enabled true


#Reset:   
ansible-playbook -i inventory/mycluster/hosts.yaml  --become --become-user=root reset.yml

  


