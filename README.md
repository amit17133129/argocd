# ArgoCD integration with Kubernetes Cluster | ArgoCD | K8S | GitHub | Git

<p align="center">
  <img width="1000" height="600" src="https://miro.medium.com/max/3840/1*0ut11LfOtm24EqTKacg2Nw.gif">
</p>

Hello guys, hope you all learning and implementing each day. I am back with another awesome devops tool. Yes it’s ArgoCD. I ope this rticle will help you to understand ArgoCD in a better way and to setup your ArgoCD in our local cluster.

## Take away from this rticle:

1. Setting a minikube single node cluster on aws.
2. Installing ArgoCD in their own namespace.
3. Setting up the GitHub repository url in project of ArgoCD.
4. Connecting ArgoCD to single node kubernetes cluster.
5. Creating Deployments using the manifests file from github and syncing the app.
6. Going through the interactive representation of kubernetes resources by ArgoCD.
7. Sync Policies (Manual Automatic)

## What is ARGOCD ?

ArgoCD is a GitOps tool that helps with your GitOps workflows. ArgoCD can be used as a standalone tool or as a part of your CI/CD workflow. ArgoCD works with Git as a source off truth, with current Kubernetes manifests, or with Helm charts.

## Why ARGOCD ?

Argo CD is a tool which will read your environment configuration (written either as a helm chart, kustomize files, jsonnet or plain yaml files) from your git repository and apply it to your Kubernetes namespaces. Some of the features of Argo CD are: support for helm, kustomize and jsonnet application declarations.

## 𝗔𝗿𝗴𝗼 𝗖𝗗 𝗶𝘀 𝗮 𝗱𝗲𝗰𝗹𝗮𝗿𝗮𝘁𝗶𝘃𝗲, 𝗚𝗶𝘁𝗢𝗽𝘀 𝗰𝗼𝗻𝘁𝗶𝗻𝘂𝗼𝘂𝘀 𝗱𝗲𝗹𝗶𝘃𝗲𝗿𝘆 𝘁𝗼𝗼𝗹 𝗳𝗼𝗿 𝗞𝘂𝗯𝗲𝗿𝗻𝗲𝘁𝗲𝘀.

ArgoCD is a declarative GitOps tool built to deploy applications to Kubernetes. It is purpose-built to deploy applications to Kubernetes so it doesn’t have the UI overhead of many tools built to deploy to multiple locations.

Argo CD automates the deployment of the desired application states in the specified target environments. Application deployments can track updates to branches, tags, or pinned to a specific version of manifests at a Git commit. See tracking strategies for additional details about the different tracking strategies available.
So, lets start by setting the minikube cluster in aws.

## Installing Minikube on Ubuntu — 20:04

Minikube is a lightweight Kubernetes implementation that creates a VM on your local machine and deploys a simple cluster containing only one node. … The Minikube CLI provides basic bootstrapping operations for working with your cluster, including start, stop, status, and delete.

<p align="center">
  <img width="800" height="160" src="https://miro.medium.com/max/700/1*W7AyyjBA-GAccj_H4pCgLA.png">
</p>

```
# minikube installation link
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/
```

## Installing Kubectl:

The Kubernetes command-line tool, kubectl, allows you to run commands against Kubernetes clusters. You can use kubectl to deploy applications, inspect and manage cluster resources, and view logs. Now we can install kubectl command using below command. Kubectl will helps us to control kubernetes cluster.

<p align="center">
  <img width="800" height="160" src="https://miro.medium.com/max/700/1*4OpMJ7yYlhcQC9ZGThBVQA.png">
</p>

```
## # Kubectl installation link
curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl#making kubectl executable
chmod +x ./kubectl# #moving ./kubectl to /usr/local/bin/kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
```

## Installing docker 🐋🐋:

As kubernetes will use docker as CRI(Container Runtime interface), so we have to install docker.

```
Installing docker 🐋🐋:

As kubernetes will use docker as CRI(Container Runtime interface), so we have to install docker.
```

<p align="center">
  <img width="800" height="300" src="https://miro.medium.com/max/700/1*_7LWsd2uNLiljlezffD3zQ.png">
</p>

## Installing conntrack:

Conntrack is command line interface conntrack provides a more flexible interface to the connnection tracking system than /proc/net/ip_conntrack. With conntrack, you can show, delete and update the existing state entries; and you can also listen to flow events. conntrackd is the user-space connection tracking daemon.

```
# Installing conntrack
sudo apt install conntrack -y
```
<p align="center">
  <img width="800" height="250" src="https://miro.medium.com/max/700/1*njb9Dy7cOIBbcSCuiIT8Tg.png">
</p>

## Starting Minikube:

Now we have to install minikube with driver=none. As we are not running minikube on virtualbox/vmware so we will use the driver as none.

<p align="center">
  <img width="1000" height="600" src="https://github.com/amit17133129/argocd/blob/main/images/minikube-start.gif?raw=true">
</p>

```
# Staring minikube
minikube start --addons=ingress --cpus=2 --install-addons=true --kubernetes-version=stable --memory=6g
```

Now we have to check that all the pods are in the running state or not. you can use kubetl get pods -A to check all the pods running in kubernetes cluster.

## Installing ARGOCD:

Now we will install ArgoCD. Here i am using 2.0.0 version of ArgoCD. You can follow the below instructon in the gif and then you can install ArgoCD on your minikube single node cluster.


<p align="center">
  <img width="1000" height="600" src="https://github.com/amit17133129/argocd/blob/main/images/install-argocd.gif?raw=true">
</p>

As you find the pods are in the running state. Now we will move towards checking the service of resource.

<p align="center">
  <img width="1000" height="150" src="https://miro.medium.com/max/2000/1*DDfUySxoE0UItEGhQoxVqQ.png">
</p>

<p align="center">
  <img width="1000" height="600" src="https://github.com/amit17133129/argocd/blob/main/images/editClusterIP-to-NodePort.gif?raw=true">
</p>

As you can find in the above gif that argocd-server service is running on ClusterIP service. So we will edit the service file of argocd-server and change the service from ClusterIP to NodePort so we can access it via the public ip of the node.


<p align="center">
  <img width="1000" height="600" src="https://github.com/amit17133129/argocd/blob/main/images/opening-ArgoCD.gif?raw=true">
</p>

To login into ArgoCD take the exposed port of argocd-server service and using public ip address of the vm you can access ArgoCD login page. The default user is `admin` and password you will get by decrypting the sercret as shown in the above gif.

```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

Using the above code you can get the password of the admin user of ArgoCD. After login successfully you will get the application page. The application page will helps you to create applications.
Connecting Repository Using HTTPS and Creating Application In ArgoCD:

First we will head towards repository section and we will add the repository url of GitHub. I have already created GitHub_Repo. In this repository you will find a basic deployment application and the service manifest files as shown in the figure below.

<p align="center">
  <img width="1000" height="150" src="https://miro.medium.com/max/2000/1*CNcv7l3wZ8DSnGKMrxKXUw.gif">
</p>

This repo contains only deployment and service manifests file which helps us to deploy pods and service. I will add this repo url into the ArgoCD repositor section. After that i will create application as shown in the below gif.

While adding the repo url you also have to select the path. Here you have to add the folder name in which you have manifest files e.g.argocd-deployment Here i am using the default project but you can create your own project.

While creating application you have to add the following:

1. Application Name.
2. Project Name (default)
3. Sync Policy → Manual / Automatic
   Argo CD has the ability to automatically sync an application when it detects differences between the desired manifests in Git, and the live state in the cluster. A benefit of automatic sync is that CI/CD pipelines no longer need direct access to the Argo CD API server to perform the deployment. Instead, the pipeline makes a commit and push to the Git repository with the changes to the manifests in the tracking Git repo. For more detlails visit here.
4. Source → GitHub repo of your manifest files.
5. Path → (folder name in my case argocd-deployment).
6. Cluster URL → Kubernetes Cluster URL.
7. NameSpace → default (or create your own namespace manually).
8. Click On Create.

After creating your application it will look something like this on your dashboard. This means that your application is created successfully.

<p align="center">
  <img width="1000" height="200" src="https://miro.medium.com/max/700/1*mv01R1q3ZrQeanwmJk9f1w.png">
</p>

Now when you click on the app then you will go to a page where ArgoCD will create interactive representation of your kubernetes resources.

<p align="center">
  <img width="1000" height="100" src="https://miro.medium.com/max/700/1*m6odmzvSao6MOVsW1XNesg.png">
</p>

Now i will scale the deployemnt manually, as ArgoCD tracks each and every moment of the kubernetes cluster. So when the deployment is rescaled then it will automatically changes the representation of pods in the webui. You will find that these newly scaled pod is outof synced i.e., we haven’t commited to GitHUb manifests files so ArgoCd will sync with the chages ou made in manifest files from GitHub and will not sync when you chnage manually.

<p align="center">
  <img width="1000" height="600" src="https://github.com/amit17133129/argocd/blob/main/images/scale-deploy.gif?raw=true">
</p>

When you set automatic sync in sync policy while creating the pplication then when you commit in the github manifest files you will see that ArgoCD will automatically launches/destroy pods and other respective resources depends on what you have commited in github manifest file. But when you use manual syn policy then you have to click on sync after updating the resources in github manifest files.

<p align="center">
  <img width="1000" height="600" src="https://github.com/amit17133129/argocd/blob/main/images/sync-101-replicas.gif?raw=true">
</p>

Now you can see how argocd is more useful for kubernetes guys. I liked this tool. Comment your views. Hope you liked this article. please like and comment.

<p align="center">
  <img width="300" height="300" src="https://miro.medium.com/max/996/0*Zy1u5LzwPHuHRUN0.gif">
</p>
