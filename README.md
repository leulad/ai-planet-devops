# ai-planet-devops

Setup A kubernetes cluster on mac m2 
Selected minikube
minikube start // using docker driver

Task 1: Setup and Configuration

    1.1 Create a Github repository
        Created Repository: https://github.com/leulad/ai-planet-devops

    1.2 Install ArgoCD
    create argocd namespace
        kubectl create namespace argocd
    Install ArgoCD using standard installation
        kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

    1.3 Install Argo Rollouts
    create argo-rollouts namespace
        kubectl create namespace argo-rollouts
    Install using standard installation method
        kubectl apply -n argo-rollouts -f https://github.com/argoproj/argo-rollouts/releases/latest/download/install.yaml
    Install kubectl plugin
        brew install argoproj/tap/kubectl-argo-rollouts

Task 2: Creating the GitOps Pipeline

    2.1 Dockerize the application
    Created sample node js webapp v1
    Added github actions to build docker image and push to docker hub located at lulad7/ai-planet-demo
    github actions workflow file located at .github/workflows/main.yml

    2.2 Deploy the application using ArgoCD
    Created namespace ai-planet-demo 
        kubectl create ns ai-planet-demo
    Created Deployment.yaml and Service.yaml - NodePort inside deployment folder
    Change default admin password    
        argocd account update-password
    Created a project on argocd named ai-planet-demo
        Added https://github.com/leulad/ai-planet-devops.git as a scoped repository inside the project
    Created and connected the Repository 
    


Task 3: Implementing a Canary Release with Argo Rollouts


Task 4:
