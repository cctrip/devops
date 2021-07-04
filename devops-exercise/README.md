翻译和解答[https://github.com/bregman-arie/devops-exercises/blob/master/README.md](https://github.com/bregman-arie/devops-exercises/blob/master/README.md)

***

## DevOps

#### 什么是DevOps？

**Amazon:**

"DevOps是文化理念，实践和工具的结合，可提高组织高速交付应用程序和服务的能力: 与使用传统软件开发和基础架构管理流程的组织相比，以更快的速度发展和改进产品。这种速度使组织可以更好地为客户提供服务，并在市场上更有效地竞争。"

**Microsoft:**

"DevOps是人员，流程和产品的结合，可以为我们的最终用户持续交付价值。“Dev”和“Ops”的收缩是指取代孤立的开发和运营来创建多学科的团队，这些团队现在可以与共享的高效实践和工具一起工作。DevOps的基本实践包括敏捷计划，持续集成，持续交付以及对应用程序的监视。"

**Red Hat:**

"DevOps描述了加速想法(例如新软件功能，增强功能请求或错误修复)从开发到在生产环境中进行部署的过程的方法，该过程可以为用户提供价值。这些方法要求开发团队和运维团队经常沟通，并以同情他人的态度对待他们的工作。可伸缩性和灵活的配置也是必要的。借助DevOps，那些最需要电源的人可以通过自助服务和自动化来获得电源。通常在标准开发环境中进行编码的开发人员会与IT运营紧密合作，以在不牺牲可靠性的情况下加快软件的构建，测试和发布的速度。"

**Google:**

"DevOps旨在提高软件交付速度，提高服务可靠性并在软件利益相关者之间建立共享所有权的组织和文化运动。"

***

#### DevOps有什么好处？有什么可以帮助我们实现？

* 合作
* 改善交付
* 提高安全性
* 加快速度
* 快速扩容
* 提高可靠性

***

#### DevOps的反模式是什么？

* 一个人负责不同的任务。例如，只有一个人可以将其他人的代码合并到存储库中
* 将生产与开发环境区别对待。例如，在开发环境中不实施安全性
* 不允许某人在周五开始发布

***

#### 什么是持续集成？

开发人员经常将代码集成到共享存储库中的一种开发实践。它的范围可以从每天或每周进行几次更改，到大规模在一个小时内进行几次更改。

验证每段代码（更改/补丁），以使更改可以安全地合并。如今，使用自动构建来测试更改以确保代码可以集成是一种常见的做法。它可以是一个运行在不同级别（单元，功能等）的多个测试的构建，也可以是所有或某些必须通过以将更改合并到存储库中的多个单独的构建。

***

#### 您能否描述从开发人员向存储库提交更改/ PR开始的CI（和/或CD）流程示例？

由于配置项流程根据所使用的技术和向其提交更改的项目的类型而异，因此对于此问题有很多答案。此类过程可以包括以下一个或多个阶段：

* 编译
* 构建
* 安装
* 配置
* 更新
* 测试

举例：

开发人员向项目提交了拉取请求。 PR（拉动请求）触发了两个作业（或一个合并的作业）。 一项工作是对变更运行lint测试，另一项任务是构建包含提交的变更的程序包，并运行多个api。

一个完整的不同CI流程可以描述开发人员如何将代码推送到存储库，然后触发工作流以构建容器映像并将其推送到注册表。进入注册表后，k8s集群将应用新更改。

***

#### 什么是持续部署？

开发人员用于将软件自动发布到生产中的开发策略，其中任何代码提交都必须通过自动化测试阶段。仅当成功时，该发行才被认为是有价值的。这消除了任何人为干预，只有在设置了生产就绪管道并实时监视和报告已部署资产后才应实施。如果在生产中发现任何问题，则应该很容易回滚到以前的工作状态。[更多内容](https://www.atlassian.com/continuous-delivery/continuous-deployment)

***

#### 什么是持续交付？

一种开发策略，通常用于将代码交付给QA和Ops进行测试。这需要具有一个具有与生产类似的功能的登台区域，在该区域中，只有经过人工检查才能接受生产的更改。由于这种人为纠缠，与连续部署相比，发布和复审之间通常存在时间滞后，使其变得更慢且更容易出错。

[更多内容](https://www.atlassian.com/continuous-delivery/continuous-deployment)

***

#### 您希望使用“配置->部署”模型还是“部署->配置”模型？为什么？

两者都有优点和缺点。例如，在“配置->部署”模型中，您构建一个映像以供多个部署使用，因此部署之间彼此不同的机会就更少了，因此它具有一致环境的明显优势。

***

#### 您熟悉哪些CI / CD最佳实践？或您认为什么是CI / CD最佳实践？

* 构建，测试和部署软件的自动化过程
* 经常提交和测试
* 测试环境应该是生产环境的克隆

***

#### 您在哪里存储CI / CD管道？为什么？

关于在哪里存储CI / CD管道定义，有多种方法：

* 应用程序存储库-将它们存储在正在构建或测试的应用程序的相同存储库中
* 中央存储库-将所有组织/项目的CI / CD管道存储在一个单独的存储库中
* 每个应用程序回购的CI回购-将与CI相关的代码与应用程序代码分开，但您并未将所有内容都放在一个地方

***

#### 您在以下领域/任务中使用什么系统和/或工具？为什么？

- **CI/CD - Jenkins, Circle CI, Travis, Drone, Argo CD, Zuul**
- **Provisioning infrastructure - Terraform, CloudFormation**
- **Configuration Management - Ansible, Puppet, Chef**
- **Monitoring & alerting - Prometheus, Nagios, zabbix, open-falcon**
- **Logging - Logstash, Graylog, Fluentd**
- **Code review - Gerrit, Review Board**
- **Code coverage - Cobertura, Clover, JaCoCo**
- **Issue tracking - Jira, Bugzilla**
- **Containers and Containers Orchestration - Docker, Podman, Kubernetes, Nomad**
- **Tests - Robot, Serenity, Gauge**

***

#### 选择工具/技术时要考虑什么？

在您的答案中，您可以提及以下一项或多项：

* 成熟/稳定与尖端 
* 社区规模
* 体系结构方面-代理与无代理，主控与无主控等 
* 学习曲线

***

#### 解释可变基础架构与不变基础架构

在可变的基础架构范例中，更改是在现有基础架构之上应用的，随着时间的推移，基础架构会建立更改历史记录。Ansible，Puppet和Chef是遵循可变基础架构范例的工具示例。

在不变的基础架构范例中，每项更改实际上都是一个新的基础架构。因此，对服务器的更改将导致新服务器而不是对其进行更新。Terraform是遵循不变基础架构范例的技术示例。

***

#### 解释“软件发行”

[https://venam.nixers.net/blog/unix/2020/03/29/distro-pkgs.html](https://venam.nixers.net/blog/unix/2020/03/29/distro-pkgs.html)

摘自文章：“因此，软件发行是关于机制和社区的责任和决定，以负担和决策来构建可交付的相干软件的组合。”

***

#### 为什么会有多个软件发行版？他们可以有什么区别？

不同的发行版可以关注不同的事物，例如：关注不同的环境（服务器，移动，桌面），支持特定的硬件，专门研究不同的领域（安全性，多媒体等）等。基本上，该软件的不同方面及其支持的功能在每个发行版中都有不同的优先级。

***

#### 什么是软件存储库？

维基百科：“软件存储库，或简称为“ repo”，是软件包的存储位置。通常会存储目录以及元数据。

***

#### 有哪些发行软件的方式？每种方法的优缺点是什么？

* Source - 在版本控制系统中维护构建脚本，以便用户在克隆存储库后可以构建您的应用程序。优点：用户可以快速签出不同版本的应用程序。缺点：需要在用户机器上安装构建工具。
* Archive - 将您所有的应用文件收集到一个存档中（例如tar），并将其交付给用户。优点：用户将所需的一切收集在一个文件中。缺点：更新时需要重复相同的过程，如果有很多依赖项，则不好。
* Package - 根据操作系统的不同，您可以使用OS软件包格式（例如，在RHEL / Fefodra中为RPM）来交付软件，并使用标准打包程序命令来安装，卸载和更新它。优点：程序包管理器负责支持安装，卸载，更新和依赖项管理。缺点：需要管理软件包存储库。
* Images - VM或容器映像，其中包已包含在其中，以便成功运行。优点：一切都是预装的，具有高度的环境隔离性。缺点：需要有关构建和优化图像的知识。

***

#### 您是否熟悉“大教堂和集市模型”？解释每个模型

* Cathedral - 发布软件时发布的源代码
* Bazaar - 源代码始终是公开可用的（例如Linux内核）

***

#### 什么是缓存？如何运作？它为什么如此重要？

缓存是对频繁使用的资源的快速访问，这些资源在计算上很昂贵或IO密集并且不经常更改。从CPU缓存到分布式缓存系统，可以存在几层缓存。常见的是内存缓存和分布式缓存。 <br/>缓存通常是包含某些数据的数据结构，例如哈希表或字典。但是，任何数据结构都可以提供缓存功能，例如集合，排序集合，排序字典等。虽然缓存在许多应用程序中使用，但是如果实施不正确或使用不当，它们可能会产生细微的错误。例如，缓存失效，过期或更新通常是非常困难且困难的。

***

#### 解释有状态和无状态

* 无状态应用程序不会在主机中存储任何数据，因此非常适合于水平扩展和微服务。
* 有状态应用程序依赖于存储来保存状态和数据，通常数据库是有状态应用程序。

***

#### 什么是可靠性？它如何适合DevOps？

在DevOps上下文中使用时，可靠性是系统从基础架构故障或中断中恢复的能力。它的一部分还能够根据您的组织或团队的需求进行扩展。

***

#### “可用性”是什么意思？有什么方法可以跟踪服务的可用性？



***

#### 描述设置某种类型的Web服务器（Nginx，Apache，IIS，Tomcat等）的工作流程



***

#### Web服务器如何工作？



***

#### 说明“开源”



***

#### 请描述您设计和/或实现的service / app / project / ...的体系结构



***

#### 您熟悉哪些类型的测试

样式，单元，功能，API，集成，冒烟，场景，

您应该能够解释您提到的内容。

***

#### 您需要在不同的操作系统（Ubuntu，RHEL等）上定期安装一个软件包（除非它已经存在）。你会怎么做？

有多种方法可以回答这个问题（这里没有对与错）：

* 简单的cron job
* 具有配置管理技术（例如Puppet，Ansible，Chef等）的管道...

***

#### 什么是混沌工程？

[https://en.wikipedia.org/wiki/Chaos_engineering](https://en.wikipedia.org/wiki/Chaos_engineering)

***

#### 什么是错误预算？



***

#### 什么是MTTF（平均故障时间）和MTTR（平均维修时间）？这些指标可帮助我们评估什么？

* MTTF（平均故障时间）正常运行时间，可以定义为系统在出现故障之前运行多长时间。
* MTTR（平均恢复时间）是修复系统所花费的时间。
* MTBF（故障之间的平均时间）产品在操作使用或测试期间的平均连续无故障时间，需要注意的是，这里探讨的MTBF并非一个实测值，而是在产品设计阶段[工程师](https://zh.wikipedia.org/wiki/工程师)依据理论所估算出的参考值[[1\]](https://zh.wikipedia.org/wiki/平均故障間隔#cite_note-1)。使用平均故障间隔时，一般假设故障的系统可以立刻修复。

***

#### 什么是“基础架构即代码”？您熟悉IAC的什么实现？

IAC（基础结构即代码）是一种定义系统基础结构或体系结构的决定性方法。一些实现是适用于Azure和Terraform的ARM模板，可以跨多个云提供商使用。

***

#### 您如何管理构建工件？

构建工件通常存储在存储库中。它们可以在发布管道中用于部署目的。通常，构建工件上有保留期。

***

#### 您正在使用/首选哪种持续集成解决方案，为什么？



***

#### 您熟悉或使用了哪些部署策略？

* 滚动更新
* 蓝绿部署
* 金丝雀部署
* 重新制定策略

***

#### 您加入了一个团队，每个人都在开发一个项目，实践是在测试工作站上本地运行测试，如果测试通过，则将其推送到存储库。目前的流程有什么问题，以及如何对其进行改进？



***

#### 解释测试驱动开发（TDD）



***

#### 解释敏捷软件开发



***

#### 您如何看待以下句子？：“实施或实践DevOps会导致软件更加安全”



***

#### 您知道什么是“post-mortem meeting”吗？您对此有何看法？



***

#### 您如何为CI / CD资源执行计划容量？ （例如服务器，存储设备等）



***

#### 您如何为依赖于其他几个应用程序的应用程序构建/实现CD？



***

#### 您如何衡量CI / CD的质量？您是否使用任何度量标准或KPI来衡量质量？



***

#### 什么是配置漂移？它引起什么问题？

当在具有完全相同的配置和软件的服务器环境中，某台或多台特定服务器正在应用其他服务器无法获得的更新或配置，并且随着时间的推移，这些服务器与所有其他服务器略有不同时，就会发生配置漂移。

这种情况可能会导致难以识别和重现的错误。

***

#### 如何应对配置漂移？

使用期望的状态配置（DSC）实现可以避免配置漂移。所需状态配置可以是一个声明性文件，用于定义系统的状态。有一些工具可以强制执行所需的状态，例如地形或天蓝色的dsc。有增量策略或完整策略。

***

#### 您是否有测试跨项目变更的经验？ （又称交叉依赖）

注意：交叉依赖是指您对单独的项目进行了两个或更多更改，并且您希望在相互构建中对其进行测试，而不是分别测试每个更改。

***

#### 您是否为开源项目做出了贡献？告诉我这种经历



***

### SRE

#### SRE和DevOps有什么区别？



***

#### 什么是SRE团队负责？

可以争论是按公司定义还是全球定义，但至少根据大公司（例如Google）而言，SRE团队负责其服务的可用性，延迟，性能，效率，变更管理，监视，紧急响应和容量规划。

***

## 网络

#### 什么是以太网

以太网只是指当今使用的最常见的局域网（LAN）类型。与跨越较大地理区域的WAN（广域网）相比，LAN是在较小区域（如办公室，大学校园甚至家庭）的计算机连接网络。

***

#### 什么是TCP/IP

一组协议，用于定义两个或多个设备如何相互通信。要了解有关TCP / IP的更多信息，请阅读(http://www.penguintutor.com/linux/basic-network-reference)

***

#### 什么是MAC地址？它是干什么用的？

MAC地址是用于标识网络上各个设备的唯一标识号或代码。

以太网上发送的数据包始终来自MAC地址，然后发送到MAC地址。如果网络适配器正在接收数据包，则它会将数据包的目标MAC地址与适配器自己的MAC地址进行比较。

***

#### 该MAC地址何时使用？：ff:ff:ff:ff:ff:ff:ff



***

#### 什么是IP地址

Internet协议地址（IP地址）是分配给连接到使用Internet协议进行通信的计算机网络的每个设备的数字标签。IP地址具有两个主要功能：主机或网络接口标识和位置寻址。

***

#### 解释子网掩码并给出示例

子网掩码是一个32位数字，用于屏蔽IP地址，并将IP地址分为网络地址和主机地址。子网掩码是通过将网络位设置为所有“ 1”并将主机位设置为所有“ 0”来实现的。在给定的网络中，有两个主机地址是为特殊目的保留的，不能分配给主机。为“ 0”地址分配了一个网络地址，为“ 255”分配了一个广播地址，并且不能将其分配给主机。

例子

```
| Address Class | No of Network Bits | No of Host Bits | Subnet mask     | CIDR notation |
| ------------- | ------------------ | --------------- | --------------- | ------------- |
| A             | 8                  | 24              | 255.0.0.0       | /8            |
| A             | 9                  | 23              | 255.128.0.0     | /9            |
| A             | 12                 | 20              | 255.240.0.0     | /12           |
| A             | 14                 | 18              | 255.252.0.0     | /14           |
| B             | 16                 | 16              | 255.255.0.0     | /16           |
| B             | 17                 | 15              | 255.255.128.0   | /17           |
| B             | 20                 | 12              | 255.255.240.0   | /20           |
| B             | 22                 | 10              | 255.255.252.0   | /22           |
| C             | 24                 | 8               | 255.255.255.0   | /24           |
| C             | 25                 | 7               | 255.255.255.128 | /25           |
| C             | 28                 | 4               | 255.255.255.240 | /28           |
| C             | 30                 | 2               | 255.255.255.252 | /30           |
```

***

#### 什么是私有IP地址？什么时候使用它？



***

#### 解释OSI模型。有几层？每层负责什么？

- Application: user end (HTTP is here)
- Presentation: establishes context between application-layer entities (Encryption is here)
- Session: establishes, manages and terminates the connections
- Transport: transfers variable-length data sequences from a source to a destination host (TCP & UDP are here)
- Network: transfers datagrams from one network to another (IP is here)
- Data link: provides a link between two directly connected nodes (MAC is here)
- Physical: the electrical and physical spec the data connection (Bits are here)

***

#### 对于以下每个方面，确定它属于哪个OSI层：

* **Error correction**
* **Packets routing - Network**
* **Cables and electrical signals - Physical**
* **MAC address - Data link**
* **IP address - Network**
* **Terminate connections - Session**
* **3 way handshake - Transport**

***

#### 您熟悉哪些交付方案？

Unitcast: 一对一通信，其中有一个发送方和一个接收方。

Broadcast: 向网络中的每个人发送消息。地址ff：ff：ff：ff：ff：ff：ff用于广播。使用广播的两个常见协议是ARP和DHCP。

Multicast: 向一组订户发送消息。它可以是一对多或多对多。

***

#### 什么是CSMA / CD？它用于现代以太网吗？

CSMA / CD代表载波侦听多路访问/冲突检测。它的主要重点是管理对共享媒体/总线的访问，在该共享媒体/总线上，在给定的时间点只能传输一个主机。

CSMA / CD算法：

1. 在发送帧之前，它将检查其他主机是否已经在发送帧。
2. 如果没有人发送，它将开始发送帧。
3. 如果两个主机同时传输，则发生冲突。
4. 两位主机都停止发送帧，并且向所有人发送“干扰信号”，通知所有人发生了冲突
5. 他们正在等待随机时间，然后再次发送
6. 每个主机等待随机时间后，便会尝试再次发送帧，因此

***

#### 描述以下网络设备及其之间的区别：

* router
* switch
* hub

***

#### 路由器怎么工作？

路由器是在两个或多个分组交换计算机网络之间传递信息的物理或虚拟设备。路由器检查给定数据包的目标Internet协议地址（IP地址），计算出到达目的地的最佳方式，然后进行相应的转发。

***

#### 什么是NAT

网络地址转换（NAT）是一种过程，其中将一个或多个本地IP地址转换为一个或多个全局IP地址，反之亦然，以便为本地主机提供Internet访问。

***

#### 什么是代理？如何运作？我们需要什么？

代理服务器充当您和Internet之间的网关。这是一个中间服务器，可将最终用户与他们浏览的网站分开。

如果您使用的是代理服务器，则互联网流量会通过代理服务器到达您请求的地址。然后，该请求通过相同的代理服务器返回（此规则有例外），然后代理服务器将从网站接收的数据转发给您。

代理服务器根据您的用例，需求或公司策略提供不同级别的功能，安全性和隐私。

***

#### 什么是TCP？如何运作？什么是三向握手？

TCP三向握手或三向握手是在TCP / IP网络中用于在服务器和客户端之间建立连接的过程。

三向握手主要用于创建TCP套接字连接。它在以下情况下起作用：

1. 客户端节点通过IP网络将SYN数据包发送到同一或外部网络上的服务器。该数据包的目的是询问/推断服务器是否为新连接打开。
2. 目标服务器必须具有可以接受和启动新连接的开放端口。当服务器从客户端节点接收到SYN数据包时，它会做出响应并返回确认收据-ACK数据包或SYN / ACK数据包。
3. 客户端节点从服务器接收SYN / ACK并以ACK数据包作为响应。

***

#### 什么是往返延迟或往返时间？

来自维基百科：“发送信号所花费的时间加上接收到该信号的确认所花费的时间”

额外的问题：什么是局域网的RTT

***

#### SSL握手如何工作？



***

#### TCP和UDP有什么区别？

TCP在客户端和服务器之间建立连接以保证程序包的顺序，另一方面，UDP不会在客户端和服务器之间建立连接，也不处理包顺序。这使UDP比TCP更轻巧，并且是流式传输等服务的理想选择。

***

#### 您熟悉哪些TCP / IP协议？



***

#### 解释"默认网关"

默认网关用作网络计算机用来将信息发送到另一个网络或Internet中的计算机的访问点或IP路由器。

***

#### 什么是ARP？怎么工作？

ARP代表地址解析协议。当您尝试ping本地网络上的IP地址（例如192.168.1.1）时，系统必须将IP地址192.168.1.1转换为MAC地址。这涉及使用ARP解析地址，因此也解析其名称。

系统保留一个ARP查找表，在其中存储有关哪些IP地址与哪些MAC地址相关联的信息。尝试将数据包发送到IP地址时，系统将首先查阅此表以查看其是否已经知道MAC地址。如果缓存了一个值，则不使用ARP。

***

#### 什么是TTL？



***

#### 什么是DHCP？怎么工作？

它代表动态主机配置协议，并为主机分配IP地址，子网掩码和网关。它是这样工作的：

1. 主机进入网络后，广播一条消息以寻找DHCP服务器（DHCP DISCOVER）
2. DHCP服务器将要约消息作为包含租赁时间，子网掩码，IP地址等的数据包发送回（DHCP OFFER）
3. 根据所接受的报价，客户端将发送回广播，通知所有DHCP服务器（DHCP REQUEST）
4. 服务器发送确认（DHCP ACK）

***

#### 什么是SSL隧道？如何运作？



***

#### 什么是Socket？您在哪里可以看到系统中的Sockets列表？





***

#### 什么是IPv6？如果拥有IPv4，为什么要考虑使用它？



***

#### 什么是VLAN？



***

#### 什么是MTU？



***

#### 如果发送的数据包大于MTU，该怎么办？





***

#### 对或错？。 Ping正在使用UDP，因为它不关心可靠的连接



***

#### 什么是SDN



***

#### 什么是ICMP？它是干什么用的？



***

#### 什么是NAT，如何工作？



***

#### 哪些因素影响网络性能？



***

#### 术语“数据平面”和“控制平面”指的是什么？

确切含义通常取决于上下文，但是总体数据平面是指将数据包和/或帧从一个接口转发到另一个接口的所有功能。而控制平面是指使用路由协议的所有功能。

还有“管理平面”，它涉及监视和管理功能。

***

#### 解释生成树协议（STP）



***

#### 什么是链路聚合？为什么使用它？



***

#### 什么是非对称路由？怎么处理呢？



***

#### 您熟悉哪些覆盖（隧道）协议？



***

#### 什么是GRE？如何运作？



***

#### 什么是VXLAN？如何运作



***

#### 什么是SNAT？



***

#### 解释OSPF



***

#### 解释latency



***

#### 解释bandwidth



***

#### 解释throughput



***

#### 执行搜索查询时，更重要的是延迟或吞吐量？以及如何确保对全球基础架构进行管理？

延迟，为了获得良好的延迟，应将搜索查询转发到最近的数据中心。

***

#### 上传视频时，延迟或吞吐量更重要吗？以及如何保证呢？

吞吐量。为了获得良好的吞吐量，应将上传流路由到未充分利用的链接。

***

#### 转发请求时还有什么其他注意事项（延迟和吞吐量除外）？

保持缓存更新（这意味着可以将请求转发到最近的数据中心）

***

#### 解释spine & leaf



***

#### 什么是网络拥塞？是什么原因造成的？



***

#### 您能告诉我有关UDP数据包格式的哪些信息？那么TCP数据包格式呢？有什么不同？



***

#### 什么是指数补偿算法？在哪里使用？



***

#### 使用汉明码，后面的数据字100111010001101的代码字是什么？

**00110011110100011101**

***

#### 给出在应用程序层中找到的协议示例

* **Hypertext Transfer Protocol (HTTP) - used for the webpages on the internet**
* Simple Mail Transfer Protocol (SMTP) - email 
* transmissionTelecommunications Network - (TELNET) - terminal emulation to allow client access to telnet server
* File Transfer Protocol (FTP) - facilitates transfer of files between any two machines
* Domain Name System (DNS) - domain name translation
* Dynamic Host Configuration Protocol (DHCP) - allocates IP addresses, subnet masks and gateways to hosts
* **Simple Network Management Protocol (SNMP) - gathers data of devices on the network**

***

#### 给出在网络层中找到的协议的示例

* **Internet Protocol (IP) - assists in routing packets from one machine to another**
* **Internet Control Message Protocol (ICMP) - lets one know what is going such as error messages and debugging information**

***

#### 什么是HSTS

HTTP严格传输安全性是一个Web服务器指令，该指令通知用户代理和Web浏览器如何通过一开始发送并返回到浏览器的响应标头来处理其连接。这将强制通过HTTPS加密进行连接，而忽略任何脚本调用以通过HTTP加载该域中的任何资源。

**Read more [here]([https://www.globalsign.com/en/blog/what-is-hsts-and-how-do-i-use-it#:~:text=HTTP%20Strict%20Transport%20Security%20(HSTS,and%20back%20to%20the%20browser.)](https://www.globalsign.com/en/blog/what-is-hsts-and-how-do-i-use-it#:~:text=HTTP Strict Transport Security (HSTS,and back to the browser.))**

***

#### SSL和TLS之间有什么区别？



***

## Linux

#### 您对Linux有什么经验？

* Administration
* Troubleshooting & Debugging
* Storage
* Networking
* Development
* **Deployments**

***

#### 与其他目录相比，/ tmp目录有什么特别之处？



***

#### 解释“ ls -l”命令输出中的每个字段

文件权限，链接数，所有者名称，所有者组，文件大小，最后修改的时间戳和目录/文件名

***

#### 解释管道。您如何执行管道？

Linux中使用管道，可以将一个管道的输出发送到另一个管道（也称为重定向）。例如：cat / etc / services | wc -l

***

#### 如何检查您过去执行的命令？



***

#### 您尝试删除文件，但失败。至少说出三种可能发生的原因

* No more disk space
* No more inodes
* **No permissions**

***

#### 什么是systemd

Systemd是一个守护程序（系统'd'，d代表守护程序）。 守护程序是在后台运行的程序，无需用户直接控制，尽管用户可以随时与守护程序对话。

systemd具有许多功能，例如用户进程控制/跟踪，快照支持，禁止锁定。

如果我们以分层方式可视化unix / linux系统，则systemd将直接落在linux内核之后。

**Hardware -> Kernel -> Daemons, System Libraries, Server Display.**

***

#### 在使用systemd的系统上，如何显示日志？

**`journalctl`**

***

#### 描述如何使某个流程/应用程序成为服务



***

#### Where system logs are located?



***

#### 如何在不每次打开文件的情况下跟踪文件的内容？



***

#### 您使用什么来解决网络问题并进行调试？



***

#### 您使用什么来解决磁盘和文件系统问题并进行调试？



***

#### 您使用什么来解决故障和调试process问题？



***

#### 您正在使用什么来调试与CPU相关的问题？



***

#### Explain iostat output



***

#### How to debug binaries?



***

#### 在/ proc中可以找到什么样的信息？



***

#### 您可以在/ proc中创建文件吗？



***

#### CPU负载和利用率之间有什么区别？



***

## 容器

#### 什么是容器？它是做什么用的？

容器是操作系统虚拟化的一种形式。单个容器可用于运行从小型微服务或软件进程到大型应用程序的任何内容。容器内包含所有必要的可执行文件、二进制代码、库和配置文件，使它们易于在不同机器上交付和运行，并具有相同的预期结果。

***

#### 容器与虚拟机 (VM) 有何不同？

容器和虚拟机之间的主要区别在于，容器允许您在操作系统上虚拟化多个工作负载，而在虚拟机的情况下，硬件被虚拟化以运行多台机器，每台机器都有自己的操作系统。您也可以将其视为容器用于操作系统级虚拟化，而 VM 用于硬件虚拟化。

* 容器不需要整个操作系统作为 VM。容器共享系统内核而不是虚拟机。
* 设置容器通常只需要几秒钟，而VMs可能需要几分钟，至少是比容器花更多的时间，因为需要有一个完整的操作系统来启动和初始化，而不像在容器中，你只需要启动应用本身。
* 容器彼此隔离，但不像虚拟机那么具体。恶意用户有可能从容器闯入主机操作系统，反之亦然。

***

#### 在哪些情况下您会使用容器以及您更愿意在哪些情况下使用虚拟机？

在以下情况下，您应该选择 VM：

* 您需要运行一个需要操作系统所有资源和功能的应用程序。
* 你需要完全隔离和安全。

你应该在什么时候选择容器：

* 你需要一个轻量级的解决方案。
* 运行单个应用程序的多个版本或实例。

***

#### 解释 Podman 或 Docker 架构



***

#### 详细描述一下运行`podman/docker run hello-world`会发生什么？

Docker CLI 将您的请求传递给 Docker 守护进程。Docker 守护程序从 Docker Hub 下载映像， Docker 守护程序使用它下载的映像创建一个新容器， Docker 守护程序将输出从容器重定向到 Docker CLI，Docker CLI 将其重定向到标准输出。

***

#### 什么是`dockerd、docker-containerd、docker-runc、docker-containerd-ctr、docker-containerd-shim`？

* dockerd - Docker 守护进程本身。您列表中的最高级别组件，也是唯一列出的“Docker”产品。提供 Docker 的所有优秀 UX 功能。

* (docker-)containerd - 也是一个守护进程，监听 Unix 套接字，公开 gRPC 端点。处理所有底层容器管理任务、存储、镜像分发、网络连接等...

* docker-)containerd-ctr - 直接与 containerd 通信的轻量级 CLI。将其视为“docker”与“dockerd”的关系。

* (docker-)runc - 用于实际运行容器的轻量级二进制文件。处理与 Linux 功能（如 cgroups、命名空间等）的低级接口...

* (docker-)containerd-shim - 在 runC 实际运行容器后，它退出（允许我们没有任何长期运行的进程负责我们的容器）。shim是位于 containerd 和 runc 之间的组件，以促进这一点。

  ![](docker-shim.png)

***

#### 描述 cgroups 和命名空间之间的区别

* cgroup: 控制组提供了一种将任务集及其所有未来子项聚合/划分为具有特定行为的分层组的机制。
* namespace：将全局系统资源包装在一个抽象中，使命名空间内的进程看起来他们拥有自己的全局资源的隔离实例。

Cgroups = limits how much you can use; namespaces = limits what you can see (and therefore use)

Cgroups involve resource metering and limiting: memory CPU block I/O network

Namespaces provide processes with their own view of the system

Multiple namespaces: pid,net, mnt, uts, ipc, user

***

#### 详细描述运行`docker pull image:tag`时会发生什么？

* Docker CLI 将您的请求传递给 Docker 守护进程。 Dockerd Logs 展示了这个过程 docker.io/library/busybox:latest 解析为具有 9 个条目的 manifestList 对象；寻找未知/amd64 匹配

* 找到与 linux/amd64 匹配的媒体类型 application/vnd.docker.distribution.manifest.v2+json，摘要 sha256:400ee2ed939df769d4681023810d2e4fb9479b8401d97003c710d0e20f7c4

* 拉 blob "sha256:61c5ed1cbdf8e801f3b73d906c61261ad916b2532d6756e7c4fbcacb975299fb 下载 61c5ed1cbdf8 到临时文件 /var/lib/docker/tmp/GetImage36

* 在 /var/lib/docker/overlay2/507df36fe373108f19df4b22a07d10de7800f33c9613acb139827ba2645444f7/diff" storage-driver=overlay2 中应用 tar
* 应用 tar sha256: 514c3a3e64d4ebf15f482c9e8909d130bcd53bcc452f0225b0a04744de7b8c43 到 507df36fe373108f19df4b208f19df4b20a0737373737373400000000000000000

***

#### 你如何运行容器？

**`podman run` or `docker run`**

***

#### `podman commit` 有什么作用？你什么时候使用它？

根据容器的更改创建新映像

***

#### 您将如何将数据从一个容器传输到另一个容器？



****

#### 当容器存在时，容器的数据会发生什么变化？



***

#### 解释以下每个命令的作用：

- docker run
- docker rm
- docker ps
- docker pull
- docker build
- docker commit

***

#### 如何删除旧的、未运行的容器？

1. 要删除一个或多个 Docker 映像，请使用 docker container rm 命令，后跟要删除的容器的 ID。
2. docker system prune 命令将删除所有停止的容器、所有悬空的镜像和所有未使用的网络。
3. docker rm $(docker ps -a -q) - 此命令将删除所有停止的容器。命令 docker ps -a -q 将返回所有现有的容器 ID 并将它们传递给将删除它们的 rm 命令。任何正在运行的容器都不会被删除。

***

### Dockerfile

***

#### 什么是Dockerfile？

Docker 可以通过读取 Dockerfile 中的指令自动构建镜像。 Dockerfile 是一个文本文档，其中包含用户可以在命令行上调用以组合图像的所有命令。

***

#### Dockerfile 中的 ADD 和 COPY 有什么区别？

COPY 接受一个 src 和目的地。它只允许您将本地文件或目录从主机（构建 Docker 映像的机器）复制到 Docker 映像本身。

ADD 也允许您这样做，但它也支持 2 个其他来源。首先，您可以使用 URL 而不是本地文件/目录。其次，您可以将源中的 tar 文件直接提取到目标中。

虽然 ADD 和 COPY 在功能上类似，但一般来说，COPY 是首选。那是因为它比 ADD 更透明。COPY 仅支持将本地文件基本复制到容器中，而 ADD 具有一些不是很明显的功能（例如仅本地 tar 提取和远程 URL 支持）。

***

#### Dockerfile 中的 CMD 和 RUN 有什么区别？

RUN 允许您在 Docker 映像内执行命令。这些命令在构建时执行一次，并作为新层写入 Docker 镜像。

CMD 是当您启动构建的镜像时容器默认执行的命令。一个 Dockerfile 只能有一个 CMD。您可以说 CMD 是 Docker 运行时操作，这意味着它不会在构建时执行。当您运行image时会发生这种情况。运行中的镜像称为容器。

***

#### 您是否执行与 Dockerfile 相关的任何检查或测试？

对此的常见答案是使用 hadolint 项目，它是基于 Dockerfile 最佳实践的 linter。

***

#### 解释什么是 Docker compose 以及它的用途？

Compose 是一个用于定义和运行多容器 Docker 应用程序的工具。借助 Compose，您可以使用 YAML 文件来配置应用程序的服务。然后，使用单个命令，从配置中创建并启动所有服务。

例如，您可以使用它来设置 ELK 堆栈，其中服务是：elasticsearch、logstash 和 kibana。每个都在自己的容器中运行。

***

#### 描述使用Docker Compose的过程

* 在 docker-compose.yml 文件中定义要一起运行的服务
* Run `docker-compose up` to run the services

***

#### 解释 Docker 互锁



***

#### 你可以在哪里存储 Docker 镜像？



***

#### 什么是Docker Hub



***

#### Docker Hub 和 Docker 云有什么区别？

Docker Hub 是一个原生的 Docker 注册服务，它允许你运行 pull 和 push 命令来从 Docker Hub 安装和部署 Docker 镜像。

Docker Cloud 建立在 Docker Hub 之上，因此与 Docker Hub 相比，Docker Cloud 为您提供了更多的选项/功能。一个例子是 Swarm 管理，这意味着您可以在 Docker Cloud 中创建新的集群。

***

#### 什么是Docker仓库？



***

#### 解释什么是image layers?

Docker 镜像由一系列层构建而成。每一层代表镜像的 Dockerfile 中的一条指令。除了最后一层之外，每一层都是只读的。每一层只是与它之前的层的一组差异。这些层堆叠在彼此的顶部。当您创建一个新容器时，您会在底层层之上添加一个新的可写层。这一层通常被称为“容器层”。对正在运行的容器所做的所有更改，例如写入新文件、修改现有文件和删除文件，都将写入这个薄的可写容器层。容器和图像之间的主要区别在于顶部可写层。添加新数据或修改现有数据的所有写入容器都存储在此可写层中。当容器被删除时，可写层也被删除。底层图像保持不变。因为每个容器都有自己的可写容器层，所有的变化都存储在这个容器层中，所以多个容器可以共享对同一个底层镜像的访问，同时又拥有自己的数据状态。

***

#### 您熟悉哪些与使用容器相关的最佳实践？



***

#### 您如何管理 Docker 中的持久存储？



***

#### 如何从容器内部连接到运行容器的主机的本地主机？



***

#### 如何将文件从 Docker 容器复制到主机，反之？



***

## Kubernetes

***

#### 什么是Kubernetes？为什么大家要使用它？

Kubernetes 是一个开源系统，用于自动部署、扩展和管理容器化应用程序。

要了解 Kubernetes 的优点，让我们看一些示例：

* 您想在多个不同位置的容器中运行某个应用程序。当然，如果是 2-3 个服务器/位置，您可以自己完成，但将其扩展到其他多个位置可能具有挑战性。
* 跨数百个容器执行更新和更改
* 处理当前负载需要放大（或缩小）的情况。

***

#### 什么是k8s集群

红帽定义：“Kubernetes 集群是一组用于运行容器化应用程序的节点机器。如果您正在运行 Kubernetes，那么您正在运行一个集群。一个集群至少包含一个工作节点和一个主节点。"

Read more [here](https://www.redhat.com/en/topics/containers/what-is-a-kubernetes-cluster)

***

### Kubernetes Nodes

#### 什么是Node？

A node is a virtual machine or a physical server that serves as a worker for running the applications.
It's recommended to have at least 3 nodes in Kubernetes production environment.

***

#### 主节点负责什么？

master 协调集群中的所有工作流：

* 调度应用
* 管理期望状态
* 滚动更新

***

#### 我们需要工作节点做什么？

工作节点是运行应用程序和工作负载的节点

***

#### 什么是kubectl？



***

#### 运行哪个命令查看你的节点？

`kubectl get nodes`

***

#### 主节点有哪些组件？


  * API Server - the Kubernetes API. All cluster components communicate through it
  * Scheduler - assigns an application with a worker node it can run on
  * Controller Manager - cluster maintenance (replications, node failures, etc.)
  * etcd - stores cluster configuration

***

#### 工作节点有哪些组件？


  * Kubelet - an agent responsible for node communication with the master.
  * Kube-proxy - load balancing traffic between app components
  * Container runtime - the engine runs the containers (Podman, Docker, ...)

****

### Kubernetes Pod

#### 解释什么是pod？



***

#### 使用nginx:alpine镜像部署一个名为”my-pod“的pod

`kubectl run my-pod --image=nginx:alpine --restart=Never`

***

#### 一个Pod可以包含多少个container?

多个容器，不过大多数情况下通常每个pod只有一个容器。

***

#### ”pods are ephemeral(短暂的)“意味着什么？

这意味着它们最终会死亡并且pod无法愈合，因此建议您不要直接创建它们。

***

#### 运行哪个命令可以查看所有命名空间下的所有pod？

`kubectl get pods --all-namespaces`

***

#### 如何删除一个pod？

`kubectl delete pod $POD_NAME`

***

### Kubernetes Deployment

#### 什么是Deployment？



***

#### 怎样创建一个deployment？


```
cat << EOF | kubectl create -f -
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx
EOF
```

***

#### 如何编辑一个deployment？

`kubectl edit deployment $NAME`

***

#### 当你编辑一个deployment并且改变了镜像后发生了什么？

pod将被终止，一个新的pod将被创建。

同时，当你查看replicaset时，你将看到老的replica已经没有任何pods，并且一个新的replicaset被创建

***

#### 如何删除一个deployment？

`kubectl delete deployment $NAME` 

`kubectl delele -f deployment.yaml`

***

#### 当你删除一个deployment时发生了什么？

与deployment关联的pod将别终止，replicaset将被移除。

***

### Kubernetes Service

#### 什么是Service？

将在一组 Pod 上运行的应用程序公开为网络服务的抽象方法。

简而言之，它允许您通过将永久 IP 地址附加到某个 pod 等方式来公开服务。

**read more [here](https://kubernetes.io/docs/concepts/services-networking/service)**

***

#### Service类型有哪些？

* ClusterIP
* NodePort
* LoadBalancer
* ExternalName

**More on this topic [here](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types)**

***

#### 如何获取一个service的信息？

`kubectl describe service $NAME`

***

#### 如何验证某个服务是否将请求转发到 pod?

`kubectl describe service $NAME`找到"Endpoints"相关的信息是否匹配`kubectl get pod -o wide`的ip

***

#### external和internal service的区别？



***

#### 如何将下面的service暴露成到外部服务？




```yaml
spec:
  selector:
    app: some-app
  ports:
    - protocol: TCP
      port: 8081
      targetPort: 8081
```

Adding `type: LoadBalancer` and `nodePort`

```yaml
spec:
  selector:
    app: some-app
  type: LoadBalancer
  ports:
    - protocol: TCP
      port: 8081
      targetPort: 8081
      nodePort: 32412
```

***

### Kubernetes Ingress

#### 什么是Ingress？


From Kubernetes docs: "Ingress exposes HTTP and HTTPS routes from outside the cluster to services within the cluster. Traffic routing is controlled by rules defined on the Ingress resource."

Read more [here](https://kubernetes.io/docs/concepts/services-networking/ingress/)

***

#### 完成下面的Ingress配置

<details>
<summary>Complete the following configuration file to make it Ingress


```yaml
metadata:
  name: someapp-ingress
spec:
```


There are several ways to answer this question.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: someapp-ingress
spec:
  rules:
  - host: my.host
    http:
      paths:
      - backend:
          serviceName: someapp-internal-service
          servicePort: 8080
```

***

#### 解释"host","http","backend"指示


```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: someapp-ingress
spec:
  rules:
  - host: my.host
    http:
      paths:
      - backend:
          serviceName: someapp-internal-service
          servicePort: 8080
```

* `host`: 访问有效域名标识
* `http`: 用于指定传入请求将使用 http 转发到内部服务的 http 行。
* `backend`：内部service的名称和端口

***

#### 什么是Ingress Controller？

Ingress 的实现。它基本上是另一个 pod（或一组 pod），负责评估和处理 Ingress 规则，并管理所有重定向。

有多种 Ingress Controller 实现（来自 Kubernetes 的是 Kubernetes Nginx Ingress Controller）。

***

#### 使用Ingress的案例？

* 多个子域名(多个主机条目，每个条目都有自己的服务)。
* 一个域名有多个服务(多个路径与不同的service做映射)

***

#### 如何列出ingress在你的namespace？

`kubectl list ingress`

***

### Kubernetes Configuration File

#### 配置文件结构


It has three main parts:

1. Metadata
2. Specification
3. Status (this automatically generated and added by Kubernetes)

***

#### 获取deployment的最新配置

`kubectl get deployment [deployment_name] -o yaml`

***

#### k8s从哪里获取状态数据？

etcd

***

### Kubernetes etcd

#### 什么是etcd？



***

### Namespace

#### 什么是namespace？

命名空间允许您将集群拆分为虚拟集群，您可以在其中以有意义且与其他组完全分离的方式对应用程序进行分组。

***

#### 为什么使用命名空间？使用一个命名空间有什么问题？

单独使用默认命名空间时，随着时间的推移，很难了解您在集群中管理的所有应用程序。命名空间使将应用程序组织成有意义的组变得更容易，例如所有监控应用程序的命名空间和所有安全应用程序的命名空间等。

命名空间也可用于管理蓝/绿环境，其中每个命名空间可以包含不同版本的应用程序，还可以共享其他命名空间（如日志记录、监控等命名空间）中的资源。

命名空间的另一个用例是一个集群、多个团队。当多个团队使用同一个集群时，他们最终可能会互相踩踏。例如，如果他们最终创建了一个同名的应用程序，这意味着其中一个团队覆盖了另一个团队的应用程序，因为 Kubernetes 中不能有太多同名的应用程序（在同一个命名空间中）。

***

#### 哪些特殊命名空间在创建集群时自动创建？


* default
* kube-system
* kube-public
* kube-node-lease

***

#### kube-system命名空间中有啥？


* Master and Kubectl processes
* System processes

***

#### kube-public命名空间中有啥？


* A configmap, which contains cluster information
* Publicely accessible data

***

#### kube-node-lease 命名空间有啥？

It holds information on hearbeats of nodes. Each node gets an object which holds information about its availability.

***

#### 什么是”Resource Quota“？



***

#### 如何创建一个Resource Quota？

`kubectl create quota some-quota --hard-cpu=2,pods=2`

***

### Kubernetes Commands

#### kubectl exec



***

#### kubectl get



***

#### kubectl api-resources



***

#### kubectl apply



***

#### kubectl describe



***

#### kubectl expose



***

#### kubectl run



***

#### kubectl create



***

#### kubectl delete



***

#### kubectl logs





***

#### kubectl scale



***

#### 什么是Minikube



***

#### 怎么监控k8s



***

#### 你怀疑其中一个pod有问题，你会怎么做？



***

#### k8s调度器做了什么？



***

#### 当你停止kubelet时，正在运行的pod会发生什么？



***

#### 当pod使用的内存超出时会发生什么？



***

#### 描述回滚的工作原理？



***

#### 什么是control loop?如何工作的？

Explained [here](https://www.youtube.com/watch?v=i9V4oCa5f9I)

***

### Kubernetes Operator

#### 什么是Operator？


Explained [here](https://kubernetes.io/docs/concepts/extend-kubernetes/operator)

"Operators are software extensions to Kubernetes that make use of custom resources to manage applications and their components. Operators follow Kubernetes principles, notably the control loop."

***

#### 为什么我们需要Operators？


The process of managing stateful applications in Kubernetes isn't as straightforward as managing stateless applications where reaching the desired status and upgrades are both handled the same way for every replica. In stateful applications, upgrading each replica might require different handling due to the stateful nature of the app, each replica might be in a different status. As a result, we often need a human operator to manage stateful applications. Kubernetes Operator is suppose to assist with this.

This also help with automating a standard process on multiple Kubernetes clusters

***

#### Operator由哪些组件组成？


1. CRD (custom resource definition)
2. Controller - Custom control loop which runs against the CRD

***

#### Operator如何工作？

It uses the control loop used by Kubernetes in general. It watches for changes in the application state. The difference is that is uses a custom control loop.
In additions.

In addition, it also makes use of CRD's (Custom Resources Definitions) so basically it extends Kubernetes API.

***

#### Operator框架有什么组成？


1. Operator SDK - allows developers to build operators
2. Operator Lifecycle Manager - helps to install, update and generally manage the lifecycle of all operators
3. Operator Metering - Enables usage reporting for operators that provide specialized services

***

#### 描述Operator Lifecycle Manager的细节

It's part of the Operator Framework, used for managing the lifecycle of operators. It basically extends Kubernetes so a user can use a declarative way to manage operators (installation, upgrade, ...).

***

#### 什么是Kubeconfig？使用它做什么？



***

#### 解释StatefulSet



***

#### Kubernetes ReplicaSet

#### ReplicaSet的目的是啥？



***

#### replicaset如何工作？



***

#### 当replicaSet死亡时会发生什么？



***

#### Kubernetes Secrets

#### 解释secrets

Secrets let you store and manage sensitive information (passwords, ssh keys, etc.)

***

#### 如何创建Secret



***

#### Secret 类型有哪些？



***

#### Kubernetes Storage

#### 解释”持久卷“。为什么我们需要它？

Persistent Volumes allow us to save data so basically they provide storage that doesn't depend on the pod lifecycle.

***

#### 持久卷类型



***

#### 什么是PersistentVolumeClaim?



***

#### 解释Storage Class



***

#### 解释Dynamic和Static Provisioning



***

#### 解释Access Modes





***

#### 解释Reclaim Policy？



***

#### Kubernetes Access Control

#### 什么是RBAC？



***

#### Role和RoleBinding



#### Role和ClusterRole的区别？



***

#### Kubernetes Misc

#### 解释什么是k8s Service Discovery



***

#### kube proxy做了什么？



***

#### 解释ConfigMap

Separate configuration from pods.
It's good for cases where you might need to change configuration at some point but you don't want to restart the application or rebuild the image so you create a ConfigMap and connect it to a pod but externally to the pod.

Overall it's good for:

* Sharing the same configuration between different pods
* Storing external to the pod configuration

***

#### 如何使用ConfigMaps


1. Create it (from key&value, a file or an env file)
2. Attach it. Mount a configmap as a volume

***

#### 解释"Horizontal Pod Autoscaler"

Scale the number of pods automatically on observed CPU utilization.

***

#### 解释Liveness probe



***

#### 解释Readiness probe



***

#### 云原生是什么意思？



***

#### 解释关于 kubernetes 的基础设施的宠物和牛方法



***

####描述您如何在 K8s 中运行容器化 Web 应用程序，该应用程序应该可以从公共 URL 访问。



***

#### 如何某些应用不可达，你如何排查故障？



***

#### 描述 Kubernetes 世界中有哪些 CustomResourceDefinitions？它们可以用来做什么？



***

#### k8s中调度如何工作？


The control plane component kube-scheduler asks the following questions,

1. What to schedule? It tries to understand the pod-definition specifications
2. Which node to schedule? It tries to determine the best node with available resources to spin a pod
3. Binds the Pod to a given node

View more [here](https://www.youtube.com/watch?v=rDCWxkvPlAw)

***

#### 如何使用lables和selectors？



***

#### 解释CronJob，什么时候使用它？



***

#### QoS种类？


* Guaranteed
* Burstable
* BestEffort

***

#### Istio

#### 什么是Istio？什么时候用它？

***

## Go

