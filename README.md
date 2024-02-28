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

    2.2 Deploy the application using ArgoCD

Task 3: Implementing a Canary Release with Argo Rollouts


Task 4:
