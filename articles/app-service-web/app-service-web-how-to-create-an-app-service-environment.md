<!-- not suitable for Mooncake -->

<properties 
	pageTitle="如何创建 Azure 环境" 
	description="App Service 环境的创建流描述" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags
	ms.service="app-service"
	ms.date="06/20/2016"
	wacn.date=""/>

# 如何创建 Azure 环境 #

Azure 环境 (ASE) 是 Azure 的一个高级服务选项，可提供多租户戳记中不会提供的增强型配置功能。ASE 功能实质上是将 Azure 部署到客户的虚拟网络。若要更好地理解 Azure 环境所提供的功能，请阅读文档：[什么是 Azure 环境][WhatisASE]。

### 概述 ###

若要创建 ASE，客户需要提供以下几项信息：

- ASE 的名称
- 用于 ASE 的订阅  
- 资源组
- Azure 虚拟网络 (VNET) 选项和子网
- ASE 资源池定义

其中每个项都有一些重要的详细信息。

- ASE 的名称将用于该 ASE 中创建的任何应用的子域。
- ASE 中创建的所有应用将位于 ASE 本身的相同订阅中
- 如果无法访问用于创建 ASE 的订阅，则无法使用 ASE 来创建应用
- 用于托管 ASE 的 VNET 必须是区域性经典“v1”VNET 
- **用于托管 ASE 的子网不得包含任何其他计算资源**
- 一个子网中只能存在一个 ASE
- 利用 2016 年 6 月的最新更改，现在可以将 ASE 部署到使用公用地址范围*或* RFC1918 地址空间（即专用地址）的虚拟网络。若要使用具有公用地址范围的虚拟网络，你需要提前创建子网，然后在 ASE 创建 UX 中选择该子网。

每个 ASE 部署都是 Azure 管理和维护的一种托管服务。客户不可访问托管 ASE 系统角色的计算资源，尽管客户确实管理着实例的数量和大小。

有两种方法来访问 ASE 创建 UI。一种方法是通过在 Azure 库中搜索“Azure 环境”，另一种方法是选择“新建”->“Web + 移动”。

如果你想让 VNET 有不同于 ASE 的资源组，必须先单独创建 VNET，然后在 ASE 创建期间进行选择。此外，如果你想要在 ASE 创建期间于现有 VNET 中创建子网，则 ASE 必须属于与 VNET 相同的资源组。

### 快速创建 ###
在创建 ASE 时，可以使用一组默认值来进行快速创建。只要输入部署的名称，即可快速创建 ASE。这样，就会在你最靠近的区域中创建 ASE，其中包含：

- 使用 RFC1918 专用地址空间并具有 512 个地址的 VNET
- 具有 256 个地址的子网
- 具有 2 个 P2 计算资源的前端池
- 具有 2 个 P1 计算资源的辅助角色池
- 用于 IP SSL 的单个 IP 地址

前端池需要 P2 或更大的大小。请小心选择想让 ASE 属于哪个订阅。可使用 ASE 来托管内容的唯一帐户必须位于用于创建它的订阅中。

![][1]

为 ASE 指定的名称将用于在 ASE 中创建的应用。如果 ASE 名称为 appsvcenvdemo，则域名将为 .*appsvcenvdemo.p.chinacloudsites.cn*。如果因此创建名为 *mytestapp* 的应用，则可寻址于 *mytestapp.appsvcenvdemo.p.chinacloudsites.cn*。不能在 ASE 名称中使用空格。如果在名称中使用大写字符，域名将为该名称的全小写形式。

在某些情况下使用默认值有很好的效果，但通常都必须进行调整。后续几节将逐一说明 ASE 的相关配置部分。

### 虚拟网络 ###
虽然快速创建功能将自动创建新的 VNET，但该功能还支持选择现有 VNET 和手动创建 VNET。如果现有的 VNET 够大，可进行选择（目前只有经典“v1”虚拟网络受支持）以支持 Azure 环境部署。VNET 必须有 8 个或更多地址。

利用 2016 年 6 月的最新更改，现在可以将 ASE 部署到使用公用地址范围*或* RFC1918 地址空间（即专用地址）的虚拟网络。若要使用具有公用地址范围的虚拟网络，你需要提前创建子网，然后在 ASE 创建 UX 中选择该子网。

如果选择预先存在的 VNET，则还需指定要使用的子网或创建一个新子网。子网必须有 8 个或更多地址，且不可有任何其他资源已包含于其中。如果尝试使用已分配 VM 的子网，ASE 创建将失败。

如果浏览 VNET 创建 UI，你需要提供：

- VNET 名称
- CIDR 表示法中的 VNET 地址范围
- 位置

VNET 的位置就是 ASE 的位置，因为 ASE 部署到该 VNET 中。

在指定或选择 VNET 后，必须创建或选择适当的子网。需要在此处提供的详细信息包括：
- 子网名称
- CIDR 表示法中的子网范围

如果你不熟悉 CIDR（无类域间路由）表示法，请注意它采用以正斜杠与 CIDR 值分隔的 IP 地址格式。类似于 *10.0.0.0/22*。CIDR 值表示对显示的 IP 地址进行掩码的前导位数。如果要以更简单的方式表达此概念，我们可以说 CIDR 值提供了 IP 范围。在本示例中，10.0.0.0/22 表示 1024 个地址，或从 10.0.0.0 到 10.0.3.255 的范围。/23 意味着 512 个地址，等等。

提醒一下，如果你想要在现有的 VNET 中创建子网，ASE 将属于与 VNET 相同的资源组。若要让 ASE 与 VNET 分属于不同的资源组，只要在创建 ASE 之前单个创建 VNET 和子网即可。

![][2]


### 计算资源池 ###

在 ASE 创建期间，可以设置每个资源池的资源数量及其大小。可以在 ASE 创建时设置资源池的大小，也可以后续再使用手动缩放选项或自动缩放选项进行缩放。

如前所述，ASE 是由前端服务器和辅助角色组成。前端服务器处理应用连接负载，辅助角色运行应用代码。在专用计算资源池中管理前端服务器。在 3 个不同的计算资源池中依次管理辅助角色，这三个池的名称是：

- 辅助池 1
- 辅助池 2
- 辅助池 3

如果你有大量针对简单 Web 应用的请求，则有可能会增加前端而减少辅助角色数量。如果你有 CPU 或流量较少的内存密集型 Web 应用，则不需要多个前端，但可能需要更多或更大的辅助角色。

![][3]

无论计算资源的大小如何，最小占用具有 2 个前端服务器和 2 个辅助角色。可对 ASE 进行配置以使用最多 55 个总计算资源。在这 55 个计算资源中，只有 50 个可用于承载工作负荷。原因在于两个方面。至少有 2 个前端计算资源。还剩最多 53 个来支持辅助池分配。为了提供容错功能，还需根据以下规则分配额外计算资源：

- 每个辅助池至少需要一个额外计算资源，该资源不能是已分配的工作负荷
- 当池中计算资源的数量超出特定值时，则需要另一个计算资源

在任何单个辅助池中，对于分配到辅助池的给定 X 个资源，容错要求如下：

- 如果 X 介于 2 到 20，则可用于工作负荷的可用计算资源量为 X-1
- 如果 X 介于 21 到 40，则可用于工作负荷的可用计算资源量为 X-2
- 如果 X 介于 41 到 53，则可用于工作负荷的可用计算资源量为 X-3

除了能够管理可分配到给定池的计算资源的数量之外，你还可控制其大小。在 Azure 环境中，可从 4 种不同大小（标记为 P1 到 P4）进行选择。有关这些大小及其定价的详细信息，请参阅此处的 [Azure 定价][AppServicePricing]。P1 到 P3 计算资源的大小与多租户环境中的可用大小相同。P4 计算资源提供具有 14 GB RAM 的 8 个内核，仅在 Azure 环境中可用。

Azure 环境的定价针对已分配的计算资源。你为分配到 Azure 环境的计算资源付费，而不考虑它们是否是托管工作负荷。

默认情况下 ASE 附带了可用于 IP SSL 的 1 个 IP 地址。如果你知道将需要更多地址，则可在此处指定或在创建后对其进行管理。
  
### 创建 Azure 环境之后 ###

在创建 ASE 后可以调整：

- 前端的数量（最小：2 个）
- 辅助角色的数量（最小：2 个）
- 可用于 IP SSL 的 IP 地址数量
- 前端或辅助角色使用的计算资源大小（前端最小大小为 P2）

不能更改：

- 位置
- 订阅
- 资源组
- 使用的 VNET
- 使用的子网

有关手动缩放、管理和监视 Azure 环境的更多详细信息，请参阅此文：[如何配置 Azure 环境][ASEConfig]

有关自动缩放的信息，请参阅本指南：
[如何配置 Azure 环境的自动缩放][ASEAutoscale]

存在不可用于自定义（如数据库和存储器）的其他依赖项。这些是由 Azure 处理且随系统出现的。系统存储对于整个 Azure 环境最多可支持 500 GB，Azure 会根据系统规模的需要来调整数据库。


## 入门
[应用程序服务环境自述文件](/documentation/articles/app-service-app-service-environments-readme)中提供了有关 Azure 环境的所有文章和操作说明。

若要开始使用 Azure 环境，请参阅 [Azure 环境简介][WhatisASE]

有关 Azure 平台的详细信息，请参阅 [Azure Web 应用][AzureAppService]。

[AZURE.INCLUDE [app-service-web-whats-changed](../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../includes/app-service-web-try-app-service.md)]
 

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png
[3]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-resources.png

<!--Links-->
[WhatisASE]: /documentation/articles/app-service-app-service-environment-intro/
[ASEConfig]: /documentation/articles/app-service-web-configure-an-app-service-environment/
[AppServicePricing]: /home/features/web-site/#price
[AzureAppService]: /documentation/services/web-sites/
[ASEAutoscale]: /documentation/articles/app-service-environment-auto-scale/

<!---HONumber=Mooncake_0627_2016-->