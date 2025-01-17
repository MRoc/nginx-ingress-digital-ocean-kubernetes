# nginx-ingress-digital-ocean-kubernetes

Set up a kubernetes cluster with two echo services and an Nginx Ingress Controller on Digital Ocean. Based on https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nginx-ingress-with-cert-manager-on-digitalocean-kubernetes


## Prerequisite: Create a Kubernetes Cluster

- Create cluster with three nodes.
- Run `kubectl get nodes` to verify that the cluster is up and running.


## 1. Deploy two echo services

- Run `kubectl apply -f ./infra/k8s/echo1.yaml`
- Run `kubectl apply -f ./infra/k8s/echo2.yaml`
- Run `kubectl get pods` and verify 2x `echo1` and 1x `echo2` are running.
- Run `kubectl get svc` and verify an internal IPs.


## 2. Install Nginx Ingress Controller

- Install Nginx Ingress Controller resources by running: `kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.1.1/deploy/static/provider/do/deploy.yaml`. This will also deploy an external load balancer.
- Run `kubectl get pods -n ingress-nginx` and verify that the controller is running.
- Run `kubectl get svc --namespace=ingress-nginx` and verify the external IP.


## 3. Create Ingress Resources

- Run `kubectl apply -f ./infra/k8s/echo_ingress.yaml`
- Run `curl echo1.example.com` and verify the response.
- Run `curl echo2.example.com` and verify the response.


## 4. Cert-manager

- Run `kubectl apply -f https://github.com/jetstack/cert-manager/releases/download/v1.15.1/cert-manager.yaml`
- Run `kubectl get all --namespace cert-manager` to verify the pods got created
- Run `kubectl create -f ./infra/k8s/staging_issuer.yaml`
- Run `kubectl create -f ./infra/k8s/prod_issuer.yaml`


## 5. Configure access from outside

- Run `kubectl apply -f ./infra/k8s/ingress_nginx_svc.yaml`
- Run `kubectl apply -f ./infra/k8s/echo_ingress.yaml`
- Run `kubectl describe ingress` to see CreateCertificate
- Run `kubectl describe certificate` to see certificate


## How to debug

Source: https://medium.com/@ManagedKube/kubernetes-troubleshooting-ingress-and-services-traffic-flows-547ea867b120

- Check thew pod is running `kubectl get pods -o wide`.
- Check the log of a pod `kubectl logs <pod_name>`.
- Check list of services `kubectl get service`.
- Check service in particular `kubectl describe service <service_name>`. Endpoints must point to the correct pods. Check the IP is accessible from another pod within the cluster as the service is a ClusterIP.
- Check the ingress `kubectl get ingress`. Here the hosts should be listed.
- Check `kubectl describe ingress <ingress_name>`.
- Find ingress pod `kubectl get pods -n ingress-nginx`.
- Connect to ingress pod `kubectl exec -it <ingress_pod_name> -n ingress-nginx sh` and run `curl -H "HOST: echo1.example.com" localhost`.
- Check ingress logs `kubectl logs <ingress_pod_name> -n ingress-nginx`.


Source: https://kubernetes.github.io/ingress-nginx/troubleshooting/

- Check `kubectl get ing -n <namespace-of-ingress-resource>`
- `kubectl get pods -n <namespace-of-ingress-controller>`
- `kubectl exec -it -n <namespace-of-ingress-controller> <ingress_controller> -- cat /etc/nginx/nginx.conf`

## Follow-up

- https://www.asykim.com/blog/deep-dive-into-kubernetes-external-traffic-policies
- https://kubernetes.io/docs/tutorials/services/source-ip/#source-ip-for-services-with-type-loadbalancer