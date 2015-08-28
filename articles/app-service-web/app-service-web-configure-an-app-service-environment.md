<properties 
	pageTitle="如何配置 App Service 环境" 
	description="App Service 环境的配置、管理和监视" 
	services="app-service\web" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.date="04/27/2015" 
	wacn.date=""/>

# 配置 App Service 环境 #

## 概述 ##

App Service 环境是预览版中正提供的新的高级层功能。它提供新的缩放和网络访问功能。通过这个新的缩放功能，你可以将 Azure App Service 的实例置于虚拟网络。如果你不熟悉 App Service 环境 (ASE) 功能，请阅读此处的文档：[什么是 App Service 环境]/app-service-app-service-environment-intro.md)。有关如何创建 ASE 的信息，请阅读此处的文档：[如何创建 App Service 环境](/documentation/articles/app-service-web-how-to-create-an-app-service-environment)。

高级别 App Service 环境包括几个主要组件：

- 在 Azure App 环境托管服务中运行的计算资源
- 存储
- 数据库
- 具有至少一个子网的虚拟网络
- Azure App 环境托管服务在其中运行的子网

要协助管理和监视 App Service 环境，可通过 Azure 预览门户中的“浏览”->“App Service 环境”来访问用于该目的的 UI。初始版本确实具有管理系统所需的内容，并将在未来几周内持续改进，增加其他功能。

![][1]

## 监视 ##

初始预览版本中未提供许多度量值功能，但将很快推出。这些度量值功能将帮助系统管理员决定系统缩放和操作。

即便是现在你也可在门户中列出 ASE 中所有 App Service 计划及 App Service 环境中所有 Web 应用。若要查看任一列表，请转到“设置”并选择感兴趣的项。

![][3]

在这两个列表中，你都可以通过正在使用的计算资源实例数和大小了解辅助池的分配情况。可使用一般方式查看有关独立 App Service 计划中性能的详细信息，即打开 App Service 计划 UI 进行查看。

![][4]

## 计算资源 ##

计算资源、存储器和数据库均通过 Azure App Service 进行操作。不过计算资源的数量和大小由用户决定。

无论计算资源的大小如何，最小占用具有 2 个前端服务器和 2 个辅助角色。可对 App Service 环境进行配置以使用最多 55 个总计算资源。在这 55 个计算资源中，只有 50 个可用于承载工作负荷。原因在于两个方面。至少有 2 个前端计算资源。还剩最多 53 个来支持辅助池分配。不过为了提供容错功能，还需根据以下规则分配额外计算资源：

- 每个辅助池至少需要一个额外计算资源，该资源不能是已分配的工作负荷
- 当池中计算资源的数量超出特定值时，则需要另一个计算资源

在任何单个辅助池中，对于分配到辅助池的给定 X 个资源，容错要求如下：

- 如果 X 介于 2 到 20，则可用于工作负荷的可用计算资源量为 X-1
- 如果 X 介于 21 到 40，则可用于工作负荷的可用计算资源量为 X-2
- 如果 X 介于 41 到 53，则可用于工作负荷的可用计算资源量为 X-3

除了能够管理可分配到给定池的计算资源的数量之外，你还可控制其大小。在 App Service 环境中，可从 4 种不同大小（标记为 P1 到 P4）进行选择。有关这些大小及其定价的详细信息，请参阅此处：[App Service 定价](/documentation/articles/app-service-value-prop-what-is) P1 到 P3 计算资源的大小与一般可用大小相同。P4 计算资源提供具有 14 GB RAM 的 8 个内核，仅在 App Service 环境中可用。

如前文所述，App Service 环境功能当前在预览版中可用，因此仍有增长的空间。除附加监视功能外，App Service 环境转移到 GA 时还将推出更多管理功能。现在有仅几项可在此界面中进行管理：

- 每个池的计算资源数量
- 每个池的计算资源大小
- 可用的 IP 地址数

若要控制这些内容，请选择顶部的“缩放”配置项。

![][2]

可以在此处调整每个池的计算资源数量及其大小。在进行任何更改之前，特别要注意一些事项：

- 所做的更改可能要花费数小时才能完成，具体取决于所请求更改的大小
- 存在正在进行的 App Service 环境配置更改时，无法启动另一更改
- 如果更改辅助池中所用计算资源的大小，可能会导致在该辅助池中运行的 Web 应用运行中断

向辅助池是其他实例良性操作，不会对引发系统冲击。更改辅助池中所用计算资源的大小则不然。为了避免在对辅助池进行大小更改期间出现任何应用停机时间，最好执行以下操作：

- 使用未使用的辅助池以所需大小显示所需实例
- 将 App Service 计划缩放到新的辅助池。  
 
与使用运行中工作负荷更改计算资源大小相比，这对运行应用造成的干扰较小。有关在 App Service 环境中缩放 Web 应用的详细信息，请转到此处：[ App Service 环境中缩放 Web 应用](/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment)

## 虚拟网络 ##

[虚拟网络][virtualnetwork]和子网都由用户控制。App Service 环境确实有一些网络要求，但其他部分都由用户来控制。这些 ASE 要求包括：

- 至少具有 512 个地址的虚拟网络
- 至少具有 256 个地址的子网 
- 虚拟网络必须为区域虚拟网络  
 
通过正常的虚拟网络 UI 管理虚拟网络。

因为此功能将 Azure App Service 置于虚拟网络，这意味着托管在 ASE 中的应用现在可直接访问通过 ExpressRoute 或站点到站点 VPN 提供的资源。App Service 环境中的应用不需要其他网络功能即可访问承载着 App Service 环境的虚拟网络可用的资源。

如果需要，现在还可以使用网络安全组来控制访问。通过此功能，可以将 App Service 环境锁定到你想限定使用的 IP 地址。有关如何操作的详细信息，请参阅此处的文档：[如何在 App Service 环境中控制入站流量](/documentation/articles/app-service-app-service-environment-control-inbound-traffic)。

## 删除 App Service 环境 ##

如果想要删除 App Service 环境，使用“App Service 环境”边栏选项卡顶部的“删除”操作即可。但不能删除具有内容的 ASE。务必删除所有 Web 应用和 App Service 计划，才能删除 App Service 环境。

## 入门

若要开始使用 App Service 环境，请参阅[如何创建 App Service 环境](/documentation/articles/app-service-web-how-to-create-an-app-service-environment)

有关 Azure App Service 平台的详细信息，请参阅 [/文档/文章/Azure App Service](../app-service/app-service-value-prop-what-is)。

[AZURE.INCLUDE [app-service-web-whats-changed](../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/configureaseblade.png
[2]: ./media/app-service-web-configure-an-app-service-environment/configurescale.png
[3]: ./media/app-service-web-configure-an-app-service-environment/configureasplist.png
[4]: ./media/app-service-web-configure-an-app-service-environment/configurewebapplist.png

<!--Links-->
[WhatisASE]: /documentation/articles/app-service-app-service-environment-intro
[Appserviceplans]: /documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview
[HowtoCreateASE]: /documentation/articles/app-service-web-how-to-create-an-app-service-environment
[HowtoScale]: /documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment
[ControlInbound]: /documentation/articles/app-service-app-service-environment-control-inbound-traffic
[virtualnetwork]: https://msdn.microsoft.com/library/azure/dn133803.aspx
[AzureAppService]: /documentation/articles/app-service-value-prop-what-is/
 

<!---HONumber=67-->