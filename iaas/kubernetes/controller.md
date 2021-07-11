# 控制器

> 在机器人技术和自动化领域，控制回路（Control Loop）是一个非终止回路，用于调节系统状态。
>
> 在 Kubernetes 中，控制器通过监控[集群](https://kubernetes.io/zh/docs/reference/glossary/?all=true#term-cluster) 的公共状态，并致力于将当前状态转变为期望的状态。

***

## 控制器模式

一个控制器至少追踪一种类型的 Kubernetes 资源。这些 [对象](https://kubernetes.io/zh/docs/concepts/overview/working-with-objects/kubernetes-objects/) 有一个代表期望状态的 `spec` 字段。 该资源的控制器负责确保其当前状态接近期望状态。

控制器可能会自行执行操作；在 Kubernetes 中更常见的是一个控制器会发送信息给 [API 服务器](https://kubernetes.io/zh/docs/reference/command-line-tools-reference/kube-apiserver/)，这会有副作用。

### 通过API服务器控制

内置控制器通过和集群 API 服务器交互来管理状态。

### 直接控制

有些控制器需要对集群外的一些东西进行修改。和外部状态交互的控制器从 API 服务器获取到它想要的状态，然后直接和外部系统进行通信 并使当前状态更接近期望状态。

***

这里，很重要的一点是，控制器做出了一些变更以使得事物更接近你的期望状态， 之后将当前状态报告给集群的 API 服务器。 其他控制回路可以观测到所汇报的数据的这种变化并采取其各自的行动。

***

## 运行控制器的方式

Kubernetes 内置一组控制器，运行在 [kube-controller-manager](https://kubernetes.io/docs/reference/generated/kube-controller-manager/) 内。 这些内置的控制器提供了重要的核心功能。

Deployment 控制器和 Job 控制器是 Kubernetes 内置控制器的典型例子。 Kubernetes 允许你运行一个稳定的控制平面，这样即使某些内置控制器失败了， 控制平面的其他部分会接替它们的工作。

你会遇到某些控制器运行在控制面之外，用以扩展 Kubernetes。 或者，如果你愿意，你也可以自己编写新控制器。 你可以以一组 Pod 来运行你的控制器，或者运行在 Kubernetes 之外。 最合适的方案取决于控制器所要执行的功能是什么。

***

## 云控制器管理器

使用云基础设施技术，你可以在公有云、私有云或者混合云环境中运行 Kubernetes。 Kubernetes 的信条是基于自动化的、API 驱动的基础设施，同时避免组件间紧密耦合。



组件 cloud-controller-manager 是指云控制器管理器， 云控制器管理器是指嵌入特定云的控制逻辑的 [控制平面](https://kubernetes.io/zh/docs/reference/glossary/?all=true#term-control-plane)组件。 云控制器管理器允许您链接集群到云提供商的应用编程接口中， 并把和该云平台交互的组件与只和您的集群交互的组件分离开。



通过分离 Kubernetes 和底层云基础设置之间的互操作性逻辑， 云控制器管理器组件使云提供商能够以不同于 Kubernetes 主项目的速度进行发布新特征。

`cloud-controller-manager` 组件是基于一种插件机制来构造的， 这种机制使得不同的云厂商都能将其平台与 Kubernetes 集成。

***

云控制器管理器中的控制器包括：

### 节点控制器 

节点控制器负责在云基础设施中创建了新服务器时为之 创建 [节点（Node）](https://kubernetes.io/zh/docs/concepts/architecture/nodes/)对象。 节点控制器从云提供商获取当前租户中主机的信息。节点控制器执行以下功能：

1. 针对控制器通过云平台驱动的 API 所发现的每个服务器初始化一个 Node 对象；
2. 利用特定云平台的信息为 Node 对象添加注解和标签，例如节点所在的 区域（Region）和所具有的资源（CPU、内存等等）；
3. 获取节点的网络地址和主机名；
4. 检查节点的健康状况。如果节点无响应，控制器通过云平台 API 查看该节点是否 已从云中禁用、删除或终止。如果节点已从云中删除，则控制器从 Kubernetes 集群 中删除 Node 对象。

某些云驱动实现中，这些任务被划分到一个节点控制器和一个节点生命周期控制器中。

### 路由控制器 

Route 控制器负责适当地配置云平台中的路由，以便 Kubernetes 集群中不同节点上的 容器之间可以相互通信。

取决于云驱动本身，路由控制器可能也会为 Pod 网络分配 IP 地址块。

### 服务控制器 

[服务（Service）](https://kubernetes.io/zh/docs/concepts/services-networking/service/)与受控的负载均衡器、 IP 地址、网络包过滤、目标健康检查等云基础设施组件集成。 服务控制器与云驱动的 API 交互，以配置负载均衡器和其他基础设施组件。 你所创建的 Service 资源会需要这些组件服务。

***

## 鉴权 

本节分别讲述云控制器管理器为了完成自身工作而产生的对各类 API 对象的访问需求。

### 节点控制器 

节点控制器只操作 Node 对象。它需要读取和修改 Node 对象的完全访问权限。

`v1/Node`:

- Get
- List
- Create
- Update
- Patch
- Watch
- Delete

### 路由控制器

路由控制器会监听 Node 对象的创建事件，并据此配置路由设施。 它需要读取 Node 对象的 Get 权限。

`v1/Node`:

- Get

### 服务控制器

服务控制器监测 Service 对象的 Create、Update 和 Delete 事件，并配置 对应服务的 Endpoints 对象。 为了访问 Service 对象，它需要 List、Watch 访问权限；为了更新 Service 对象 它需要 Patch 和 Update 访问权限。 为了能够配置 Service 对应的 Endpoints 资源，它需要 Create、List、Get、Watch 和 Update 等访问权限。

`v1/Service`:

- List
- Get
- Watch
- Patch
- Update

### 其他 

云控制器管理器的实现中，其核心部分需要创建 Event 对象的访问权限以及 创建 ServiceAccount 资源以保证操作安全性的权限。

`v1/Event`:

- Create
- Patch
- Update

`v1/ServiceAccount`:

- Create

用于云控制器管理器 [RBAC](https://kubernetes.io/zh/docs/reference/access-authn-authz/rbac/) 的 ClusterRole 如下例所示：

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: cloud-controller-manager
rules:
- apiGroups:
  - ""
  resources:
  - events
  verbs:
  - create
  - patch
  - update
- apiGroups:
  - ""
  resources:
  - nodes
  verbs:
  - '*'
- apiGroups:
  - ""
  resources:
  - nodes/status
  verbs:
  - patch
- apiGroups:
  - ""
  resources:
  - services
  verbs:
  - list
  - patch
  - update
  - watch
- apiGroups:
  - ""
  resources:
  - serviceaccounts
  verbs:
  - create
- apiGroups:
  - ""
  resources:
  - persistentvolumes
  verbs:
  - get
  - list
  - update
  - watch
- apiGroups:
  - ""
  resources:
  - endpoints
  verbs:
  - create
  - get
  - list
  - watch
  - update
```