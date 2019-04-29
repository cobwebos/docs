---
title: 管理智能组
description: 管理通过警报实例创建的智能组
author: lingliw
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/21/19
ms.author: v-lingwu
ms.subservice: alerts
ms.openlocfilehash: a9434bebe82687b14f45b3c375572717d71d0e55
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60713306"
---
# <a name="manage-smart-groups"></a>管理智能组
[智能组](https://aka.ms/smart-groups)使用机器学习算法根据共现或相似性将警报分组在一起，以便用户现在可以管理智能组，而不必单独管理每个警报。 本文将介绍如何在 Azure Monitor 中访问和使用智能组。
1.  若要查看为警报实例创建的智能组，可以执行以下任一操作
     1. 从“警报摘要”页单击“智能组”    
    ![监视](./media/alerts-managing-smart-groups/sg-alerts-summary.jpg)
     2. 从“所有警报”页单击“警报(按智能组)”   
     ![监视](./media/alerts-managing-smart-groups/sg-all-alerts.jpg)
2.  这会转到通过警报实例创建的所有智能组的列表视图。 你现在可以处理智能组，而不是筛选多个警报。   
![监视](./media/alerts-managing-smart-groups/sg-list.jpg)
3.  单击任何智能组将打开详细信息页，你可以在其中查看分组原因以及成员警报。 此聚合允许你处理单个智能组，而不是筛选多个警报。   
![监视](./media/alerts-managing-smart-groups/sg-details.jpg)

