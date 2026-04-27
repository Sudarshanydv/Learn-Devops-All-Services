KUBERNETES INSTALLATION (K8)
Saturday 15-02-2025
OLD COMMNADS 
Qubernetes Setup Step by Step -

K8 SETUP 
*********
LAUNCH INSTANCE - T2.SMALL AND SELECT AMI2 WHILE SELECT OS AMAZON LINUX 

Master Node SG - Add first before run command 
6443 → API Server (MOST IMPORTANT)
2379-2380 → etcd 
10250 → kubelet
10259 → scheduler
10257 → controller-manager
1] 	sudo su 
2]	sudo yum update -y && sudo yum upgrade -y  
3]	yum install docker -y 
4]	systemctl enable docker && systemctl start docker
    service docker status - Here check docker running mode or not
5]	sudo su -
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-\$basearch
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF

Check here this file text - nano /etc/yum.repos.d/kubernetes.repo

6]  nano  /etc/sysctl.d/k8s.conf - using this 1st create file
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

sysctl --system
setenforce 0
 
7] yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
8] systemctl enable kubelet && systemctl start kubelet
9] kubeadm init --ignore-preflight-errors=all
10] here created one token copy for use worker node 

kubeadm join 172.31.45.219:6443 --token u70356.z5eahunbz1sloioy \
        --discovery-token-ca-cert-hash sha256:6b298addbe1a316d7f0f672bf52943e50e68da586f05b573a6c48a6675c5bf98

ADD inbond rule PORT 6443, IP - IPv4 IN MASTER NODE SG
11] mkdir -p $HOME/.kube
12] sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
13] sudo chown $(id -u):$(id -g) $HOME/.kube/config
14] wget https://docs.projectcalico.org/v3.20/manifests/calico.yaml 
15] kubectl apply -f calico.yaml
16] hostnamectl set-hostname masternode
17] hostname 

THEN WORKER NODE 
Worker Node SG - add before run command
10250 → kubelet
30000-32767 → NodePort (for apps)

1] 	sudo su 
2]	sudo yum update -y && sudo yum upgrade -y  
3]	yum install docker -y 
4]	systemctl enable docker && systemctl start docker
	service docker status - check docker or running mode
5] 
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-\$basearch
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF

Check your file data - nano /etc/yum.repos.d/kubernetes.repo

6]  nano  /etc/sysctl.d/k8s.conf      = 1st paste this for open file
than paste this in file -
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

sysctl --system
setenforce 0

7] yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
8] systemctl enable kubelet && systemctl start kubelet

Add inbond rule in security group- Custome TCP
ADD PORT 6443 AND 10250, IPv4 IN WORKER NODE SG 

THEN PASTE JOIN TOKEN - created Worker node
when once it add the token than showing there - this line
Run ‘kubectl get nodes’ on the control-plane to see this node join the cluster 

Here go to master node and check there - your node active there

kubectl get nodes - after master node run this command 
Shows there - 
status 	ROLES - if the see like that’s means created successfuly
Ready	Control-Plane
Ready 	<none>

End Here all Q8 Installation

POD Is always in running mode its not possible to stop anytime
if u want to stop is only possible way to delete directly.

Kubernetes is an open-source container orchestration tool used to deploy, manage, and scale containerized applications automatically.
It helps DevOps engineers manage multiple containers across multiple servers from one place.

How Kubernetes Works (Step)
Developer creates container using Docker
Upload container image to Docker Hub or registry
Write Kubernetes YAML configuration file
Apply configuration using kubectl command
Kubernetes deploys application automatically on cluster servers
Create Docker image → push image to Docker Hub → write YAML file → run kubectl apply → application deployed automatically

Where Kubernetes is Used 
Deploy containerized applications – containers created using Docker
Auto scaling applications – scaling Pods automatically inside Kubernetes cluster
Load balancing applications – traffic distribution using Kubernetes Services
Application deployment on cloud servers – deployment on Amazon EC2 cluster nodes
Manage container networking – communication using Kubernetes Services and Ingress

eg. - If an application needs to run on 10 containers, Kubernetes automatically deploys and manages those containers instead of running them manually one by one.

In Project - In a project, if multiple Docker containers need to run on several EC2 servers, Kubernetes manages deployment, scaling, and monitoring automatically instead of manual container management.


Now here all work in MASTER Server

POD
Pod it is a smallest deployment unit.
Pod types 
Reguler - 
Replication controller 	- Autoscalling  
Replica set 			- Autoscalling
Demon Set 			- Monitoring 
Job 					- Backup
Crownjob 			- Schedule backup 

Create here new directory -  mkdir kubernetes, cd kubernetes
And create there one file - nano hello.yml

Manifest file write ex.
File name - hello.yml
apiVersion:   this verions use  - v1, apps/v1, batch/v1
kind: - Pod type
metadata: pod name (any other names)
spec: means specifications

apiVersion: v1
kind: Pod
metadata:
   name: mynewpod
spec:
   containers:
       - image: nginx
         name: mynewcont
         ports:
            - containerPort: 80
              hostPort: 80

save this - 
run this command - kubectl apply -f hello.yml
kubectl get pods - show created pod 
file end here.

For create a new pod -	kubectl apply -f hello.yml
Q8 chya aat janyasathi - 	kubectl exec -it mynewpod bash 
Check pods created list - 	kubectl get pods or pod or po  
For more details - 		kubectl describe pod mynewpod
Delete pod - 			kubectl delete pod mynewpod
Delete 2 pods - 			kubectl delete mynewpod mysecondpod
Q8 delete all pod (--all means all services) - kubectl delete all --all

Create Namespace
For checking namespace - kubectl get namespace or ns
For creating namespace - kubectl create ns webspace
for delte - kubectl delete namespace <ns-name> 

Namespace create using manifest file - namespace.yml

apiVersion: v1
kind: Namespace
metadata:
  name: customnamespace

exec apply file - kubectl apply -f namespace.yml
check created namespace - kubectl get ns
========================================================
create namespace with in appspace - 

apiVersion: v1
kind: Pod
metadata:
   name: applicationpod
   namespace: appspace
spec:
   containers:
       - image: nginx
         name: cont1
         ports:
            - containerPort: 80
              hostPort: 80 

kubectl create namespace appspace - Before applying this, run
apply file - kubectl apply -f hello.yml
for check appspace namespace - kubectl get pods -n appspace
========================================================
Namespace create 1st using manifest file - namespace.yml

apiVersion: v1
kind: Namespace
metadata:
  name: webspace

for webspace - create and add namespace using yml file

apiVersion: v1
kind: Pod
metadata:
   name: websitepod
   namespace: webspace
spec:
   containers:
       - image: nginx
         name: cont2
         ports:
            - containerPort: 80
              hostPort: 80

exec apply file - kubectl apply -f hello.yml
for check appspace namespace - kubectl get pods -n webspace
for delete all namespace - kubectl delete ns webspace  / appspace
====================================================
POD Lebeling
POD Lebeling - Use lebels like a using tag 
Create file - nano hello.yml

apiVersion: v1
kind: Pod
metadata:
   name: hellopod
   labels:
      website: hellopod
spec:
   containers:
       - image: nginx
         name: cont3
         ports:
            - containerPort: 80
              hostPort: 80

exec apply file - kubectl apply -f hello.yml
check labels pods -  kubectl get pod -l website
show pods with a labels - kubectl get pods --show-labels
========================================================

Without labal pod


apiVersion: v1
kind: Pod
metadata:
   name: mysecoundpod
	website: secondpod
spec:
   containers:
       - image: nginx
         name: cont5
         ports:
            - containerPort: 80

exec apply file - kubectl apply -f file2.yml
check pods - kubectl get pods
====================================================

POD kaym running mode mde rahil nhitr  
Tyala stop kinva start krta yet nhi; tyala delete kru shkto direct

22/02/2025 Saturday
Replication Controller

Definition
Replication Controller ensures a fixed number of Pods are always running in the cluster. If a Pod fails, it creates a new Pod automatically.

When to Use Replication Controller
Use when working with older Kubernetes clusters
Use when only simple label matching is required
Use when advanced deployment features are not needed

Is Replication Controller Good or Bad
Good for learning basics and old systems
Not recommended for modern production environments


Replication controller / set -

	Create file - replica.yml

apiVersion: v1
kind: ReplicationController
metadata:
  name: myreplica
spec:
  replicas: 5
  template:
    metadata:
      labels:
        replica: myreplica
    spec:
       containers:
         - image: nginx
           name: mycont1
           ports:
            - containerPort: 80
              hostPort: 80

Then - kubectl apply -f replica.yml
Create here - replicationcontroller
kubectl get rc - check replica created  
kubectl get pods -  created 5 pods 
kubectl scale rc myreplica --replicas=10 - for scaleup 10 replicas
kubectl scale rc myreplica --replicas=1 - for scaledown 1 replicas
kubectl delete pod hellopod - delete old pod
kubectl get pods - chek pods
kubectl exec -it <podname> bash - for enter/execute pod
kubectl exec -it myreplica-ws796 bash - like this
service nginx status - check nginx start or not
apt-get install nano -y - for install a nano command
and edit your index.html - edit file content there
yum update -y, yum install nano -y ,nano index.html
For use Deployment and check the result to searching your IP add
for deployment use this command 

kubectl describe rc myreplica - for more description
kubectl delete pod <pod1> <pod2> - for delete single single files
kubectl delete rc <myreplica> - for delete replication controller
then check - kubectl get rc & get pods
Replication Controller end here


Replica Set
Definition
ReplicaSet is an improved version of Replication Controller that ensures the required number of Pods are always running using advanced label selectors.

When to Use ReplicaSet
Use in modern Kubernetes environments
Use when managing Pods through Deployments
Use when advanced label selection is required
Use in production-level applications

Diffrence -
Replication Controller is old and basic. ReplicaSet is new, advanced, and recommended for modern Kubernetes usage.

Here new version start - apps/v1

Create file - nano job.yml

apiVersion: apps/v1
kind: ReplicaSet
metadata:
   name: myreplicaset
spec:
   replicas: 5
   selector:
      matchLabels:
         replicaset: pod
   template:
     metadata:
       labels:
          replicaset: pod
     spec:
       containers:
           - image: nginx
             name: cont1
             ports:
               - containerPort: 80
                 hostPort: 80

kubectl apply -f replicaset.yml - for apply file
kubectl get rs / replicaset - check desire sets are running
kubectl get pods - check created 5 pods here
kubectl scale rs myreplicaset --replicas=10 - for scaleup 10 replicas
kubectl scale rs myreplicaset --replicas=1 - for scaledown 1 replicas
kubectl delete pod secondpod - delete old pod
kubectl get pods - chek pods
kubectl exec -it <podname> bash - for enter/execute pod
kubectl exec -it myreplica-ws796 bash - like this
kubectl describe rs myreplicaset - for more description
kubectl delete pod <pod1> <pod2> - for delete single single
kubectl delete rs myreplicaset - for delete replication controller
then check - kubectl get rc & get pods

Saturday 01/03/2025
Here new session (JOB / CRONJOB)
JOB use for - Job (Run Task One Time)

A Job is used to run a task only once or a limited number of times until it completes successfully. It is mainly used for one-time operations such as running a database backup script once, processing files once, performing data migration, or generating a report one time.

Create here File - job.yml

apiVersion: batch/v1
kind: Job
metadata:
   name: myjobpod
spec:
   template:
      metadata:
         labels:
           myjoblabel: pods
      spec:
         restartPolicy: OnFailure
         containers:
           - image: luksa/batch-job
             name: myjobcontainer

kubectl apply -f job.yml - for create 
kubectl get job - for check jobs
kubectl delete job <jobname> - for delete job
then Create file  - cronjob.yml
Schedule backup do here…

CronJob (Run Task Repeatedly on Schedule)

A CronJob is used to run a task automatically at scheduled time intervals. It is mainly used for repeated operations such as running a daily database backup, sending weekly email reports, performing system cleanup every night, or taking log backups every hour automatically.

Create file - nano cj.yml

apiVersion: batch/v1
kind: CronJob
metadata:
  name: mycronjob
spec:
  schedule: "5 * * * *"
  jobTemplate:
    spec:
      template:
        metadata:
          labels:
            hello: pod
        spec:
          containers:
          - name: cont1
            image: busybox
          restartPolicy: Never

kubectl apply -f cronjob.yml - for create cronjob
kubectl get cronjob - for check cronjob
kubectl delete cronjob <name> -    for delete cronjob

Monitor for daemon set
 then Create file  - monitor.yml   for daemonset 

apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: monitoringpod
spec:
  selector:
     matchLabels:
       hello: pod
  template:
     metadata:
      labels:
         hello: pod
     spec:
         containers:
           - image: luksa/ssd-monitor
             name: mymonitorcontainer

kubectl apply -f monitor.yml - for apply
kubectl get ds - for check daemon set
kubectl get pods - for creating pod chek
kubectl delete ds monitoringpod - for delete ds

END HERE ALL PODS TYPES

Here Start SERVICES
1)	Cluster IP
2)	Node Port
3)	Loadbalancer
 There are three type of services available here…

Clusterip

Here 1st run this rc.yml file for creating 5 pods…

Create file - rc.yml   means replicationcontroller.yml

apiVersion: v1
kind: ReplicationController
metadata:
  name: myreplica
spec:
  replicas: 5
  template:
    metadata:
      labels:
        clusterip: pod
    spec:
       containers:
         - image: nginx
           name: mycont1
           ports:
            - containerPort: 80
              hostPort: 80

here create 5 pods thr rc.yml
kubectl apply -f rc.yml - for creating clusterip labels pods.
kubectl get rc - chek created 
kubectl get pods - chek creted 5 pod
kubectl describe pod <podname> - check add labels - change
Then here write - clusterip file - 

apiVersion: v1
kind: Service
metadata:
  name: myclusterservice
spec:
  type: ClusterIP
  ports:
    - port: 80
      targetPort: 80
  selector:
     clusterip: pod

kubectl apply -f clusterip.yml - for create clusterip
kubectl get service - for check clusterip 
kubectl delete all --all - for delete all pods thr using create rc
kubectl describe service myclusteripservice - for describe endpoint check your IPs, if he is running showing here. 
kubectl get pods - for check pods run or pending
kubectl exec -it myreplica-tscxq bash - execute and enter 
curl http://192.168.68.152:80 - use check your site run or not
if here your index.html file show here that’s mean site is run.
kubectl delete service <name> - for delete cluster service
kubectl delete rc myreplica - delete rc myreplica
kubectl delete service --all - delele all service

End Here ClusterIP

Nodeport

Nodeport.yml

Create here file - Nodeport.yml

apiVersion: v1
kind: Service
metadata:
  name: mynodeportservice
spec:
  type: NodePort
  ports:
    - port: 80
      targetPort: 80
  selector:
    clusterip: pod

kubectl apply -f nodeport.yml - for apply 
kubectl get service - for check service 


then go worker instance and add inbond rule to showing your terminal - eg. 80:30212/TCP
search google instance worker ip - http://15.206.127.217:31467
if here not show result then once delete and run again
kubectl delete service <servicename> - delete service 

END HERE NODEPORT

LOADBALANCER 

create file here - load.yml

apiVersion: v1
kind: Service
metadata:
  name: loadbalancerservice
spec:
  type: LoadBalancer
  ports:
    - port: 80
      targetPort: 80
  selector:
    clusterip: pod

kubectl apply -f load.yml - for apply
kubectl get service - for show loadbalancer
then go to ec2 instance and create loadbalancer
here open krte time target group me http ke bad terminal ka loadbalancer ka port no dalo http port - 80:32458 use 32458
then index.html and save.
Then copy dns name and search - if show your nginx page menas run well perfectly if not run then try again.
kubectl delete all --all - all delets

END LOADBALANCER

EMPTY DIRECTORY USE FOR ADD MOUNT

Create here file - emptydirectory.yml

apiVersion: v1
kind: Pod
metadata:
  name: emptypod
spec:
  containers:
    - image: nginx
      name: nginx
      ports:
        - containerPort: 80
      volumeMounts:
        - name: htmlfolderpath
          mountPath: /usr/share/nginx/html
  volumes:
    - name: htmlfolderpath
      emptyDir: {}

kubectl apply -f emptypod - for apply 
kubectl get pods - for showing pod
kubectl describe pod emptypod - ther mount option show ur folder
kubectl exec -it emptypod bash - for enter and chek 
cd /usr/share/nginx/html - check there showing all folders 



PERSISTANT VOLUME & PERSISTANT VOLUME CLAIM

Persistant Volume
create file here - pv.yml

apiVersion: v1
kind: PersistentVolume
metadata:
  name: pvvolume
  labels:
      volume: pv
spec:
  capacity:
    storage: 30Gi  # Use Gi instead of gb for proper Kubernetes storage units
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  nfs:
    server: fs-06c2c174dfbd981f4.efs.ap-south-1.amazonaws.com
    path: "/hello"  # create one folder on your kubernetes path

kubectl apply -f pv.yml - for apply 
kubectl get pv - for show volume 
kubectl delete pv <name> - for delete

PERSISTANT VOLUME CLAIM

create file here - pvclaim.yml

apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: myvolumeclaim
  labels:
    volume: pv
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
  selector:
    matchLabels:
      volume: pv

kubectl apply -f pvclaim.yml - for apply 
kubectl get pvc - for check created pvclaim
kubectl get pv - there showing bound new storage
kubectl delete pvc <name> - for delete
kubectl delete pv <name> - for delete
kubectl delete all --all - For delete all services 

***KUBERNETES END HERE***
