---
title: 管理智能组
description: 管理通过警报实例创建的智能组
ms.topic: conceptual
ms.subservice: alerts
ms.date: 09/24/2018
ms.openlocfilehash: 763bfefcf71b0be43722b99f31641015a5991607
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92105832"
---
# <a name="manage-smart-groups"></a>管理智能组

[智能组](./alerts-smartgroups-overview.md?toc=%252fazure%252fazure-monitor%252ftoc.json)使用机器学习算法根据共现或相似性将警报分组在一起，以便用户现在可以管理智能组，而不必单独管理每个警报。 本文将介绍如何在 Azure Monitor 中访问和使用智能组。

1. 若要查看为警报实例创建的智能组，可以执行以下操作之一：

     1. 单击 "**警报摘要**" 页中的 "**智能组**"。    
    ![屏幕截图显示突出显示了智能组的 "警报摘要" 页面。](./media/alerts-managing-smart-groups/sg-alerts-summary.jpg)
    
     1. 单击 "所有警报" 页上的 "智能组警报"。   
     ![屏幕截图显示 "所有警报" 页，其中突出显示了智能组 "警报"。](./media/alerts-managing-smart-groups/sg-all-alerts.jpg)

2. 这会转到通过警报实例创建的所有智能组的列表视图。 你现在可以处理智能组，而不是筛选多个警报。   
![屏幕截图显示 "所有警报" 页。](./media/alerts-managing-smart-groups/sg-list.jpg)

3. 单击任何智能组将打开详细信息页，你可以在其中查看分组原因以及成员警报。 此聚合允许你处理单个智能组，而不是筛选多个警报。   
![屏幕截图显示详细信息页。](./media/alerts-managing-smart-groups/sg-details.jpg)