# nginx-ingress-digital-ocean-kubernetes

https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nginx-ingress-with-cert-manager-on-digitalocean-kubernetes


## Prerequisite: Create a Kubernetes Cluster

- Create cluster with three nodes
- Run `kubectl get nodes` to verify that the cluster is up and running


## 1. Deploy two echo services

- Run `kubectl apply -f ./infra/k8s/echo1.yaml`
- Run `kubectl apply -f ./infra/k8s/echo2.yaml`
- Run `kubectl get pods` and verify 2x `echo1` and 1x `echo2` are running
- Run `kubectl get svc` and verify an internal IPs


## 2. Install Nginx Ingress Controller

- Install Nginx Ingress Controller resources by running: `kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.1.1/deploy/static/provider/do/deploy.yaml`. This will also deploy an external load balancer.
- Run `kubectl get pods -n ingress-nginx` and verify that the controller is running
- Run `kubectl get svc --namespace=ingress-nginx` and verify the external IP


## 3. Create Ingress Resources

- Run `kubectl apply -f ./infra/k8s/echo_ingress.yaml`


## Follow-up

- https://www.asykim.com/blog/deep-dive-into-kubernetes-external-traffic-policies
- https://kubernetes.io/docs/tutorials/services/source-ip/#source-ip-for-services-with-type-loadbalancer