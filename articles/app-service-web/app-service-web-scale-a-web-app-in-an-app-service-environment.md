<!-- not suitable for Mooncake -->

<properties 
	pageTitle="如何在 Azure 环境中缩放应用" 
	description="在 Azure 环境中缩放应用" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor="jimbe"/>

<tags
	ms.service="app-service"
	ms.date="01/14/2016"
	wacn.date=""/>

# 在 Azure 环境中缩放应用 #

从较高层面讲，Azure 环境实质上是将 Azure 个人部署到你的 VNET，并且仅可通过订阅进行管理。因为位于 VNET 中，它们提供新的网络功能，此外还可扩展到 Azure 环境中常规可用之外的范围。有关 Azure 环境 (ASE) 定义的详细信息，请参阅[什么是 Azure 环境][WhatisASE]。有关创建 Azure 环境或在 Azure 环境中创建 Web 应用的详细信息，请参阅[如何创建 Azure 环境][HowtoCreateASE]和[如何在 Azure 环境中创建 Web 应用][CreateWebappinASE]

提醒：当你对 Web 应用、移动应用或 API 应用的缩放属性进行常规更改时，实际是在更改 App Service 计划 (ASP) 级别。有关缩放 App Service 计划的详细信息，或仅有关 Azure 环境之外 App Service 计划的详细信息，请参阅[在 Azure 中缩放 Web 应用][ScaleWebapp]和 [App Service 计划深度概述][Appserviceplans]。

在 Azure 环境中缩放应用与正常缩放应用非常相似。在 Azure 中，通常可以在三个方面进行缩放：

- 定价计划
- （专用实例的）辅助角色数量
- 实例数。

在 ASE 中，无需选择或更改定价计划。在功能方面，它已处于高级定价功能级别。Azure 环境中也没有共享辅助角色。均为专用辅助角色。

在辅助角色大小方面，ASE 管理员可以指定用于每个辅助角色池的计算资源大小。这意味着，如有需要，可让辅助池 1 具有 P4 计算资源，而辅助池 2 则具有 P1 计算资源。它们不需要按照大小顺序排列。有关这些大小及其定价的详细信息，请参阅此处的文档 [Azure 定价][AppServicePricing]。此操作会将 Azure 环境中 Web 应用和 App Service 计划的缩放选项保留为：

- 辅助池选择项
- 实例数

对任一项目的更改均通过针对 ASE 托管的 App Service 计划中显示的 UI 进行。无法将 ASP 增加到超出 ASP 所在辅助角色池中可用的计算资源数。如果需要更多实例，则需让 ASE 管理员向相应辅助池添加更多计算资源。有关重新配置 ASE 的信息，请参阅：[如何配置 Azure 环境][HowtoConfigureASE]。你还可以利用 ASE 自动缩放功能来根据计划或指标增加容量。若要获取有关配置 ASE 环境本身自动缩放的详细信息，请参阅[如何配置 Azure 环境的自动缩放][ASEAutoscale]。

![][1]

### 扩展实例数 ###

首次在 Azure 环境中创建 Web 应用时，应将其增加到至少 2 个实例以提供容错功能。

如果你的 ASE 具有足够的容量，那么此步骤非常简单。转到包含你想要增加的站点的 App Service 计划，然后选择“扩展”。这会打开 UI，你可以在其中为 ASP 手动设置缩放或设置自动缩放规则。若要手动缩放应用，只需将“缩放依据”设置为“手动输入的实例计数”。从此处将滑块拖到所需的数量，或者在滑块旁边的框中输入所需的数量。

![][2]

ASE 中的 ASP 自动缩放规则与一般运行方式相同。你可以选择“缩放依据”下面的“CPU 百分比”，根据 CPU 百分比创建 ASP 的自动缩放规则，或使用“计划和性能规则”创建更复杂的规则。若要查看有关配置自动缩放的更完整详细信息，请参阅本指南中的[在 Azure 中缩放 Web 应用][AppScale]。


### 辅助池选择项 ###

如前所述，辅助角色池选项需通过 ASP UI 访问。打开想要缩放的 ASP 边栏选项卡，然后选择辅助角色池。你将看到已在 Azure 环境中配置的所有辅助角色池。如果只有一个辅助池，则将仅列出该池。要更改 ASP 所在的辅助角色池，只需选择希望将 App Service 计划移动到的目标辅助角色池即可。

![][3]

将 ASP 从一个辅助角色池移到另一个池之前，请确保有足够的容量可以容纳该 ASP。辅助池列表中不仅列出了辅助池名，还显示了该辅助池中的可用辅助角色数。请确保具有足够的可用实例来容纳 App Service 计划。如果想移动到的目标辅助池需要更多计算资源，请让 ASE 管理员进行添加。

> [AZURE.NOTE] 从一个辅助角色池移出 ASP 会导致该 ASP 中的应用重新启动。这可能导致应用出现短暂的停机时间，具体取决于应用重新启动所需的时间长短。

## 入门

若要开始使用 Azure 环境，请参阅[如何创建 Azure 环境][HowtoCreateASE]

有关 Azure 平台的详细信息，请参阅 [Azure Web 应用][AzureAppService]。

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png

<!--Links-->
[WhatisASE]: /documentation/articles/app-service-app-service-environment-intro/
[ScaleWebapp]: /documentation/articles/web-sites-scale/
[HowtoCreateASE]: /documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoConfigureASE]: /documentation/articles/app-service-web-configure-an-app-service-environment/
[CreateWebappinASE]: /documentation/articles/app-service-web-how-to-create-a-web-app-in-an-ase/
[Appserviceplans]: /documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[AppServicePricing]: /home/features/web-site/#price
[AzureAppService]: /documentation/services/web-sites/
[ASEAutoscale]: /documentation/articles/app-service-environment-auto-scale/
[AppScale]: /documentation/articles/web-sites-scale/

<!---HONumber=Mooncake_0328_2016-->