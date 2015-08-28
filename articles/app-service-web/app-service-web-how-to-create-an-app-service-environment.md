<properties 
	pageTitle="如何创建 App Service 环境" 
	description="App Service 环境的创建流描述" 
	services="app-service\web" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.date="04/27/2015" 
	wacn.date=""/>

# 如何创建 App Service 环境 #

App Service 环境 (ASE) 是目前预览版中提供的 Azure App Service 的高级服务选项。它提供一种在多租户戳记中不可用的增强的配置功能。若要更好地理解 App Service 环境所提供的功能，请阅读文档：[什么是 App Service 环境][WhatisASE]。

### 概述 ###

ASE 功能实质上是将 Azure App Service 部署到客户的 VNET。若要执行此操作客户需要：

- 至少具有 512 (/23) 个地址的区域 VNET
- 在此 VNET 中至少具有 256 (/ 24) 个地址的子网
- 子网**不能包含任何其他计算资源**。只有一个 App Service 环境可部署到子网中。如果子网中已存在任何其他计算资源，则创建尝试将失败。

如果你没有想要用以托管 App Service 环境的 VNET，则可在创建 App Service 环境期间创建一个 VNET。

每个 ASE 部署都是 Azure 管理和维护的一种托管服务。客户不可访问托管 ASE 系统角色的计算资源，尽管客户确实管理着实例的数量和大小。

## App Service 环境的创建 ##

有两种方法来访问 ASE 创建 UI。一种是通过在 Azure 应用商店中搜索 ***App Service 环境***，一种是通过“新建”->“Web + 移动”完成。

### 快速创建 ###
进入创建 UI 后，只需输入部署的名称即可快速创建 ASE。这将依次创建具有 512 个地址的 VNET、该 VNET 中具有 256 个地址的子网、辅助池 1 中具有 2 个前端和 2 个辅助角色的 ASE 环境。请务必选择想要系统所在的位置和想要它所处的订阅。可使用 ASE 来托管内容的唯一帐户必须位于用于创建它的订阅中。

为 ASE 指定的名称将用于在 ASE 中创建的 Web 应用。如果 ASE 名称为 appsvcenvdemo，则域名称将为 *appsvcenvdemo.p.azurewebsites.net*。因此，如果创建了名为 mytestapp 的 Web 应用，则可在 *mytestapp.appsvcenvdemo.p.azurewebsites.net* 中访问它。不能在名称中使用空白区域。如果在名称中使用大写字符，域名将为该名称的全小写形式。


![][1]

### 计算资源池 ###

用于 App Service 环境的计算资源在计算资源池中进行管理，该池允许对你在池中的计算资源数量及大小进行配置。App Service 环境由前端服务器和辅助角色组成。前端服务器处理应用连接负载，辅助角色运行应用代码。在专用计算资源池中管理前端服务器。在 3 个不同的计算资源池中依次管理辅助角色，这三个池的名称是：

- 辅助池 1
- 辅助池 2
- 辅助池 3

如果你有大量针对简单 Web 应用的请求，则很可能会增加前端而减少辅助角色数量。如果你有 CPU 或流量较少的内存密集型 Web 应用，则不需要多个前端，但可能需要更多或更大的辅助角色。

无论计算资源的大小如何，最小占用具有 2 个前端服务器和 2 个辅助角色。可对 App Service 环境进行配置以使用最多 55 个总计算资源。在这 55 个计算资源中，只有 50 个可用于承载工作负荷。原因在于两个方面。至少有 2 个前端计算资源。还剩最多 53 个来支持辅助池分配。不过为了提供容错功能，还需根据以下规则分配额外计算资源：

- 每个辅助池至少需要一个额外计算资源，该资源不能是已分配的工作负荷
- 当池中计算资源的数量超出特定值时，则需要另一个计算资源

在任何单个辅助池中，对于分配到辅助池的给定 X 个资源，容错要求如下：

- 如果 X 介于 2 到 20，则可用于工作负荷的可用计算资源量为 X-1
- 如果 X 介于 21 到 40，则可用于工作负荷的可用计算资源量为 X-2
- 如果 X 介于 41 到 53，则可用于工作负荷的可用计算资源量为 X-3

除了能够管理可分配到给定池的计算资源的数量之外，你还可控制其大小。在 App Service 环境中，可从 4 种不同大小（标记为 P1 到 P4）进行选择。有关这些大小及其定价的详细信息，请参阅此处 [App Service 定价][AppServicePricing]。P1 到 P3 计算资源的大小与多租户环境中的可用大小相同。P4 计算资源提供具有 14 GB RAM 的 8 个内核，仅在 App Service 环境中可用。

App Service 环境的定价针对已分配的计算资源。你为分配到 App Service 环境的计算资源付费，而不考虑它们是否是托管工作负荷。



### VNET 的创建 ###
虽然快速创建功能将自动创建新的 VNET，但该功能还支持选择现有 VNET 和手动创建 VNET。如果 VNET 有足够空间来支持 App Service 环境部署，则可以选择现有 VNET。VNET 必须具有 512 个地址或更多。如果选择预先存在的 VNET，则还需指定要使用的子网或创建一个新子网。子网需要具有 256 个地址或更多。

如果浏览 VNET 创建 UI，你需要提供：

- VNET 名称
- CIDR 表示法中的 VNET 地址范围
- 子网名称
- CIDR 表示法中的子网范围

如果你不熟悉 CIDR 表示法，注意它采用 10.0.0.0/22 格式，其中 /22 指定范围。在此示例，/22 意味着 1024 个地址或从 10.0.0.0 到 - 10.0.3.255 的范围。/23 意味着 512 个地址，等等。

![][2]

### App Service 环境的大小定义 ###

要配置的下一项是系统的规模。默认情况下有 2 个 Front End P2 计算资源、2 个 P1 辅助角色和 1 个 IP 地址。有 2 个前端以提供高可用性并分配负载。前端最小大小为 P2 以确保其具有足够的容量来支持适中的系统。如果你知道系统需要支持大量请求，则可以调整前端的数量和所用的服务器的大小。

正如前文所述，ASE 内具有 3 个客户可定义的辅助池。计算资源大小可为 P1 到 P4。默认情况只有 2 个在辅助池 1 中配置的 P1 辅助角色。这足以支持具有 1 个实例的单个 App Service 计划。

滑块会自动调整以反映 App Service 环境中可用的总计算容量。当滑块在任何一个池内调整时，其他滑块将在到达 55 前进行更改以反映剩余的可用计算资源数量。
 
![][3]

添加将变为可用的新实例不会快速发生。如果你知道将需要其他计算资源，则应提前预配它们。预配时间可能需要数小时，具体取决于添加到系统的资源的数量。请记住为确保系统能满足容错要求，在每个辅助池中，每个 ASE 需要具有一个可用的保留实例。

默认情况下 ASE 附带了可用于 IP SSL 的 1 个 IP 地址。如果你知道将需要更多地址，则可在此处指定或在创建后对其进行管理。
  
### 在创建 App Service 环境后 ###

在创建 ASE 后可以调整：

- 前端的数量（最小：2 个）
- 辅助角色的数量（最小：2 个）
- IP 地址的数量
- 前端或辅助角色使用的计算资源大小（前端最小大小为 P2）

不能更改：

- 位置
- 订阅
- 资源组
- 使用的 VNET
- 使用的子网

有关 App Service 环境的管理和监视详细信息，请参阅此处：[如何配置 App Service 环境][ASEConfig]

存在不可用于自定义（如数据库和存储器）的其他依赖项。这些是由 Azure 处理且随系统出现的。系统存储器针对整个 App Service 环境支持最多 500 GB。


## 入门

若要开始使用 App Service 环境，请参阅 [App Service 环境简介][WhatisASE]

有关 Azure App Service 平台的详细信息，请参阅 [Azure App Service][AzureAppService]。

[AZURE.INCLUDE [app-service-web-whats-changed](../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../includes/app-service-web-try-app-service.md)]
 

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/createaseblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/createasenetwork.png
[3]: ./media/app-service-web-how-to-create-an-app-service-environment/createasescale.png

<!--Links-->
[WhatisASE]: /documentation/articles/app-service-app-service-environment-intro
[ASEConfig]: /documentation/articles/app-service-web-configure-an-app-service-environment
[AzureAppService]: /documentation/articles/app-service-value-prop-what-is

<!---HONumber=67-->