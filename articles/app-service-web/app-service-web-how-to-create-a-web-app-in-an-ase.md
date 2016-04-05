<!-- not suitable for Mooncake -->

<properties
	pageTitle="在 Azure 环境中创建 Web 应用"
	description="了解如何在 Azure 环境中创建 Web 应用和 App Service 计划"
	services="app-service"
	documentationCenter=""
	authors="ccompy"
	manager="stefsch"
	editor=""/>

<tags
	ms.service="app-service"
	ms.date="01/14/2016"
	wacn.date=""/>

# 在 Azure 环境中创建 Web 应用

## 概述

本教程说明如何在 [Azure 环境](/documentation/articles/app-service-app-service-environment-intro) (ASE) 中创建 Web 应用和 App Service 计划。

> [AZURE.NOTE] 如果你想要了解如何创建 Web 应用，但不需要在 Azure 环境中实现，请参阅[创建 .NET Web 应用](/documentation/articles/web-sites-dotnet-get-started)，或适用于其他语言和框架的相关教程之一。

## 先决条件

本教程假设你已创建一个 Azure 环境。如果尚未创建，请参阅[创建 Azure 环境](/documentation/articles/app-service-web-how-to-create-an-app-service-environment)。

## 创建 Web 应用

1. 在 [Azure 管理门户](https://manage.windowsazure.cn/)中，单击“新建”>“Web + 移动”>“Web 应用”。 

	![][1]

2. 选择你的订阅。

	如果你有多个订阅，请注意，若要在 Azure 环境中创建应用，需要使用在创建环境时所用的订阅来创建应用。

3. 选择或创建资源组。

	资源组可你让以单位形式管理相关的 Azure 资源，并可在为应用创建基于角色的访问控制 (RBAC) 规则时发挥作用。有关详细信息，请参阅[管理 Azure 资源][ResourceGroups]。

4. 选择或创建 App Service 计划。

	App Service 计划是一组托管的 Web 应用。选择定价时，支付的价格适用于 App Service 计划而不是单个应用。要增加 Web 应用的实例数，增加 App Service 计划的实例数即可，此操作将影响该计划中的所有 Web 应用。某些功能（如站点槽位或虚拟网络集成）在计划内也有数量限制。有关详细信息，请参阅 [Azure App Service 计划概述](/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview)。

	可以通过查看计划名称下注释的位置，来识别 ASE 中的 App Service 计划。

	![][5]

	如果你想要使用已存在于 Azure 环境中的 App Service 计划，请选择该计划。如果你想要创建新的 App Service 计划，请参阅本教程的以下部分：[在 Azure 环境中创建 App Service 计划](#createplan)。

5. 输入 Web 应用的名称，然后单击“创建”。

	你的 Web 应用名称在 Azure Web 应用中必须唯一。这意味着，如果你要创建名为“thisismywebapp”的 Web 应用，则 Azure Web 应用中当前不能存在任何其他名为“thisismywebapp”的 Web 应用。

	在 ASE 中，Web 应用的 URL 是：

	[站点名称].[Azure 环境的名称].p.chinacloudsites.cn

	而不是

	[站点名称].chinacloudsites.cn

## <a name="createplan"></a>创建 App Service 计划

当你在 Azure 环境中创建 App Service 计划时，辅助角色选择因为在 ASE 中没有共享的辅助角色而有所不同。必须使用的辅助角色也就是由管理员分配给 ASE 的辅助角色。这意味着在创建新的计划时，分配给 ASE 辅助角色池的辅助角色数，必须超过该辅助角色池中所有计划的实例总数。如果 ASE 辅助角色池中没有足够的辅助角色来创建计划，则需要与 ASE 管理员合作来添加辅助角色。

而 Azure 环境托管的 App Service 计划还有另一项差异，那就是缺少定价选项。如果你有 Azure 环境，支付系统所用的计算资源费用，但该环境中的计划不产生附加的费用。一般说来，创建 App Service 计划时即选择了决定计费的定价计划。Azure 环境本质上是一个可以在其中创建内容的专用位置。你只为环境付费而不为托管内容付费。

下面说明如何在根据本教程前面所述创建 Web 应用时创建 App Service 计划。

1. 在计划选择 UI 中单击“新建”，并提供计划的名称，就跟平常在 ASE 以外的地方所做的一样。

2. 从位置选择器中选择你想要使用的 ASE。

	由于 Azure 环境本质上是专用的部署位置，因此它将显示在“位置”下。

	![][2]

	在位置选择器中选择 ASE 之后，App Service 计划创建 UI 将更新。位置现在显示 ASE 系统的位置及其所在区域，而辅助角色池选择器将取代定价计划选择器。

	![][3]

### 选择辅助角色池

通常，在 Azure 中和 Azure 环境以外，专用价格计划通常有 3 种计算大小可供选择。同理，对于 ASE，最多可以定义 3 个辅助角色池，并指定用于该辅助角色池的计算实例大小。对 ASE 的租户来说，这意味着他们不是根据 App Service 计划的计算实例大小来选择定价计划，而是选择所谓的辅助角色池。

辅助角色池选择项 UI 将在名称下方显示用于该辅助角色池的计算实例大小。可用数量是指该池中可用的计算实例数。此值仅指未被占用的实例数量，而总池实际上可能具有比此数值更多的实例。如果需要调整 Azure 环境以添加更多计算资源，请参阅[配置 Azure 环境](/documentation/articles/app-service-web-configure-an-app-service-environment)。

![][4]

在本示例中，你只能看到两个可用辅助角色池。这是因为 ASE 管理员仅将主机分配到了这两个辅助角色池中。将 VM 分配到第三个辅助池时，则会显示该池。

## 创建 Web 应用后

运行 Web 应用和管理 ASE 中的 App Service 计划时，有几点注意事项需要考虑。

如前文所述，ASE 所有者要负责系统的大小，因此也要负责确保有足够的容量来承载所需 App Service 计划。如果没有可用的辅助角色，则将无法创建 App Service 计划。这一点也适用于扩展 Web 应用。如果需要更多实例，则必须让 Azure 环境管理员添加更多辅助角色。

创建 Web 应用和 App Service 计划后，最好进行扩展。在 ASE 中，始终需要具有至少 2 个 App Service 计划的实例，以便为应用提供容错能力。在 ASE 中缩放 App Service 计划的方式与平时通过 App Service 计划 UI 进行操作的方式一样。有关缩放的详细信息，请参阅[如何在 Azure 环境中缩放 Web 应用](/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment)

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: /documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: /documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: /documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: /documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment
[HowtoConfigureASE]: /documentation/articles/app-service-web-configure-an-app-service-environment
[ResourceGroups]: /documentation/articles/resource-group-portal/
[AzurePowershell]: /documentation/articles/powershell-install-configure/

<!---HONumber=Mooncake_0328_2016-->