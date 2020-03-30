---
title: 使用 Azure 哨兵* 调查警报*微软文档
description: 使用本教程了解如何使用 Azure Sentinel 调查警报。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: df855aa768fdeb279482b8407259be1a644322d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585197"
---
# <a name="tutorial-detect-threats-out-of-the-box"></a>教程：检测开箱即用的威胁


> [!IMPORTANT]
> 开箱即用的威胁检测当前处于公共预览版中。
> 此功能在没有服务级别协议的情况下提供，不建议用于生产工作负载。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

 [将数据源](quickstart-onboard.md) 连接到 Azure Sentinel 后，希望在发生可疑情况时收到通知。 为了使您能够执行此操作，Azure Sentinel 为您提供了现成的内置模板。 这些模板由 Microsoft 的安全专家和分析人员团队根据已知威胁、常见攻击媒介和可疑活动升级链设计。 启用这些模板后，它们将自动搜索整个环境中任何看起来可疑的活动。 许多模板可以根据您的需求进行自定义，以搜索或筛选出活动。 这些模板生成的警报将创建可在环境中分配和调查的事件。

本教程可帮助您使用 Azure Sentinel 检测威胁：

> [!div class="checklist"]
> * 使用开箱即用的检测
> * 自动响应威胁

## <a name="about-out-of-the-box-detections"></a>关于开箱即用检测

若要查看所有现成的检测，请转到 **Analytics**，然后转到“规则模板”****。 此选项卡包含所有的 Azure Sentinel 内置规则。

   ![使用 Azure Sentinel 通过内置检测来查找威胁](media/tutorial-detect-built-in/view-oob-detections.png)

以下模板类型可用：

- **Microsoft 安全**- Microsoft 安全模板自动从其他 Microsoft 安全解决方案中生成的警报实时创建 Azure Sentinel 事件。 您可以使用 Microsoft 安全规则作为模板来创建具有类似逻辑的新规则。 有关安全规则的详细信息，请参阅从[Microsoft 安全警报自动创建事件](create-incidents-from-alerts.md)。
- **Fusion** - 基于 Fusion 技术，Azure Sentinel 中的高级多阶段攻击检测使用可扩展的机器学习算法，可以将多个产品中的许多低保真警报和事件关联到高保真和可操作的事件中。 默认情况下启用融合。 由于逻辑是隐藏的，因此不能将它用作模板来创建多个规则。
- **机器学习行为分析**- 这些模板基于专有的 Microsoft 机器学习算法，因此您无法查看其工作方式和运行时间的内部逻辑。 由于逻辑是隐藏的，因此不能将它用作模板来创建多个规则。
-   **计划**— 计划分析规则是由 Microsoft 安全专家编写的计划查询。 您可以看到查询逻辑并对其进行更改。 可以使用计划规则作为模板创建具有类似逻辑的新规则。

## <a name="use-out-of-the-box-detections"></a>使用开箱即用的检测

1. 为了使用内置模板，请单击 **"创建规则"** 以基于该模板创建新的活动规则。 每个条目都有一个自动检查所需的数据源的列表，这可能导致**禁用 Create 规则**。
  
   ![使用 Azure Sentinel 通过内置检测来查找威胁](media/tutorial-detect-built-in/use-built-in-template.png)
 
1. 这将基于所选模板打开规则创建向导。 所有详细信息都是自动填充的，对于**计划规则**或**Microsoft 安全规则**，您可以自定义逻辑以更好地适合您的组织，或者基于内置模板创建其他规则。 按照规则创建向导中的步骤并完成基于模板创建规则的步骤后，新规则将显示在 **"活动规则"** 选项卡中。

有关向导中字段的详细信息，请参阅[教程：创建自定义分析规则以检测可疑威胁](tutorial-detect-threats-custom.md)。



## <a name="next-steps"></a>后续步骤
在本教程中，您学习了如何使用 Azure Sentinel 开始检测威胁。 

要了解如何自动响应威胁，请[在 Azure Sentinel 中设置自动威胁响应](tutorial-respond-threats-playbook.md)。

