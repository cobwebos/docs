---
title: 纵向扩展功能和容量 - Azure 应用服务 | Microsoft Docs
description: 了解如何扩展 Azure 应用服务中的应用规模以增加容量和功能。
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 7d51fa43eaaef756439663907faa51e917e6147a
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642456"
---
# <a name="scale-up-an-app-in-azure-app-service"></a>在 Azure App Service 中纵向扩展应用

本文介绍如何在 Azure 应用服务中缩放应用。 缩放的工作流有两种：纵向扩展和横向扩展；本文介绍纵向扩展工作流。

* [纵向扩展](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)：获取更多 CPU、内存、磁盘空间和额外功能，例如专用虚拟机 (VM)、自定义域和证书、过渡槽、自动缩放以及更多功能。 可以通过更改应用所属的应用服务计划的定价层来提升。
* [横向扩展](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)：增加用于运行应用的 VM 实例数。
  最多可以向外缩放到 20 个实例，具体取决于定价层。 独立层中的[应用服务环境](environment/intro.md)会进一步将横向扩展计数增加到 100 个实例。 有关向外缩放的详细信息，请参阅[手动或自动缩放实例计数](../monitoring-and-diagnostics/insights-how-to-scale.md)。 可在该文中了解如何使用自动缩放，即根据预定义的规则和计划自动缩放实例计数。

缩放设置仅需几秒即可应用，并且会影响[应用服务计划](../app-service/overview-hosting-plans.md)中的所有应用。
缩放设置不需要更改代码或重新部署应用程序。

有关各个应用服务计划的定价和功能的信息，请参阅[应用服务定价详细信息](https://azure.microsoft.com/pricing/details/web-sites/)。  

> [!NOTE]
> 在从**免费**层切换应用服务计划之前，必须首先删除对 Azure 订阅施加的[支出限制](https://azure.microsoft.com/pricing/spending-limits/)。 若要查看或更改 Microsoft Azure 应用服务订阅的选项, 请参阅[Microsoft Azure 订阅][azuresubscriptions]。
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>向上缩放定价层

> [!NOTE]
> 要纵向扩展到 PremiumV2 层，请参阅[为应用服务配置 PremiumV2 层](app-service-configure-premium-tier.md)。
>

1. 在浏览器中，打开 [Azure 门户][portal]。

1. 在应用服务应用页的左侧菜单中, 选择 "**纵向扩展 (应用服务计划)** "。
   
3. 选择层, 然后选择 "**应用**"。 选择不同的类别 (例如, "**生产**"), 并**查看其他选项**以显示更多层。
   
    ![导航以纵向扩展 Azure 应用规模。][ChooseWHP]

    操作完成后, 会看到一个通知弹出窗口, 其中包含绿色的 "成功" 复选标记。

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>与缩放相关的资源
如果应用依赖于其他服务，如 Azure SQL 数据库或 Azure 存储，则可单独对这些资源进行纵向扩展。 这些资源不由应用服务计划管理。

1. 在应用的 "**概述**" 页中, 选择 "**资源组**" 链接。
   
    ![与向上缩放 Azure 应用相关的资源](./media/web-sites-scale/RGEssentialsLink.png)

2. 在 "**资源组**" 页的 "**摘要**" 部分中, 选择要缩放的资源。 以下屏幕截图显示了 SQL 数据库资源。
   
    ![导航到资源组页面对 Azure 应用进行纵向扩展](./media/web-sites-scale/ResourceGroup.png)

    若要纵向扩展相关资源, 请参阅特定资源类型的文档。 例如, 若要纵向扩展单个 SQL 数据库, 请参阅[在 AZURE SQL 数据库中缩放单一数据库资源](../sql-database/sql-database-single-database-scale.md)。 若要纵向扩展 Azure Database for MySQL 资源, 请参阅[缩放 MySQL 资源](../mysql/concepts-pricing-tiers.md#scale-resources)。

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>比较定价层

有关详细信息（例如每个定价层的 VM 大小），请参阅[应用服务定价详细信息](https://azure.microsoft.com/pricing/details/app-service)。

有关服务限制、配额和约束的表以及每个层级所支持的功能，请参阅[应用服务限制](../azure-subscription-service-limits.md#app-service-limits)。

<a name="Next Steps"></a>

## <a name="more-resources"></a>更多资源

有关缩放应用服务应用的视频，请参阅以下资源：
 
* [When to Scale Azure Websites](https://azure.microsoft.com/resources/videos/azure-web-sites-free-vs-standard-scaling/)（何时缩放 Azure 网站）- 主讲人：Stefan Schackow
* [Auto Scaling Azure Websites, CPU or Scheduled](https://azure.microsoft.com/resources/videos/auto-scaling-azure-web-sites/)（自动缩放 Azure 网站、CPU 或计划）- 主讲人：Stefan Schackow
* [How Azure Websites Scale](https://azure.microsoft.com/resources/videos/how-azure-web-sites-scale/)（Azure 网站如何缩放）- 主讲人：Stefan Schackow

<!-- LINKS -->
[vmsizes]:https://azure.microsoft.com/pricing/details/app-service/
[SQLaccountsbilling]:https://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:https://account.windowsazure.com/subscriptions
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png
