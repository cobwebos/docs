<properties 
	pageTitle="如何在 App Service 环境中扩展 Web 应用" 
	description="在 App Service 环境中扩展 Web 应用" 
	services="app-service\web" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.date="04/27/2015" 
	wacn.date=""/>

# 在 App Service 环境中扩展 Web 应用 #

在高级别中，App Service 环境实质上是将 Azure App Service 个人部署到你的虚拟网络，并且仅可通过订阅进行管理。因为位于虚拟网络中，它们提供新的网络功能，此外还可扩展到 Azure App Service 环境中常规可用之外的范围。如果需要有关 App Service 环境 (ASE) 定义的详细信息，请参阅[什么是 App Service 环境][WhatisASE]。有关创建 App Service 环境或在 App Service 环境中创建 Web 应用的详细信息，请参阅[如何创建 App Service 环境][HowtoCreateASE]和[如何在 App Service 环境中创建 Web 应用][CreateWebappinASE]

便捷提示：当你对 Web 应用的缩放属性进行常规更改时，实际是在更改 App Service 计划级别。有关缩放 App Service 计划的详细信息，或仅有关 App Service 环境之外 App Service 计划的详细信息，请参阅[在 Azure App Service 中扩展 Web 应用][ScaleWebapp]和 [App Service 计划深度概述][Appserviceplans]。

在 App Service 环境中扩展 Web 应用与正常扩展 Web 应用程序非常相似。在 Azure App Service 中，通常有三项内容可以扩展：

- 定价计划
- （专用实例的）辅助角色数量
- 实例数。

在 ASE 中，无需选择或更改定价计划。在功能方面，它已处于高级定价功能级别。App Service 环境中也没有共享辅助角色。均为专用辅助角色。ASE 管理员可以指定用于每个辅助池的计算资源大小，而不采用固定大小。这意味着，如有需要，可让辅助池 1 具有 P4 计算资源，而辅助池 2 则具有 P1 计算资源。它们不需要按照大小顺序排列。有关这些大小及其定价的详细信息，请参阅此处的文档 [Azure App Service 定价][AppServicePricing]。此操作会将 App Service 环境中 Web 应用和 App Service 计划的缩放选项保留为：

- 辅助池选择项
- 实例数

对任一项目的更改均通过 App Service 计划中显示的 UI 进行。

![][1]

### 扩展实例数 ###

首次在 App Service 环境中创建 Web 应用时，应将其增加到至少 2 个实例以提供容错功能。

如果你的 ASE 具有足够的容量，那么此步骤非常简单。转到包含你想要增加的站点的 App Service 计划，然后选择“扩展”。此操作将打开 UI，在该处将实例指示符向上滑动到所需值并保存即可。

![][2]

无法将 App Service 计划增加到超出其所在辅助池中可用的计算资源数。如果需要更多实例，则需让 ASE 管理员向相应辅助池添加更多计算资源。有关重新配置 ASE 的信息，请参阅：[如何配置 App Service 环境][HowtoConfigureASE]
 

### 辅助池选择项 ###

从 App Service 计划 UI 访问辅助池选择项。打开想要扩展的 App Service 计划并选择辅助池。将显示已在 App Service 环境中配置的所有辅助池。如果只有一个辅助池，则将仅列出该池。要更改 App Service 计划所在的辅助池，只需选择希望将 App Service 计划移动到的目标辅助池即可。

![][3]

执行此操作之前，务必确保具有足够的容量来容纳 App Service 计划。辅助池列表中不仅列出了辅助池名，还显示了该辅助池中的可用辅助角色数。请确保具有足够的可用实例来容纳 App Service 计划。如果想移动到的目标辅助池需要更多计算资源，请让 ASE 管理员进行添加。

从辅助池移动 Web 应用将导致 Web 应用的重新启动。这可能导致应用出现短暂的停机时间，具体取决于重启的时长。

## 入门

若要开始使用 App Service 环境，请参阅[如何创建 App Service 环境][HowtoCreateASE]

有关 Azure App Service 平台的详细信息，请参阅 [Azure App Service][AzureAppService]。

[AZURE.INCLUDE [app-service-web-whats-changed](../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/scaleasp.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/scaleinstances.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/scalepool.png

<!--Links-->
[WhatisASE]: /documentation/articles/app-service-app-service-environment-intro
[ScaleWebapp]: /documentation/articles/web-sites-scale
[HowtoCreateASE]: /documentation/articles/app-service-web-how-to-create-an-app-service-environment
[HowtoConfigureASE]: /documentation/articles/app-service-web-configure-an-app-service-environment/
[CreateWebappinASE]: /documentation/articles/app-service-web-how-to-create-a-web-app-in-an-ase
[Appserviceplans]: /documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview
[AzureAppService]: /documentation/articles/app-service-value-prop-what-is
 

<!---HONumber=67-->