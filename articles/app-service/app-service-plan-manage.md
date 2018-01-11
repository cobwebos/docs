---
title: "在 Azure 中管理应用服务计划 | Microsoft Docs"
description: "了解如何执行不同的任务来管理应用服务计划。"
keywords: "应用服务, azure 应用服务, 缩放, 应用服务计划, 更改, 创建, 管理"
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cephalin
ms.openlocfilehash: 61179c5bf29ed2c338b45ba909ec01237806cf26
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2018
---
# <a name="manage-an-app-service-plan-in-azure"></a>在 Azure 中管理应用服务计划

[应用服务计划](azure-web-sites-web-hosting-plans-in-depth-overview.md)提供应用服务应用需要运行的资源。 本操作方法指南介绍如何管理应用服务计划。

## <a name="create-an-app-service-plan"></a>创建应用服务计划

> [!TIP]
> 如果已有应用服务环境，请参阅[在应用服务环境中创建应用服务计划](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan)。

可以在创建应用时创建一个空的应用服务计划。

在 [Azure 门户](https://portal.azure.com)中，单击**“新建”** > **“Web + 移动”**，然后选择**“Web 应用”**或其他应用服务应用类型。

![在 Azure 门户中创建应用。][createWebApp]

然后可以选择现有的应用服务计划，或者为新应用创建计划。

 ![创建应用服务计划。][createASP]

要创建应用服务计划，可单击“[+] 新建”，键入“应用服务计划”名称，并选择相应的“位置”。 单击“定价层”，并为服务选择适当的服务定价层。 选择“全部查看”以查看其他定价选项，例如“免费”和“共享”。 

选择定价层后，单击“选择”按钮。

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>将应用移到另一个应用服务计划

只要源计划和目标计划在_同一个资源组和地理区域_中，就可将应用移到另一个应用服务计划。

若要将应用移到另一个计划，请在 [Azure 门户](https://portal.azure.com)中导航到要移动的应用。

在“菜单”中，查找“应用服务计划”部分。

选择“更改应用服务计划”启动该过程。

“更改应用服务计划”将打开“应用服务计划”选择器。 选择要将此应用移到的现有计划。 

> [!IMPORTANT]
> “选择应用服务计划”页已根据以下条件筛选： 
> - 在同一资源组中 
> - 在同一地理区域中 
> - 在同一 Web 空间中  
> 
> Web 空间是应用服务中的逻辑构造，用于定义服务器资源的分组。 地理区域（例如“美国西部”）包含许多 Web 空间，旨在通过应用服务分配客户。 目前无法在 Web 空间之间移动应用服务资源。 
> 

![应用服务计划选择器。][change]

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

每个计划都有自己的定价层。 例如，将站点从“免费”层移到“标准”层时，分配给站点的所有应用都可使用“标准”层的功能和资源。 但是，将应用从更高的分层计划移到更低的分层计划意味着不再有权访问某些功能。 如果应用使用的功能在目标计划中不可用，则会出现错误，指出哪个正在使用的功能不可用。 例如，如果某个应用使用 SSL 证书，则可能会出现以下错误消息：`Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`在这种情况下，需要将目标计划的定价层提高到“基本”或更高，或者需要删除与应用建立的所有 SSL 连接，然后才能将该应用移到目标计划。

## <a name="move-an-app-to-a-different-region"></a>将应用移到不同的区域

运行应用的区域是该应用的应用服务计划所在的区域。 但是，无法更改应用服务计划的区域。 如果想要在不同的区域中运行应用，替代方法是使用应用克隆。 不管位于什么区域，克隆都会在新的或现有的应用服务计划中复制应用。

可以在菜单的“开发工具”部分中找到“克隆应用”。

> [!IMPORTANT]
> 克隆有一些限制，详见 [Azure 应用服务应用克隆](app-service-web-app-cloning.md)。

## <a name="scale-an-app-service-plan"></a>缩放应用服务计划

若要纵向扩展应用服务计划的定价层，请参阅[在 Azure 中纵向扩展应用](web-sites-scale.md)。

若要增加应用的实例计数，请参阅[手动或自动缩放实例计数](../monitoring-and-diagnostics/insights-how-to-scale.md)。

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>删除应用服务计划

为了避免产生意外的费用，删除应用服务计划中的最后一个应用时，应用服务默认也会删除该计划。 如果选择保留该计划，应将该计划更改为“免费”层，以免产生费用。

> [!IMPORTANT]
> 未与任何应用关联的**应用服务计划**仍会产生费用，因为它们继续保留配置的 VM 实例。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [纵向扩展 Azure 中的应用](web-sites-scale.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
