What is repository about ?
==========================
This repository holds the ansible automation script to configure Kubernetes cluster on EC2 machines and this can be used to test and deploy dockerized applications into kubernetes cluster.

What is Kubernetes ?
==================
[Kubernetes](https://kubernetes.io/) is an open-source system for automating deployment, scaling, and management of containerized applications in clustered environment.  

Features
========
* Automatic binpacking
* Self-healing
* Horizontal scaling
* Service discovery and load balancing ( TCP - layer 4 )
* Automated rollouts and rollbacks
* Batch execution

Kubernetes Architecture
=======================
![Alt text](https://cdn.thenewstack.io/media/2016/11/Chart_02_Kubernetes-Architecture.png "K8S Arch")

Who should use it ?
===================
If you are the one familiar with Docker and using it for a while and ready to jump in further and want to run your dockerized applications in production workload.

Prerequisites
=============
* Atleast two servers belongs to same subnet ( you can use EC2 instances - one for master and other for node ) and enable all traffic within security group.
* Instance Types
	* Master --> t2.large --> 2 core, 8GB RAM
	* Node --> t2.xlarge --> 4 core, 16GB RAM
* AMI ( ami-eeff82f8 ) should be used. 
* Install AWS cli on master node and make sure aws cli configured with credentials having ECR access to pull and push docker images. (if you are using ECR as Image registry)
* Ansible installed on local system where you will run this script.
* Instances ssh private keys available to access EC2 machines, keys will be used by ansible to access and configure K8s cluster on these instances.

Getting started 
===============
Before you start, make sure you have completed the checklist from prerequisites. 

* Clone the repository
```
-bash-4.2# https://github.com/nikitsrj/Kubernetes-Cluster-Ansible.git
Cloning into 'kubernetes-ansible-cluster-setup'...
remote: Counting objects: 75, done.
remote: Compressing objects: 100% (57/57), done.
remote: Total 75 (delta 23), reused 0 (delta 0)
Receiving objects: 100% (75/75), 16.88 KiB | 0 bytes/s, done.
Resolving deltas: 100% (23/23), done.
Checking connectivity... done.

-bash-4.2# cd Kubernetes-Cluster-Ansible
-bash-4.2# ls -la
total 44
drwxr-xr-x 5 root root 4096 Aug 17 06:05 .
drwxr-xr-x 3 root root 4096 Aug 17 06:05 ..
-rw-r--r-- 1 root root  124 Aug 17 06:05 ansible-playbook.yml
drwxr-xr-x 8 root root 4096 Aug 17 06:05 .git
-rw-r--r-- 1 root root   23 Aug 17 06:05 .gitignore
-rw-r--r-- 1 root root   25 Aug 17 06:05 hosts
drwxr-xr-x 3 root root 4096 Aug 17 06:05 K8SCONFIG
-rwxr-xr-x 1 root root 4329 Aug 17 06:05 mkcrt
-rw-r--r-- 1 root root 1333 Aug 17 06:05 README.md
drwxr-xr-x 4 root root 4096 Aug 17 06:05 roles
```

Edit hosts file and it should look like as follows 
```
[kubemaster]
<MASTERPUBLICIP> ansible_ssh_extra_args='-o StrictHostKeyChecking=no' ansible_connection=ssh ansible_ssh_user=root ansible_ssh_private_key_file=~/.ssh/<SSH-KEY>
[kubenode]
<NODEPUBLICIP> ansible_ssh_extra_args='-o StrictHostKeyChecking=no' ansible_connection=ssh ansible_ssh_user=root ansible_ssh_private_key_file=~/.ssh/<SSH-KEY>
```


Note:- Update MASTERPUBLICIP and NODEPUBLICIP and SSH-KEY entries in hosts file.
	
Now edit ansible-playbook.yml and update MASTERPRIVATEIP value and save it.

Run ansible playbook
====================
```
ansible-playbook -i hosts ansible-playbook.yml
```

If everything goes well, we can verify the cluster by following command.
```
[root@ip-172-28-0-21 ~]# kubectl cluster-info
Kubernetes master is running at http://localhost:8080
Heapster is running at http://localhost:8080/api/v1/proxy/namespaces/kube-system/services/heapster
kubernetes-dashboard is running at http://localhost:8080/api/v1/proxy/namespaces/kube-system/services/kubernetes-dashboard
Grafana is running at http://localhost:8080/api/v1/proxy/namespaces/kube-system/services/monitoring-grafana
InfluxDB is running at http://localhost:8080/api/v1/proxy/namespaces/kube-system/services/monitoring-influxdb
```

Access dashboard and grafana
============================
* [Kubernetes Dashboard] (http://masterpublicip:8080/ui)
* [Monitoring] (http://masterpublicip:8080/api/v1/proxy/namespaces/kube-system/services/monitoring-grafana/)


References
==========
[Kubernetes definition by RedHat](https://www.redhat.com/en/containers/what-is-kubernetes) 
[Kubernetes Basics](https://kubernetes.io/docs/tutorials/kubernetes-basics/)
