# Install rancher on OKE cluster 

- DEV URL: https://10.247.7.9/
- PROD URL: https://10.247.7.18/

- username: developer
- Password: o9dOXT9A4N429YB0

### 1. Prerequisites
- You already have an OKE cluster up and running.
- You have kubectl configured to access your OKE cluster.
- Helm is installed locally.
- You have a namespace for Rancher (we’ll create one if needed).

### 2. Create a Namespace for Rancher
```
kubectl create namespace cattle-system
```

### 3. Add the Rancher Helm Chart Repository

```
helm repo add rancher-stable https://releases.rancher.com/server-charts/stable
helm repo update
```
### 4. Install Cert-Manager (Required by Rancher)
Rancher needs cert-manager for SSL certificate management.

```
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.15.1/cert-manager.yaml
```

- Wait until all pods in cert-manager namespace are Running:

```
kubectl get pods -n cert-manager
```

### 5. Install Rancher with OKE Internal  LoadBalancer
- Normally Rancher defaults to LoadBalancer service

```
helm install rancher rancher-stable/rancher \
  --namespace cattle-system \
  --set hostname=rancher.local \
  --set bootstrapPassword=admin \
  --set ingress.enabled=false \
  --set service.type=ClusterIP
```
### Parameters explained:

- --set hostname=rancher.local → Placeholder hostname (not used for ClusterIP testing).
- --set ingress.enabled=false → Disable ingress (we’ll use ClusterIP only).
- --set service.type=ClusterIP → Ensures no LoadBalancer is created.

### 6. Access Rancher via ClusterIP
Check Rancher service:

```
kubectl get svc -n cattle-system
```

### 7. Initial Login
 
- Default username: admin
- Password: admin

### 8. Verify LB Creation

```
kubectl get svc -n cattle-system rancher -o wide
```


- (Optional)Here’s the command for OKE internal LB

```
kubectl patch svc rancher -n cattle-system \
  -p '{
    "spec": {
      "type": "LoadBalancer"
    },
    "metadata": {
      "annotations": {
        "service.beta.kubernetes.io/oci-load-balancer-internal": "true",
        "service.beta.kubernetes.io/oci-load-balancer-shape": "flexible",
        "service.beta.kubernetes.io/oci-load-balancer-shape-flex-min": "10",
        "service.beta.kubernetes.io/oci-load-balancer-shape-flex-max": "100",
        "service.beta.kubernetes.io/oci-load-balancer-subnet1": "ocid1.subnet.oc1.ap-mumbai-1.aaaaaaaa5mw6a23gadaoysagedik4n2rheeojcr3ptzqn7kuq3bqtln5dkta"
      }
    }
  }'
```



```
helm install rancher rancher-stable/rancher \
  --namespace cattle-system \
  --set hostname=rancher.local \
  --set bootstrapPassword=admin \
  --set service.type=LoadBalancer \
  --set service.annotations."service\.beta\.kubernetes\.io/oci-load-balancer-internal"="true" \
  --set service.annotations."service\.beta\.kubernetes\.io/oci-load-balancer-shape"="flexible" \
  --set service.annotations."service\.beta\.kubernetes\.io/oci-load-balancer-shape-flex-min"="10" \
  --set service.annotations."service\.beta\.kubernetes\.io/oci-load-balancer-shape-flex-max"="100" \
  --set service.annotations."service\.beta\.kubernetes\.io/oci-load-balancer-subnet1"="ocid1.subnet.oc1.ap-mumbai-1.aaaaaaaa5mw6a23gadaoysagedik4n2rheeojcr3ptzqn7kuq3bqtln5dkta"
  ```
