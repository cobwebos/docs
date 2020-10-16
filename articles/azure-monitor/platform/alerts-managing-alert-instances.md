---
title: 在 Azure Monitor 中管理警报实例
description: 管理整个 Azure 中的警报实例
ms.subservice: alerts
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 82905cba5f391365ada13f4e5df5ad139f4c121e
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92102864"
---
# <a name="manage-alert-instances-with-unified-alerts"></a>使用统一警报管理警报实例

使用 Azure Monitor 中的[统一警报体验](./alerts-overview.md)，可以看到 Azure 中的所有不同类型的警报。 这些警报来自多个订阅，显示在单个窗格中。 本文介绍了如何查看警报实例以及如何查找特定的警报实例以进行故障排除。

> [!NOTE]
> 只能访问过去 30 天内生成的警报。

## <a name="go-to-the-alerts-page"></a>转到“警报”页

可以通过以下任意一种方式转到“警报”页：

- 在 [Azure 门户](https://portal.azure.com/)中，选择“监视” > “警报”。  

     ![监视警报的屏幕截图](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
- 使用特定资源的上下文。 打开资源，转到“监视”  部分，然后选择“警报”  。 登陆页已经过预先筛选，以显示有关特定资源的警报。

     ![资源监视警报的屏幕截图](media/alerts-managing-alert-instances/alert-resource.JPG)

- 使用特定资源组的上下文。 打开资源组，转到“监视”  部分，然后选择“警报”  。 登陆页已经过预先筛选，以显示有关特定资源的警报。    

     ![资源组监视警报的屏幕截图](media/alerts-managing-alert-instances/alert-rg.JPG)

## <a name="find-alert-instances"></a>查找警报实例

“警报摘要”  页提供了整个 Azure 中的所有警报实例的概览。 可以通过选择**多个订阅**（最多 5 个）或通过在**资源组**、特定**资源**或**时间范围**中进行筛选来修改摘要视图。 选择“警报总数”  或任何严重性区段可以转到警报的列表视图。     

![“警报摘要”页的屏幕截图](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
在“所有警报”  页上，列出了 Azure 上的所有警报实例。 如果是从警报通知进入到门户中，则可以使用可用的筛选器在该特定的警报实例上收缩范围。

> [!NOTE]
> 如果你是通过选择任何严重性区段进入了该页面，则列表将针对该严重性进行预先筛选。

除了上一页上可用的筛选器之外，还可以基于监视服务（例如指标的平台）、监视条件（已触发或已解决）、严重性、警报状态（新的/已确认/已关闭）或智能组 ID 进行筛选。

![“所有警报”页的屏幕截图](media/alerts-managing-alert-instances/all-alerts.jpg)

> [!NOTE]
> 如果你是通过选择任何严重性区段进入了该页面，则列表将针对该严重性进行预先筛选。

选择任何警报实例将打开“警报详细信息”  页，让你可以查看有关该特定警报实例的更多详细信息。   

![“警报详细信息”页的屏幕截图](media/alerts-managing-alert-instances/alert-details.jpg)