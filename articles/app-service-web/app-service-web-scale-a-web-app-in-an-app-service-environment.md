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
	ms.date="04/06/2016"
	wacn.date=""/>

# 在 Azure 环境中缩放应用 #

在 Azure 中，通常可以在三个方面进行缩放：

- 定价计划
- 辅助角色大小 
- 实例数。

在 ASE 中，无需选择或更改定价计划。在功能方面，它已处于高级定价功能级别。

在辅助角色大小方面，ASE 管理员可以指定用于每个辅助角色池的计算资源大小。这意味着，如有需要，可让辅助池 1 具有 P4 计算资源，而辅助池 2 则具有 P1 计算资源。它们不需要按照大小顺序排列。有关这些大小及其定价的详细信息，请参阅此处的文档 [Azure 定价][AppServicePricing]。此操作会将 Azure 环境中 Web 应用和 App Service 计划的缩放选项保留为：

- 辅助池选择项
- 实例数

对任一项目的更改均通过针对 ASE 托管的 App Service 计划中显示的 UI 进行。

![][1]

无法将 ASP 增加到超出 ASP 所在辅助角色池中可用的计算资源数。如果需要该辅助池中的计算资源，需让 ASE 管理员进行添加。有关重新配置 ASE 的信息，请阅读此处的信息：[如何配置 Azure 环境][HowtoConfigureASE]。你还可以利用 ASE 自动缩放功能来根据计划或指标增加容量。若要获取有关配置 ASE 环境本身自动缩放的更多详细信息，请参阅[如何配置 Azure 环境的自动缩放][ASEAutoscale]。

你可以使用来自不同辅助池或相同辅助池的计算资源，创建多个 App Service 计划。例如，如果在辅助池 1 中有 (10) 个可用的计算资源，你可以选择使用 (6) 个计算资源创建一个 App Service 计划，而第二个 App Service 计划使用 (4) 个计算资源。

### 扩展实例数 ###

当你首次在 Azure 环境中创建 Web 应用时，它会从 1 个实例开始。你可以再扩大至其他实例，为应用提供额外的计算资源。

如果你的 ASE 具有足够的容量，那么此步骤非常简单。转到包含你想要增加的站点的 App Service 计划，然后选择“扩展”。这会打开 UI，你可以在其中为 ASP 手动设置缩放或设置自动缩放规则。若要手动缩放应用，只需将“缩放依据”设置为“手动输入的实例计数”。从此处将滑块拖到所需的数量，或者在滑块旁边的框中输入所需的数量。

![][2]

ASE 中的 ASP 自动缩放规则与一般运行方式相同。你可以选择“缩放依据”下面的“CPU 百分比”，根据 CPU 百分比创建 ASP 的自动缩放规则，或使用“计划和性能规则”创建更复杂的规则。若要查看有关配置自动缩放的更完整详细信息，请参阅本指南：[在 Azure Web 应用中缩放应用][AppScale]。


### 辅助池选择项 ###

如前所述，辅助角色池选项需通过 ASP UI 访问。打开想要缩放的 ASP 边栏选项卡，然后选择辅助角色池。你将看到已在 Azure 环境中配置的所有辅助角色池。如果只有一个辅助池，则将仅列出该池。要更改 ASP 所在的辅助角色池，只需选择希望将 App Service 计划移动到的目标辅助角色池即可。

![][3]

将 ASP 从一个辅助角色池移到另一个池之前，请确保有足够的容量可以容纳该 ASP。辅助池列表中不仅列出了辅助池名，还显示了该辅助池中的可用辅助角色数。请确保具有足够的可用实例来容纳 App Service 计划。如果想移动到的目标辅助池需要更多计算资源，请让 ASE 管理员进行添加。

> [AZURE.NOTE] 从一个辅助池移出 ASP 会导致该 ASP 中的应用冷启动。这可能会导致请求的运行速度变慢，因为你的应用在新计算资源上冷启动。使用 Azure Web 应用中的[应用程序预热功能][AppWarmup]可以避免冷启动。本文所述的应用程序初始化模块对冷启动也有效，因为当应用在新的计算资源上冷启动时，也会调用初始化进程。

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
[AppWarmup]: http://ruslany.net/2015/09/how-to-warm-up-azure-web-app-during-deployment-slots-swap/

<!---HONumber=Mooncake_0509_2016-->