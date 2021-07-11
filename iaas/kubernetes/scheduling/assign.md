# Pod分配

你可以约束一个 [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/) 只能在特定的 [节点](https://kubernetes.io/zh/docs/concepts/architecture/nodes/) 上运行。 有几种方法可以实现这点，推荐的方法都是用 [标签选择算符](https://kubernetes.io/zh/docs/concepts/overview/working-with-objects/labels/)来进行选择。 通常这样的约束不是必须的，因为调度器将自动进行合理的放置（比如，将 Pod 分散到节点上， 而不是将 Pod 放置在可用资源不足的节点上等等）。但在某些情况下，你可能需要进一步控制 Pod 停靠的节点。

* nodeSelector
* 亲和性和反亲和性
* 污点和容忍度

***

## nodeSelector

`nodeSelector` 是节点选择约束的最简单推荐形式。`nodeSelector` 是 PodSpec 的一个字段。 它包含键值对的映射。

使用方式：

1. 给节点添加标签

   ```shell
   kubectl label nodes <node-name> <label-key>=<label-value>
   ```

2. 给pod配置nodeSelector

   ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     name: nginx
     labels:
       env: test
   spec:
     containers:
     - name: nginx
       image: nginx
       imagePullPolicy: IfNotPresent
     #配置nodeSelector
     nodeSelector:
       disktype: ssd
   ```

***

## 亲和性和反亲和性

`nodeSelector` 提供了一种非常简单的方法来将 Pod 约束到具有特定标签的节点上。 亲和性/反亲和性功能极大地扩展了你可以表达约束的类型。关键的增强点包括：

1. 语言更具表现力（不仅仅是“对完全匹配规则的 AND”）
2. 你可以发现规则是“软需求”/“偏好”，而不是硬性要求，因此， 如果调度器无法满足该要求，仍然调度该 Pod
3. 你可以使用节点上（或其他拓扑域中）的 Pod 的标签来约束，而不是使用 节点本身的标签，来允许哪些 pod 可以或者不可以被放置在一起。

亲和性功能包含两种类型的亲和性，即“节点亲和性”和“Pod 间亲和性/反亲和性”。 节点亲和性就像现有的 `nodeSelector`（但具有上面列出的前两个好处），然而 Pod 间亲和性/反亲和性约束 Pod 标签而不是节点标签（在上面列出的第三项中描述， 除了具有上面列出的第一和第二属性）。

***

### 节点亲和性

节点亲和性概念上类似于 `nodeSelector`，它使你可以根据节点上的标签来约束 Pod 可以调度到哪些节点。

目前有两种节点亲和性：

* requiredDuringSchedulingIgnoredDuringExecution，指定了将 Pod 调度到一个节点上 *必须*满足的规则。
* preferredDuringSchedulingIgnoredDuringExecution，指定调度器将尝试执行但不能保证的*偏好*。

节点亲和性通过 PodSpec 的 `affinity` 字段下的 `nodeAffinity` 字段进行指定。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: with-node-affinity
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: kubernetes.io/e2e-az-name
            operator: In
            values:
            - e2e-az1
            - e2e-az2
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1
        preference:
          matchExpressions:
          - key: another-node-label-key
            operator: In
            values:
            - another-node-label-value
  containers:
  - name: with-node-affinity
    image: k8s.gcr.io/pause:2.0
```

你可以在上面的例子中看到 `In` 操作符的使用。新的节点亲和性语法支持下面的操作符： `In`，`NotIn`，`Exists`，`DoesNotExist`，`Gt`，`Lt`。 你可以使用 `NotIn` 和 `DoesNotExist` 来实现节点反亲和性行为，或者使用 [节点污点](https://kubernetes.io/zh/docs/concepts/scheduling-eviction/taint-and-toleration/) 将 Pod 从特定节点中驱逐。

如果你同时指定了 `nodeSelector` 和 `nodeAffinity`，*两者*必须都要满足， 才能将 Pod 调度到候选节点上。

如果你指定了多个与 `nodeAffinity` 类型关联的 `nodeSelectorTerms`，则 **如果其中一个** `nodeSelectorTerms` 满足的话，pod将可以调度到节点上。

如果你指定了多个与 `nodeSelectorTerms` 关联的 `matchExpressions`，则 **只有当所有** `matchExpressions` 满足的话，Pod 才会可以调度到节点上。

如果你修改或删除了 pod 所调度到的节点的标签，Pod 不会被删除。 换句话说，亲和性选择只在 Pod 调度期间有效。

***

### 节点反亲和性

Pod 间亲和性与反亲和性使你可以 *基于已经在节点上运行的 Pod 的标签* 来约束 Pod 可以调度到的节点，而不是基于节点上的标签。

 Pod 间反亲和性通过 PodSpec 中 `affinity` 字段下的 `podAntiAffinity` 字段进行指定。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: with-pod-affinity
spec:
  affinity:
    podAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: security
            operator: In
            values:
            - S1
        topologyKey: topology.kubernetes.io/zone
    podAntiAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 100
        podAffinityTerm:
          labelSelector:
            matchExpressions:
            - key: security
              operator: In
              values:
              - S2
          topologyKey: topology.kubernetes.io/zone
  containers:
  - name: with-pod-affinity
    image: k8s.gcr.io/pause:2.0

```

***

### 案例

##### 始终放置在相同节点上

在三节点集群中，一个 web 应用程序具有内存缓存，例如 redis。 我们希望 web 服务器尽可能与缓存放置在同一位置。

下面是一个简单 redis Deployment 的 YAML 代码段，它有三个副本和选择器标签 `app=store`。 Deployment 配置了 `PodAntiAffinity`，用来确保调度器不会将副本调度到单个节点上。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-cache
spec:
  selector:
    matchLabels:
      app: store
  replicas: 3
  template:
    metadata:
      labels:
        app: store
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: app
                operator: In
                values:
                - store
            topologyKey: "kubernetes.io/hostname"
      containers:
      - name: redis-server
        image: redis:3.2-alpine
```

下面 webserver Deployment 的 YAML 代码段中配置了 `podAntiAffinity` 和 `podAffinity`。 这将通知调度器将它的所有副本与具有 `app=store` 选择器标签的 Pod 放置在一起。 这还确保每个 web 服务器副本不会调度到单个节点上。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-server
spec:
  selector:
    matchLabels:
      app: web-store
  replicas: 3
  template:
    metadata:
      labels:
        app: web-store
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: app
                operator: In
                values:
                - web-store
            topologyKey: "kubernetes.io/hostname"
        podAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: app
                operator: In
                values:
                - store
            topologyKey: "kubernetes.io/hostname"
      containers:
      - name: web-app
        image: nginx:1.16-alpine
```

如果我们创建了上面的两个 Deployment，我们的三节点集群将如下表所示。

|    node-1     |    node-2     |    node-3     |
| :-----------: | :-----------: | :-----------: |
| *webserver-1* | *webserver-2* | *webserver-3* |
|   *cache-1*   |   *cache-2*   |   *cache-3*   |

如你所见，`web-server` 的三个副本都按照预期那样自动放置在同一位置。

```
kubectl get pods -o wide
```

输出类似于如下内容：

```
NAME                           READY     STATUS    RESTARTS   AGE       IP           NODE
redis-cache-1450370735-6dzlj   1/1       Running   0          8m        10.192.4.2   kube-node-3
redis-cache-1450370735-j2j96   1/1       Running   0          8m        10.192.2.2   kube-node-1
redis-cache-1450370735-z73mh   1/1       Running   0          8m        10.192.3.1   kube-node-2
web-server-1287567482-5d4dz    1/1       Running   0          7m        10.192.2.3   kube-node-1
web-server-1287567482-6f7v5    1/1       Running   0          7m        10.192.4.3   kube-node-3
web-server-1287567482-s330j    1/1       Running   0          7m        10.192.3.2   kube-node-2
```

##### 永远不放置在相同节点

上面的例子使用 `PodAntiAffinity` 规则和 `topologyKey: "kubernetes.io/hostname"` 来部署 redis 集群以便在同一主机上没有两个实例。

***

## 污点和容忍度

节点亲和性（详见[这里](https://kubernetes.io/zh/docs/concepts/scheduling-eviction/assign-pod-node/#affinity-and-anti-affinity)） 是 [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/) 的一种属性，它使 Pod 被吸引到一类特定的[节点](https://kubernetes.io/zh/docs/concepts/architecture/nodes/)。 这可能出于一种偏好，也可能是硬性要求。 Taint（污点）则相反，它使节点能够排斥一类特定的 Pod。

容忍度（Tolerations）是应用于 Pod 上的，允许（但并不要求）Pod 调度到带有与之匹配的污点的节点上。

污点和容忍度（Toleration）相互配合，可以用来避免 Pod 被分配到不合适的节点上。 每个节点上都可以应用一个或多个污点，这表示对于那些不能容忍这些污点的 Pod，是不会被该节点接受的。

***

### 操作

1. 节点新增污点

   ```shell
   #增加污点
   kubectl taint nodes node1 key1=value1:NoSchedule
   #删除污点
   kubectl taint nodes node1 key1=value1:NoSchedule-
   ```

2. Pod配置污点容忍

   ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     name: nginx
     labels:
       env: test
   spec:
     containers:
     - name: nginx
       image: nginx
       imagePullPolicy: IfNotPresent
     tolerations:
     - key: "example-key"
       operator: "Exists"
       effect: "NoSchedule"
   
   ```

***

### 基于污点的驱逐

污点的 effect 值 `NoExecute`会影响已经在节点上运行的 Pod

- 如果 Pod 不能忍受 effect 值为 `NoExecute` 的污点，那么 Pod 将马上被驱逐
- 如果 Pod 能够忍受 effect 值为 `NoExecute` 的污点，但是在容忍度定义中没有指定 `tolerationSeconds`，则 Pod 还会一直在这个节点上运行。
- 如果 Pod 能够忍受 effect 值为 `NoExecute` 的污点，而且指定了 `tolerationSeconds`， 则 Pod 还能在这个节点上继续运行这个指定的时间长度。

当某种条件为真时，节点控制器会自动给节点添加一个污点。当前内置的污点包括：

- `node.kubernetes.io/not-ready`：节点未准备好。这相当于节点状态 `Ready` 的值为 "`False`"。
- `node.kubernetes.io/unreachable`：节点控制器访问不到节点. 这相当于节点状态 `Ready` 的值为 "`Unknown`"。
- `node.kubernetes.io/memory-pressure`：节点存在内存压力。
- `node.kubernetes.io/disk-pressure`：节点存在磁盘压力。
- `node.kubernetes.io/pid-pressure`: 节点的 PID 压力。
- `node.kubernetes.io/network-unavailable`：节点网络不可用。
- `node.kubernetes.io/unschedulable`: 节点不可调度。
- `node.cloudprovider.kubernetes.io/uninitialized`：如果 kubelet 启动时指定了一个 "外部" 云平台驱动， 它将给当前节点添加一个污点将其标志为不可用。在 cloud-controller-manager 的一个控制器初始化这个节点后，kubelet 将删除这个污点。

在节点被驱逐时，节点控制器或者 kubelet 会添加带有 `NoExecute` 效应的相关污点。 如果异常状态恢复正常，kubelet 或节点控制器能够移除相关的污点。

***

