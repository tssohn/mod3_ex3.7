# mod3_ex3.7
mod 3 exercise 3.7a lab
Kubernetes Part 1 – Hands-On Activity

Overview

This exercise guides you through deploying a simple Nginx application on a shared Amazon EKS (Elastic Kubernetes Service) cluster. You will learn to connect to the cluster, organize workloads using namespaces, deploy applications, expose them externally via a LoadBalancer, and attach service accounts for access control.

⸻

Prerequisites

Before starting, ensure you have the following:
	•	✅ Access to an existing Amazon EKS cluster
	•	✅ AWS CLI installed and configured
	•	✅ kubectl installed and connected to your cluster

⸻

Step 1: Connect to the Shared EKS Cluster

The kubeconfig file defines how kubectl connects to and manages Kubernetes clusters, containing:
	•	Cluster Information: API server endpoint and credentials
	•	User Credentials: Authentication tokens or certificates
	•	Namespace and Contexts: Default context configuration
To authenticate and configure your connection:
    aws eks update-kubeconfig --name shared-eks-cluster --region <your-region>

Step 2: Create a Namespace

Namespaces help organize Kubernetes resources logically.
Create your namespace: kubectl create namespace <your-name>-eks-activity
Verify creation: kubectl get namespaces

Step 3: Deploy a Simple Nginx Application

Create a new file named nginx-deployment.yaml:
apiVersion: apps/v1
kind: Deployment
metadata:
  name: <your-name>-nginx-deployment
  namespace: <your-name>-eks-activity
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
        name: uploadsvc
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80

Apply the deployment: kubectl apply -f nginx-deployment.yaml
Check pod status: kubectl get pods -n <your-name>-eks-activity

Step 4: Expose Your Application with a Load Balancer Service

Create a new file named nginx-service.yaml:
apiVersion: v1
kind: Service
metadata:
  name: <your-name>-nginx-service
  namespace: <your-name>-eks-activity
spec:
  type: LoadBalancer
  selector:
    name: uploadsvc
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80

Apply the service configuration: kubectl apply -f nginx-service.yaml
Verify the service and external IP assignment: kubectl get svc -n <your-name>-eks-activity

Step 5: Add a Service Account

Create a new file named service-account.yaml:
apiVersion: v1
kind: ServiceAccount
metadata:
  name: <your-name>-nginx-service-account
  namespace: <your-name>-eks-activity
Apply the configuration: kubectl apply -f service-account.yaml
Update the deployment to use this service account by adding: spec:
    serviceAccountName: <your-name>-nginx-service-account

Then reapply the deployment: kubectl apply -f nginx-deployment.yaml

Confirm the deployment uses the correct service account: kubectl get deployment -o yaml -n <your-name>-eks-activity | grep serviceAccount

Step 6: Access the Application via Load Balancer DNS

Retrieve the external DNS name of your service: kubectl get svc -n <your-name>-eks-activity

Open the EXTERNAL-IP or DNS in a web browser to verify the Nginx welcome page is accessible.

Cleanup (Optional)

To remove all resources created for this activity: kubectl delete namespace <your-name>-eks-activity

Summary

In this activity, you:
	•	Connected to an EKS cluster using kubeconfig
	•	Created a dedicated namespace
	•	Deployed an Nginx application using a Deployment
	•	Exposed the application via a LoadBalancer Service
	•	Added and bound a Service Account
	•	Verified access through the public Load Balancer DNS