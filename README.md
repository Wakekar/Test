We’ll do 3 things:

1.  Kubernetes YAML (Deployment + Service)
2.  Updated Jenkinsfile to deploy automatically
3.  Instructions to make it work with Minikube or a Kubernetes cluster

Deployment runs 1 replica of your HTML app
Container listens on port 80
Service exposes it as NodePort 30080
On Minikube, you can access it via:
        -minikube service html-app-service
OR  
        -http://<node-ip>:30080

How to Make It Work
Pre-requisites:

1.  Jenkins user has docker access ✅ (done)
2.  Kubernetes CLI installed on Jenkins server:
       kubectl version --client
3.  Your cluster is reachable by Jenkins:
   Minikube: start minikube locally on Jenkins host:
          minikube start
Or any K8s cluster with proper kubeconfig at /var/lib/jenkins/.kube/config

Deployment:

*  Copy k8s-deployment.yaml to repo root
*  Run the pipeline → Jenkins will:

1.  Build & push Docker image
2.  Apply K8s deployment → pod + service will be created
    Access app at:
          http://<minikube-ip>:30080
          # or node-ip if using cloud instance
    
