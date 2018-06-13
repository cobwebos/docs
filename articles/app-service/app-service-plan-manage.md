---
title: 在 Azure 中管理应用服务计划 | Microsoft Docs
description: 了解如何执行不同的任务来管理应用服务计划。
keywords: 应用服务, azure 应用服务, 缩放, 应用服务计划, 更改, 创建, 管理
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cephalin
ms.openlocfilehash: 1dfe8a903e19ff524a1c4a0228e6aefcbe9ff183
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
ms.locfileid: "29117674"
---
# <a name="manage-an-app-service-plan-in-azure"></a>在 Azure 中管理应用服务计划

[Azure 应用服务计划](azure-web-sites-web-hosting-plans-in-depth-overview.md)提供应用服务应用需要运行的资源。 本指南介绍如何管理应用服务计划。

## <a name="create-an-app-service-plan"></a>创建应用服务计划

> [!TIP]
> 如果已有应用服务环境，请参阅[在应用服务环境中创建应用服务计划](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan)。

在创建应用时可以创建一个空的应用服务计划，也可以创建一个计划。

1. 在 [Azure 门户](https://portal.azure.com)中，选择“新建” > “Web + 移动”，然后选择“Web 应用”或其他应用服务应用类型。

2. 选择现有的应用服务计划，或者为新应用创建计划。

   ![在 Azure 门户中创建应用。][createWebApp]

   若要创建计划，请执行以下操作：

   a. 选择“[+] 新建”。

      ![创建应用服务计划。][createASP] 

   b. 对于“应用服务计划”，输入计划的名称。

   c. 对于“位置”，选择适当的位置。

   d.单击“下一步”。 对于“定价层”，选择适当的服务定价层。 选择“全部查看”以查看其他定价选项，例如“免费”和“共享”。 选择定价层后，单击“选择”按钮。

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>将应用移到另一个应用服务计划

只要源计划和目标计划在_同一个资源组和地理区域_中，就可将应用移到另一个应用服务计划。

1. 在 [Azure 门户](https://portal.azure.com)中，浏览到要移动的应用。

2. 在菜单上，查找“应用服务计划”部分。

3. 选择“更改应用服务计划”以打开“应用服务计划”选择器。

   ![应用服务计划选择器。][change] 

4. 在“应用服务计划”选择器中，选择要将此应用移到的现有计划。   

> [!IMPORTANT]
> “选择应用服务计划”页已根据以下条件筛选： 
> - 在同一资源组中 
> - 在同一地理区域中 
> - 在同一 Web 空间中  
> 
> Web 空间是应用服务中的逻辑构造，用于定义服务器资源的分组。 地理区域（例如“美国西部”）包含许多 Web 空间，以便分配使用应用服务的客户。 目前，不能在 Web 空间之间移动应用服务资源。 
> 

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

每个计划都有自己的定价层。 例如，将站点从“免费”层移到“标准”层时，分配给站点的所有应用都可使用“标准”层的功能和资源。 但是，将应用从更高的分层计划移到更低的分层计划意味着不再有权访问某些功能。 如果应用使用的功能在目标计划中不可用，则会出现错误，指出哪个正在使用的功能不可用。 

例如，如果其中一个应用使用 SSL 证书，你可能会看到此错误消息：

`Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

在这种情况下，在将应用移到目标计划之前，需要执行以下任一操作：
- 将目标计划的定价层向上扩展到**基本**或更高层。
- 删除应用的所有 SSL 连接。

## <a name="move-an-app-to-a-different-region"></a>将应用移到不同的区域

运行应用的区域是该应用的应用服务计划所在的区域。 但是，无法更改应用服务计划的区域。 如果想要在不同的区域中运行应用，替代方法是使用应用克隆。 不管位于什么区域，克隆都会在新的或现有的应用服务计划中复制应用。

可以在菜单的“开发工具”部分中找到“克隆应用”。

> [!IMPORTANT]
> 克隆具有一些限制。 可以在 [Azure 应用服务应用克隆](app-service-web-app-cloning.md)中查看相关信息。

## <a name="scale-an-app-service-plan"></a>缩放应用服务计划

若要纵向扩展应用服务计划的定价层，请参阅[在 Azure 中纵向扩展应用](web-sites-scale.md)。

若要增加应用的实例计数，请参阅[手动或自动缩放实例计数](../monitoring-and-diagnostics/insights-how-to-scale.md)。

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>删除应用服务计划

为了避免产生意外的费用，删除应用服务计划中的最后一个应用时，应用服务默认也会删除该计划。 如果改为选择保留该计划，应将该计划更改为“免费”层，以免产生费用。

> [!IMPORTANT]
> 未与任何应用关联的应用服务计划仍会产生费用，因为它们继续保留配置的 VM 实例。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [纵向扩展 Azure 中的应用](web-sites-scale.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
