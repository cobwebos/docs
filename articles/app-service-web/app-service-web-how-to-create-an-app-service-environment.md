---
title: "如何创建应用服务环境"
description: "App Service 环境的创建流描述"
services: app-service
documentationcenter: 
author: ccompy
manager: stefsch
editor: 
ms.assetid: 81bd32cf-7ae5-454b-a0d2-23b57b51af47
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2016
ms.author: ccompy
translationtype: Human Translation
ms.sourcegitcommit: af98ae3b425e2e0584da53721249dbcd605a17d1
ms.openlocfilehash: 8d8e1ce7758691a392fdb6d6459e04c6b4f7f5f6


---
# <a name="how-to-create-an-app-service-environment"></a>如何创建应用服务环境
### <a name="overview"></a>概述
应用服务环境 (ASE) 是 Azure 应用服务的一个高级服务选项，可提供多租户戳记中不会提供的增强型配置功能。  ASE 功能实质上是将 Azure 应用服务部署到客户的虚拟网络。  若要更好地理解 App Service 环境所提供的功能，请阅读文档：[什么是 App Service 环境][WhatisASE]。

### <a name="before-you-create-your-ase"></a>创建 ASE 前的准备工作
请务必了解不能更改的事项。  创建 ASE 后，不能更改的方面包括：

* 位置
* 订阅
* 资源组
* 使用的 VNet
* 使用的子网 
* 子网大小

选择 VNet 及指定子网时，请确保大小足够容纳任何未来增长。  

### <a name="creating-an-app-service-environment"></a>创建 App Service 环境？
有两种方法来访问 ASE 创建 UI。  一种是通过在 Azure 应用商店中搜索***应用服务环境***，一种是通过“新建”->“Web + 移动”->“应用服务环境”完成。  若要创建 ASE，请执行以下操作：

1. 提供 ASE 的名称。  为 ASE 指定的名称将用于在 ASE 中创建的应用。  如果 ASE 名称为 appsvcenvdemo，则子域名称将是 *appsvcenvdemo.p.azurewebsites.net*。  因此，如果创建了名为 *mytestapp* 的应用，则可在 *mytestapp.appsvcenvdemo.p.azurewebsites.net* 中访问它。  不能在 ASE 名称中使用空格。  如果在名称中使用大写字符，域名将为该名称的全小写形式。  如果使用 ILB，则不在子域中使用 ASE 名称，但会在 ASE 创建过程中显式声明该名称
   
    ![][1]
2. 选择你的订阅。  用于 ASE 的订阅也会是用于创建该 ASE 中的所有应用的订阅。  不能将 ASE 放入位于其他订阅中的 VNet
3. 选择或指定新的资源组。  用于 ASE 的资源组必须与用于 VNet 的资源组相同。  如果选择预先存在的 VNet，则 ASE 的资源组选择会更新，以反映 VNet 的资源组。
   
    ![][2]
4. 选择虚拟网络和位置。  可以选择创建新的 VNet，也可以选择预先存在的 VNet。  如果选择新的 VNet，则可以指定名称和位置。 新 VNet 的地址范围为 192.168.250.0/23，并拥有定义为 192.168.250.0/24 的名称为 **default** 的子网。  还可以直接选择预先存在的经典或 Resource Manager VNet。  选择的 VIP 类型确定是否可以从 Internet（外部）直接访问 ASE，或 ASE 是否使用内部负载均衡器 (ILB)。  若要了解更多详细信息，请参阅[在应用服务环境中使用内部负载均衡器][ILBASE]。  如果选择外部 VIP 类型，则可以选择系统创建用于 IPSSL 的外部 IP 地址数量。  如果选择内部，则需要指定 ASE 要使用的子域。  可以将 ASE 部署到使用公共地址范围*或* RFC1918 地址空间 （即专用地址）的虚拟网络。  若要使用具有公用地址范围的虚拟网络，需要提前创建 VNet。  如果选择预先存在的 VNet，需要在 ASE 创建期间创建新的子网。  **不能在门户中使用预先创建的子网。如果使用 Resource Manager 模板创建 ASE，则可以创建具有预先存在的子网的 ASE。**  若要从模板创建 ASE，请使用此处的信息：[从模板创建应用服务环境][ILBAseTemplate]和[从模板创建 ILB 应用服务环境][ASEfromTemplate]。

### <a name="details"></a>详细信息
创建的 ASE 具有 2 个前端和 2 个辅助角色。  前端充当 HTTP/HTTPS 终结点，并将流量发送到作为托管应用程序的角色的工作程序。   可以在创建 ASE 后调整数量，甚至可以设置这些资源池的自动缩放规则。  有关手动缩放、管理和监视应用服务环境的更多详细信息，请参阅此文：[如何配置应用服务环境][ASEConfig] 

ASE 使用的子网中只能存在该 ASE。  该子网不能用于该 ASE 以外的任何对象

### <a name="after-app-service-environment-creation"></a>在创建 App Service 环境后
在创建 ASE 后可以调整：

* 前端的数量（最小：2 个）
* 辅助角色的数量（最小：2 个）
* 可用于 IP SSL 的 IP 地址数量
* 前端或辅助角色使用的计算资源大小（前端最小大小为 P2）

有关手动缩放、管理和监视应用服务环境的更多详细信息，请参阅此文：[如何配置应用服务环境][ASEConfig] 

有关自动缩放的信息，请参阅此指南：[如何配置应用服务环境的自动缩放][ASEAutoscale]

存在不可用于自定义（如数据库和存储器）的其他依赖项。  这些是由 Azure 处理且随系统出现的。  系统存储对于整个应用服务环境最多可支持 500 GB，Azure 会根据系统规模的需要来调整数据库。

## <a name="getting-started"></a>入门
[README for Application Service Environments](../app-service/app-service-app-service-environments-readme.md)（应用程序服务环境自述文件）中提供了有关应用服务环境的所有文章和操作说明。

若要开始使用 App Service 环境，请参阅 [App Service 环境简介][WhatisASE]

有关 Azure App Service 平台的详细信息，请参阅 [Azure App Service][AzureAppService]。

[!INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ASEConfig]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/ 
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ILBASE]: http://azure.microsoft.com/documentation/articles/app-service-environment-with-internal-load-balancer/
[ILBAseTemplate]: http://azure.microsoft.com/documentation/templates/201-web-app-ase-create/
[ASEfromTemplate]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-create-ilb-ase-resourcemanager/



<!--HONumber=Dec16_HO3-->


