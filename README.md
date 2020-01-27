# kuber-on-centos


*This repository is used to create ***Kubernetes Cluster*** using **10** simple steps on ***bare VM's**** 


## Table of Contents

* [What are the pre-requisites ?](#pre-requisites)
* [What are the VM's provisioned ?](#configuration)
* [How to deploy kubernetes cluster ?](#deploy)
* [How to access Kubernetes Dashboard ?](#dashboard)


<a id="pre-requisites"></a>
## What are the pre-requisites ?
* [Git](https://git-scm.com/downloads "Git")


<a id="configuration"></a>
## What are the VM's provisioned ?

***Note: We are not going to create any VM's during this process. User is expected to have VM's before proceeding with this repository***

*Below is the ***example configuration*** that we are going to refer ***through out this repository***.*

*Name*                 |*IP*             |*OS*     |
|----------------------|-----------------|---------|
*co-dsk-kube-master*   |*192.168.237.209*|*CentOS7*|
*co-kube1*             |*192.168.237.204*|*CentOS7*|
*co-kube2*             |*192.168.237.205*|*CentOS7*|
*co-kube3*             |*192.168.237.210*|*CentOS7*|


<a id="deploy"></a>

***Execute on all nodes***

## ***Step 1***

*To download and copy the repo to all nodes*

`yum install git -y`
`git clone https://github.com/rakeshbanik/kuber-on-centos7.git`
`chmod 0777 -R kuber-on-centos7`


## ***Step 2***

*Check hostnames on all nodes*

` nano /etc/hostname` 


## ***Step 3***

*Before executing the below script please don't forget to change the IP's and Host Names as per your requirements in the below script, for example:
192.168.237.209 co-dsk-kube-master
192.168.237.204 co-kube1
192.168.237.205 co-kube2
192.168.237.210 co-kube3*

`kuber-on-centos7/provisioning/prerequisites/install.sh`


## ***Step 4***

*Reboot all master/worker nodes*

`reboot`

***Execute the below steps 5 and 6 only on master node***
## ***Step 5***

`cd kuber-on-centos7/provisioning/vm-master`

`./install.sh`


## ***Step 6***

*Get the token and save it somewhere*

`kubeadm token create --print-join-command`


***Execute the below script on all worker nodes***

## ***Step 7***

*Before executing the ***below*** script please ***don't forget*** to change the ***kubeadm join !!!!*** in the script. We can get the ***token*** by running the ***Step 6*** command on* ***master node IP***  

  
`cd kuber-on-centos/provisioning/vm-worker`

`./install.sh`


***Execute the below script only on master node IP***

## ***Step 8***

*To install HELM*

`cd kuber-on-centos/provisioning/helm`

`./install.sh`


***Verify Kubernetes installation is success by executing below two commands to see all the nodes and pods.***

## ***Step 9***

`kubectl get nodes`

`kubectl get pods -o wide --all-namespaces`


## ***Step 10***
<a id="dashboard"></a>

## How to access Kubernetes Dashboard ?

*To install dashboard*

`kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0-rc2/aio/deploy/recommended.yaml`

*Execetue following command to start the dashboard process (need to be root user)*

`kubectl proxy  --accept-hosts='^*$'`

*Kubectl will make Dashboard available at*

 http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/

*To create and account and access the dashboard,*

`kubectl create serviceaccount dashboard -n default`

`kubectl create clusterrolebinding dashboard-admin -n default --clusterrole=cluster-admin --serviceaccount=default:dashboard`

`kubectl get secret $(kubectl get serviceaccount dashboard -o jsonpath="{.secrets[0].name}") -o jsonpath="{.data.token}" | base64 --decode`

**Copy the output, and select *Token* in browser and paste the token. then click Sign In**
