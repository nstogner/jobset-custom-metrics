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

# if GKE GMP is enabled
kubectl apply -f ./podmonitoring.yaml

# Apply example jobset
kubectl apply -f ./example-jobset.yaml
kubectl apply -f ./example-tpu-jobset.yaml
```

## Test

```bash
kubectl port-forward -n kube-system svc/kube-state-metrics 8080:8080

curl localhost:8080/metrics | grep jobset_
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  6808    0  6808    0     0   279k      0 --:--:-- --:--:-- --:--:--  289k
# HELP kube_customresource_jobset_specified_replicas Total number of jobs in the jobset
# TYPE kube_customresource_jobset_specified_replicas gauge
kube_customresource_jobset_specified_replicas{customresource_group="jobset.x-k8s.io",customresource_kind="JobSet",customresource_version="v1alpha2",name="example",replicated_job_name="other-workers"} 1
kube_customresource_jobset_specified_replicas{customresource_group="jobset.x-k8s.io",customresource_kind="JobSet",customresource_version="v1alpha2",name="example",replicated_job_name="workers"} 2
kube_customresource_jobset_specified_replicas{customresource_group="jobset.x-k8s.io",customresource_kind="JobSet",customresource_version="v1alpha2",name="nicks-two-slice",replicated_job_name="slice",some_custom_label="some-custom-jobset-label-value",tpu_accelerator="tpu-v5p-slice",tpu_topology="2x2x1"} 2
# HELP kube_customresource_jobset_ready_replicas Total number of jobs in a ready state for the jobset
# TYPE kube_customresource_jobset_ready_replicas gauge
kube_customresource_jobset_ready_replicas{customresource_group="jobset.x-k8s.io",customresource_kind="JobSet",customresource_version="v1alpha2",name="example",replicated_job_name="other-workers"} 0
kube_customresource_jobset_ready_replicas{customresource_group="jobset.x-k8s.io",customresource_kind="JobSet",customresource_version="v1alpha2",name="example",replicated_job_name="workers"} 0
kube_customresource_jobset_ready_replicas{customresource_group="jobset.x-k8s.io",customresource_kind="JobSet",customresource_version="v1alpha2",name="nicks-two-slice",replicated_job_name="slice",some_custom_label="some-custom-jobset-label-value",tpu_accelerator="tpu-v5p-slice",tpu_topology="2x2x1"} 0
# HELP kube_customresource_jobset_restarts Total number of times the JobSet has restarted
# TYPE kube_customresource_jobset_restarts gauge
kube_customresource_jobset_restarts{customresource_group="jobset.x-k8s.io",customresource_kind="JobSet",customresource_version="v1alpha2",name="example"} 3
```

## Deploying the GKE