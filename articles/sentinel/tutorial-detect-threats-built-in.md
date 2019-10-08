---
title: 用 Azure Sentinel 调查警报 |Microsoft Docs
description: 使用本教程了解如何使用 Azure Sentinel 调查警报。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 6d61b94a50716966ac0b3c75551ddd9d8e291965
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72024462"
---
# <a name="tutorial-detect-threats-out-of-the-box"></a>教程：检测开箱即用的威胁


> [!IMPORTANT]
> 全新的威胁检测目前为公共预览版。
> 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

在将 [数据源连接](quickstart-onboard.md)@no__t 1To Azure Sentinel 后，需要在出现可疑情况时收到通知。 为了使你能够执行此操作，Azure Sentinel 为你提供现成的内置模板。 这些模板是由 Microsoft 的安全专家和分析师团队设计的，它们基于已知的威胁、常见攻击媒介和可疑活动升级链。 启用这些模板后，它们会自动搜索任何在你的环境中看起来可疑的活动。 可以根据需要自定义许多模板来搜索或筛选出活动。 这些模板生成的警报将创建可在环境中分配和调查的事件。

本教程可帮助你检测 Azure Sentinel 的威胁：

> [!div class="checklist"]
> * 使用现成的检测
> * 自动响应威胁

## <a name="about-out-of-the-box-detections"></a>关于开箱即用检测

若要查看所有现成的检测，请依次访问 "**分析**" 和 "**规则模板**"。 此选项卡包含所有的 Azure Sentinel 内置规则。

   ![使用 Azure Sentinel 通过内置检测来查找威胁](media/tutorial-detect-built-in/view-oob-detections.png)

以下模板类型可用：

- **Microsoft 安全**-microsoft 安全模板会自动从其他 Microsoft 安全解决方案中生成的警报创建 Azure Sentinel 事件。 您可以使用 Microsoft 安全规则作为模板来创建具有类似逻辑的新规则。 有关安全规则的详细信息，请参阅[从 Microsoft 安全警报自动创建事件](create-incidents-from-alerts.md)。
- 基于**合成技术**，Azure 中的高级多阶段攻击检测使用可缩放的机器学习算法，可将多个产品间的大量低保真警报和事件关联到高保真且可操作发生. 默认情况下启用合成。 由于逻辑处于隐藏状态，因此不能将其用作模板来创建多个规则。
- **机器学习行为分析**-这些模板基于专有的 Microsoft 机器学习算法，因此您无法看到它们的工作原理和运行时间的内部逻辑。 由于逻辑处于隐藏状态，因此不能将其用作模板来创建多个规则。
-   **计划**-计划的分析规则是由 Microsoft 安全专家编写的计划查询。 您可以查看查询逻辑并对其进行更改。 您可以使用计划的规则作为模板来创建具有类似逻辑的新规则。

## <a name="use-out-of-the-box-detections"></a>使用现成的检测

1. 若要使用内置模板，请单击 "**创建规则**" 以基于该模板创建新的活动规则。 每个条目都具有自动检查的所需数据源列表，这可能会导致禁用**创建规则**。
  
   ![使用 Azure Sentinel 通过内置检测来查找威胁](media/tutorial-detect-built-in/use-built-in-template.png)
 
1. 这会基于所选模板打开规则创建向导。 所有详细信息均为 autofilled，对于**计划规则**或**Microsoft 安全规则**，你可以自定义逻辑来更好地满足你的组织，或者根据内置模板创建其他规则。 执行规则创建向导中的步骤并根据模板创建规则后，新规则将出现在 "**活动规则**" 选项卡中。

有关向导中的字段的详细信息，请参阅 [Tutorial：创建自定义分析规则以检测可疑威胁 @ no__t。



## <a name="next-steps"></a>后续步骤
在本教程中，已学习如何使用 Azure Sentinel 来检测威胁。 

若要了解如何自动响应威胁，请[在 Azure Sentinel 中设置自动威胁响应](tutorial-respond-threats-playbook.md)。

