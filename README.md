# JobSet Custom Metrics

## Configure

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
```

## Test

```bash
kubectl port-forward -n kube-system svc/kube-state-metrics 8080:8080

curl localhost:8080/metrics | grep jobset_
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  178k    0  178k    0     0  4124k      0 --:--:-- --:--:-- --:--:-- 4150k
# HELP kube_customresource_jobset_specified_replicas Total number of jobs in the jobset
# TYPE kube_customresource_jobset_specified_replicas gauge
kube_customresource_jobset_specified_replicas{customresource_group="jobset.x-k8s.io",customresource_kind="JobSet",customresource_version="v1alpha2"} 2
# HELP kube_customresource_jobset_ready_replicas Total number of jobs in a ready state for the jobset
# TYPE kube_customresource_jobset_ready_replicas gauge
kube_customresource_jobset_ready_replicas{customresource_group="jobset.x-k8s.io",customresource_kind="JobSet",customresource_version="v1alpha2"} 2
```