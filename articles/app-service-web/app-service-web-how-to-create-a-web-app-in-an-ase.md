<properties
	pageTitle="如何在 App Service 环境中创建 Web 应用"
	description="Web 应用的创建流程和 App Service 环境的已检查 App Service 计划"
	services="app-service\web"
	documentationCenter=""
	authors="ccompy"
	manager="stefsch"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.date="04/27/2015"
	wacn.date=""/>

# 如何在 App Service 环境中创建 Web 应用 #

在 App Service 环境 (ASE) 中创建 Web 应用与常规创建操作几乎相同。如果不熟悉 App Service 环境功能，请阅读此处的文档[什么是 App Service 环境](/documentation/articles/app-service-app-service-environment-intro)。

要在 ASE 中创建 Web 应用，需要首先需要拥有一个 ASE。有关如何创建 ASE 的信息信息，请阅读此处的文档：[如何创建 App Service 环境](/documentation/articles/app-service-web-how-to-create-an-app-service-environment)。

创建 Web 应用的第一步是创建或选择一个 App Service 计划 (ASP)。在 ASE 中创建 ASP 一开始与其常规创建方式相同，即从“新建”->“Web + Mobile”->“Web 应用”开始，完成 Web 应用创建流程。

![][1]


如果你使用已在 App Service 环境中创建的 App Service 计划，请选择该计划，输入 Web 应用的名称并选择“创建”。与创建 Web 应用的常规流程相同。此处的主要区别在于 Web 应用的访问地址：

在 [*sitename*].[*App Service 环境名称*].p.azurewebsites.net 处进行访问

而不是

在 [*sitename*].azurewebsites.net 处进行访问

现在要求 Web 应用名在整个 Azure App Service 中是唯一的。这意味着，如果想要创建名为“thisismywebapp”的 Web 应用，则 Azure App Service 中当前不能存在任何其他名为“thisismywebapp”的 Web 应用。

### App Service 计划 ###

App Service 计划是一组托管的 Web 应用。选择定价时，支付的价格适用于 App Service 计划而不是单个应用。要增加 Web 应用的实例数，增加 ASP 的实例数即可，此操作将影响该计划中的所有 Web 应用。某些功能（如站点槽位或虚拟网络集成）在计划内也有数量限制。可从此处的文档了解有关 App Service 计划的详细信息：[Azure App Service 计划深度解析](/documentation/articles//azure-web-sites-web-hosting-plans-in-depth-overview)

如果要订立新的 App Service 计划，则与在 App Service 环境中创建 ASP 有所不同。最重要的是，辅助角色选项将不同，因为 App Service 环境中不存在共享辅助角色。必须使用已由管理员分配到 App Service 环境的辅助角色。这意味着要创建新的 ASP，就需要向 App Service 环境分配了比 App Service 环境中所有 ASP 实例总数更多的辅助角色。如果 App Service 环境中没有足够的辅助角色，则需要与 App Service 环境管理员合作添加所需线程，才能创建 ASP。

与 App Service 环境托管的 ASP 的另一个不同点则是缺乏定价选项。对于 App Service 环境，你是为系统使用的计算资源付费，而不对该环境中的 ASP 额外付费。一般说来，创建 ASP 时即选择了决定计费的定价计划。App Service 环境本质上是一个可以在其中创建内容的专用位置。你只为环境付费而不为托管内容付费。

### 选择 App Service 环境 ###

由于 App Service 环境实质上是一个专用部署位置，所以可以通过从位置选取器中选择你希望使用的 ASE 进行启动。

![][2]

选择后，UI 将使用辅助池选取器更新并替换定价计划选取器。位置显示 ASE 系统的名称及其所在区域。在 URL 下方，ASE 的域名会将通常出现的 azurewebsites.net 替换为 App Service 环境的名称。

![][3]

### 选择辅助池 ###

通常，在 Azure App Service 中和 App Service 环境外，根据所选专用价格计划的不同，共有 3 种大小可用。同理，具有 ASE 的客户可以最多定义 3 个辅助池并指定用于该辅助池的 VM 大小。无需为 ASP 选择定价计划，只需选择“辅助池”即可。

辅助池选择项 UI 将在名称下方显示用于该辅助池的 VM 大小。可用数量是指该池中可用的 VM 数。此值仅指未被占用的 VM 数量，而总池实际上可能具有比此数值更多的 VM。如果需要调整 App Service 环境以添加更多计算资源，请参阅此处的文档：[配置 App Service 环境](/documentation/articles/app-service-web-configure-an-app-service-environment)。

![][4]

此示例中只能看到两个可用辅助池。这是因为 ASE 管理员仅将 VM 分配到了这两个辅助池中。将 VM 分配到第三个辅助池时，则会显示该池。

### 创建 Web 应用后 ###

运行 Web 应用和管理 ASE 中的 ASP 时，有几点注意事项需要考虑。

如前文所述，ASE 所有者要负责系统的大小，因此也要负责确保有足够的容量来承载所需 ASP。如果没有可用的辅助角色，则将无法创建 ASP。这一点也适用于扩展 Web 应用。如果需要更多实例，则必须让 App Service 环境管理员添加更多辅助角色。

创建 Web 应用和 ASP 后，最好进行扩展。在 ASE 中，始终需要具有至少 2 个 ASP 的实例，以便为应用提供容错能力。在 ASE 中扩展 ASP 与通过 ASP UI 进行常规缩放相同。有关扩展的详细信息，请阅读此处的文档：[如何在 App Service 环境中扩展 Web 应用](/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment)

[AZURE.INCLUDE [app-service-web-whats-changed](../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png

<!--Links-->
[WhatisASE]: /documentation/articles/app-service-app-service-environment-intro
[Appserviceplans]: /documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview
[HowtoCreateASE]: /documentation/articles/app-service-web-how-to-create-an-app-service-environment
[HowtoScale]: /documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment
[HowtoConfigureASE]: /documentation/articles/app-service-web-configure-an-app-service-environment
 

<!---HONumber=67-->