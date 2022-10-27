# kube-descheduler

This is a Kubernetes descheduler. It is a tool that evicts pods based on a set of strategies. This is useful for running on a regular basis to ensure that your cluster is always running optimally. This repo was created to add documentation and examples for the descheduler in action.

## Usage

### Job mode

In job mode, the descheduler is run once and then exits. This is useful for running the descheduler as a Kubernetes job.

Note: In the examples below, descheduler will every night at midnight. You can change this to your desired schedule. See the Kubernetes documentation for more information on cron jobs.

To run the descheduler in job mode, use the following command to deploy the job:

#### Dry-run only mode
Note: In this mode, the descheduler will only log pods that it would have evicted if it were not a dry run but will not action.

```bash
kubectl create ns descheduler --dry-run=client -o yaml | kubectl apply -f -
kubectl apply -f https://raw.githubusercontent.com/SupportTools/kube-descheduler/main/manifets/job/dry-run.yaml
```

#### Active mode
Note: In this mode, the descheduler will log pods that it would have evicted and will action.

```bash
kubectl create ns descheduler --dry-run=client -o yaml | kubectl apply -f -
kubectl apply -f https://raw.githubusercontent.com/SupportTools/kube-descheduler/main/manifets/job/normal.yaml
```

You can review the logs of the job by running the following command:

```bash
kubectl -n descheduler logs -l app=kube-descheduler
```

Example output:
    
```bash
I1027 05:26:55.973047       1 named_certificates.go:53] "Loaded SNI cert" index=0 certName="self-signed loopback" certDetail="\"apiserver-loopback-client@1666848415\" [serving] validServingFor=[apiserver-loopback-client] issuer=\"apiserver-loopback-client-ca@1666848415\" (2022-10-27 04:26:54 +0000 UTC to 2023-10-27 04:26:54 +0000 UTC (now=2022-10-27 05:26:55.972979087 +0000 UTC))"
I1027 05:26:55.973220       1 secure_serving.go:210] Serving securely on [::]:10258
I1027 05:26:55.973302       1 tlsconfig.go:240] "Starting DynamicServingCertificateController"
I1027 05:26:56.013292       1 reflector.go:221] Starting reflector *v1.Namespace (0s) from k8s.io/client-go/informers/factory.go:134
I1027 05:26:56.013326       1 reflector.go:221] Starting reflector *v1.PriorityClass (0s) from k8s.io/client-go/informers/factory.go:134
I1027 05:26:56.013340       1 reflector.go:221] Starting reflector *v1.Pod (0s) from k8s.io/client-go/informers/factory.go:134
I1027 05:26:56.013348       1 reflector.go:257] Listing and watching *v1.PriorityClass from k8s.io/client-go/informers/factory.go:134
I1027 05:26:56.013379       1 reflector.go:257] Listing and watching *v1.Pod from k8s.io/client-go/informers/factory.go:134
I1027 05:26:56.013333       1 reflector.go:257] Listing and watching *v1.Namespace from k8s.io/client-go/informers/factory.go:134
I1027 05:26:56.215226       1 node.go:49] "Node lister returned empty list, now fetch directly"
I1027 05:27:01.086202       1 descheduler.go:318] Building a cached client from the cluster for the dry run
I1027 05:27:01.086286       1 descheduler.go:122] Pulling resources for the cached client from the cluster
I1027 05:27:01.119920       1 reflector.go:221] Starting reflector *v1.Pod (0s) from k8s.io/client-go/informers/factory.go:134
I1027 05:27:01.120123       1 reflector.go:257] Listing and watching *v1.Pod from k8s.io/client-go/informers/factory.go:134
I1027 05:27:01.220023       1 descheduler.go:343] Building a pod evictor
I1027 05:27:01.221039       1 nodeutilization.go:202] "Node is appropriately utilized" node="a0ubdevopsmp01" usage=map[cpu:1450m memory:2304Mi pods:9] usagePercentage=map[cpu:36.25 memory:29.106068501587 pods:8.181818181818182]
I1027 05:27:01.221129       1 nodeutilization.go:202] "Node is appropriately utilized" node="a0ubdevopsmp02" usage=map[cpu:1450m memory:2304Mi pods:8] usagePercentage=map[cpu:36.25 memory:29.106068501587 pods:7.2727272727272725]
I1027 05:27:01.221212       1 nodeutilization.go:202] "Node is appropriately utilized" node="a0ubdevopsmp03" usage=map[cpu:1350m memory:2176Mi pods:9] usagePercentage=map[cpu:33.75 memory:27.489010435891412 pods:8.181818181818182]
I1027 05:27:01.221297       1 nodeutilization.go:202] "Node is appropriately utilized" node="a0ubdevopswp01" usage=map[cpu:2080m memory:2219215872 pods:25] usagePercentage=map[cpu:26 memory:6.598978511694901 pods:22.727272727272727]
I1027 05:27:01.221374       1 nodeutilization.go:202] "Node is appropriately utilized" node="a0ubdevopswp02" usage=map[cpu:1340m memory:786Mi pods:23] usagePercentage=map[cpu:16.75 memory:2.4507534554154846 pods:20.90909090909091]
I1027 05:27:01.221496       1 nodeutilization.go:202] "Node is appropriately utilized" node="a0ubdevopswp03" usage=map[cpu:1675m memory:1658505728 pods:26] usagePercentage=map[cpu:20.9375 memory:4.931666691229068 pods:23.636363636363637]
I1027 05:27:01.221606       1 nodeutilization.go:202] "Node is appropriately utilized" node="a0ubdevopswp04" usage=map[cpu:2340m memory:2734773760 pods:14] usagePercentage=map[cpu:29.25 memory:8.132022442829333 pods:12.727272727272727]
I1027 05:27:01.221703       1 nodeutilization.go:202] "Node is appropriately utilized" node="a0ubdevopswp05" usage=map[cpu:2340m memory:2734773760 pods:14] usagePercentage=map[cpu:29.25 memory:8.132022442829333 pods:12.727272727272727]
I1027 05:27:01.221763       1 lownodeutilization.go:135] "Criteria for a node under utilization" CPU=20 Mem=20 Pods=20
I1027 05:27:01.221807       1 lownodeutilization.go:136] "Number of underutilized nodes" totalNumber=0
I1027 05:27:01.221871       1 lownodeutilization.go:149] "Criteria for a node above target utilization" CPU=50 Mem=50 Pods=50
I1027 05:27:01.221907       1 lownodeutilization.go:150] "Number of overutilized nodes" totalNumber=0
I1027 05:27:01.221946       1 lownodeutilization.go:153] "No node is underutilized, nothing to do here, you might tune your thresholds further"
I1027 05:27:01.221985       1 removeduplicates.go:103] "Processing node" node="a0ubdevopsmp01"
I1027 05:27:01.222260       1 removeduplicates.go:103] "Processing node" node="a0ubdevopsmp02"
I1027 05:27:01.222389       1 removeduplicates.go:103] "Processing node" node="a0ubdevopsmp03"
I1027 05:27:01.222594       1 removeduplicates.go:103] "Processing node" node="a0ubdevopswp01"
I1027 05:27:01.222852       1 removeduplicates.go:103] "Processing node" node="a0ubdevopswp02"
I1027 05:27:01.223083       1 removeduplicates.go:103] "Processing node" node="a0ubdevopswp03"
I1027 05:27:01.223529       1 removeduplicates.go:103] "Processing node" node="a0ubdevopswp04"
I1027 05:27:01.223783       1 removeduplicates.go:103] "Processing node" node="a0ubdevopswp05"
I1027 05:27:01.224006       1 pod_antiaffinity.go:84] "Processing node" node="a0ubdevopsmp01"
I1027 05:27:01.224224       1 pod_antiaffinity.go:84] "Processing node" node="a0ubdevopsmp02"
I1027 05:27:01.224358       1 pod_antiaffinity.go:84] "Processing node" node="a0ubdevopsmp03"
I1027 05:27:01.224478       1 pod_antiaffinity.go:84] "Processing node" node="a0ubdevopswp01"
I1027 05:27:01.225551       1 pod_antiaffinity.go:84] "Processing node" node="a0ubdevopswp02"
I1027 05:27:01.226349       1 pod_antiaffinity.go:84] "Processing node" node="a0ubdevopswp03"
I1027 05:27:01.227025       1 pod_antiaffinity.go:84] "Processing node" node="a0ubdevopswp04"
I1027 05:27:01.227477       1 pod_antiaffinity.go:84] "Processing node" node="a0ubdevopswp05"
I1027 05:27:01.227866       1 descheduler.go:426] "Number of evicted pods" totalEvicted=0
I1027 05:27:01.228024       1 reflector.go:227] Stopping reflector *v1.Pod (0s) from k8s.io/client-go/informers/factory.go:134
I1027 05:27:01.228414       1 secure_serving.go:255] Stopped listening on [::]:10258
I1027 05:27:01.228463       1 tlsconfig.go:255] "Shutting down DynamicServingCertificateController"
```

You can see that the descheduler is not evicting any pods. This is because the nodes are not overutilized. The descheduler will only evict pods if the nodes are overutilized.

After running the job you can cleanup everything by running the following commands:

```bash
kubectl -n descheduler delete job descheduler
kubectl -n descheduler delete pod -l app=descheduler
kubectl delete ns descheduler
```

### Cronjob mode

The descheduler can also be run as a cronjob. This is useful if you want to run the descheduler on a regular basis. The following example shows how to run the descheduler as a cronjob.
In cronjob mode, the descheduler will run a schedule. This is useful if you want to run descheduler every night during off-peak hours.

To run the descheduler in cronjob mode, use the following command to deploy the job:

#### Dry-run only mode
Note: In this mode, the descheduler will only log pods that it would have evicted if it were not a dry run but will not action.

```bash
kubectl create ns descheduler --dry-run=client -o yaml | kubectl apply -f -
kubectl apply -f https://raw.githubusercontent.com/SupportTools/kube-descheduler/main/manifets/cronjob/dry-run.yaml
```

#### Active mode
Note: In this mode, the descheduler will log pods that it would have evicted and will action.

```bash
kubectl create ns descheduler --dry-run=client -o yaml | kubectl apply -f -
kubectl apply -f https://raw.githubusercontent.com/SupportTools/kube-descheduler/main/manifets/cronjob/normal.yaml
```

You can review the logs of the job by running the following command:

```bash
kubectl -n descheduler logs -l app=kube-descheduler
```

#### Deployment mode
In deployment mode, the descheduler will run continuously (5min delay between runs) and will evict pods as required. This is useful if you have an environment that is constantly changing and you want the descheduler to run continuously.

You can customize the interval between runs by changing the `--descheduling-interval` flag in the deployment manifest. (Default is 5 minutes)

Note: You can run descheduler as cronjob every 5mins to create the same effect but this is not recommended as it will create a lot of load on the cluster with the pods being created and destroyed. In addition, the descheduler can cached results from previous runs to reduce the load on the cluster.

To run the descheduler in deployment mode, use the following command to deploy the job:

#### Dry-run only mode
Note: In this mode, the descheduler will only log pods that it would have evicted if it were not a dry run but will not action.

```bash
kubectl create ns descheduler --dry-run=client -o yaml | kubectl apply -f -
kubectl apply -f https://raw.githubusercontent.com/SupportTools/kube-descheduler/main/manifets/deployment/dry-run.yaml
```

#### Active mode
Note: In this mode, the descheduler will log pods that it would have evicted and will action.

```bash
kubectl create ns descheduler --dry-run=client -o yaml | kubectl apply -f -
kubectl apply -f https://raw.githubusercontent.com/SupportTools/kube-descheduler/main/manifets/deployment/normal.yaml
```

You can review the logs of the job by running the following command:

```bash
kubectl -n descheduler logs -l app=kube-descheduler
```