# AI Planet  DevOps Demo
All Changes with commits present in repo - read commit messages
## Initial steps:

Setup A kubernetes cluster on mac m2 Selected minikube \
`minikube start` - using docker driver

## Task 1: Setup and Configuration

### 1.1 Create a Github repository
Created Repository: https://github.com/leulad/ai-planet-devops

### 1.2 Install ArgoCD
- create argocd namespace \
    `kubectl create namespace argocd` \
- Install ArgoCD using standard installation  
        `kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml`

### 1.3 Install Argo Rollouts
- create argo-rollouts namespace \
    `kubectl create namespace argo-rollouts` 
- Install using standard installation method \
    `kubectl apply -n argo-rollouts -f https://github.com/argoproj/argo-rollouts/releases/latest/download/install.yaml` 
- Install kubectl plugin \
    `brew install argoproj/tap/kubectl-argo-rollouts`

## Task 2: Creating the GitOps Pipeline

### 2.1 Dockerize the application
- Created sample node js webapp v1 
- Create production grade Dockerfile
- Added github actions to build docker image and push to docker hub located at lulad7/ai-planet-demo 
- github actions workflow file located at .github/workflows/main.yml 

### 2.2 Deploy the application using ArgoCD
- Created namespace ai-planet-demo \
    `kubectl create ns ai-planet-demo` 
- Created Deployment.yaml and Service.yaml - NodePort inside deployment folder
- Change default admin password \
    `argocd account update-password`
- Created a project on argocd named ai-planet-demo \
- Added https://github.com/leulad/ai-planet-devops.git as a scoped repository inside the project
![alt text](./images-for-readme/2.2argo-repo.png)
- Created and connected the Repository 
- Added the application ai-planet-demo to the project ai-planet-demo
![alt text](./images-for-readme/2.2argo-create.png)
    #### Challenge: Unable to run amd64 version on mac m2 requiring arm64 image,
    - Fix:- added platform: arm64 to github actions main.yml file on build and push docker image step
- Application ai-planet-demo with image version ai-planet-demo:1.0.2 running succesfully AI Planet version 1
![alt text](./images-for-readme/2.2argo-ai-final.png)

## Task 3: Implementing a Canary Release with Argo Rollouts
    
### 3.1 Define a rollout strategy
#### Modified deployment.yaml
#### First Rollout
- changed from Deployment type to Rollout
- added canary strategy to deployment file with 1 minute interval between each step
    ```yaml
    strategy:
            canary:
            steps:
            - setWeight: 20
            - pause: {}
            - setWeight: 40
            - pause: {duration: 1}
            - setWeight: 60
            - pause: {duration: 1} 
    ```
- Using exisiting version for first deployment ai-planet-demo:1.2
- Push to repository to main branch and trigger github actions 
- At the same time Wait for argocd to detect the change in manifest files and redeploy the rollout
![alt text](./images-for-readme/3.1argo-final.png)
- Run command to inspect rollout \
            `kubectl argo rollouts get rollout ai-planet-demo-rollout -n ai-planet-demo --watch`
![alt text](./images-for-readme/3.1argo-ai-final.png)
- Rollout successfully deployed for first time without waiting or approval from operator
### 3.2 Trigger a rollout
#### Second Rollout
- Make modification to we app change to AI Planet Version 2
- Change Github actions file to build image version ai-planet-demo:1.3
- Modify Rollout Deployment file to use image version ai-planet-demo:1.3
- Push repository to main branch and trigger github actions automatically 
![alt text](./images-for-readme/action-final.png)
- At the same time Wait for argocd to detect the change in manifest files and deploy revision2 of the rollout automatically
![alt text](./images-for-readme/3.2argo-ai-middle.png)
### 3.3 Monitor the Rollout
Run command: \
    `kubectl argo rollouts get rollout ai-planet-demo-rollout -n ai-planet-demo --watch`
- First only set to 20% trafic and waits for operator approval
![alt text](./images-for-readme/3.2argo-middle.png)
- Approve and promote the rollout \
            `kubectl argo rollouts promote ai-planet-demo-rollout -n ai-planet-demo` \
            then:
            40% -> 1 minute pause -> 60% -> 1 minute pause -> 100% rollout
![alt text](./images-for-readme/argo-final-final.png)
        
## Task 4: Document and Cleanup
- Documentation Comleted alongside working on project
- Beautify Readme.md file
### 4.2 Clean Up
- Delete the application and project from ArgoCD
- Run command to uninstall argo rollouts and delete namespace \
    `kubectl delete -n argo-rollouts -f https://github.com/argoproj/argo-rollouts/releases/latest/download/install.yaml` \
    `kubectl delete ns argo-rollouts`
- Run command to uninstall argo rollout plugin
    `brew uninstall argoproj/tap/kubectl-argo-rollouts`
- Run command to uninstall ArgoCD and delete namespace \
    `kubectl delete -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml` \
    `kubectl delete ns argocd`
- Run command to delete ai-planet-demo namespace \
    `kubectl delete ns ai-planet-demo`
- Run command to delete minikube cluster profile \
    `minikube delete`
- Follow recommended procedures to disable Github Actions and Delete repository if required


