---
title: 管理警报实例
description: 管理整个 Azure 中的警报实例
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: 29c2f58e4b4bea50d156192c818f8f91bbfeab4e
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2018
ms.locfileid: "53283683"
---
# <a name="manage-alert-instances"></a>管理警报实例
使用 Azure Monitor 中的[统一警报体验](https://aka.ms/azure-alerts-overview)，现在可以看到你在 Azure 中的所有不同类型的警报，这些警报来自多个订阅，显示在单个窗格中。 本文分步介绍了如何查看警报实例，以及如何深入门户来查找特定的警报实例以进行故障排除。

1. 有三种方式可登陆到“警报”页面

   + 在[门户](https://portal.azure.com/)中选择“监视器”，然后在“监视器”部分下选择“警报”。  
    ![监视](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
   + 可以从特定**资源**的上下文中导航到“警报”。 打开某个资源后，在其目录中导航到“监视”部分，选择“警报”，登陆页面将针对该特定资源的警报进行预先筛选。
   
    ![监视](media/alerts-managing-alert-instances/alert-resource.JPG)
    
   + 可以从特定**资源组**的上下文中导航到“警报”。 打开某个资源组后，在其目录中导航到“监视”部分，选择“警报”，登陆页面将针对该特定资源组的警报进行预先筛选。    
   
    ![监视](media/alerts-managing-alert-instances/alert-rg.JPG)

1.  你将登陆到“警报摘要”页面，该页面提供了你在整个 Azure 中的所有警报实例的概述。 可以通过选择**多个订阅**（最多 5 个）或通过在**资源组**、特定**资源**或**时间范围**中进行筛选来修改摘要视图。 单击“警报总数”或严重性区段可以转到警报的列表视图。     
    ![警报摘要](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
1.  你将登陆到“所有警报”页面，其中列出了整个 Azure 中的所有警报实例。如果是从警报通知进入到门户中，则可以使用可用的筛选器在该特定的警报实例上收缩范围。 （**注意**：如果是通过单击任何严重性区段进入的页面，则在你登陆时，列表将针对该严重性进行预先筛选）。 除了上一页面上可用的筛选器之外，你现在还可以基于监视服务（例如指标的平台）、监视条件（已触发或已解决）、严重性、警报状态（新的/已确认/已关闭）或智能组 ID 进行筛选。

    ![所有警报](media/alerts-managing-alert-instances/all-alerts.jpg)

    > [!NOTE]
    >  如果是通过单击任何严重性区段进入的页面，则在你登陆到此页面时，列表将针对该严重性进行预先筛选。
 
1.  单击任何警报实例都会打开“警报详细信息”页面，可以在其中深入了解该特定警报实例的信息。   
![警报详细信息](media/alerts-managing-alert-instances/alert-details.jpg)  
