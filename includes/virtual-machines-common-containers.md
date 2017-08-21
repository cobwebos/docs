Azure 云解决方案以虚拟机为基础构建（模拟物理计算机硬件），可实现软件部署的灵活打包，以及相比于物理硬件更好的资源整合。 [Docker](https://www.docker.com) 容器和 docker 生态系统显著扩展了开发、提供和管理分发式软件的方式。 容器中的应用程序代码与主机 VM 以及同一 VM 上的其他容器隔离。 这种隔离为用户提供更多开发和部署灵活性。

Azure 提供以下 Docker 值：

* 提供[许多](../articles/virtual-machines/linux/docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)[不同的](../articles/virtual-machines/linux/dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)方式，可根据具体需求为容器创建 Docker 主机
* [Azure 容器服务](https://azure.microsoft.com/documentation/services/container-service/)使用 **marathon** 和 **swarm** 等协调器来创建容器主机的群集。
* [Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md) 和[资源组模板](../articles/resource-group-authoring-templates.md)可简化复杂的分布式应用程序的部署和更新
* 可与许多专有和开放源配置管理工具集成

而且由于可通过编程方式在 Azure 上创建 VM 和 Linux 容器，因此还可以使用 VM 和容器*协调*工具来创建多组虚拟机 (VM)，并在 Linux 容器和现在的 [Windows 容器](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview)中部署应用程序。

本文不仅从较高水平讨论了这些概念，还包含了很多链接，可帮助你了解与 Azure 上的容器和群集使用相关的详细信息、教程和产品。 如果已了解全部内容，而只是需要这些链接，可在[使用容器的工具](#tools-for-working-with-azure-vms-and-containers)中找到这些链接。

## <a name="the-difference-between-virtual-machines-and-containers"></a>虚拟机与容器之间的区别
虚拟机在[虚拟机监控程序](http://en.wikipedia.org/wiki/Hypervisor)提供的单独硬件虚拟化环境中运行。 在 Azure 中，[虚拟机](https://azure.microsoft.com/services/virtual-machines/)服务可为用户处理所有事务：用户只需选择操作系统并进行配置（或上传自定义 VM 映像），&mdash;由此创建虚拟机即可。 虚拟机是经过时间考验、“久经沙场”的技术，可使用许多工具来管理 OS 及其包含的应用。  VM 中的应用相对于主机 OS 处于隐藏状态。 从 VM 上的应用程序或用户的视角来看，VM 看起来是自治物理计算机。

[Linux 容器](http://en.wikipedia.org/wiki/LXC)以及使用 Docker 工具创建和托管的容器不使用虚拟机监控程序来提供隔离。 使用容器时，容器主机会利用 Linux 内核的流程和文件系统隔离功能，仅向容器显示其应用、特定的内核功能及其自己的隔离文件系统。 从容器内运行的应用的角度来看，该容器似乎是一个独特的 OS 实例。 包含在容器内的应用无法查看该容器以外的流程或其他任何资源。

在 Docker 容器中使用的资源大大少于在 VM 中使用的。 Docker 容器所采用的应用程序隔离和执行模型不共享 Docker 主机的内核。 容器的磁盘空间占用量要少得多，因为它不包括整个 OS。 启动时间以及所需磁盘空间显著低于 VM 中的。
对于在 Windows 上运行的应用，Windows 容器具有与 Linux 容器相同的优点。 Windows 容器支持 Docker 映像格式和 Docker API，但还可以使用 PowerShell 对它们进行管理。 两种容器运行时随 Windows 容器、Windows Server 容器和 Hyper-V 容器提供。 Hyper-V 容器通过在超级优化 VM 中托管每个容器而具有附加的隔离层。 若要了解有关 Windows 容器的详细信息，请参阅[关于 Windows 容器](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview)。 若要开始在 Azure 中使用 Windows 容器，请了解如何[部署 Azure 容器服务群集](../articles/container-service/dcos-swarm/container-service-deployment.md)。

## <a name="what-are-containers-good-for"></a>容器适合于哪些方面？

容器可以提高：

* 应用程序代码进行广泛开发和共享的速度
* 对应用进行测试的速度和置信度
* 对应用进行部署的速度和置信度

容器是在容器主机（一种操作系统）上和 Azure（指 Azure 虚拟机）中执行。 即使已经喜欢容器的设计理念，仍然需要一个 VM 基础结构用于托管容器，但好处是容器可以在任意 VM 上运行（不过举例来说，容器是否希望 Linux 或 Windows 执行环境将具有重要意义）。


## <a name="what-are-containers-good-for"></a>容器适合于哪些方面？
容器在很多方面用处很大，和 [Azure 云服务](https://azure.microsoft.com/services/cloud-services/)和 [Azure Service Fabric](../articles/service-fabric/service-fabric-overview.md) 一样，容器鼓励创建面向单一服务、微服务的分布式应用程序，其中的应用程序设计是基于更多小的、可组合的部件，而不是更大、耦合更强的组件。

在公有云环境（例如 Azure）中尤其如此，可随时随地租用 VM。 不仅能获得独立且快速的部署和协调工具，还能做出更高效的应用程序基础结构决策。

例如，当前你可能有一个包含 9 个大规模 Azure VM 的部署，适用于高度可用的分布式应用程序。 如果此应用程序的组件可在容器中进行部署，则你可能只能使用 4 个 VM 并将应用程序组件部署在 20 个容器内，以实现冗余和负载均衡。

当然，这只是一个示例，但是如果能在自己的方案中做到这一点，那么就能利用更多容器而非更多 Azure VM 来调整以适应用量尖峰，并能比从前更高效地使用剩余的总体 CPU 负载。

此外，还有许多方案不适合于微服务方法；你最了解微服务和容器是否能带来帮助。

### <a name="container-benefits-for-developers"></a>容器为开发人员带来的好处
一般而言，很容易能看出容器技术代表了技术的进步，但还有其他更具体的优点好处。 我们以 Docker 容器为例展开讨论。 本主题目前不会深入探究 Docker（有关这部分内容，请参阅[什么是 Docker？](https://www.docker.com/whatisdocker/)，或查阅 [wikipedia](http://wikipedia.org/wiki/Docker_%28software%29)），但 Docker 及其生态系统可为开发人员和 IT 专业人士带来巨大好处。

开发人员很快就会喜欢上 Docker 容器，因为首先它有助于轻松使用 Linux 和 Windows 容器：

* 他们可以使用简单的增量命令来创建易于部署的固定映像，并可使用 dockerfile 自动构建这些映像。
* 通过对[公有](https://registry.hub.docker.com/)或[私有 docker 注册表](../articles/virtual-machines/virtual-machines-linux-docker-registry-in-blob-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)使用简单的 [git](https://git-scm.com/) 式推送和拉取命令，就可对这些映像进行共享
* 他们可以考虑用单独的应用程序组件来代替计算机
* 他们可以使用大量适合于 docker 容器的工具以及不同的基础映像

### <a name="container-benefits-for-operations-and-it-professionals"></a>容器可为运营和 IT 专业人士带来的好处
IT 和运营专家还可以从容器与虚拟机的组合中获益。

* 包含在容器内的服务独立于 VM 主机执行环境
* 包含在容器内的代码能证实是相同的
* 包含在容器内的服务可快速启动、停止并在开发、测试和生产环境之间快速移动

这些以及其他更多特性对大型企业具有很大的吸引力，这些企业中的专业信息技术组织的工作是将资源（包括纯粹的处理能力）合理分配到多项任务中，这些任务不仅是为了维持业务，更是要提高客户满意度并实现目标。 小型企业、ISV 和初创企业也具有完全相同的要求，但他们对此的描述可能有所不同。

## <a name="what-are-virtual-machines-good-for"></a>虚拟机适合于哪些方面？
虚拟机是云计算的支柱，这一点不会发生改变。 如果虚拟机启动速度更慢、磁盘占用空间更大且不能直接映射到微服务体系结构，则它们具有非常重要的好处：

1. 默认情况下，它们可为主机计算机提供更稳健的默认安全保护
2. 它们支持任一种常用的操作系统和应用程序配置
3. 它们提供长久持续的工具生态系统以用于命令和控制
4. 它们提供执行环境以托管容器

最后一项很重要，因为包含在容器内的应用程序仍然需要特定的操作系统和 CPU 类型，具体取决于应用程序的调用。 务必要牢记的一点是，在 VM 上安装容器是因为其中包含希望部署的应用程序；容器不能代替 VM 或操作系统。

## <a name="high-level-feature-comparison-of-vms-and-containers"></a>VM 和容器的高级功能对比
下表从非常高的水平描述了 VM 与 Linux 容器之间存在的功能差异类型（没有太多额外工作）。 请注意，一些功能可能或多或少都能满足需要，具体取决于自己的应用程序需求，而且与所有软件一样，额外工作可提供增强的功能支持，在安全性方面尤其如此。

| 功能 | VM | 容器 |
|:--- | --- | --- |
| “默认”安全支持 |达到更高程度 |程度略低 |
| 所需的磁盘内存 |完整操作系统加上应用 |仅限应用要求 |
| 启动所需时间 |明显更长：操作系统引导和应用程序加载 |明显短很多：仅应用程序需要启动，因为内核已经在运行 |
| 可移植性 |通过适当的准备实现可移植 |在映像格式内可移植；通常更小 |
| 映像自动化 |根据操作系统和应用差别很大 |[Docker 注册表](https://registry.hub.docker.com/)；其他 |

## <a name="creating-and-managing-groups-of-vms-and-containers"></a>创建和管理多组 VM 和容器
这时候，任何架构师、开发人员或 IT 运营专家可能会想“我可以自动完成所有这些事情；这真的是数据中心即服务！”。

说得对，的确可以这样，而且有任意数量的系统，可能已经使用了其中的许多系统，可管理 Azure VM 组和使用脚本（通常使用 [CustomScriptingExtension for Windows](https://msdn.microsoft.com/library/azure/dn781373.aspx) 或 [CustomScriptingExtension for Linux](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)）注入自定义代码。 用户可以（可能已经）使用 PowerShell 或者 Azure CLI 脚本实现 Azure 自动部署。

随后，这些功能通常迁移到 [Puppet](https://puppetlabs.com/) 和 [Chef](https://www.chef.io/) 等工具，以实现大规模的自动创建和配置 VM。 （此处有一些链接指向[在 Azure 中使用这些工具](#tools-for-working-with-containers)的说明。）

### <a name="azure-resource-group-templates"></a>Azure 资源组模板
最近，Azure 发布了 [Azure 资源管理](../articles/resource-manager-deployment-model.md) REST API，并更新了 PowerShell 和 Azure CLI 工具，使之更容易使用。 用户可以使用包含 Azure 资源管理 API 的 [Azure Resource Manager 模板](../articles/resource-group-authoring-templates.md)部署、修改或重新部署整个应用程序拓扑，使用：

* [使用模板的 Azure 门户](https://github.com/Azure/azure-quickstart-templates)&mdash;提示，使用“DeployToAzure”按钮
* [Azure CLI](../articles/virtual-machines/linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="deployment-and-management-of-entire-groups-of-azure-vms-and-containers"></a>部署和管理整个组的 Azure VM 和容器
有多个常用系统可部署整个组的 VM，并在其上安装 Docker（或其他 Linux 容器主机系统）作为可自动化组。 有关直接链接，请参阅下文的[容器和工具](#containers-and-vm-technologies)部分。 有许多系统都或多或少能实现这一点，此列表不能尽录。 根据技能组合和具体情况，它们可能有用，也可能不发挥作用。

Docker 有自己的 VM 创建工具集 ([docker-machine](../articles/virtual-machines/linux/docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) 和一个负载均衡、docker-container 群集管理工具 ([swarm](../articles/virtual-machines/virtual-machines-linux-docker-swarm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json))。 此外，[Azure Docker VM 扩展程序](https://github.com/Azure/azure-docker-extension/blob/master/README.md)附带了默认的 [`docker-compose`](https://docs.docker.com/compose/) 支持，此功能可在多个容器内部署已配置的应用程序容器。

另外，用户还可以尝试使用 [Mesosphere 的数据中心操作系统 (DCOS)](http://docs.mesosphere.com)。 DCOS 基于开源的 [mesos](http://mesos.apache.org/)“分布式系统内核”，有助于用户将数据中心视为一个可寻址服务进行处理。 DCOS 拥有适合于多个重要系统（[Spark](http://spark.apache.org/) 和 [Kafka](http://kafka.apache.org/) 及其他）的内置程序包，以及 [Marathon](https://mesosphere.github.io/marathon/)（一种容器控制系统）和 [Chronos](https://mesos.github.io/chronos/)（一种分布式计划程序）等内置服务。 Mesos 是借鉴了从 Twitter、AirBnb 和其他网络级企业学到的经验。 还可以使用 **swarm** 作为协调引擎。

另外，[kubernetes](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/) 是一种用于 VM 和容器组管理的开放源系统，借鉴了从 Google 学习的经验。 用户甚至可以将 [kubernetes 与 weave 配合使用，以提供网络支持](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)。

[Deis](http://deis.io/overview/) 是一种开放源“平台即服务”(PaaS)，可帮助在自己的服务器上轻松部署和管理应用程序。 Deis 构建在 Docker 和 CoreOS 的基础之上，可提供轻型 PaaS，其中包含一个受 Heroku 启发的工作流。

[CoreOS](https://coreos.com/os/docs/latest/booting-on-azure.html) 是一种 Linux 分发，可提供优化的占用空间和 Docker 支持，且具有名为 [rkt](https://github.com/coreos/rkt) 的自有容器系统，还有一个名为 [fleet](https://coreos.com/fleet/docs/latest/) 的容器组管理工具。

Ubuntu 是另一个非常受欢迎的 Linux 分发，可以非常好地支持 Docker，还支持 [Linux（LXC 式）群集](https://help.ubuntu.com/lts/serverguide/lxc.html)。

## <a name="tools-for-working-with-azure-vms-and-containers"></a>使用 Azure VM 和容器所需的工具
使用容器和 Azure VM 时需借助工具。 本节仅列出了部分与容器和组相关的最有用或最重要的概念和工具，以及与其搭配使用的更大配置和协调工具。

> [!NOTE]
> 此领域正迅速发生着令人惊奇的变化，尽管我们尽最大努力使本主题及其中的链接保持最新，依然无法做到详尽。 请确保搜索有兴趣的科目，以便掌握最新动态！
>
>

### <a name="containers-and-vm-technologies"></a>容器和 VM 技术
部分 Linux 容器技术：

* [Docker](https://www.docker.com)
* [LXC](https://linuxcontainers.org/)
* [CoreOS 和 rkt](https://github.com/coreos/rkt)
* [开放容器项目](http://opencontainers.org/)
* [RancherOS](http://rancher.com/rancher-os/)

Windows 容器链接：

* [Windows 容器](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview)

Visual Studio Docker 链接：

* [Visual Studio Tools for Docker](https://docs.microsoft.com/en-us/dotnet/core/docker/visual-studio-tools-for-docker)

Docker 工具：

* [Docker 后台程序](https://docs.docker.com/installation/#installation)
* Docker 客户端
  * [Windows Docker Client on Chocolatey](https://chocolatey.org/packages/docker)
  * [Docker 安装说明](https://docs.docker.com/installation/#installation)

Microsoft Azure 上的 Docker：

* [适用于 Azure 上 的 Linux 的 Docker VM 扩展](../articles/virtual-machines/linux/dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure Docker VM 扩展用户指南](https://github.com/Azure/azure-docker-extension/blob/master/README.md)
* [从 Azure 命令行接口 (Azure CLI) 使用 Docker VM 扩展](../articles/virtual-machines/linux/classic/cli-use-docker.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [在 Azure 门户中使用 Docker VM 扩展](../articles/virtual-machines/linux/classic/portal-use-docker.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [如何在 Azure 上使用 docker-machine](../articles/virtual-machines/linux/docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [如何在 Azure 上将 docker 与 swarm 一起使用](../articles/virtual-machines/virtual-machines-linux-docker-swarm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [在 Azure 上使用 Docker 和 Compose 入门](../articles/virtual-machines/linux/docker-compose-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [使用 Azure 资源组模板在 Azure 上快速创建 Docker 主机](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)
* [对包含在容器内的应用程序的 `compose`](https://github.com/Azure/azure-docker-extension#11-public-configuration-keys) 提供内置支持
* [在 Azure 上实现 Docker 专用注册表](../articles/virtual-machines/virtual-machines-linux-docker-registry-in-blob-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Linux 分发和 Azure 示例：

* [CoreOS](https://coreos.com/os/docs/latest/booting-on-azure.html)

配置、群集管理和容器协调：

* [CoreOS 上的 Fleet](https://coreos.com/fleet/docs/latest/)
* Deis

  * [使用 CoreOS 和 Weave 自动部署 Kubernetes 群集的完整指南](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
  * [Kubernetes Visualizer](https://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure/)
* [Mesos](http://mesos.apache.org/)

  * [Mesosphere 的数据中心操作系统 (DCOS)](https://docs.mesosphere.com/1.7/overview/design/azure-container-service/)
* [Jenkins](https://jenkins.io/) 和 [Hudson](http://hudson-ci.org/)

  * [Jenkins VM Agent plugin for Azure](https://wiki.jenkins.io/display/JENKINS/Azure+VM+Agents+plugin)（适用于 Azure 的 Jenkins VM Agent 插件）
  * [GitHub repo：适用于 Azure 的 Jenkins Storage 插件](https://github.com/jenkinsci/windows-azure-storage-plugin)
  * [第三方：适用于 Azure 的 Hudson Slave 插件](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
  * [第三方：适用于 Azure 的 Hudson Storage 插件](https://github.com/hudson3-plugins/windows-azure-storage-plugin)
* [Azure 自动化](https://azure.microsoft.com/services/automation/)

  * [视频：如何在 Linux VM 上使用 Azure 自动化](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)
* Powershell DSC for Linux

  * [博客：如何执行 Powershell DSC for Linux](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
  * [GitHub：Docker 客户端 DSC](https://github.com/anweiss/DockerClientDSC)

## <a name="next-steps"></a>后续步骤
了解 [Docker](https://www.docker.com) 和 [Windows 容器](https://msdn.microsoft.com/virtualization/windowscontainers/about/about_overview)。

<!--Anchors-->
[microservices]: http://martinfowler.com/articles/microservices.html
[microservice]: http://martinfowler.com/articles/microservices.html
<!--Image references-->
