---
title: 在应用服务环境 v1 中创建 Web 应用
description: 了解如何在应用服务环境 v1 中创建 Web 应用和应用服务计划
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: 983ba055-e9e4-495a-9342-fd3708dcc9ac
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/11/2017
ms.author: ccompy
ms.openlocfilehash: 1e8540409c6174ad02bd2d9d57c53e0279f49871
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
ms.locfileid: "29386905"
---
# <a name="create-a-web-app-in-an-app-service-environment-v1"></a>在应用服务环境 v1 中创建 Web 应用

> [!NOTE]
> 本文介绍应用服务环境 v1。  应用服务环境有一个较新版本，此版本更易于使用并在更强大的基础结构上运行。 若要深入了解新版本，请先参阅[应用服务环境简介](intro.md)。
> 

## <a name="overview"></a>概述
本教程介绍如何在[应用服务环境 v1](app-service-app-service-environment-intro.md) (ASE) 中创建 Web 应用和应用服务计划。 

> [!NOTE]
> 如果想要了解如何创建 Web 应用，但不需要在应用服务环境中进行操作，请参阅[创建 .NET Web 应用](../app-service-web-get-started-dotnet.md)，或适用于其他语言和框架的相关教程之一。
> 
> 

## <a name="prerequisites"></a>先决条件
本教程假设已创建一个应用服务环境。 如果尚未创建，请参阅[创建应用服务环境](app-service-web-how-to-create-an-app-service-environment.md)。 

## <a name="create-a-web-app"></a>创建 Web 应用
1. 在 [Azure 门户](https://portal.azure.com/)中，单击“创建资源”>“Web + 移动”>“Web 应用”。 
   
    ![][1]
2. 选择订阅。  
   
    如果有多个订阅，请注意，若要在应用服务环境中创建应用，需要使用创建环境时所用的订阅来创建应用。 
3. 选择或创建资源组。
   
    使用资源组能够以单位形式管理相关的 Azure 资源，并可在为应用创建基于角色的访问控制 (RBAC) 规则时发挥作用。 有关详细信息，请参阅 [Azure 资源管理器概述][ResourceGroups]。 
4. 选择或创建应用服务计划。
   
    应用服务计划是一组托管的 Web 应用。  选择定价时，支付的价格通常适用于应用服务计划而不是单个应用。 在 ASE 中，为分配给 ASE 的计算实例而不是向 ASP 列出的内容付费。  要增加 Web 应用的实例数，增加应用服务计划的实例数即可，此操作将影响该计划中的所有 Web 应用。  某些功能（如站点槽位或虚拟网络集成）在计划内也有数量限制。  有关详细信息，请参阅 [Azure 应用服务计划概述](../azure-web-sites-web-hosting-plans-in-depth-overview.md)
   
    可以通过查看计划名称下注释的位置，来识别 ASE 中的应用服务计划。  
   
    ![][5]
   
    如果想要使用已存在于应用服务环境中的应用服务计划，请选择该计划。 如果想要创建新的应用服务计划，请参阅本教程的以下部分：[在应用服务环境中创建应用服务计划](#createplan)。
5. 输入 Web 应用的名称，并单击“创建”。 
   
    如果 ASE 使用外部 VIP，则 ASE 中应用的 URL 为：[站点名称].[应用服务环境的名称].p.azurewebsites.net 而不是 [站点名称].azurewebsites.net
   
    如果 ASE 使用内部 VIP，则该 ASE 中应用的 URL 为：[站点名称].[在 ASE 创建过程中指定的子域]   
    在 ASE 创建过程中选择 ASP 后，可在“名称”下看到子域更新

## <a name="createplan"></a>创建应用服务计划
在应用服务环境中创建应用服务计划时，由于 ASE 中没有共享的辅助角色，因此辅助角色的选择会有所不同。  必须使用的辅助角色也就是由管理员分配给 ASE 的辅助角色。这意味着在创建新的计划时，分配给 ASE 辅助角色池的辅助角色数，必须超过该辅助角色池中所有计划的实例总数。  如果 ASE 辅助角色池中没有足够的辅助角色来创建计划，则需要与 ASE 管理员合作来添加辅助角色。

而应用服务环境托管的应用服务计划还有另一项差异，那就是缺少定价选项。  如果拥有应用服务环境，是在为系统所用的计算资源付费，且该环境中的计划不产生附加的费用。  一般说来，创建应用服务计划时即选择了决定计费的定价计划。  应用服务环境本质上是一个可以在其中创建内容的专用位置。  只为环境付费而不为托管内容付费。

下面说明如何在根据本教程前面所述创建 Web 应用时创建应用服务计划。

1. 在计划选择 UI 中单击“新建”，并提供计划的名称，就跟平常在 ASE 以外的进行的操作一样。
2. 从位置选取器中选择要使用的 ASE。
   
    由于应用服务环境本质上是专用的部署位置，因此它会显示在“位置”下。 
   
    ![][2]
   
    在位置选择器中选择 ASE 之后，应用服务计划创建 UI 将更新。  位置现在显示 ASE 系统的位置及其所在区域，而辅助角色池选择器将取代定价计划选择器。  
   
    ![][3]

### <a name="selecting-a-worker-pool"></a>选择辅助角色池
通常，在 Azure App Service 中和应用服务环境外，根据所选专用定价计划的不同，共有 3 种计算大小可用。  同理，对于 ASE，最多可以定义 3 个辅助角色池，并指定用于该辅助角色池的计算实例大小。  对 ASE 的租户来说，这意味着他们不是根据应用服务计划的计算实例大小来选择定价计划，而是选择所谓的辅助角色池。  

辅助角色池选择项 UI 会在名称下方显示用于该辅助角色池的计算实例大小。  可用数量是指该池中可用的计算实例数。  此值仅指未被占用的实例数量，而总池实际上可能具有比此数值更多的实例。  如需调整应用服务环境以添加更多计算资源，请参阅[配置应用服务环境](app-service-web-configure-an-app-service-environment.md)。

![][4]

在本示例中，只能看到两个可用辅助角色池。 这是因为 ASE 管理员仅将主机分配到了这两个辅助角色池中。  将 VM 分配到第三个辅助池时，则会显示该池。  

## <a name="after-web-app-creation"></a>创建 Web 应用后
运行 Web 应用和管理 ASE 中的应用服务计划时，有几点注意事项需要考虑。  

如前文所述，ASE 所有者要负责系统的大小，因此也要负责确保有足够的容量来承载所需应用服务计划。 如果没有可用的辅助角色，则将无法创建应用服务计划。  这一点也适用于扩展 Web 应用。  如果需要更多实例，则必须让应用服务环境管理员添加更多辅助角色。

创建 Web 应用和应用服务计划后，最好进行扩展。  在 ASE 中，始终需要具有至少 2 个应用服务计划的实例，以便为应用提供容错能力。  在 ASE 中缩放应用服务计划的方式与平时通过应用服务计划 UI 进行操作的方式一样。  有关缩放的详细信息，请参阅[如何在应用服务环境中缩放 Web 应用](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[Appserviceplans]: ../azure-web-sites-web-hosting-plans-in-depth-overview.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[ResourceGroups]: ../../azure-resource-manager/resource-group-overview.md
[AzurePowershell]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/
