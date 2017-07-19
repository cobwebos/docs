---
title: "创建外部 Azure 应用服务环境"
description: "阐释如何在创建应用或独立项时创建 Azure 应用服务环境"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 94dd0222-b960-469c-85da-7fcb98654241
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 3f1418b71a7327264e29e3f08ef97b254ee9930d
ms.contentlocale: zh-cn
ms.lasthandoff: 06/26/2017

---
# <a name="create-an-external-app-service-environment"></a>创建外部应用服务环境 #

应用服务环境 (ASE) 是指 Azure 应用服务部署到 Azure 虚拟网络 (VNet) 的子网中。 可通过两种方式来部署 ASE：

- 使用外部 IP 地址上的 VIP，它通常称为外部 ASE。
- 使用内部 IP 地址上的 VIP，它通常被称为 ILB ASE，因为内部终结点是一个内部负载平衡器 (ILB)。

本文介绍如何创建外部 ASE。 有关 ASE 的概述，可先参阅[应用服务环境简介][Intro]。若要详细了解如何创建 ILB ASE，请参阅[创建和使用 ILB ASE][MakeILBASE]。

## <a name="before-you-create-your-ase"></a>创建 ASE 前的准备工作 ##

请务必记住在 ASE 创建后不可更改的内容，包括：

- 位置
- 订阅
- 资源组
- 使用的虚拟网络
- 使用的子网
- 子网大小

> [!NOTE]
> 选择虚拟网络和指定子网时，请确保大小足够容纳任何未来增长。 建议大小是地址长度为 128 位的 `/25`。
>

## <a name="three-ways-to-create-an-ase"></a>创建 ASE 的 3 种方法 ##

创建 ASE 有 3 种方法。 可在以下情况或通过以下方式创建 ASE：

- 在创建应用服务计划时，只需一步即可创建 ASE 和应用服务计划。
- 通过独立 ASE 创建 UI，这会创建一个不含任何内容的 ASE。 该 ASE 创建 UI 体验更为高级，可由此通过内部负载均衡器 (ILB) 创建 ASE。
- 通过 Resource Manager 模板。 这适用于高级用户，详情请参见[基于模板创建 ASE][MakeASEfromTemplate]。

未使用 ILB 创建的 ASE 具有公共 VIP。 这意味着到 ASE 中的应用的所有 HTTP 流量都将命中一个可访问 Internet 的 IP 地址： 通过 ILB 创建的 ASE 在虚拟网络 IP 地址上拥有终结点。 这些应用不直接向 Internet 公开。

## <a name="create-an-ase-and-an-app-service-plan-together"></a>同时创建 ASE 和应用服务计划 ##

应用服务计划是一个应用容器。 在应用服务中创建应用时，始终需要选择或创建一个应用服务计划。 容器模型是：环境保存应用服务计划，应用服务计划保存应用。

若要同时创建应用服务计划和 ASE：

1. 在 [Azure 门户](https://portal.azure.com/)中，单击“新建”&gt;“Web + 移动”&gt;“Web 应用”

    ![][1]
1. 选择你的订阅。 如果有多个订阅，请注意，若要在 ASE 中创建应用，需要使用创建 ASE 时所用的订阅进行创建。
1. 选择或创建资源组。 资源组可让你以单位形式管理相关的 Azure 资源，并可在为应用创建基于角色的访问控制 (RBAC) 规则时发挥作用。 有关详细信息，请参阅 [Azure Resource Manager 概述][ARMOverview]。
1. 单击应用服务计划并选择“新建”。

    ![][2]
1. 在“位置”下拉列表中，选择要创建 ASE 的区域。 如果选择现有 ASE，则不会开始新 ASE 的创建，而仅在所选 ASE 中创建应用服务计划。
1. 单击“定价计划”UI，选择一个独立定价 SKU。 如果选择一个独立 SKU 卡和一个非 ASE 的位置，即表示你希望在此位置新建一个 ASE。 单击定价卡页面上的“选择”后，执行此操作会显示 ASE 创建 UI。 独立的 SKU 仅能与 ASE 结合使用。 此外，仅可在独立的 ASE 中使用任何其他定价 SKU。

    ![][3]
1. 输入你的 ASE 的名称。 ASE 名称在应用的可寻址名称中使用。 如果 ASE 名称为 _appsvcenvdemo_，则子域名称为 *appsvcenvdemo.p.azurewebsites.net*。 如果创建了名为 *mytestapp* 的应用，则可在 *mytestapp.appsvcenvdemo.p.azurewebsites.net* 中访问它。 不能在 ASE 名称中使用空格。 如果在名称中使用大写字符，域名将为该名称的全小写形式。

    ![][4]
1. 选择“新建”或“选择现有”。 只有所选区域中拥有虚拟网络时，才可选择现有虚拟网络。 如果选择“新建”，需为虚拟网络提供一个名称；将创建带有该名称的新 Resource Manager 虚拟网络，该网络在所选区域中的地址空间为 `192.168.250.0/23`。 如果选择“选择现有”，则需要：
    1. 选择虚拟网络地址块（若有多个）。
    2. 提供新的子网名称。
    3. 选择子网的大小。 **请注意：大小应该足够容纳 ASE 未来任何的增长。** 建议的大小是地址长度为 128 位的 `/25`，并可处理最大大小的 ASE。 例如，建议不要使用 `/28`，因为仅有 16 位地址可用。 基础结构需求将至少占用 5 个地址，`/28` 子网中最多只剩下 11 个实例。
    4. 选择子网 IP 范围。

选择“创建”后，就会开始 ASE 创建过程。 这还将创建应用服务计划和应用。 ASE、应用服务计划和应用都将位于同一订阅和同一资源组中。 如果需要 ASE 与应用服务计划和应用分属于不同的资源组，或者需要 ILB ASE，请采用独立 ASE 创建体验。

## <a name="create-an-ase-by-itself"></a>自动创建 ASE ##

逐步执行独立 ASE 创建流会创建不含任何内容的 ASE。 空的 ASE 存在基础结构，每月仍会产生费用。 完成此工作流的主要目的是通过 ILB 创建 ASE 或在其自身的资源组中创建 ASE。 创建 ASE 之后，可通过使用常规应用创建体验和选择新 ASE 作为位置在此 ASE 中创建应用。

可通过两种方式访问 ASE 创建 UI：在 Azure 应用商店中搜索“应用服务环境”，或者访问“新建”-&gt;“Web + 移动”-&gt;“应用服务环境”。 若要使用独立创建体验创建 ASE：

1. 提供 ASE 的名称。 为 ASE 指定的名称将用于在 ASE 中创建的应用。 如果 ASE 的名称为 *mynewdemoase*，则子域名称为 *.mynewdemoase.p.azurewebsites.net*。 如果创建了名为 *mytestapp* 的应用，则可在 *mytestapp.mynewdemoase.p.azurewebsites.net* 中访问它。 不能在 ASE 名称中使用空格。 如果在名称中使用大写字符，域名将为该名称的全小写形式。 如果使用 ILB，则不在子域中使用 ASE 名称，但会在 ASE 创建过程中显式声明该名称。

    ![][5]
1.  选择你的订阅。 用于 ASE 的订阅也会是用于创建该 ASE 中的所有应用的订阅。 不能将 ASE 放入位于其他订阅的虚拟网络中。
1.  选择或指定新的资源组。 ASE 所用的资源组必须与虚拟网络使用的相同。 如果选择现有虚拟网络，则 ASE 的资源组选择将更新，以反映虚拟网络的资源组。

    ![][6]
1. 选择虚拟网络和位置。 可选择创建新的虚拟网络或选择现有的虚拟网络。 如果选择新的虚拟网络，则可指定名称和位置。 新虚拟网络的地址范围为 192.168.250.0/23，并拥有定义为 192.168.250.0/24 的 **default** 子网。 仅可选择一个 Resource Manager 虚拟网络。 “VIP 类型”选择决定 ASE 能否从 Internet（外部）直接访问或是否使用内部负载均衡器 (ILB)。 若要了解详细信息，请参阅[在应用服务环境中使用内部负载均衡器][MakeILBASE]。 如果选择外部 VIP 类型，则可选择为实现基于 IP 的 SSL 而创建系统时所用的外部 IP 地址数。 如果选择“内部”，则需要指定 ASE 要使用的子域。 可以将 ASE 部署到使用公共地址范围*或* RFC1918 地址空间 专用地址）的虚拟网络。 为使用具有公用地址范围的虚拟网络，需要提前创建虚拟网络。 如果选择现有的虚拟网络，需要在 ASE 创建期间创建新的子网。 **不能在门户中使用预先创建的子网。如果使用 Resource Manager 模板创建 ASE，则可以创建具有现有子网的 ASE。** 若要从模板创建 ASE，请参阅[从模板创建应用服务环境][MakeASEfromTemplate]

## <a name="app-service-environment-v1"></a>应用服务环境 v1 ##

仍可创建初版 ASE 功能 (ASEv1) 的实例。 若要获得该体验，请在应用商店内搜索“应用服务环境 v1”。 该创建体验与独立 ASE 创建体验相同。 完成后，将通过两个前端和两个辅助角色创建 ASEv1。 ASEv1 创建后，需要管理前端和辅助角色。 它们不会在创建应用服务计划时自动添加。 前端充当 HTTP/HTTPS 终结点并将流量发送到辅助角色，后者是托管应用的角色。 可在创建 ASE 后调整数量。 若要深入了解 ASEv1，请参阅[应用服务环境 v1 简介][ASEv1Intro]。 有关 ASEv1 缩放、管理和监视的更多详细信息，请参阅[如何配置应用服务环境][ConfigureASEv1]。

<!--Image references-->
[1]: ./media/how_to_create_an_external_app_service_environment/createexternalase-create.png
[2]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreate.png
[3]: ./media/how_to_create_an_external_app_service_environment/createexternalase-pricing.png
[4]: ./media/how_to_create_an_external_app_service_environment/createexternalase-embeddedcreate.png
[5]: ./media/how_to_create_an_external_app_service_environment/createexternalase-standalonecreate.png
[6]: ./media/how_to_create_an_external_app_service_environment/createexternalase-network.png



<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[ASEReadme]: ./readme.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: ../../app-service-web/app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: ../../app-service-web/app-service-app-service-environment-intro.md
[webapps]: ../../app-service-web/app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[APIapps]: ../../app-service-api/app-service-api-apps-why-best-platform.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md

