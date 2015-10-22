<properties 
   pageTitle="Service Fabric 概述" 
   description="由微服务组成的应用程序的 Service Fabric 概述。Service Fabric 是一个分布式系统平台，可用于构建面向云的可缩放、可靠且易管理的应用程序" 
   services="service-fabric" 
   documentationCenter=".net" 
   authors="msfussell" 
   manager="timlt" 
   editor="masnider"/>

<tags
   ms.service="service-fabric"
   ms.date="08/25/2015"
   wacn.date=""/>

# Service Fabric 概述
Service Fabric 是一个分布式系统平台，可用于生成面向云的可缩放、易管理的可靠应用程序。Service Fabric 解决了开发和管理云应用程序中的重大挑战。通过使用 Service Fabric，开发人员和管理员不仅可以避免解决复杂的基础结构问题，而且可以专注实现了解它们是可缩放、可靠且易于管理的要求的任务关键型工作负荷。Service Fabric 代表用于构建和管理这些企业级第 1 层云规模服务的下一代中间件平台。

## 由微服务组成的应用程序 ##
利用 Service Fabric，你能够构建和管理可缩放且可靠的应用程序，该应用程序由在计算机的共享池（通常称为 Service Fabric 群集）上以非常高的密度运行的微服务组成。它提供复杂的运行时，用于构建分布式、可缩放的无状态和有状态微服务和全面的应用程序管理功能，以便预配、部署、监视、升级/修补和删除已部署应用程序。

Service Fabric 为当今很多 Microsof 服务提供支持，例如 Azure SQL 数据库、Azure DocumentDB、Cortana、Power BI、Microsoft Intune、Azure 事件中心、许多核心 Azure 服务和 Skype for Business，仅举几例。

Service Fabric 适用于创建“基于云开发”的服务，该服务可以根据需要先为小型服务，然后成长为包含数百或数千台计算机的大规模缩放服务，并适用于在某一区域或跨区域的可用性组间创建 Service Fabric 群集。

当今的 Internet 规模服务是使用微服务构建而成。示例微服务是协议网关、用户配置文件、购物车，库存处理、队列、缓存等。Service Fabric 是微服务平台，为每个微服务提供可以是无状态或有状态的唯一名称。

Service Fabric 为由这些微服务组成的应用程序提供全面的运行时和生命周期管理功能。它在 Service Fabric 群集间部署和激活的容器内部托管微服务。就像通过从 VM 移动到容器可能出现的密度的数量级增加一样，当从容器移动到微服务时，可能出现类似的密度数量级。例如，单个 Azure SQL 数据库群集（基于 Service Fabric 构建而成）包含数百台计算机，这些计算机运行数以千计的容器，该容器总共托管成千上万的数据库（每个数据库都是有状态 Service Fabric 微服务）。这同样适用于事件中心和上面提到的其他服务。这就是术语超大规模可用于描述 Service Fabric 功能的原因，如果容器为你提供高密度，那么微服务则为你提供超大规模。

![Service Fabric 平台][Image1]

## 无状态和有状态 Service Fabric 微服务

无状态微服务（例如协议网关、Web 代理等）不维护任何请求之外的任何可变状态及其来自服务的响应。Azure 云服务辅助角色是无状态服务的一个示例。有状态微服务（例如用户帐户、数据库、设备、购物车、队列等）维护请求及其响应之外的可变、权威状态。当今的 Internet 规模应用程序包含无状态和有状态微服务的组合。
 
有状态微服务为什么很重要？ 为什么不是只将无状态服务应用于所有内容？ 两个原因：

1) 通过在同一台计算机上保持代码和数据关闭来构建高吞吐量、低延迟、故障容错 OLTP 服务的功能，例如交互式存储前端、搜索、物联网 (IoT) 系统、交易系统、信用卡处理和欺诈检测系统、个人记录管理等。

2) 有状态微服务简化了应用程序设计，因为它们会删除附加队列和缓存的需要，这些队列和缓存不再需要传统上需要用来处理纯无状态应用程序的可用性和延迟需求。由于有状态服务天然就高度可用且具有低延迟，这意味着作为一个整体的应用程序中要管理的移动部件更少。

有关使用 Service Fabric 构建的应用程序模式和设计的详细信息，请参阅[应用程序方案](/documentation/articles/service-fabric-application-scenarios)

## 应用程序生命周期管理
Service Fabric 为云应用程序的整个应用程序生命周期管理 (ALM) 提供一流的支持：从开发到部署、到日常管理、再到维护以及到最终解除授权。

利用 Service Fabric ALM 功能，应用程序管理员/IT 操作人员能够使用低接触的简单工作流配置、部署、修补和监视应用程序。这些内置的工作流极大地减少了 IT 操作人员保持应用程序持续可用的负担。

大多数应用程序包含无状态和有状态微服务以及一起部署的其他 EXE/运行时的组合。通过在应用程序和已打包微服务上采用强类型，利用 Service Fabric 能够部署多个应用程序实例，其中每个都可以进行独立地管理和升级。重要的是，Service Fabric 能够部署*任何*可执行文件或运行时并使这些可靠。例如，它可以用于部署 ASP.NET 5、node.js、脚本或组成你的应用程序的任何内容。
  
有关应用程序生命周期管理的详细信息，请参阅[应用程序生命周期](/documentation/articles/service-fabric-application-lifecycle)

## 关键功能
通过使用 Service Fabric，你可以：

- 开发可自我修复的高度可缩放的应用程序。

- 使用“计算机上的数据中心”方法开发。本地开发环境是 Azure 数据中心中运行的相同代码。
 
- 开发由微服务、可执行文件和你选择的其他应用程序框架组成的应用程序，例如 ASP.NET、nodejs 等。

- 开发无状态和有状态（微）服务并使这些高度可靠。

- 通过使用有状态（微）服务代替缓存和队列来简化你的应用程序的设计。
 
- 在数秒内部署应用程序。

- 部署到 Azure 或通过零代码更改运行 Windows Server 的本地云。编写一次，然后部署到任何 Service Fabric 群集。

- 以比虚拟机更高的密度部署应用程序，每台计算机部署成千上万的应用程序。

- 并排部署不同版本的相同应用程序，每个都可以进行独立升级。
 
- 管理有状态应用程序的生命周期，同时无需任何停机时间，包括重大和非重大升级。

- 使用 .NET API、Powershell 或 REST 接口管理应用程序。
 
- 在应用程序内独立地升级和修补微服务。

- 监视和诊断应用程序的运行状况并设置策略以执行自动修复。

- 轻松地增加或减少 Service Fabric 群集，以便根据可用的资源了解应用程序规模。

- 观察自我修复资源平衡器在 Service Fabric 群集中安排应用程序的重新分布，以从故障中恢复并根据可用资源优化负载的分布。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 后续步骤

有关详细信息：[技术概述](/documentation/articles/service-fabric-technical-overview)。 
* 设置 Service Fabric [开发环境](service-fabric-get-started)。
* 选择服务的[框架](service-fabric-choose-framework)。

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png

 

<!---HONumber=74-->