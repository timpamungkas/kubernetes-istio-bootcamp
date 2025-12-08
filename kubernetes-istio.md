- [Kubernetes](#kubernetes)
  - [Hello Kubernetes](#hello-kubernetes)
  - [Kubectl Command Cheat Sheet](#kubectl-command-cheat-sheet)
  - [Scaling Pod](#scaling-pod)
    - [Troubleshoot](#troubleshoot)
  - [Declarative Kubernetes](#declarative-kubernetes)
  - [Namespace](#namespace)
  - [Label](#label)
  - [Dashboard](#dashboard)
  - [Volume](#volume)
    - [Empty Dir](#empty-dir)
    - [Host Path](#host-path)
    - [Local](#local)
  - [Configmap](#configmap)
    - [Creating configmap from command line](#creating-configmap-from-command-line)
    - [Creating configmap from file](#creating-configmap-from-file)
  - [Secret](#secret)
    - [Creating secret from command line](#creating-secret-from-command-line)
    - [Creating secret from file](#creating-secret-from-file)
  - [Service](#service)
    - [NodePort](#nodeport)
- [Nginx Load Balancer](#nginx-load-balancer)
  - [Ingress Over TLS](#ingress-over-tls)
  - [Introducing Helm](#introducing-helm)
  - [Sealed Secret](#sealed-secret)
    - [Installing kubeseal](#installing-kubeseal)
    - [Syntax](#syntax)
  - [Resource Monitoring - Metrics Server](#resource-monitoring---metrics-server)
  - [Resource Monitoring - Kube Prometheus Stack](#resource-monitoring---kube-prometheus-stack)
  - [Monitoring Ingress Nginx](#monitoring-ingress-nginx)
  - [Ingress Nginx Combination](#ingress-nginx-combination)
    - [Official Documentation](#official-documentation)
  - [Autoscaling](#autoscaling)
  - [Stateful Set](#stateful-set)
  - [Stateful Set on Practice](#stateful-set-on-practice)
  - [Namespace Quota](#namespace-quota)
  - [Private Repository](#private-repository)
  - [Helm Spring Boot Rest API 01](#helm-spring-boot-rest-api-01)
  - [Helm Chartmuseum (Spring Boot REST API 02)](#helm-chartmuseum-spring-boot-rest-api-02)
  - [Helm Spring Boot Rest API 03](#helm-spring-boot-rest-api-03)
  - [Helm Github As Repository (Spring Boot REST API 03)](#helm-github-as-repository-spring-boot-rest-api-03)
  - [Multiple Helm Charts (Spring Boot REST API 04)](#multiple-helm-charts-spring-boot-rest-api-04)
  - [ArgoCD](#argocd)
  - [Tips ArgoCD](#tips-argocd)
  - [ArgoCD Sensitive Data](#argocd-sensitive-data)
  - [Reloader](#reloader)
  - [CRD (Custom Resource Definition)](#crd-custom-resource-definition)
  - [Cert Manager](#cert-manager)
- [East-West Traffic](#east-west-traffic)
  - [Istio : Kube-Prometheus](#istio--kube-prometheus)
- [Istio : Nginx](#istio--nginx)
  - [Istio : Basic](#istio--basic)
  - [Istio : Enable Sidecar Injection](#istio--enable-sidecar-injection)
  - [Istio : Kiali](#istio--kiali)
  - [Istio : Jaeger](#istio--jaeger)
  - [Istio : Open Telemetry](#istio--open-telemetry)
  - [Prometheus, Grafana, Istio](#prometheus-grafana-istio)
  - [Securing Kiali](#securing-kiali)
  - [Istio : Traffic Routing](#istio--traffic-routing)
  - [Istio : Nginx Combination](#istio--nginx-combination)
  - [Istio : Load Balancer](#istio--load-balancer)
  - [Istio : Canary Release](#istio--canary-release)
  - [Istio : Canary Release](#istio--canary-release-1)
  - [Istio : VirtualService](#istio--virtualservice)
  - [Istio : DestinationRule](#istio--destinationrule)
  - [Istio : Retry](#istio--retry)
  - [Istio : Circuit Breaker](#istio--circuit-breaker)
  - [Istio : Mutual TLS](#istio--mutual-tls)

---

# Kubernetes
## Hello Kubernetes

```bash
# Create deployment
kubectl create deployment my-nginx --image nginx:stable

# List deployments
kubectl get deployment
kubectl get deployments
kubectl get deploy

# Show documentation for 'get' command
kubectl get -h

# List pods 
kubectl get pod
kubectl get pods
kubectl get po

# Describe pod
kubectl describe pod [pod-name]

# Delete deployment (uncomment)
# kubectl delete deployment my-nginx

# Create service for nginx deployment
kubectl expose deployment my-nginx --type NodePort --port 80

# List service
kubectl get service
kubectl get services
kubectl get svc

# [Minikube only] Show exposed url
minikube service [service-name] --url
```
  
The `get` and `describe` can be used for many kubernetes resources.

```bash
# Get list of available kubernetes resources
kubectl api-resources

# Get list of object
kubectl get [resource-name-or-shortname]
kubectl get service
kubectl get svc
kubectl get deployment
kubectl get deploy

# Describe object
kubectl describe [resource-name-or-shortname] [object name]
kubectl describe service my-nginx
kubectl describe deployment my-nginx
kubectl describe pod my-nginx-someRandomNumber
```

## Kubectl Command Cheat Sheet
[Cheat sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)

## Scaling Pod

```bash
# Deploy your own image (from previous lecture)
kubectl create deployment my-devops-blue --image [your-docker-username]/devops-blue:2.0.0

# Or you can use public image from my docker
kubectl create deployment my-devops-blue --image timpamungkas/devops-blue:2.0.0

# Create service for nginx deployment
kubectl expose deployment my-devops-blue --type LoadBalancer --port 8111 --name my-devops-blue-lb

# Describe service
kubectl describe service my-devops-blue-lb

# Scale to 3 pod replicas
kubectl scale deployment my-devops-blue --replicas 3

# See detailed pod info
kubectl get pod -o wide

```

- Endpoint to see the pod API docs : *http://localhost:8111/devops/blue/swagger-ui.html*
- Endpoint to see the pod IP : *http://localhost:8111/devops/blue/api/hello*
  

### Troubleshoot
If you cannot access *http://localhost:8111/...* after one minute, try this: 

  - **Docker Desktop** : 
     + click on the Docker whale icon on taskbar and restart docker desktop
     + or simply restart your laptop
  - **Minikube** :
     + open new terminal window, and run `minikube tunnel`
     + back to first terminal, and run `kubectl get service`, see the `EXTERNAL-IP` field for devops-blue service
     + curl to *http://[external-ip]:8111/...*


## Declarative Kubernetes

  - [Kubernetes YAML configuration reference (API reference)](https://kubernetes.io/docs/reference/kubernetes-api/)
  - [Common labels](https://kubernetes.io/docs/concepts/overview/working-with-objects/common-labels/)

```bash
# apply configuration
kubectl apply -f path-to-config-file.yml

# delete configuration
kubectl delete -f path-to-config-file.yml
```

----

## Namespace
**This course uses `devops` as namespace. Please adjust the namespace (`-n`) parameter on `kubectl` into your own namespace if you use different namespace.**
  
----

## Label

```bash
# port forwarding
# kubectl port-forward [pod-name] [host-port]:[pod-port]

# get pod on namespace 'devops'
kubectl get pod -n devops

# forwarding one of the pod into host port 9111
# adjust the pod name
kubectl port-forward -n devops devops-port-forward-deployment-6d44c8c69d-kcvvj 9111:8111
```

## Dashboard

```bash
# Minikube addons list
minikube addons list

# K8s dashboard
minikube dashboard
```

## Volume

### Empty Dir

```bash
# Apply the yml
kubectl apply -f devops-volume-empty-dir.yml

# Find the pod name on namespace 'devops'
kubectl get pod -n devops

# Find container name on certain pod (equals to pod spec on declarative file)
kubectl get pods -n devops [pod-name] -o jsonpath='{.spec.containers[*].name}'

# Run bash to devops-blue container inside [pod-name]
kubectl exec -n devops [pod-name] -c devops-blue -ti -- /bin/sh

# Run bin/sh (alpine linux shell) to alpine-linux container inside [pod-name]
kubectl exec -n devops [pod-name] -c alpine-linux -ti -- /bin/sh

```

### Host Path

```bash
# SSH into minikube
minikube ssh

# Mount host directory to minikube, adjust according your own machine
# Directory must exists
# Example (Windows): minikube mount d://minikube-volume:/data/upload/minikube
minikube mount [directory-on-host]:/data/upload/minikube
```

### Local

```bash
# List nodes
kubectl get node

# Describe node (e.g. to see node's label)
kubectl describe node [node-name]

# Apply the yml
kubectl apply -f devops-volume-local.yml

# Find the pod name on namespace 'devops'
kubectl get pod -n devops

# Find container name on certain pod (equals to pod spec on declarative file)
kubectl get pods -n devops [pod-name] -o jsonpath='{.spec.containers[*].name}'

# Run bash to devops-blue container inside [pod-name]
kubectl exec -n devops [pod-name] -c devops-blue -ti bash

# Run bin/sh (alpine linux shell) to alpine-linux container inside [pod-name]
kubectl exec -n devops [pod-name] -c alpine-linux -ti /bin/sh

```

## Configmap

```bash
# List configmap
kubectl get configmap -n devops

# See configmap content (output as yaml)
kubectl get configmap -n devops -o yaml [configmap-name]

# See configmap content (output as json)
kubectl get configmap -n devops -o json [configmap-name]

# Edit configmap content
kubectl edit configmap -n devops [configmap-name]

# Restart deployment (re-create pod without downtime)
kubectl rollout restart deployment -n devops [deployment-name]
```

### Creating configmap from command line

```bash
# Use --from-literal [key]=[value]
# Or  --from-literal=[key]=[value]
kubectl create configmap [configmap-name] -n devops --from-literal key.literal.one="This is my value for first key" --from-literal key.literal.two="While this is the value for second key"
```

### Creating configmap from file

The filename will become configmap key, while file content become configmap value.

```bash
# Basic syntax
kubectl create configmap [configmap-name] -n devops --from-file=[path-to-file]

# From single file
kubectl create configmap configmap-file-single -n devops --from-file=configmap-source.yml

# From multiple files sample (each filename will become one key)
kubectl create configmap configmap-file-multi -n devops --from-file=configmap-source.json --from-file=configmap-source.properties --from-file=configmap-source.txt

# From binary (e.g. image), the content will base64-encoded on configmap 
kubectl create configmap configmap-file-binary -n devops --from-file=configmap-source.png

# From folder (each filename will become one key)
kubectl create configmap configmap-folder -n devops --from-file=configmap-sources

```

## Secret

```bash
# List secret
kubectl get secret -n devops

# See secret content (output as yaml)
kubectl get secret -n devops -o yaml [configmap-name]

# See secret content (output as json)
kubectl get secret -n devops -o json [configmap-name]

# Edit configmap content
kubectl edit secret -n devops [configmap-name]

# Restart deployment (re-create pod without downtime)
kubectl rollout restart deployment -n devops [deployment-name]
```
### Creating secret from command line

```bash
# Use --from-literal [key]=[value]
# Or  --from-literal=[key]=[value]
kubectl create secret generic [secret-name] -n devops --from-literal key.literal.one="This is my secret value for first key" --from-literal key.literal.two="While this is the secret  value for second key"
```

### Creating secret from file

The filename will become secret key, while file content become secret value.

```bash
# Basic syntax
kubectl create configmap [configmap-name] -n devops --from-file=[path-to-file]

# From single file
kubectl create configmap configmap-file-single -n devops --from-file=configmap-source.yml

# From multiple files sample (each filename will become one key)
kubectl create configmap configmap-file-multi -n devops --from-file=configmap-source.json --from-file=configmap-source.properties --from-file=configmap-source.txt

# From binary (e.g. image), the content will base64-encoded on configmap 
kubectl create configmap configmap-file-binary -n devops --from-file=configmap-source.png

# From folder (each filename will become one key)
kubectl create configmap configmap-folder -n devops --from-file=configmap-sources

```

## Service

### NodePort

```bash
# Expose nodeport on minikube
minikube service -n devops devops-blue-nodeport --url
```

# Nginx Load Balancer
[Nginx configuration](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/)
[Ingress annotations](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/)

```bash
# create blue pods
kubectl apply -f devops-ingress.yml

# create ingress
ingress-nginx-load-balancer.yml
```


## Ingress Over TLS

[Online self-signed SSL generator](https://regery.com/en/security/ssl-tools/self-signed-certificate-generator)
Or, use [OpenSSL](https://www.openssl.org/)

```bash
# Generate K8s secret for TLS certificate
kubectl create secret tls api-devops-local-cert --key [path-to-key-file] --cert [path-to-crt-file]
```

## Introducing Helm
  - [Artifact hub](https://artifacthub.io/)
  - [Ingress Nginx](https://kubernetes.github.io/ingress-nginx/deploy/#quick-start)

```bash
# Install nginx ingress controller using helm
helm upgrade --install ingress-nginx ingress-nginx --repo https://kubernetes.github.io/ingress-nginx --namespace ingress-nginx --create-namespace
```


## Sealed Secret

 Full documentation [here](https://github.com/bitnami-labs/sealed-secrets).

### Installing kubeseal
[Non Windows](https://github.com/bitnami-labs/sealed-secrets#installation)
[Windows](https://github.com/bitnami-labs/sealed-secrets/releases). Find `kubeseal` release. In windows, it might not updated, so you might want to follow [this link](https://github.com/bitnami-labs/sealed-secrets#installation-from-source) for up-to-date release. However, installing from source requires [go installation](https://go.dev/dl/).
To extract tar.gz file on Windows, use tools like [7zip](https://www.7-zip.org/)

### Syntax
 ```bash
# Install sealed secret via helm
helm upgrade --install sealed-secrets sealed-secrets --set-string fullnameOverride=sealed-secrets-controller --repo https://bitnami-labs.github.io/sealed-secrets --namespace kube-system

# Create secret, output it only at terminal (not upload to kubernetes cluster)
kubectl create secret generic my-secret -n devops -o yaml --dry-run=client --from-file [path to my-config-file.yml]

# Port forward the sealed secret service to port 8899
kubectl port-forward -n kube-system service/sealed-secrets-controller 8899:8080

# Download public certificate
kubeseal --controller-name=sealed-secrets-controller --controller-namespace=kube-system --fetch-cert > mycert.pem

# Seal the secret using kubeseal
kubeseal -flag < [path-to-input-secret-file] > [path-to-output-sealed-secret-file]

# Example, output is yml file format, using mycert.pem public certificate
kubeseal --cert mycert.pem -o yaml < my-secret-file.yml > my-sealed-secret-file.yml

# Apply the sealed secret
kubectl apply -f my-sealed-secret-file.yml
 ```


## Resource Monitoring - Metrics Server

```bash
# Delete minikube cluster
minikube delete

# Restart using workaround to enable resource monitor
minikube start --extra-config=kubelet.housekeeping-interval=10s

# Enable metrics server
minikube addons enable metrics-server

# Enable ingress
minikube addons enable ingress
```

## Resource Monitoring - Kube Prometheus Stack
**NOTE : Kube prometheus stack might not works on local kubernetes (like minikube)**

[Official documentation](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack)

```bash
# Enable metrics server
minikube addons enable metrics-server

# Enable ingress
minikube addons enable ingress

# Installing kube-prometheus-stack via helm, with additional parameters. 
# Run this in folder monitoring-kube-prometheus
helm upgrade --install my-kube-prometheus-stack --repo https://prometheus-community.github.io/helm-charts kube-prometheus-stack --namespace monitoring --create-namespace --values values-monitoring.yaml

# Tunneling
# Access via localhost/grafana    localhost/prometheus    localhost/alertmanager
minikube tunnel

# ----------------------------------------
# Alternative (expose via port-forward)
# Prometheus at localhost:9000
kubectl port-forward service/my-kube-prometheus-stack-prometheus -n monitoring 9090:9090

# Grafana at localhost:3000
kubectl port-forward service/my-kube-prometheus-stack-grafana -n monitoring 3000:80

# Alert manager at localhost:9093
kubectl port-forward service/my-kube-prometheus-stack-alertmanager -n monitoring 9093:9093
```

## Monitoring Ingress Nginx

```bash
# Fresh start - delete minikube cluster
minikube delete

# Fresh start - restart using workaround to enable resource monitor
minikube start --extra-config=kubelet.housekeeping-interval=10s

# 1. Disable minikube ingress
minikube addons disable ingress

# 2. Enable metrics server
minikube addons enable metrics-server

# 3. Install nginx ingress controller using helm
helm upgrade --install ingress-nginx ingress-nginx --repo https://kubernetes.github.io/ingress-nginx --namespace ingress-nginx --create-namespace

# 4. Install kube-prometheus-stack using helm
helm upgrade --install my-kube-prometheus-stack --repo https://prometheus-community.github.io/helm-charts kube-prometheus-stack --namespace monitoring --create-namespace

# 5. Upgrade nginx installation. Run on folder monitoring-ingress-nginx
helm upgrade ingress-nginx ingress-nginx --repo https://kubernetes.github.io/ingress-nginx --namespace ingress-nginx --values values-ingress-nginx.yml

# Optional : Check that nginx configured
helm get values ingress-nginx --namespace ingress-nginx

# 6. Upgrade kube-prometheus installation. Run on folder monitoring-ingress-nginx
helm upgrade my-kube-prometheus-stack kube-prometheus-stack --repo https://prometheus-community.github.io/helm-charts --namespace monitoring --values values-kube-prometheus.yml

# Optional : Check that prometheus is configured
helm get values my-kube-prometheus-stack --namespace monitoring

# 7. Apply the deployment sample
kubectl apply -f devops-monitoring.yml

# 8. Tunneling
# Access via localhost/grafana    localhost/prometheus
minikube tunnel
```

## Ingress Nginx Combination

### Official Documentation
  - [Nginx ingress annotation](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations)
  - [Nginx ingress config map](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/configmap)
  - [Nginx variables](http://nginx.org/en/docs/varindex.html)

```bash
# Fresh start - delete minikube cluster
minikube delete

# Fresh start - restart using workaround to enable resource monitor
minikube start --extra-config=kubelet.housekeeping-interval=10s

# Generate K8s secret for TLS certificate
kubectl create secret tls api-devops-local-cert -n devops --key [path-to-key-file] --cert [path-to-crt-file]

# 1. Install nginx ingress controller using helm
helm upgrade --install my-ingress-nginx ingress-nginx --repo https://kubernetes.github.io/ingress-nginx --namespace ingress-nginx --create-namespace

# 2. Apply the deployment sample
kubectl apply -f ingress-combination-deployment.yml

# 3. Apply ingress
kubectl apply -f ingress-combination-ingress.yml

# 4. Tunneling
# Access via localhost/grafana    localhost/prometheus
minikube tunnel

# 5. Apply nginx config for request / response custom header
kubectl apply -f ingress-combination-nginx-configmap-headers.yml
kubectl apply -f ingress-combination-nginx-configmap-config.yml

# 6. restart nginx
kubectl rollout restart deployment -n ingress-nginx my-ingress-nginx-controller


# Monitoring - 1. Enable metrics server
minikube addons enable metrics-server

# Monitoring - 2. Install kube-prometheus-stack using helm
helm upgrade --install my-kube-prometheus-stack --repo https://prometheus-community.github.io/helm-charts kube-prometheus-stack --namespace monitoring --create-namespace

# Monitoring - 3. Upgrade nginx installation
helm upgrade my-ingress-nginx ingress-nginx --repo https://kubernetes.github.io/ingress-nginx --namespace ingress-nginx --values values-ingress-nginx.yml

# Monitoring - 4. Upgrade kube-prometheus installation
helm upgrade my-kube-prometheus-stack kube-prometheus-stack --repo https://prometheus-community.github.io/helm-charts --namespace monitoring --values values-kube-prometheus.yml
```

## Autoscaling

```bash
# Enable metrics server
minikube addons enable metrics-server

# Apply the deployment sample
kubectl apply -f devops-autoscaling.yml

# Apply Horizontal Pod Autoscaler (HPA)
kubectl apply -f devops-hpa.yml

# Examine HPA (migh take times for metric update)
kubectl get hpa -n devops
```

## Stateful Set

```bash
kubectl port-forward -n devops devops-stateful-set-main-0 9110:8111
kubectl port-forward -n devops devops-stateful-set-main-1 9111:8111
kubectl port-forward -n devops devops-stateful-set-main-2 9112:8111
```

## Stateful Set on Practice

```bash
helm upgrade --install my-rabbitmq rabbitmq --repo https://charts.bitnami.com/bitnami  --namespace rabbitmq --create-namespace --values values-rabbitmq.yml
```

The URL is **http://rabbitmq.local/rabbitmq/** (it has **/** at the end)

```bash
kubectl port-forward -n rabbitmq my-rabbitmq-0 9220:15672
kubectl port-forward -n rabbitmq my-rabbitmq-1 9221:15672
kubectl port-forward -n rabbitmq my-rabbitmq-2 9222:15672
```


## Namespace Quota
[ResourceQuota reference](https://kubernetes.io/docs/concepts/policy/resource-quotas/)


## Private Repository

```bash
# Create secret for dockerhub
kubectl create secret docker-registry dockerhub-secret --docker-server=https://index.docker.io/v1/ --docker-username=[your-username] --docker-password=[your-password] --docker-email=[your-email]
```

## Helm Spring Boot Rest API 01

[Helm template tricks & tips](https://helm.sh/docs/howto/charts_tips_and_tricks/)
[Helm template best practice](https://helm.sh/docs/howto/charts_tips_and_tricks/)
[Go template language](https://pkg.go.dev/text/template)

```bash
# Run these on folder helm-charts

# [Optional] Render helm template
helm template helm-yellow-01 spring-boot-rest-api --namespace devops --create-namespace --values ..\kubernetes\helm-spring-boot-rest-api-01\values-spring-boot.yml

# Install helm release
helm upgrade --install helm-yellow-01 spring-boot-rest-api --namespace devops --create-namespace --values ..\kubernetes\helm-spring-boot-rest-api-01\values-spring-boot.yml

# list helm
helm list -n devops
```


## Helm Chartmuseum (Spring Boot REST API 02)
 - [Artifact hub](https://artifacthub.io/packages/helm/chartmuseum/chartmuseum)
 - [Chartmuseum API List](https://github.com/helm/chartmuseum#api)

```bash
# Create chartmuseum username & password using secret
kubectl apply -f secret-chartmuseum.yml

# Install chartmuseum on helm
helm upgrade --install my-chartmuseum chartmuseum --repo https://chartmuseum.github.io/charts  --namespace chartmuseum --create-namespace --values values-chartmuseum.yml

# Package chart as tgz to be uploaded
helm package spring-boot-rest-api

# Create release spring-boot-rest-api from local chartmuseum, use chart version 0.1.0
helm upgrade --install helm-yellow-02 spring-boot-rest-api --repo http://chartmuseum.local/chartmuseum --username chartmuseum --password password --namespace devops --create-namespace --version 0.1.0 --values values-spring-boot.yml
```

## Helm Spring Boot Rest API 03

```bash
# Install helm release (values.yml + values-dev.yml)
helm upgrade --install helm-blue-03 spring-boot-rest-api --repo http://chartmuseum.local/chartmuseum --username chartmuseum --password password --namespace devops --create-namespace --version 0.1.0 --values values.yml --values values-dev.yml 

# Install helm release (values.yml + values-prod.yml)
helm upgrade --install helm-blue-03 spring-boot-rest-api --repo http://chartmuseum.local/chartmuseum --username chartmuseum --password password --namespace devops --create-namespace --version 0.1.0 --values values.yml --values values-prod.yml 
```


## Helm Github As Repository (Spring Boot REST API 03)

```bash
helm upgrade --install helm-blue-03-github spring-boot-rest-api --repo https://[username].github.io/[repository-name]/ --namespace devops --create-namespace --version 0.1.0 --values values.yml --values values-dev.yml 
```


## Multiple Helm Charts (Spring Boot REST API 04)

```bash
# Update chart dependencies (run on folder which contains Chart.yaml)
helm dependency update

# Run chart with dependencies (run on folder which contains values.yml and values-dev.yml)
helm upgrade --install helm-blue-04 chart-with-dependencies --namespace devops --create-namespace --values values.yml --values values-dev.yml 
```


## ArgoCD

```bash
# Install argocd
helm upgrade --install my-argocd argo-cd --repo https://argoproj.github.io/argo-helm --namespace argocd --create-namespace --values values-argocd.yml
```

[Create github personal access token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)


## Tips ArgoCD

[Notification](https://argocd-notifications.readthedocs.io)
[User management](https://argo-cd.readthedocs.io/en/stable/operator-manual/user-management)


## ArgoCD Sensitive Data

The yaml references available on folder `kubernetes-istio-docs/devops-app-deployment`

```bash
kubeseal --controller-name=sealed-secrets-controller --controller-namespace=kube-system --fetch-cert > mycert.pem

# Example, output is yml file format, using mycert.pem public certificate
kubeseal --cert mycert.pem -o yaml < devops-secret-plain.yml > devops-secret-sealed.yml
```


## Reloader

[Documentation](https://github.com/stakater/Reloader)

```bash
# Install reloader
helm upgrade --install my-reloader reloader --repo https://stakater.github.io/stakater-charts --namespace reloader --create-namespace

# Sealed secret certificate
kubeseal --controller-name=sealed-secrets-controller --controller-namespace=kube-system --fetch-cert > mycert.pem

# Seal the secret
kubeseal --cert mycert.pem -o yaml < devops-reloader-secret-plain.yml > devops-reloader-secret-sealed.yml

# Apply the sealed secret
kubectl apply -f devops-reloader-secret-sealed.yml

# Apply configmap
kubectl apply -f devops-reloader-configmap.yml

# Apply deployment
kubectl apply -f devops-reloader-deployment.yml
```


## CRD (Custom Resource Definition)

[ArgoCD CRD](https://argo-cd.readthedocs.io/en/stable/operator-manual/declarative-setup/)


## Cert Manager
```bash
helm upgrade --install cert-manager cert-manager --repo https://charts.jetstack.io --namespace cert-manager --create-namespace --set installCRDs="true"
```


# East-West Traffic

[Blue API documentation](http://api.devops.local/devops/blue/swagger-ui.html)


## Istio : Kube-Prometheus 

```bash
# Install kube-prometheus
helm upgrade --install kube-prometheus-stack --repo https://prometheus-community.github.io/helm-charts kube-prometheus-stack --namespace istio-system --create-namespace --values values-kube-prometheus.yml
```


# Istio : Nginx

```bash
# nginx
helm upgrade --install ingress-nginx ingress-nginx --repo https://kubernetes.github.io/ingress-nginx --namespace ingress-nginx --create-namespace --values values-ingress-nginx.yml
```


## Istio : Basic
[Istio homepage](https://istio.io/)
[Istio Installation guide](https://istio.io/latest/docs/setup/platform-setup/)

```bash
# Istio base & CRD
helm upgrade --install istio-base base --repo https://istio-release.storage.googleapis.com/charts --namespace istio-system --create-namespace

# Istio daemon (istiod)
helm upgrade --install istiod istiod --repo https://istio-release.storage.googleapis.com/charts --namespace istio-system --create-namespace

# Scrape istio metrics
kubectl apply -f istio-scrape.yml

# Deployment apply
kubectl apply -f devops-istio-basic-deployment-2.0.0.yml
```


## Istio : Enable Sidecar Injection

```bash
# add label to namespace
kubectl apply -f devops-istio-enable-sidecar.yml

# dummy pod
kubectl apply -f devops-istio-dummy-blue.yml

# restart existing deployment
kubectl rollout restart deployment -n devops

# remove dummy pod
kubectl delete -f devops-istio-dummy-blue.yml
```


## Istio : Kiali
[Kiali homepage](https://kiali.io/)
[Kiali yml configuration](https://kiali.io/docs/configuration/kialis.kiali.io/)

```bash
# Kiali Server
helm upgrade --install kiali-server kiali-server --repo https://kiali.org/helm-charts --namespace istio-system --create-namespace --values values-kiali-server.yml
```


## Istio : Jaeger

```bash
# Need to install certmanager
helm upgrade --install cert-manager cert-manager --repo https://charts.jetstack.io --namespace cert-manager --create-namespace --set installCRDs="true"

# create namespace
kubectl apply -f devops-istio-jaeger-namespace.yml

# Create jaeger operator
# Latest version, google with keyword : jaegertracing.io install kubernetes operator
# wait for operator to complete
kubectl apply -f https://github.com/jaegertracing/jaeger-operator/releases/download/v1.36.0/jaeger-operator.yaml -n observability

# Install simple jaeger
kubectl apply -f install-jaeger-simple.yml

# Update istiod, send 100% trace to jaeger
helm upgrade istiod istiod --repo https://istio-release.storage.googleapis.com/charts --namespace istio-system --values values-istiod-after-jaeger.yml

# apply deployment
kubectl apply -f devops-istio-basic-deployment-2.0.1.yml
```


## Istio : Open Telemetry

```bash
# Make sure cert-manager already installed
helm upgrade --install cert-manager cert-manager --repo https://charts.jetstack.io --namespace cert-manager --create-namespace --set installCRDs="true"

# Install open telemetry operator
kubectl apply -f https://github.com/open-telemetry/opentelemetry-operator/releases/latest/download/opentelemetry-operator.yaml

# Install open telemetry objects
kubectl apply -f devops-open-telemetry.yml

# Deploy application with open telemetry sidecar
kubectl apply -f devops-istio-basic-deployment-2.0.0-otel.yml
```

## Prometheus, Grafana, Istio

```bash
# Port forward application
kubectl port-forward -n devops [istio-otel-deployment-blue-pod-name] 8111:8111

# Port forward istio
kubectl port-forward -n devops [istio-otel-deployment-blue-pod-name] 15020:15020
```

URL for prometheus:
 - application : http://localhost:8111/devops/blue/actuator/prometheus
 - istio : http://localhost:15020/stats/prometheus

## Securing Kiali

```bash
# Create service account token
kubectl apply -f kiali-sa-token.yml

# Use token auth
helm upgrade --install kiali-server kiali-server --repo https://kiali.org/helm-charts --namespace istio-system --create-namespace --values values-kiali-server-secure.yml
```

## Istio : Traffic Routing
Clear all deployment (pod, service, ingress) on `devops` namespace, then apply below 

```bash
# 2.0.0
kubectl apply -f devops-istio-routing-2.0.0.yml

# 2.0.1 (additional pod)
kubectl apply -f devops-istio-routing-2.0.1.yml

# Service & Ingress
kubectl apply -f devops-istio-routing-service.yml
```


## Istio : Nginx Combination
 - Clear all deployment (pod, service, ingress) on `devops` namespace, then apply below.
 - Clear all istio objects (virtual service, destination rule)

```bash
# sidecar injection
kubectl apply -f devops-istio-nginx-namespace.yml

# restart ingress
kubectl rollout restart deployment -n ingress-nginx

# Deployment & Service
kubectl apply -f devops-istio-nginx.yml

# Ingress
kubectl apply -f devops-istio-nginx-ingress.yml
```


## Istio : Load Balancer

[Istio Destination Rule](https://istio.io/latest/docs/reference/config/networking/destination-rule/)

 - Clear all deployment (pod, service, ingress) on `devops` namespace, then apply below.
 - Clear all istio objects (virtual service, destination rule)

```bash
kubectl apply -f devops-istio-load-balancer.yml
```


## Istio : Canary Release
 - Clear all deployment (pod, service, ingress) on `devops` namespace, then apply below.
 - Clear all istio objects (virtual service, destination rule)

```bash
# 2.0.0
kubectl apply -f devops-istio-canary-2.0.0.yml

# 2.0.1 (new white pod)
kubectl apply -f devops-istio-canary-2.0.1.yml

# Service & Ingress
kubectl apply -f devops-istio-canary-service.yml
```


## Istio : Canary Release
 - Clear all deployment (pod, service, ingress) on `devops` namespace, then apply below.
 - Clear all istio objects (virtual service, destination rule)

```bash
kubectl apply -f devops-istio-dark-launching.yml
```


## Istio : VirtualService
[Istio VirtualService official documentation](https://istio.io/latest/docs/reference/config/networking/virtual-service)


## Istio : DestinationRule
[Istio DestinationRule official documentation](https://istio.io/latest/docs/reference/config/networking/destination-rule)


## Istio : Retry
Use this on `VirtualService.spec.retryOn` : `5xx,400,401,403,404,405,406,408,413,415,429,431` 


## Istio : Circuit Breaker


## Istio : Mutual TLS 

```bash
# Modify namespace (enable / disable sidecar) & apply the file
kubectl apply -f devops-istio-mtls-deployment.yml

# Modify tls mode and apply
kubectl apply -f devops-istio-mtls-mode.yml

# Reload deployment on namespaces after namespace modification
kubectl rollout restart deployment -n ingress-nginx

kubectl rollout restart deployment -n devops-blue

kubectl rollout restart deployment -n devops-yellow

kubectl rollout restart deployment -n devops-white
```


kubectl apply -f devops-istio-mtls-deployment.yml

kubectl apply -f devops-istio-mtls-mode.yml

kubectl rollout restart deployment -n ingress-nginx

kubectl rollout restart deployment -n devops-blue

kubectl rollout restart deployment -n devops-yellow

kubectl rollout restart deployment -n devops-white
