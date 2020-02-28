---
title: 管理 Azure Monitor 中的警报实例
description: 管理整个 Azure 中的警报实例
ms.subservice: alerts
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 1910b3b474012365e8117f584379b2b29f8ce3de
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77667612"
---
# <a name="manage-alert-instances-with-unified-alerts"></a>用统一警报管理警报实例

随着 Azure Monitor 的[统一警报体验](https://aka.ms/azure-alerts-overview)，你可以在 Azure 上查看所有不同类型的警报。 这跨多个订阅，在一个窗格中。 本文介绍如何查看警报实例，以及如何查找特定的警报实例进行故障排除。

> [!NOTE]
> 只能访问在过去30天内生成的警报。

## <a name="go-to-the-alerts-page"></a>请参阅 "警报" 页

可以通过以下任一方式来切换到 "警报" 页：

- 在[Azure 门户](https://portal.azure.com/)中，选择 "**监视** > **警报**"。  

     ![监视器警报的屏幕截图](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
- 使用特定资源的上下文。 打开资源，中转到 "**监视**" 部分，然后选择 "**警报**"。 预先筛选登陆页面，查找特定资源的警报。

     ![资源监视警报的屏幕截图](media/alerts-managing-alert-instances/alert-resource.JPG)

- 使用特定资源组的上下文。 打开资源组，中转到 "**监视**" 部分，然后选择 "**警报**"。 预先筛选登陆页面，查找特定资源组中的警报。    

     ![资源组监视警报的屏幕截图](media/alerts-managing-alert-instances/alert-rg.JPG)

## <a name="find-alert-instances"></a>查找警报实例

"**警报摘要**" 页面提供了跨 Azure 的所有警报实例的概述。 您可以通过选择**多个订阅**（最多为5个），或通过跨**资源组**、特定**资源**或**时间范围**进行筛选来修改 "摘要" 视图。 选择 "**警报总数**" 或任何 "严重性" 带区，以跳到警报的列表视图。     

!["警报摘要" 页的屏幕截图](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
在 "**所有警报**" 页上，将列出所有 Azure 上的警报实例。 如果是从警报通知进入到门户中，则可以使用可用的筛选器在该特定的警报实例上收缩范围。

> [!NOTE]
> 如果通过选择任何严重性区段进入了页面，则会为该严重性预筛选列表。

除了上一页提供的筛选器外，还可以根据监视服务的基础进行筛选（例如，用于指标的平台）、监视条件（已触发或已解决）、严重性、警报状态（新的/已确认/已关闭）或智能组 ID。

!["所有警报" 页的屏幕截图](media/alerts-managing-alert-instances/all-alerts.jpg)

> [!NOTE]
> 如果通过选择任何严重性区段进入了页面，则会为该严重性预筛选列表。

选择任何警报实例将打开 "**警报详细信息**" 页，使您可以查看有关该特定警报实例的更多详细信息。   

![警报详细信息页的屏幕截图](media/alerts-managing-alert-instances/alert-details.jpg)  

