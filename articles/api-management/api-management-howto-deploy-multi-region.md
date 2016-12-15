---
title: "如何将 Azure API 管理服务实例部署到多个 Azure 区域"
description: "了解如何将 Azure API 管理服务实例部署到多个 Azure 区域。"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 47389ad6-f865-4706-833f-846115e22e4d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2016
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: d99e2d885d56e3752a3b1caf51e52c801acaab52
ms.openlocfilehash: 98201867fd8b1b5f074aa6135e04b04faf384224


---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>如何将 Azure API 管理服务实例部署到多个 Azure 区域
API 管理多区域部署，该部署可使 API 发布者在任意数量的所需 Azure 区域中分配单个 API 管理服务。 这有助于减少地理上分散的 API 使用者所感知的请求延迟，并且还改善其中一个区域处于离线状态时的服务可用性。 

最初创建 API 管理服务时，它只包含一个[单位][单位]，并且驻留在单个 Azure 区域中，该区域指定为主要区域。 可通过 Azure 门户轻松添加其他区域。 API 管理网关服务器部署到每个区域，并且调用流量将路由到最近的网关。 如果一个区域处于离线状态，则传入流量自动重定向到下一个最近的网关。 

> [!IMPORTANT]
> 多区域部署仅在**[高级][高级]**层中可用。
> 
> 

## <a name="add-region"> </a>将 API 管理服务实例部署到新区域
> [!NOTE]
> 如果尚未创建 API 管理服务实例，请参阅 [创建 API 管理服务实例][创建 API 管理服务实例]教程中的[创建 API 管理服务实例][创建 API 管理服务实例]。
> 
> 

在 Azure 门户中，导航到 API 管理服务实例的“规模和定价”页。 

![“缩放”选项卡][api-management-scale-service]

若要部署到新的区域，请单击工具栏中的“+ 添加区域”。

![添加区域][api-management-add-region]

从下拉列表中选择位置，并通过滑块为其设置单位数。

![指定单位][api-management-select-location-units]

单击“添加”将选择放置在“位置”表中。 

重复此过程，直到配置所有位置，然后单击工具栏中的“保存”，启动部署过程。

## <a name="remove-region"> </a>从位置中删除 API 管理服务实例
在 Azure 门户中，导航到 API 管理服务实例的“规模和定价”页。 

![“缩放”选项卡][api-management-scale-service]

若要删除位置，请使用表右端的 **...** 按钮打开上下文菜单。 选择“删除”选项。

![删除区域][api-management-remove-region]

确认删除，然后单击“保存”应用所做的更改。

[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[创建 API 管理服务实例]: api-management-get-started.md#create-service-instance
[创建 API 管理服务实例]: api-management-get-started.md

[将 API 管理服务实例部署到新区域]: #add-region
[从区域中删除 API 管理服务实例]: #remove-region

[单位]: http://azure.microsoft.com/pricing/details/api-management/
[高级]: http://azure.microsoft.com/pricing/details/api-management/




<!--HONumber=Nov16_HO3-->


