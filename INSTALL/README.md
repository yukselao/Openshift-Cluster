# Openshift-Cluster 3.11

    3 master
    3 worker node
    1 load balancer
    1 dns server


**#create keygen and copy rsa key for all server**

    ssh-keygen
    for host in 10.57.165.60 \
             10.57.165.61 \
    		 10.57.165.62 \
    		 10.57.165.63 \
    		 10.57.165.64 \
    		 10.57.165.65 \
    		 10.57.165.66 \
             10.57.165.68; \
    do ssh-copy-id -i ~/.ssh/id_rsa.pub $host; \
    done

**####The current repository is added to all machines###**	

    cat > /etc/yum.repos.d/Openshift.repo <<EOF
    [openshift]
    name=CentOS-OpenShift
    baseurl=http://mirror.centos.org/centos/7/paas/x86_64/openshift-origin311/
    gpgcheck=0
    enabled=1
    EOF

    cat > /etc/yum.repos.d/Glusterfs.repo <<EOF
    [gluster6]
    name=Gluster 6 Repositoru
    baseurl=http://mirror.centos.org/centos/7/storage/$basearch/gluster-6/
    gpgcheck=0
    enabled=1
    EOF

    cat > /etc/yum.repos.d/centos.repo <<EOF
    [extras]
    name=CentOS-$releasever - Extras
    mirrorlist=http://mirrorlist.centos.org/?release=7&arch=
    $basearch&repo=extras
    baseurl=http://mirror.centos.org/centos/7/extras/$basearch/
    enabled=1
    gpgcheck=0
    gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    priority=1
    
    [base]
    name=CentOS-$releasever - Base
    mirrorlist=http://mirrorlist.centos.org/?release=7&arch=
    $basearch&repo=os
    baseurl=http://mirror.centos.org/centos/7/os/$basearch/
    enabled=1
    gpgcheck=0
    gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5
    priority=1
    
    #released updates
    [updates]
    name=CentOS-$releasever - Updates
    mirrorlist=http://mirrorlist.centos.org/?release=7&arch=
    $basearch&repo=updates
    baseurl=http://mirror.centos.org/centos/7/updates/$basearch/
    enabled=1
    gpgcheck=0
    gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    priority=1
    EOF

**#copy repostiry other machine with scp**

    scp /etc/yum.repos.d/centos.repo root@10.57.165.61:/etc/yum.repos.d/centos.repo
    scp /etc/yum.repos.d/centos.repo root@10.57.165.62:/etc/yum.repos.d/centos.repo
    scp /etc/yum.repos.d/centos.repo root@10.57.165.63:/etc/yum.repos.d/centos.repo
    scp /etc/yum.repos.d/centos.repo root@10.57.165.64:/etc/yum.repos.d/centos.repo
    scp /etc/yum.repos.d/centos.repo root@10.57.165.65:/etc/yum.repos.d/centos.repo
    scp /etc/yum.repos.d/centos.repo root@10.57.165.66:/etc/yum.repos.d/centos.repo
    
    scp /etc/yum.repos.d/Glusterfs.repo root@10.57.165.61:/etc/yum.repos.d/Glusterfs.repo
    scp /etc/yum.repos.d/Glusterfs.repo root@10.57.165.62:/etc/yum.repos.d/Glusterfs.repo
    scp /etc/yum.repos.d/Glusterfs.repo root@10.57.165.63:/etc/yum.repos.d/Glusterfs.repo
    scp /etc/yum.repos.d/Glusterfs.repo root@10.57.165.64:/etc/yum.repos.d/Glusterfs.repo
    scp /etc/yum.repos.d/Glusterfs.repo root@10.57.165.65:/etc/yum.repos.d/Glusterfs.repo
    scp /etc/yum.repos.d/Glusterfs.repo root@10.57.165.66:/etc/yum.repos.d/Glusterfs.repo
    
    scp /etc/yum.repos.d/Openshift.repo root@10.57.165.61:/etc/yum.repos.d/Openshift.repo
    scp /etc/yum.repos.d/Openshift.repo root@10.57.165.62:/etc/yum.repos.d/Openshift.repo
    scp /etc/yum.repos.d/Openshift.repo root@10.57.165.63:/etc/yum.repos.d/Openshift.repo
    scp /etc/yum.repos.d/Openshift.repo root@10.57.165.64:/etc/yum.repos.d/Openshift.repo
    scp /etc/yum.repos.d/Openshift.repo root@10.57.165.65:/etc/yum.repos.d/Openshift.repo
    scp /etc/yum.repos.d/Openshift.repo root@10.57.165.66:/etc/yum.repos.d/Openshift.repo

**#add computers name on hosts file**

    echo "10.57.165.60  labocms01 labocms01.lab.com" | tee --append /etc/hosts
    echo "10.57.165.61  labocms02 labocms02.lab.com" | tee --append /etc/hosts
    echo "10.57.165.68  labocms03 labocms03.lab.com" | tee --append /etc/hosts
    echo "10.57.165.62  labocnd01 labocnd01.lab.com" | tee --append /etc/hosts
    echo "10.57.165.63  labocnd02 labocnd02.lab.com" | tee --append /etc/hosts
    echo "10.57.165.64  labocnd03 labocnd03.lab.com" | tee --append /etc/hosts
    echo "10.57.165.65  laboclb01 laboclb01.lab.com" | tee --append /etc/hosts
    echo "10.57.165.66  labocdns01 labocdns01.lab.com" | tee --append /etc/hosts

**#update machine repository(all server)**

    yum clean all
    yum update -y
    reboot

**#install Openhift required components (all server)**

    yum install -y wget git zile nano net-tools docker-1.13.1 bind-utils iptables-services bridge-utils bash-completion kexec-tools sos psacct openssl-devel httpd-tools NetworkManager python-cryptography python2-pip python-devel python-passlib java-1.8.0-openjdk-headless "@Development Tools"
    yum -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
    sed -i -e "s/^enabled=1/enabled=0/" /etc/yum.repos.d/epel.repo
    systemctl start NetworkManager
    systemctl enable NetworkManager

**#add ansible repository for ansible-2.7.5 on master1 server**

    cat > /etc/yum.repos.d/ansible.repo <<EOF
    [Ansible]
    name=Ansible
    baseurl=https://releases.ansible.com/ansible/rpm/release/epel-7-x86_64/
    gpgcheck=0
    enabled=1
    EOF
**#update repository**

    yum clean all
    yum update -y

**#install ansible**

    yum -y --enablerepo=epel install ansible-2.7.5 pyOpenSSL
    git clone https://github.com/openshift/openshift-ansible.git
    cd openshift-ansible && git fetch && git checkout release-3.11 && cd ..

**#install atomic and glusterfs-fuse (all server)**

    yum install atomic glusterfs-fuse -y
    atomic host upgrade
    reboot

**#enable docker**

    systemctl stop docker
    rm -rf /var/lib/docker/*
    systemctl restart docker
    systemctl enable docker

**#DNS server information is added to the first line of resolv.conf (for all server)**

> install "DNS.txt"

    vi /etc/resolv.conf
    nameserver 10.57.165.66(dns server ip addres)

**#check dns server work (all server)**

    dig openshift-internal.lab.com
    dig oktay.savdi.apps.lab.com
    dig labocms02.lab.com

**#check origin versiyon**

    yum info origin	

**#add ansible file**

> ("ansible.txt")

    vi inventory.ini

**#check ansible work and result conf file**

    ansible -i inventory.ini all -m ping
    ansible -i inventory.ini all -m shell -a "cat /etc/hosts"
    ansible -i inventory.ini all -m shell -a "cat /etc/resolv.conf"	

**#check 	prerequisites.yml if its ok "take snopshot macine"** 

    ansible-playbook -i inventory.ini openshift-ansible/playbooks/prerequisites.yml

**#if its ok prerequisites.yml "take snopshot macine" and run** 

    deploy_cluster.yml
    ansible-playbook -i inventory.ini openshift-ansible/playbooks/deploy_cluster.yml

**##attention###**
if the process is stuck "**wait for control plane for pods to become ready**" -> the name cannot be resolved.

    vi /etc/resolv.conf (for All server)
    nameserver 10.57.165.66 (dns server ip addres)

**#check its work!**

    oc get nodes
    
**#Check Docker registery not emptydir!**

    oc get dc docker-registry -n default --export -o yaml | grep registry-storage -B 2
