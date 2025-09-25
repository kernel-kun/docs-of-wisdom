# GitHub Runners – Possible Improvements

A curated list of potential enhancements for scaling and managing GitHub Self-Hosted Runners.

---

## 1. Optimizing Scale-Out Operations
- **KEDA-based Scaling**  
  Speed up auto-scaling by using [KEDA GitHub Runner Scaler](https://keda.sh/docs/2.14/scalers/github-runner/) to scale RunnerSets up or down based on queued GitHub jobs.
- **Image Prefetching**  
  Reduce cold-start time by pre-pulling images. See [Improve Kubernetes Image Caching](https://seifrajhi.github.io/blog/improve-k8s-image-caching/).
- **Peer-to-Peer Image Distribution**  
  Use [Spegel](https://github.com/spegel-org/spegel) for efficient, peer-to-peer image fetching across nodes.
- **ASG Warm Pools**  
  Maintain AWS Auto Scaling Group warm pools to achieve faster node startup times.  
  Reference: [AWS EC2 Auto Scaling Warm Pools](https://docs.aws.amazon.com/autoscaling/ec2/userguide/ec2-auto-scaling-warm-pools.html).

---

## 2. Maintaining a Large Number of Runner Types
- **Assigning Labels to Runner Types**
  - Add labels to existing runners via the [REST API](https://docs.github.com/en/rest/actions/self-hosted-runners?apiVersion=2022-11-28#add-custom-labels-to-a-self-hosted-runner-for-an-organization).
  - Programmatically assign labels during initial configuration as described [here](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners/using-labels-with-self-hosted-runners#programmatically-assign-labels).
- **Restricting High-End Runner Access**  
  Control which repositories can access specific runner groups.  
  Reference: [Controlling Access to Larger Runners](https://docs.github.com/en/actions/using-github-hosted-runners/using-larger-runners/controlling-access-to-larger-runners#changing-which-repositories-can-access-a-runner-group).

---

## 3. Observability and Metrics
- **Deploy a Complete Monitoring Stack**  
  Use [kube-prometheus-stack](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack) for Prometheus, Grafana, and Alertmanager.
- **Runner Metrics**  
  Enable metrics for runners as documented in the [Actions Runner Controller guide](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners-with-actions-runner-controller/deploying-runner-scale-sets-with-actions-runner-controller#enabling-metrics).
- **GitHub-Side Metrics**  
  Correlate with GitHub-side runner metrics using the [github-org-runner-exporter](https://github.com/tchelovilar/github-org-runner-exporter).
- **Lower-Level Metrics**  
  - [Node Exporter](https://github.com/prometheus/node_exporter) for CPU, memory, network, disk, and system load.
  - [kube-state-metrics](https://github.com/kubernetes/kube-state-metrics) for Kubernetes object metrics (Pods, Deployments, Services, etc.).

---

## 4. Avoiding Noisy Neighbors
- Set `requests` equal to `limits` on critical runner Pods.
- Use the **Guaranteed** QoS class for the most critical workflows or runner types.

---

## 5. Dynamically Adjusting AutoScalingRunnerSets Minimum Replicas
- Consider dynamically tuning the **`minimumReplicas`** field of an [`AutoScalingRunnerSet`](https://github.com/actions/actions-runner-controller) to maintain a baseline number of warm runners during peak times.
- This can be automated with:
  - A **CronJob** that patches the resource on a schedule.
  - A **shell-operator** hook that reacts to custom metrics or time-based events.
- References:
  - [Actions Runner Controller – AutoScalingRunnerSet](https://github.com/actions/actions-runner-controller/blob/master/docs/automatically-scaling-runners.md)
  - [shell-operator documentation](https://flant.github.io/shell-operator/)

---

*These improvements can be adopted independently or combined to create a more efficient, resilient, and observable GitHub runner platform.*
