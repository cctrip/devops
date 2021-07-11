# Kube-scheduler

在 Kubernetes 中，*调度* 是指将 [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/) 放置到合适的 [Node](https://kubernetes.io/zh/docs/concepts/architecture/nodes/) 上，然后对应 Node 上的 [Kubelet](https://kubernetes.io/docs/reference/generated/kubelet) 才能够运行这些 pod。

调度器通过 kubernetes 的监测（Watch）机制来发现集群中新创建且尚未被调度到 Node 上的 Pod。 调度器会将发现的每一个未调度的 Pod 调度到一个合适的 Node 上来运行。 调度器会依据下文的调度原则来做出调度选择。

[kube-scheduler](https://kubernetes.io/zh/docs/reference/command-line-tools-reference/kube-scheduler/) 是 Kubernetes 集群的默认调度器，并且是集群 [控制面](https://kubernetes.io/zh/docs/reference/glossary/?all=true#term-control-plane) 的一部分。

对每一个新创建的 Pod 或者是未被调度的 Pod，kube-scheduler 会选择一个最优的 Node 去运行这个 Pod。然而，Pod 内的每一个容器对资源都有不同的需求，而且 Pod 本身也有不同的资源需求。因此，Pod 在被调度到 Node 上之前， 根据这些特定的资源调度需求，需要对集群中的 Node 进行一次过滤。

在一个集群中，满足一个 Pod 调度请求的所有 Node 称之为 *可调度节点*。 如果没有任何一个 Node 能满足 Pod 的资源请求，那么这个 Pod 将一直停留在 未调度状态直到调度器能够找到合适的 Node。

调度器先在集群中找到一个 Pod 的所有可调度节点，然后根据一系列函数对这些可调度节点打分， 选出其中得分最高的 Node 来运行 Pod。之后，调度器将这个调度决定通知给 kube-apiserver，这个过程叫做 *绑定*。

在做调度决定时需要考虑的因素包括：单独和整体的资源请求、硬件/软件/策略限制、 亲和以及反亲和要求、数据局域性、负载间的干扰等等。

***

## 调度流程

kube-scheduler 给一个 pod 做调度选择包含两个步骤：

1. 过滤
2. 打分

过滤阶段会将所有满足 Pod 调度需求的 Node 选出来。 例如，PodFitsResources 过滤函数会检查候选 Node 的可用资源能否满足 Pod 的资源请求。 在过滤之后，得出一个 Node 列表，里面包含了所有可调度节点；通常情况下， 这个 Node 列表包含不止一个 Node。如果这个列表是空的，代表这个 Pod 不可调度。

在打分阶段，调度器会为 Pod 从所有可调度节点中选取一个最合适的 Node。 根据当前启用的打分规则，调度器会给每一个可调度节点进行打分。

最后，kube-scheduler 会将 Pod 调度到得分最高的 Node 上。 如果存在多个得分最高的 Node，kube-scheduler 会从中随机选取一个。

支持以下两种方式配置调度器的过滤和打分行为：

1. [调度策略](https://kubernetes.io/zh/docs/reference/scheduling/policies) 允许你配置过滤的 *断言(Predicates)* 和打分的 *优先级(Priorities)* 。
2. [调度配置](https://kubernetes.io/zh/docs/reference/scheduling/config/#profiles) 允许你配置实现不同调度阶段的插件， 包括：`QueueSort`, `Filter`, `Score`, `Bind`, `Reserve`, `Permit` 等等。 你也可以配置 kube-scheduler 运行不同的配置文件。

***

### 过滤和打分

[kube-scheduler](https://kubernetes.io/docs/reference/generated/kube-scheduler/) 根据调度策略指定的*断言（predicates）\*和*优先级（priorities）* 分别对节点进行[过滤和打分](https://kubernetes.io/zh/docs/concepts/scheduling-eviction/kube-scheduler/#kube-scheduler-implementation)。

你可以通过执行 `kube-scheduler --policy-config-file <filename>` 或 `kube-scheduler --policy-configmap <ConfigMap>` 设置并使用[调度策略](https://kubernetes.io/zh/docs/reference/config-api/kube-scheduler-policy-config.v1/)。

#### 断言 

以下*断言*实现了过滤接口：

- `PodFitsHostPorts`：检查 Pod 请求的端口（网络协议类型）在节点上是否可用。

- `PodFitsHost`：检查 Pod 是否通过主机名指定了 Node。

- `PodFitsResources`：检查节点的空闲资源（例如，CPU和内存）是否满足 Pod 的要求。

- `MatchNodeSelector`：检查 Pod 的节点[选择算符](https://kubernetes.io/zh/docs/concepts/overview/working-with-objects/labels/) 和节点的 [标签](https://kubernetes.io/zh/docs/concepts/overview/working-with-objects/labels/) 是否匹配。

- `NoVolumeZoneConflict`：给定该存储的故障区域限制， 评估 Pod 请求的[卷](https://kubernetes.io/zh/docs/concepts/storage/volumes/)在节点上是否可用。

- `NoDiskConflict`：根据 Pod 请求的卷是否在节点上已经挂载，评估 Pod 和节点是否匹配。

- `MaxCSIVolumeCount`：决定附加 [CSI](https://kubernetes.io/zh/docs/concepts/storage/volumes/#csi) 卷的数量，判断是否超过配置的限制。

- `CheckNodeMemoryPressure`：如果节点正上报内存压力，并且没有异常配置，则不会把 Pod 调度到此节点上。

- `CheckNodePIDPressure`：如果节点正上报进程 ID 稀缺，并且没有异常配置，则不会把 Pod 调度到此节点上。

- `CheckNodeDiskPressure`：如果节点正上报存储压力（文件系统已满或几乎已满），并且没有异常配置，则不会把 Pod 调度到此节点上。

- `CheckNodeCondition`：节点可用上报自己的文件系统已满，网络不可用或者 kubelet 尚未准备好运行 Pod。 如果节点上设置了这样的状况，并且没有异常配置，则不会把 Pod 调度到此节点上。

- `PodToleratesNodeTaints`：检查 Pod 的[容忍](https://kubernetes.io/zh/docs/concepts/scheduling-eviction/taint-and-toleration/) 是否能容忍节点的[污点](https://kubernetes.io/zh/docs/concepts/scheduling-eviction/taint-and-toleration/)。

- `CheckVolumeBinding`：基于 Pod 的卷请求，评估 Pod 是否适合节点，这里的卷包括绑定的和未绑定的 [PVCs](https://kubernetes.io/zh/docs/concepts/storage/persistent-volumes/) 都适用。

***

#### 优先级 

以下*优先级*实现了打分接口：

- `SelectorSpreadPriority`：属于同一 [Service](https://kubernetes.io/zh/docs/concepts/services-networking/service/)、 [StatefulSet](https://kubernetes.io/zh/docs/concepts/workloads/controllers/statefulset/) 或 [ReplicaSet](https://kubernetes.io/zh/docs/concepts/workloads/controllers/replicaset/) 的 Pod，跨主机部署。

- `InterPodAffinityPriority`：实现了 [Pod 间亲和性与反亲和性](https://kubernetes.io/zh/docs/concepts/scheduling-eviction/assign-pod-node/#inter-pod-affinity-and-anti-affinity)的优先级。

- `LeastRequestedPriority`：偏向最少请求资源的节点。 换句话说，节点上的 Pod 越多，使用的资源就越多，此策略给出的排名就越低。

- `MostRequestedPriority`：支持最多请求资源的节点。 该策略将 Pod 调度到整体工作负载所需的最少的一组节点上。

- `RequestedToCapacityRatioPriority`：使用默认的打分方法模型，创建基于 ResourceAllocationPriority 的 requestedToCapacity。

- `BalancedResourceAllocation`：偏向平衡资源使用的节点。

- `NodePreferAvoidPodsPriority`：根据节点的注解 `scheduler.alpha.kubernetes.io/preferAvoidPods` 对节点进行优先级排序。 你可以使用它来暗示两个不同的 Pod 不应在同一节点上运行。

- `NodeAffinityPriority`：根据节点亲和中 PreferredDuringSchedulingIgnoredDuringExecution 字段对节点进行优先级排序。 你可以在[将 Pod 分配给节点](https://kubernetes.io/zh/docs/concepts/scheduling-eviction/assign-pod-node/)中了解更多。

- `TaintTolerationPriority`：根据节点上无法忍受的污点数量，给所有节点进行优先级排序。 此策略会根据排序结果调整节点的等级。

- `ImageLocalityPriority`：偏向已在本地缓存 Pod 所需容器镜像的节点。

- `ServiceSpreadingPriority`：对于给定的 Service，此策略旨在确保该 Service 关联的 Pod 在不同的节点上运行。 它偏向把 Pod 调度到没有该服务的节点。 整体来看，Service 对于单个节点故障变得更具弹性。

- `EqualPriority`：给予所有节点相等的权重。

- `EvenPodsSpreadPriority`：实现了 [Pod 拓扑扩展约束](https://kubernetes.io/zh/docs/concepts/workloads/pods/pod-topology-spread-constraints/)的优先级排序。