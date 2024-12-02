Documentation for Installing Kubecost and Integrating  Grafana 
overview
This document guides you through the installation and configuration of Kubecost, Grafana in a Kubernetes environment. These tools work together to monitor the costs of Kubernetes resources.
Table of Contents
1. Introduction
2. Prerequisites
3. Components Overview
    * Kubecost
    * Grafana
 Installation Steps
    * Step 1: Install Kubecost
    * Step 2: install Grafana 
1. Verification
2. Troubleshooting
3. Conclusion


1. Introduction
Kubecost is an open-source cost management platform for Kubernetes that provides visibility into how Kubernetes resources (pods, containers, etc.) consume resources and incur costs. By integrating Kubecost with and enhanced monitoring and cost tracking with high scalability.
This document provides a detailed, step-by-step guide on installing and configuring Kubecost, Mimir, and the OpenTelemetry Collector without using Helm.


Prerequisites:
Before you start the installation, ensure that you have the following prerequisites in place:
* A Running Kubernetes Cluster: You must have a running Kubernetes cluster, with a tool like kubectl to interact with it.
* kubectl: A command-line tool to interact with the Kubernetes cluster.
* A container registry like Docker Hub to pull container images Kubecost Requires Prometheus or Mimir: Kubecost requires either Prometheus or Mimir for storing metric data
       Helm installed

Components Overview

Kubecost
Kubecost helps monitor and manage Kubernetes costs. It aggregates data from Prometheus (or Mimir) and calculates resource usage costs based on cloud pricing models.
Main Features:
* Cost Allocation: Break down costs by namespaces, workloads, services, etc.
* Cost Optimization: Offers insights for cost-saving opportunities in your Kubernetes clusters.
* Integration: Can integrate with Prometheus or Mimir for metrics collection.

￼




 Grafana 
Grafana is an open-source visualization and analytics platform that allows users to query, visualize, alert, and explore their data, regardless of where it is stored. It supports a variety of data sources and is widely used for monitoring infrastructure, applications, and custom metrics.
		
 Visualization Dashboards:
* Create interactive, customizable dashboards with graphs, heatmaps, tables, and more.
* Use pre-built templates or design from scratch.
Multi-Source Support:
* Connect to a wide range of data sources, including Prometheus, Loki, Elasticsearch, InfluxDB, Graphite, OpenTelemetry, and more.
* Works with SQL databases like MySQL and PostgreSQL.


￼


Installation Steps:


Minikube (Local Kubernetes Cluster)
Minikube is a tool that creates a local Kubernetes cluster on your machine. It's great for development and testing.
1.Steps to set up Minikube:

“brew install minikube”


Start Minikube:

“minikube start”

This will download the necessary images and start a local Kubernetes cluster. By default, Minikube uses a Virtual Machine (VM) or Docker as the underlying provider.


2.Install kubectl:
* If you don’t already have kubectl, Minikube will automatically configure it, but you can also install it manually.


“brew install kubectl”


3. Install Kubecost
- [ ] To install Kubecost,you need to deploy it manually using k8s YAML files 
- [ ] 1.1. Create a Namespace for Kubecost:

“kubectl create namespace kubecost”


       1.2. Apply Kubecost Deployment YAML
       Download the Kubecost deployment files and apply them to your                     
       Kubernetes cluster:

kubectl apply -f  https://raw.githubusercontent.com/kubecost/cost-analyzer-helm-chart/develop/kubeost.yaml

This command will deploy all Kubecost components, including the Cost Analyzer, UI, and backend services.




Install Kubecost Using Helm

Add Kubecost Helm Repository:


“helm repo add kubecost https://kubecost.github.io/cost-analyzer/“
“helm repo update”

 helm --install kubecost kubecost/cost-analyzer -f values.yaml

1.3. Verify Kubecost Installation
You can verify if Kubecost is running by checking the pods in the kubecost namespace:
“kubectl get pods -n kubecost”

You should see pods such as kubecost-cost-analyzer, kubecost-ui, and others related to Kubecost running in the kubecost namespace.


Accessing Kubecost UI
After successfully installing Kubecost, you can access its UI (dashboard) to monitor your Kubernetes cost data and usage.

 1.4  Port Forwarding Kubecost UI
Since Kubecost typically runs inside a Kubernetes cluster, you will need to port-forward the Kubecost UI service to make it accessible from your local machine.

  1.5 Port Forwarding the Kubecost UI Service
Identify the Kubecost UI service by listing the services in the kubecost namespace: 
“kubectl get svc -n kubecost”
Look for the service called kubecost-cost-analyzer (or similar). The name may vary depending on your specific setup.

Port Forward the Kubecost UI service to a local port (e.g., 9090) so you can access it from your browser. Use the following command:

“kubectl port-forward -n kubecost svc/kubecost-cost-analyzer 9090:9090”


This command will forward port 9090 on your local machine to the Kubecost UI's port inside the cluster. If kubecost-cost-analyzer is the name of the service, this is what you should use. Otherwise, replace it with the correct service name.


Access Kubecost UI in the Browser:
Open your web browser and go to the following address:

“http://localhost:9090”




￼





Install Grafana for Dashboard Visualization
Grafana allows you to visualize Kubecost metrics and other data sources. You can connect it to both Mimir and OTEL-Collector.


  “helm repo add grafana https://grafana.github.io/helm-charts”
  “helm repo update “
   “helm install grafana grafana/grafana --namespace grafana --create-namespace”


Access Grafana: Port-forward Grafana to access it locally:

“kubectl port-forward --namespace grafana svc/grafana 3000:80”

[Go to http://localhost:3000. Default login is admin / admin, or as set in the Helm installation.]
 
Now add datasource Prometheus from the dashboard and add to get the metrics


Steps to Import and View a Dashboard
1. Ensure Prometheus is Configured Properly
    * Go to Configuration > Data Sources in Grafana.
    * Click on the Prometheus data source you added and click Save & Test to confirm it's working.
    * If it’s working, proceed to the next step.

 Obtain a Dashboard to Import

        * Use a prebuilt Grafana dashboard from Grafana's Dashboard Repository.
            * Example: Kubernetes dashboards, Node Exporter dashboards, or specific application dashboards.
        * Use a JSON file provided by the application or tool you’re monitoring. 



Import the Dashboard
* In Grafana:
    * Go to the "+" (Create) menu on the left sidebar and select Import.
    * You’ll see three options:
        1. Grafana.com Dashboard ID:
            * Enter the ID of a dashboard from Grafana's repository (e.g., 1860 for Kubernetes).
            * Click Load.



Troubleshooting
While setting up and integrating Kubecost, Mimir, and the OpenTelemetry Collector, you may encounter a few issues. Below are some common problems and how to resolve them.

General Troubleshooting Tips
Check Pod Status: Use kubectl get pods -n <namespace> to check the status of the deployed pods. Pods in the Pending state often indicate resource allocation issues or misconfigured deployments.

kubectl get pods -n kubecost
kubectl get pods -n Grafana



Pod Logs: Use kubectl logs to inspect the logs of a specific pod. This is especially useful for debugging application failures.
to check logs for the Kubecost pod:

“kubectl logs -n kubecost <kubecost-pod-name>”


To check logs for the OpenTelemetry Collector:

“kubectl logs -n observability <otel-collector-pod-name>”




Kubecost UI Not Accessible: If you're unable to access the Kubecost UI, ensure that the service is exposed correctly, and the port forwarding is working as expected.

Verify the service:
“kubectl get svc -n kubecost”

If using port-forwarding, ensure there are no firewall rules or issues with the local environment.

Kubecost Deployment Failed:
Inspect the deployment logs:

“kubectl logs deployment/kubecost -n kubecost”


Check the event logs for additional information on why the deployment might have failed:

kubectl get events -n kubecost




Conclusion
 you have successfully deployed and integrated Kubecost and the Grafana in your Kubernetes environment without using Helm. Here’s a summary of what has been accomplished:
1. Kubecost is now running in your Kubernetes cluster, providing you with detailed insights into the cost allocation and resource usage of your workloads.
2. Grafana:The Grafana supports a variety of data sources and is widely used for monitoring infrastructure, applications, and custom metrics. 
   
 With this setup, you now have a fully functioning observability and cost monitoring solution for your Kubernetes environment. These integrations provide powerful cost optimization, performance monitoring, and long-term metric storage to help you better manage your Kubernetes clusters.




Scripts to implement this process
 
Readme.md

#!/bin/bash

    echo "Welcome to the Kubecost and Grafana Setup!"
    echo "This script will help you deploy Kubecost and Grafana on a Minikube Kubernetes cluster."

    echo "Step 1: Starting Minikube (if not already running)..."
    minikube start

    echo "Step 2: Adding Helm repositories..."
    helm repo add kubecost https://kubecost.github.io/cost-analyzer/
    helm repo update

    echo "Step 4: Installing Grafana..."
    helm install grafana grafana/grafana --namespace grafana --create-namespace

    echo "Step 5: Installing Kubecost..."
    helm install kubecost kubecost/cost-analyzer --namespace kubecost --create-namespace

    echo "Step 6: Exposing Services..."
    kubectl expose deployment kubecost-cost-analyzer --type=NodePort --name=kubecost-service --namespace=kubecost
    kubectl expose deployment grafana --type=NodePort --name=grafana-service --namespace=grafana

    echo "Setup complete! Access the services using the following URLs:"
    echo "Grafana: $(minikube service grafana-service --url -n grafana)"
    echo "Kubecost: $(minikube service kubecost-service --url -n kubecost)"



Provision-test-pod.sh

#!/bin/bash

echo "Creating a test pod..."
kubectl apply -f pod-template.yaml
echo "Test pod provisioned!"






Pod-template.yaml

apiVersion: v1
kind: Pod
metadata:
  name: kubecost-grafana
  labels:
    app: kubecost-grafana
spec:
  containers:
  - name: kubecost
    image: kubecost/cost-model:latest
    ports:
    - containerPort: 9090
  - name: grafana
    image: grafana/grafana:latest
    ports:
    - containerPort: 3000


Validate.sh


#!/bin/bash

echo "Validating deployment..."

echo "Checking if Kubecost pods are running..."
kubectl get pods -n kubecost | grep Running

echo "Checking if Grafana pods are running..."
kubectl get pods -n grafana | grep Running

echo "Checking if Prometheus pods are running..."
kubectl get pods -n prometheus | grep Running

echo "Validation complete! If all pods are running, your setup is successful."





Deploy.sh


#!/bin/bash

# Display a message to let the user know the deployment is starting
echo "Starting the deployment..."

# Apply the Kubernetes configuration file to create the resources
kubectl apply -f pod-template.yaml

# Display a message to confirm that the deployment is complete
echo "Deployment is done! Your resources are created in Kubernetes."








