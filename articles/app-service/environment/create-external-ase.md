---
title: "创建外部 Azure 应用服务环境"
description: "阐释如何在创建应用或独立项时，创建应用服务环境"
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
ms.openlocfilehash: 70c43b25aea364d7254137b46af31f851dcf8bc6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-external-app-service-environment"></a>创建外部应用服务环境 #

Azure 应用服务环境是指将 Azure App Service 部署到 Azure 虚拟网络 (VNet) 的子网中。 可通过两种方法部署应用服务环境 (ASE)：

- 使用外部 IP 地址上的 VIP，通常称为外部 ASE。
- 使用内部 IP 地址上的 VIP，通常称为 ILB ASE，因为内部终结点是一个内部负载均衡器 (ILB)。

本文介绍如何创建外部 ASE。 有关 ASE 的概述，请参阅[应用服务环境简介][Intro]。 若要了解如何创建 ILB ASE，请参阅[创建和使用 ILB ASE][MakeILBASE]。

## <a name="before-you-create-your-ase"></a>创建 ASE 前的准备工作 ##

创建 ASE 后，不能更改以下项：

- 位置
- 订阅
- 资源组
- 使用的 VNet
- 使用的子网
- 子网大小

> [!NOTE]
> 选择 VNet 和指定子网时，请确保大小足够容纳未来增长。 建议大小是地址长度为 128 位的 `/25`。
>

## <a name="three-ways-to-create-an-ase"></a>创建 ASE 的 3 种方法 ##

可通过 3 种方法创建 ASE：

- 创建应用服务计划时。 使用此方法，只需 1 步即可创建 ASE 和应用服务计划。
- 作为独立项操作。 此方法可创建独立的 ASE，其中不包含任何内容。 此方法是创建 ASE 的更高级过程。 可由此通过 ILB 创建 ASE。
- 通过 Azure 资源管理器模板。 此方法适用于高级用户。 有关详细信息，请参阅[从模板创建 ASE][MakeASEfromTemplate]。

外部 ASE 具有公共 VIP，这意味着到 ASE 中应用的所有 HTTP/HTTPS 流量都会命中一个可访问 Internet 的 IP 地址。 通过 ILB 创建的 ASE 具有 ASE 所用子网的 IP 地址。 托管在 ILB ASE 中的应用不直接向 Internet 公开。

## <a name="create-an-ase-and-an-app-service-plan-together"></a>同时创建 ASE 和应用服务计划 ##

应用服务计划是一个应用容器。 在应用服务中创建应用时，需选择或创建应用服务计划。 容器模型环境保存应用服务计划，应用服务计划保存应用。

若要同时创建 ASE 和应用服务计划：

1. 在 [Azure 门户](https://portal.azure.com/)中，选择“新建” > “Web + 移动” > “Web 应用”。

    ![创建 Web 应用][1]

2. 选择订阅。 在相同订阅中创建应用和 ASE。

3. 选择或创建资源组。 使用资源组，可以单位形式管理相关的 Azure 资源。 为应用建立基于角色的访问控制 (RBAC) 规则时，资源组也可发挥作用。 有关详细信息，请参阅 [Azure 资源管理器概述][ARMOverview]。

4. 选择应用服务计划，然后选择“新建”。

    ![新建应用服务计划][2]

5. 在“位置”下拉列表中，选择要创建 ASE 的区域。 如果选择现有 ASE，则不会新建 ASE。 应用服务计划将在所选 ASE 中进行创建。 

6. 选择“定价层”，然后选择其中一个“独立”定价 SKU。 如果选择独立 SKU 卡和非 ASE 的位置，则会在该位置新建一个 ASE。 若要开始创建 ASE 的过程，请选择“选择”。 独立 SKU 仅能与 ASE 结合使用。 此外，仅可在独立 ASE 中使用任何其他定价 SKU。

    ![定价层选择][3]

7. 输入你的 ASE 的名称。 此名称在应用的可寻址名称中使用。 如果 ASE 名称为 appsvcenvdemo，则域名为 .appsvcenvdemo.p.azurewebsites.net。 如果创建名为 mytestapp 的应用，则可在 mytestapp.appsvcenvdemo.p.azurewebsites.net 中访问它。 不能在名称中使用空格。 如果使用大写字符，则域名为该名称的全小写形式。

    ![新建应用服务计划名称][4]

8. 指定 Azure 虚拟网络详细信息。 选择“新建”或“选择现有”。 只有所选区域中具有 VNet 时，才可使用选择现有 VNet 选项。 如果选择“新建”，需输入 VNet 的名称。 随即创建带有该名称的新资源管理器 VNet。 它使用所选区域中的地址空间 `192.168.250.0/23`。 如果选择“选择现有”，则需要：

    a. 选择 VNet 地址块（若有多个）。

    b. 输入新的子网名称。

    c. 选择子网的大小。 请记住选择足够的大小，以容纳 ASE 的未来增长。 建议使用 `/25`，其地址长度为 128 位且能够容纳最大尺寸的 ASE。 例如，建议不要使用 `/28`，因为仅有 16 位地址可用。 基础结构至少占用 5 个地址。 在 `/28` 子网中，最多剩下 11 个缩放实例。

    d.单击“下一步”。 选择子网 IP 范围。

9. 选择“创建”以创建 ASE。 此过程还会创建应用服务计划和应用。 ASE、应用服务计划和应用都位于同一订阅和同一资源组中。 如果 ASE 需要单独的资源组，或者你需要 ILB ASE，请按照以下步骤自动创建 ASE。

## <a name="create-an-ase-by-itself"></a>自动创建 ASE ##

如果创建独立的 ASE，则其中不含任何内容。 空的 ASE 存在基础结构，每月仍会产生费用。 按照这些步骤通过 ILB 创建 ASE，或在其自身的资源组中创建 ASE。 创建 ASE 后，可使用常规过程在其中创建应用。 选择新 ASE 作为位置。

1. 在 Azure Marketplace 中搜索“应用服务环境”，或者选择“新建” > “Web + 移动” > “应用服务环境”。 

2. 输入 ASE 的名称。 此名称用于在 ASE 中创建的应用。 如果该名称为 mynewdemoase，则子域名为 .mynewdemoase.p.azurewebsites.net。 如果创建名为 mytestapp 的应用，则可在 mytestapp.mynewdemoase.p.azurewebsites.net 中访问它。 不能在名称中使用空格。 如果使用大写字符，则域名为该名称的全小写形式。 如果使用 ILB，则不在子域中使用 ASE 名称，但会在 ASE 创建过程中显式声明该名称。

    ![ASE 命名][5]

3. 选择订阅。 此订阅也是 ASE 中所有应用使用的订阅。 不能将 ASE 放入位于其他订阅中的 VNet。

4. 选择或指定新的资源组。 用于 ASE 的资源组必须与用于 VNet 的资源组相同。 如果选择现有 VNet，则 ASE 的资源组选择会更新，以反映 VNet 的资源组。 如果使用资源管理器模板，则可使用不同于 VNet 资源组的资源组来创建 ASE。 若要从模板创建 ASE，请参阅[从模板创建应用服务环境][MakeASEfromTemplate]。

    ![资源组选择][6]

5. 选择 VNet 和位置。 可选择创建新的 VNet，也可选择现有 VNet： 

    * 如果选择新的 VNet，则可指定名称和位置。 新 VNet 的地址范围为 192.168.250.0/23，并拥有名为“默认”的子网。 子网定义为 192.168.250.0/24。 仅可选择一个资源管理器 VNet。 “VIP 类型”选择决定 ASE 能否从 Internet（外部）直接访问或是否使用 ILB。 若要深入了解这些选项，请参阅[在应用服务环境中创建和使用内部负载均衡器][MakeILBASE]。 

      * 如果对“VIP 类型”选择“外部”，则可选择为实现基于 IP 的 SSL 而创建系统时所用的外部 IP 地址数。 
    
      * 如果对“VIP 类型”选择“内部”，则需指定 ASE 要使用的域。 可将 ASE 部署到使用公用或专用地址范围的 VNet。 若要使用具有公用地址范围的 VNet，需要提前创建 VNet。 
    
    * 如果选择现有 VNet，需要在 ASE 创建期间创建新的子网。 不能在门户中使用预先创建的子网。如果使用资源管理器模板，则可创建具有现有子网的 ASE。 若要从模板创建 ASE，请参阅[从模板创建应用服务环境][MakeASEfromTemplate]。

## <a name="app-service-environment-v1"></a>应用服务环境 v1 ##

仍可创建初版应用服务环境 (ASEv1) 的实例。 若要开始该过程，请在 Marketplace 内搜索“应用服务环境 v1”。 采用与创建独立 ASE 相同的方式创建 ASE。 完成后，ASEv1 就有两个前端和两个辅助角色。 ASEv1 创建后，必须管理前端和辅助角色。 它们不会在创建应用服务计划时自动添加。 前端充当 HTTP/HTTPS 终结点，并将流量发送给辅助角色。 辅助角色是托管应用的角色。 可在创建 ASE 后，调整前端和辅助角色的数量。 

若要深入了解 ASEv1，请参阅[应用服务环境 v1 简介][ASEv1Intro]。 若要深入了解如何缩放、管理和监视 ASEv1，请参阅[如何配置应用服务环境][ConfigureASEv1]。

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
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
