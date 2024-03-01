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
    Added the application ai-planet-demo to the project ai-planet-demo
    Challenge: Unable to run amd64 version on mac m2 requiring arm64 image,
        Fix:- added platform: arm64 to github actions main.yml file on build and push docker image step
    application ai-planet-demo with image version ai-planet-demo:1.0.2 running succesfully 
        AI Planet version 1

Task 3: Implementing a Canary Release with Argo Rollouts
    
    3.1 Define a rollout strategy
        Modified deployment.yaml
            First Rollout
            changed from Deployment type to Rollout
            added canary strategy to deployment file with 1 minute interval between each step
                strategy:
                  canary:
                  steps:
                  - setWeight: 20
                  - pause: {}
                  - setWeight: 40
                  - pause: {duration: 1}
                  - setWeight: 60
                  - pause: {duration: 1}
            Using exisiting version for first deployment ai-planet-demo:1.2
            Push to repository to main branch and trigger github actions 
            At the same time Wait for argocd to detect the change in manifest files and redeploy the rollout

            Run command to inspect rollout
            kubectl argo rollouts get rollout ai-planet-demo-rollout -n ai-planet-demo --watch
            Rollout successfully deployed for first time without waiting or approval from operator
    3.2 Trigger a rollout
            Second Rollout
            Make modification to we app change to AI Planet Version 2
            Change Github actions file to build image version ai-planet-demo:1.3

            Modify Rollout Deployment file to use image version ai-planet-demo:1.3
            Push repository to main branch and trigger github actions automatically 
            At the same time Wait for argocd to detect the change in manifest files and deploy revision2 of the rollout automatically

            First only set to 20% trafic and waits for operator approval
            Approve and promote the rollout 
            kubectl argo rollouts promote ai-planet-demo-rollout -n ai-planet-demo 
            then:
            40% -> 1 minute pause -> 60% -> 1 minute pause -> 100% rollout
    3.3 Monitor the Rollout
        


Task 4:
