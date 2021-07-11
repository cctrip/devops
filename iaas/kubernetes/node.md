# Node

Kubernetes 通过将容器放入在节点（Node）上运行的 Pod 中来执行你的工作负载。 节点可以是一个虚拟机或者物理机器，取决于所在的集群配置。 每个节点包含运行 [Pods](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/) 所需的服务； 这些节点由 [控制面](https://kubernetes.io/zh/docs/reference/glossary/?all=true#term-control-plane) 负责管理。

***

## 节点管理

向 [API 服务器](https://kubernetes.io/zh/docs/reference/command-line-tools-reference/kube-apiserver/)添加节点的方式主要有两种：

1. 节点上的 `kubelet` 向控制面执行自注册；
2. 你，或者别的什么人，手动添加一个 Node 对象。

在你创建了 Node 对象或者节点上的 `kubelet` 执行了自注册操作之后， 控制面会检查新的 Node 对象是否合法。例如，如果你使用下面的 JSON 对象来创建 Node 对象：

```json
{
  "kind": "Node",
  "apiVersion": "v1",
  "metadata": {
    "name": "10.240.79.157",
    "labels": {
      "name": "my-first-k8s-node"
    }
  }
}
```

Kubernetes 会在内部创建一个 Node 对象作为节点的表示。Kubernetes 检查 `kubelet` 向 API 服务器注册节点时使用的 `metadata.name` 字段是否匹配。 如果节点是健康的（即所有必要的服务都在运行中），则该节点可以用来运行 Pod。 否则，直到该节点变为健康之前，所有的集群活动都会忽略该节点。

***

### 节点自注册

当 kubelet 标志 `--register-node` 为 true（默认）时，它会尝试向 API 服务注册自己。 这是首选模式，被绝大多数发行版选用。

对于自注册模式，kubelet 使用下列参数启动：

- `--kubeconfig` - 用于向 API 服务器表明身份的凭据路径。
- `--cloud-provider` - 与某[云驱动](https://kubernetes.io/zh/docs/reference/glossary/?all=true#term-cloud-provider) 进行通信以读取与自身相关的元数据的方式。
- `--register-node` - 自动向 API 服务注册。
- `--register-with-taints` - 使用所给的污点列表（逗号分隔的 `<key>=<value>:<effect>`）注册节点。 当 `register-node` 为 false 时无效。
- `--node-ip` - 节点 IP 地址。
- `--node-labels` - 在集群中注册节点时要添加的 [标签](https://kubernetes.io/zh/docs/concepts/overview/working-with-objects/labels/)。 （参见 [NodeRestriction 准入控制插件](https://kubernetes.io/zh/docs/reference/access-authn-authz/admission-controllers/#noderestriction)所实施的标签限制）。
- `--node-status-update-frequency` - 指定 kubelet 向控制面发送状态的频率。

***

### 手动管理节点

你可以使用 [kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/) 来创建和修改 Node 对象。

如果你希望手动创建节点对象时，请设置 kubelet 标志 `--register-node=false`。

你可以修改 Node 对象（忽略 `--register-node` 设置）。 例如，修改节点上的标签或标记其为不可调度。

你可以结合使用节点上的标签和 Pod 上的选择算符来控制调度。 例如，你可以限制某 Pod 只能在符合要求的节点子集上运行。

如果标记节点为不可调度（unschedulable），将阻止新 Pod 调度到该节点之上，但不会 影响任何已经在其上的 Pod。 这是重启节点或者执行其他维护操作之前的一个有用的准备步骤。

node部分操作指令：

```shell
kubectl cordon my-node                                                # 标记 my-node 节点为不可调度
kubectl drain my-node                                                 # 对 my-node 节点进行清空操作，为节点维护做准备
kubectl uncordon my-node                                              # 标记 my-node 节点为可以调度
kubectl top node my-node                                              # 显示给定节点的度量值
kubectl cluster-info                                                  # 显示主控节点和服务的地址
kubectl cluster-info dump                                             # 将当前集群状态转储到标准输出
kubectl cluster-info dump --output-directory=/path/to/cluster-state   # 将当前集群状态输出到 /path/to/cluster-state

# 如果已存在具有指定键和效果的污点，则替换其值为指定值。
kubectl taint nodes foo dedicated=special-user:NoSchedule

# 部分更新某节点
kubectl patch node k8s-node-1 -p '{"spec":{"unschedulable":true}}'


```

***

### 状态

```shell
$ kubectl describe node $NODE
Name:               ip-172-31-208-90.cn-northwest-1.compute.internal
Roles:              <none>
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/instance-type=t3.2xlarge
                    beta.kubernetes.io/os=linux
                    eks.amazonaws.com/capacityType=ON_DEMAND
                    eks.amazonaws.com/nodegroup=test-2
                    eks.amazonaws.com/nodegroup-image=ami-0815bf44519d2e1c5
                    failure-domain.beta.kubernetes.io/region=cn-northwest-1
                    failure-domain.beta.kubernetes.io/zone=cn-northwest-1b
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=ip-172-31-208-90.cn-northwest-1.compute.internal
                    kubernetes.io/os=linux
                    node.kubernetes.io/instance-type=t3.2xlarge
                    topology.kubernetes.io/region=cn-northwest-1
                    topology.kubernetes.io/zone=cn-northwest-1b
Annotations:        csi.volume.kubernetes.io/nodeid: {"csi.chubaofs.com":"ip-172-31-208-90.cn-northwest-1.compute.internal"}
                    node.alpha.kubernetes.io/ttl: 0
                    volumes.kubernetes.io/controller-managed-attach-detach: true
CreationTimestamp:  Fri, 18 Jun 2021 17:16:27 +0800
Taints:             <none>
Unschedulable:      false
#节点状态
Conditions:
  Type             Status  LastHeartbeatTime                 LastTransitionTime                Reason                       Message
  ----             ------  -----------------                 ------------------                ------                       -------
  MemoryPressure   False   Sun, 11 Jul 2021 13:11:24 +0800   Fri, 18 Jun 2021 17:16:26 +0800   KubeletHasSufficientMemory   kubelet has sufficient memory available
  DiskPressure     False   Sun, 11 Jul 2021 13:11:24 +0800   Fri, 18 Jun 2021 17:16:26 +0800   KubeletHasNoDiskPressure     kubelet has no disk pressure
  PIDPressure      False   Sun, 11 Jul 2021 13:11:24 +0800   Fri, 18 Jun 2021 17:16:26 +0800   KubeletHasSufficientPID      kubelet has sufficient PID available
  #节点是否健康并准备好接受pod
  Ready            True    Sun, 11 Jul 2021 13:11:24 +0800   Fri, 18 Jun 2021 17:16:47 +0800   KubeletReady                 kubelet is posting ready status
#地址
Addresses:
  InternalIP:   172.31.208.90
  Hostname:     ip-172-31-208-90.cn-northwest-1.compute.internal
  InternalDNS:  ip-172-31-208-90.cn-northwest-1.compute.internal
#容量
Capacity:
 attachable-volumes-aws-ebs:  25
 cpu:                         8
 ephemeral-storage:           209702892Ki
 hugepages-1Gi:               0
 hugepages-2Mi:               0
 memory:                      32523936Ki
 pods:                        58
#可分配
Allocatable:
 attachable-volumes-aws-ebs:  25
 cpu:                         7910m
 ephemeral-storage:           192188443124
 hugepages-1Gi:               0
 hugepages-2Mi:               0
 memory:                      31507104Ki
 pods:                        58
System Info:
 Machine ID:                 ec2bdaa155ed1f5f21a7018baaa8bf35
 System UUID:                ec2bdaa1-55ed-1f5f-21a7-018baaa8bf35
 Boot ID:                    a25656cf-44fa-4eee-af91-cbb9f1573777
 Kernel Version:             5.4.117-58.216.amzn2.x86_64
 OS Image:                   Amazon Linux 2
 Operating System:           linux
 Architecture:               amd64
 Container Runtime Version:  docker://19.3.13
 Kubelet Version:            v1.19.6-eks-49a6c0
 Kube-Proxy Version:         v1.19.6-eks-49a6c0
ProviderID:                  aws:///cn-northwest-1b/i-0833a0faca071fa94
Non-terminated Pods:         (19 in total)
  Namespace                  Name                                          CPU Requests  CPU Limits   Memory Requests  Memory Limits  AGE
  ---------                  ----                                          ------------  ----------   ---------------  -------------  ---
  dayu-web                   db-test-mysql-0                               230m (2%)     2200m (27%)  384Mi (1%)       2240Mi (7%)    22d
  default                    cfs-csi-node-4gqxv                            700m (8%)     0 (0%)       2304Mi (7%)      0 (0%)         22d
  default                    debug-agent-c57n5                             0 (0%)        0 (0%)       0 (0%)           0 (0%)         22d
  default                    nginx-deployment-55649fd747-jwk24             0 (0%)        0 (0%)       0 (0%)           0 (0%)         22d
  fe-invoice2                fe-invoice2-test-b4p4k                        100m (1%)     200m (2%)    256Mi (0%)       512Mi (1%)     3d1h
  fe-wholeenav               fe-wholeenav-dev-djb9r                        100m (1%)     200m (2%)    128Mi (0%)       256Mi (0%)     3d21h
  kruise-system              kruise-controller-manager-7f77ddc667-l29ng    100m (1%)     100m (1%)    256Mi (0%)       256Mi (0%)     22d
  kruise-system              kruise-daemon-dq2cd                           0 (0%)        50m (0%)     0 (0%)           128Mi (0%)     22d
  kube-system                aws-node-lmqch                                10m (0%)      0 (0%)       0 (0%)           0 (0%)         16d
  kube-system                kube-proxy-2tkgp                              100m (1%)     0 (0%)       0 (0%)           0 (0%)         22d
  lens-metrics               node-exporter-669xx                           10m (0%)      200m (2%)    24Mi (0%)        100Mi (0%)     18d
  lm-connector-center        lm-connector-center-dev-s7xqj                 200m (2%)     1 (12%)      256Mi (0%)       1Gi (3%)       9d
  log-pilot                  es-0                                          1 (12%)       1 (12%)      2Gi (6%)         2Gi (6%)       22d
  log-pilot                  kibana-57d95d8c46-9gwwf                       1 (12%)       1 (12%)      2Gi (6%)         2Gi (6%)       22d
  log-pilot                  log-pilot-nmkbs                               200m (2%)     0 (0%)       200Mi (0%)       500Mi (1%)     22d
  monitoring                 blackbox-exporter-556d889b47-cp5tt            30m (0%)      60m (0%)     60Mi (0%)        120Mi (0%)     22d
  monitoring                 node-exporter-45lkk                           112m (1%)     270m (3%)    200Mi (0%)       220Mi (0%)     22d
  monitoring                 ops-bash-bp9gs                                0 (0%)        0 (0%)       0 (0%)           0 (0%)         22d
  monitoring                 ops-grafana-74bf5b9f58-twpc8                  100m (1%)     200m (2%)    100Mi (0%)       200Mi (0%)     22d
Allocated resources:
  (Total limits may be over 100 percent, i.e., overcommitted.)
  Resource                    Requests      Limits
  --------                    --------      ------
  cpu                         3992m (50%)   6480m (81%)
  memory                      8264Mi (26%)  9652Mi (31%)
  ephemeral-storage           0 (0%)        0 (0%)
  attachable-volumes-aws-ebs  0             0
Events:                       <none>
```

***

### 节点控制器 

节点[控制器](https://kubernetes.io/zh/docs/concepts/architecture/controller/)是 Kubernetes 控制面组件，管理节点的方方面面。

节点控制器在节点的生命周期中扮演多个角色。 第一个是当节点注册时为它分配一个 CIDR 区段（如果启用了 CIDR 分配）。

第二个是保持节点控制器内的节点列表与云服务商所提供的可用机器列表同步。 如果在云环境下运行，只要某节点不健康，节点控制器就会询问云服务是否节点的虚拟机仍可用。 如果不可用，节点控制器会将该节点从它的节点列表删除。

第三个是监控节点的健康情况。节点控制器负责在节点不可达 （即，节点控制器因为某些原因没有收到心跳，例如节点宕机）时， 将节点状态的 `NodeReady` 状况更新为 "`Unknown`"。 如果节点接下来持续处于不可达状态，节点控制器将逐出节点上的所有 Pod（使用体面终止）。 默认情况下 40 秒后开始报告 "`Unknown`"，在那之后 5 分钟开始逐出 Pod。

节点控制器每隔 `--node-monitor-period` 秒检查每个节点的状态。

#### 心跳机制 

Kubernetes 节点发送的心跳（Heartbeats）有助于确定节点的可用性。 心跳有两种形式：`NodeStatus` 和 [`Lease` 对象](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.21/#lease-v1-coordination-k8s-io)。 每个节点在 `kube-node-lease`[名字空间](https://kubernetes.io/zh/docs/concepts/overview/working-with-objects/namespaces/) 中都有一个与之关联的 `Lease` 对象。 `Lease` 是一种轻量级的资源，可在集群规模扩大时提高节点心跳机制的性能。

`kubelet` 负责创建和更新 `NodeStatus` 和 `Lease` 对象。

- 当状态发生变化时，或者在配置的时间间隔内没有更新事件时，kubelet 会更新 `NodeStatus`。 `NodeStatus` 更新的默认间隔为 5 分钟（比不可达节点的 40 秒默认超时时间长很多）。
- `kubelet` 会每 10 秒（默认更新间隔时间）创建并更新其 `Lease` 对象。 `Lease` 更新独立于 `NodeStatus` 更新而发生。 如果 `Lease` 的更新操作失败，`kubelet` 会采用指数回退机制，从 200 毫秒开始 重试，最长重试间隔为 7 秒钟。

#### 可靠性 

大部分情况下，节点控制器把逐出速率限制在每秒 `--node-eviction-rate` 个（默认为 0.1）。 这表示它每 10 秒钟内至多从一个节点驱逐 Pod。

当一个可用区域（Availability Zone）中的节点变为不健康时，节点的驱逐行为将发生改变。 节点控制器会同时检查可用区域中不健康（NodeReady 状况为 Unknown 或 False） 的节点的百分比。如果不健康节点的比例超过 `--unhealthy-zone-threshold` （默认为 0.55）， 驱逐速率将会降低：如果集群较小（意即小于等于 `--large-cluster-size-threshold` 个节点 - 默认为 50），驱逐操作将会停止，否则驱逐速率将降为每秒 `--secondary-node-eviction-rate` 个（默认为 0.01）。

在单个可用区域实施这些策略的原因是当一个可用区域可能从控制面脱离时其它可用区域 可能仍然保持连接。 如果你的集群没有跨越云服务商的多个可用区域，那（整个集群）就只有一个可用区域。

跨多个可用区域部署你的节点的一个关键原因是当某个可用区域整体出现故障时， 工作负载可以转移到健康的可用区域。 因此，如果一个可用区域中的所有节点都不健康时，节点控制器会以正常的速率 `--node-eviction-rate` 进行驱逐操作。 在所有的可用区域都不健康（也即集群中没有健康节点）的极端情况下， 节点控制器将假设控制面节点的连接出了某些问题， 它将停止所有驱逐动作直到一些连接恢复。

节点控制器还负责驱逐运行在拥有 `NoExecute` 污点的节点上的 Pod， 除非这些 Pod 能够容忍此污点。 节点控制器还负责根据节点故障（例如节点不可访问或没有就绪）为其添加 [污点](https://kubernetes.io/zh/docs/concepts/scheduling-eviction/taint-and-toleration/)。 这意味着调度器不会将 Pod 调度到不健康的节点上。

### 节点容量 

Node 对象会跟踪节点上资源的容量（例如可用内存和 CPU 数量）。 通过[自注册](https://kubernetes.io/zh/docs/concepts/architecture/nodes/#self-registration-of-nodes)机制生成的 Node 对象会在注册期间报告自身容量。 如果你[手动](https://kubernetes.io/zh/docs/concepts/architecture/nodes/#manual-node-administration)添加了 Node，你就需要在添加节点时 手动设置节点容量。

Kubernetes [调度器](https://kubernetes.io/docs/reference/generated/kube-scheduler/)保证节点上 有足够的资源供其上的所有 Pod 使用。它会检查节点上所有容器的请求的总和不会超过节点的容量。 总的请求包括由 kubelet 启动的所有容器，但不包括由容器运行时直接启动的容器， 也不包括不受 `kubelet` 控制的其他进程。

> **说明：** 如果要为非 Pod 进程显式保留资源。请参考 [为系统守护进程预留资源](https://kubernetes.io/zh/docs/tasks/administer-cluster/reserve-compute-resources/#system-reserved)。

***

## 节点拓扑 

**FEATURE STATE:** `Kubernetes v1.16 [alpha]`

如果启用了 `TopologyManager` [特性门控](https://kubernetes.io/zh/docs/reference/command-line-tools-reference/feature-gates/)， `kubelet` 可以在作出资源分配决策时使用拓扑提示。 参考[控制节点上拓扑管理策略](https://kubernetes.io/zh/docs/tasks/administer-cluster/topology-manager/) 了解详细信息。

***

## 节点体面关闭

**FEATURE STATE:** `Kubernetes v1.21 [beta]`

kubelet 会尝试检测节点系统关闭事件并终止在节点上运行的 Pods。

在节点终止期间，kubelet 保证 Pod 遵从常规的 [Pod 终止流程](https://kubernetes.io/zh/docs/concepts/workloads/pods/pod-lifecycle/#pod-termination)。

体面节点关闭特性依赖于 systemd，因为它要利用 [systemd 抑制器锁](https://www.freedesktop.org/wiki/Software/systemd/inhibit/) 在给定的期限内延迟节点关闭。

体面节点关闭特性受 `GracefulNodeShutdown` [特性门控](https://kubernetes.io/docs/reference/command-line-tools-reference/feature-gates/) 控制，在 1.21 版本中是默认启用的。

注意，默认情况下，下面描述的两个配置选项，`ShutdownGracePeriod` 和 `ShutdownGracePeriodCriticalPods` 都是被设置为 0 的，因此不会激活 体面节点关闭功能。 要激活此功能特性，这两个 kubelet 配置选项要适当配置，并设置为非零值。

在体面关闭节点过程中，kubelet 分两个阶段来终止 Pods：

1. 终止在节点上运行的常规 Pod。
2. 终止在节点上运行的[关键 Pod](https://kubernetes.io/zh/docs/tasks/administer-cluster/guaranteed-scheduling-critical-addon-pods/#marking-pod-as-critical)。

节点体面关闭的特性对应两个 [`KubeletConfiguration`](https://kubernetes.io/zh/docs/tasks/administer-cluster/kubelet-config-file/) 选项：

- ShutdownGracePeriod：
  - 指定节点应延迟关闭的总持续时间。此时间是 Pod 体面终止的时间总和，不区分常规 Pod 还是 [关键 Pod](https://kubernetes.io/zh/docs/tasks/administer-cluster/guaranteed-scheduling-critical-addon-pods/#marking-pod-as-critical)。
- ShutdownGracePeriodCriticalPods：
  - 在节点关闭期间指定用于终止 [关键 Pod](https://kubernetes.io/zh/docs/tasks/administer-cluster/guaranteed-scheduling-critical-addon-pods/#marking-pod-as-critical) 的持续时间。该值应小于 `ShutdownGracePeriod`。

例如，如果设置了 `ShutdownGracePeriod=30s` 和 `ShutdownGracePeriodCriticalPods=10s`， 则 kubelet 将延迟 30 秒关闭节点。 在关闭期间，将保留前 20（30 - 10）秒用于体面终止常规 Pod， 而保留最后 10 秒用于终止 [关键 Pod](https://kubernetes.io/zh/docs/tasks/administer-cluster/guaranteed-scheduling-critical-addon-pods/#marking-pod-as-critical)。

***

