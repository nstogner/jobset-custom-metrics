
```bash
# Create local cluster.
kind create cluster

# Install JobSet controller.
kubectl apply --server-side -f https://github.com/kubernetes-sigs/jobset/releases/download/v0.5.2/manifests.yaml

# Install kube-state-metrics.
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm upgrade --install -f kube-state-metrics-values.yaml kube-state-metrics prometheus-community/kube-state-metrics -n kube-system

# Apply example jobset
kubectl apply -f ./example-jobset.yaml

# Test metrics
kubectl port-forward -n kube-system svc/kube-state-metrics 8080:8080
curl localhost:8080/metrics | grep specified_jobs
curl localhost:8080/metrics | grep ready_jobs
```