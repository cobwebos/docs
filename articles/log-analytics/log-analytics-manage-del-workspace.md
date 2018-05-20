---
title: 删除 Azure Log Analytics 工作区 | Microsoft Docs
description: 了解在个人订阅中创建 Log Analytics 工作区后如何删除它，以及如何重构工作区模型。
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 084b8fa0addbe2ddeee699b58c9b0de1a6f27d99
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
---
# <a name="delete-an-azure-log-analytics-workspace-with-the-azure-portal"></a>使用 Azure 门户删除 Azure Log Analytics 工作区
本文介绍如何使用 Azure 门户删除可能不再需要的 Log Analytics 工作区。 

## <a name="to-delete-a-workspace"></a>删除工作区 
删除 Log Analytics 工作区时，会在 30 天内从服务中删除与工作区相关的所有数据。  删除工作区时需谨慎，因为其中可能含有会对服务操作产生不利影响的重要数据和配置。 考虑使用其他将数据存放在 Log Analytics 中的 Azure 服务和源，例如：

* Application Insights
* Azure 安全中心
* Azure 自动化
* 在 Windows 和 Linux 虚拟机上运行的代理
* 在环境中于 Windows 和 Linux 计算机上运行的代理
* System Center Operations Manager
* 管理解决方案 

被配置为向工作区报告的任何代理和 System Center Operations Manager 管理组继续处于孤立状态。  清点哪些代理、解决方案和其他 Azure 服务与工作区集成，然后再继续操作。   
 
如果是管理员并且存在多个关联到工作区的用户，则会断开这些用户和该工作区之间的关联。 如果这些用户与其他工作区关联，他们可以继续通过其他工作区使用 Log Analytics。 但是，如果他们未与其他工作区关联，则需要创建工作区才能使用 Log Analytics。 

1. 登录到 [Azure 门户](http://portal.azure.com)。 
2. 在 Azure 门户中，单击左下角的“更多服务”。 在资源列表中，键入“Log Analytics”。 开始键入时，会根据输入筛选该列表。 选择“Log Analytics”。
3. 在 Log Analytics 订阅窗格中，选择一个工作区，然后从中间窗格的顶端单击“删除”。<br><br> ![从工作区属性窗格中删除选项](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace.png)<br>  
4. 显示询问是否确实要删除工作区的确认消息窗口时，单击“是”。<br><br> ![确认删除工作区](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace-confirm.png)

