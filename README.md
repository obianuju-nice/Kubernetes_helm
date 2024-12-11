
PREQUISTE:

1. installl containerd or docker for the runtime engine and ensure any of the engines is up and running (systemctl status docker && systemctl start docker)..... GET THE INSTALLATION FROM DOCER DIRECTORY IN README.md

2. Install Kubernetes which pre installs kubectl

curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo kubernetes --version 

3. install helm ........ see commands below

curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null

sudo apt-get install apt-transport-https --yes

echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list

sudo apt-get update

sudo apt-get install helm



4. install minikube ........this is for your cluster in dev/test environment

sudo apt install -y curl wget apt-transport-https

sudo curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64

sudo install minikube-linux-amd64 /usr/local/bin/minikube

minikube version



4. Install KUbectl

curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl

chmod +x kubectl
kubectl --version

5. SET UP MINIKUBE CLUSTER

minikube start --driver=docker

kubectl get nodes



ERROR:    Exiting due to DRV_NOT_HEALTHY: Found driver(s) but none were healthy. See above for suggestions how to fix installed drivers.



sudo usermod -aG docker $USER

sudo chmod 666 /var/run/docker.sock

minikube start --driver=docker





6. INSTALLATION OF INGRESS-NGINX CONTROLLER ON THE MINIKUBE CLUSTER



install INGRESS-NGINX CONTROLLER 

helm upgrade --install ingress-nginx ingress-nginx \

--repo https://kubernetes.github.io/ingress-nginx \

--namespace ingress-nginx --create-namespace



NOTE:      sudo minikube addons enable ingress     ......USE THIS TO ADD INGRESS TO YOUR MININKUBE CLUSTER 





TLS CERTIFICATE SIGNINIG

To generate a self sined certificate for TLS certificate of your app then Run the below code in your bash terminal 



openssl req -x509 -nodes -days 365 -newkey rsa:2048 \

  -keyout nickos-tls.key -out nickos-tls.crt \

  -subj "/CN=https://Nickos.online O=https://Nickos.online"



This will generate two files:

- `tls.key` (the private key and send it to "nickos-tls.key")

- `tls.crt` (the certificate)





NOTE:  Helm installs resources or kss objets in the following order below: 

* Namespace, NetworkPolicy, ResourceQuota, LimitRange, PodSecurityPolicy, PodDisruptionBudget, ServiceAccount, Secret, SecretList, ConfigMap, StorageClass, PersistentVolume, PersistentVolumeClaim, CustomResourceDefinition, ClusterRole, ClusterRoleList, ClusterRoleBinding, ClusterRoleBindingList, Role, RoleList, RoleBinding, RoleBindingList, Service, DaemonSet, Pod, ReplicationController, ReplicaSet, Deployment, HorizontalPodAutoscaler, StatefulSet, Job, CronJob, Ingress, APIService





####    STEPS TO FOLLOW WHEN DEPLOYING  #### 

####  Proomethues deployment  ####

1. kubectl create namespace <namespaceName>

2. kubectl get namespaces

3. helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

4. helm repo update

5. helm install nice-prometheus prometheus-community/prometheus --namespace nickos -ns nickos 

nickos-prometheus   ........EX: helm install <releaseName> <chart> --namespace <yourNamespace> 

6. vim value.yml, service.yml and chart.yml to update these files

7. helm install <releaseName> <chartNsame> --set controller.publishService.enabled=true

####   2nd METHOD OF MANUALLY CREATING THE HELM    ####  ... creates 2 directories & 2 yml files

1. git clone the directory that you want to work on

2. kubectl create namespace <namespaceName>

3. helm create <chartName> Ex: helm create Nickosapp1

4. helm install <releaseName> <./chartName> Ex: helm install nice-app ./nickosapp1

5. helm install --debug --dry-run <releaseName> <./chartName>  ... just renders the template files for you see them on the console.

6. cd Nickosapp1 and view all the files and directories structure 

6. code .

7. update the files to remove the default nginx app according to your use case 

8. add, commit and push your code to github repo 





Helm commands:

helm version 

helm list .... Lists all the releases   ...... which are the installed charts

helm show values <chartName> ex: prometheus-community/prometheus

helm list --namespace <namesSpace> 

helm search repo <repoName>

helm get manifest <releaseName> -n <namespace>

helm edit <releaseName>  or kubectl edit svc

helm uninstall  <releaseName>     ex: helm uninstall nice-nginx 

helm inspect values …..... to see the helm current configuration files before installing the chart

helm template .......... gets the kubernetes resource definitions,

helm upgrade  <releaseName> <chart> .... upgrades a release to a new version 

helm rollback  <releaseName> <revision>  .... rolls back a release to an old version version

helm delete  <releaseName>    .... deletes a release name

helm search repo  <repoName>

helm repo add <repoName> <repo-url>

helm repo update 





k8s commands

kubectl version

kubectl cluster-info 

kubectl get nodes

kubectl get all

kubectl create namespaces team1

kubectl get namespace team1

kubectl delete namespace <namespaceName>

kubectl get pods -n <namespaceName>

kubectl get service <serviceName> -n <namespaceName>

kubectl describe service <serviceName> -n <namespaceName>

kubectl describe pod <podName> -n <namespaceName>

kubectl get pods -o wide

kubectl get pods -o yaml

kubectl get deploy,po,svc

kubectl describe deployment <deploymentName> ex:  uju-nginx-f4d47975b-4tmqh 

kubectl exec -it <podName> --bin/bash

kubectl port-forward service/serviceName <localport:pod-Port>    ...........To be used in the browser 

kubectl port-forward podName <localport:pod-Port>

kubectl scale --replicas=3 deployment/deploymentName

kubectl logs  <podName> -c <containerName>

kubectl rollout status deployment/nickos-app

kubectl rollout pause deployment/nickos-app

kubectl rollout resume deployment/nickos-app

kubectl rollout undo deployment/nickos-app

kubectl rollout history deployment/nickos-app 



kubectl get ingress -n team1

kubectl describe ingress ingress -n team1














<!-- ensure you have installed docker or containerd
install kubernetes 

sudo swapoff -a
sed -i '/ swap / s/^/#/' /etc/fstab
sudo apt update
sudo apt upgrade -y
sudo apt install -y apt-transport-https ca-certificates curl
curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt update
sudo apt install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl


install minikube or kind cluster for testing or EKS, AKS , GKE clusters
install helm  -->








