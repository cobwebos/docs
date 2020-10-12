---
title: 用 Azure Sentinel 调查警报 |Microsoft Docs
description: 了解如何使用现成的内置 Azure 威胁检测模板，这些模板可在发生可疑问题时通知你。
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
ms.date: 07/06/2020
ms.author: yelevin
ms.openlocfilehash: 5d73337c25c812363b7a542bf42372ca3baa10e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88605429"
---
# <a name="tutorial-detect-threats-out-of-the-box"></a>教程：检测最新威胁


> [!IMPORTANT]
> 全新的威胁检测目前为公共预览版。
> 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

将[数据源连接](quickstart-onboard.md)   到 Azure Sentinel 后，需要在出现可疑情况时收到通知。 这就是 Azure Sentinel 提供现成的内置模板来帮助您创建威胁检测规则的原因。 这些模板是由 Microsoft 的安全专家和分析师团队设计的，它们基于已知的威胁、常见攻击媒介和可疑活动升级链。 从这些模板创建的规则将在环境中自动搜索看起来可疑的任何活动。 可以自定义许多模板来搜索活动，或根据需要进行筛选。 这些规则生成的警报将创建可在环境中分配和调查的事件。

本教程可帮助你检测 Azure Sentinel 的威胁：

> [!div class="checklist"]
> * 使用全新威胁检测
> * 自动响应威胁

## <a name="about-out-of-the-box-detections"></a>关于开箱即用检测

若要查看所有现成的检测，请转到 **Analytics**，然后转到“规则模板”。 此选项卡包含所有的 Azure Sentinel 内置规则。

   :::image type="content" source="media/tutorial-detect-built-in/view-oob-detections.png" alt-text="使用 Azure Sentinel 通过内置检测来查找威胁&quot;:::

以下模板类型可用：

- **Microsoft 安全性**
   
   Microsoft 安全模板会自动创建其他 Microsoft 安全解决方案中生成的警报的 Azure Sentinel 事件。 您可以使用 Microsoft 安全规则作为模板来创建具有类似逻辑的新规则。 有关安全规则的详细信息，请参阅 [从 Microsoft 安全警报自动创建事件](create-incidents-from-alerts.md)。

- **合成** 

    基于合成技术，Azure 中的高级多阶段攻击检测使用可缩放的机器学习算法，可将多个产品之间的许多低保真警报和事件关联到高保真且可操作的事件中。 默认情况下启用合成。 由于逻辑是隐藏的，因此不可自定义，因此只能用此模板创建一个规则。

- **机器学习行为分析**

    这些模板基于专有的 Microsoft 机器学习算法，因此无法看到它们的工作方式和运行时间的内部逻辑。 由于逻辑是隐藏的，因此不可自定义，因此只能为此类型的每个模板创建一个规则。

- **计划**

    计划分析规则基于 Microsoft 安全专家编写的内置查询。 您可以查看查询逻辑并对其进行更改。 您可以使用 &quot;计划的规则" 模板并自定义查询逻辑和计划设置以创建新规则。

## <a name="use-out-of-the-box-detections"></a>使用现成的检测

1. 若要使用内置模板，请单击模板名称，然后单击详细信息窗格中的 " **创建规则** " 按钮，基于该模板创建新的活动规则。 每个模板都具有所需数据源的列表。 打开该模板时，会自动检查数据源的可用性。 如果存在可用性问题，则可能已禁用 " **创建规则** " 按钮，或者你可能会看到该效果的警告。
  
    :::image type="content" source="media/tutorial-detect-built-in/use-built-in-template.png" alt-text="使用 Azure Sentinel 通过内置检测来查找威胁&quot;:::

以下模板类型可用：

- **Microsoft 安全性**
   
   Microsoft 安全模板会自动创建其他 Microsoft 安全解决方案中生成的警报的 Azure Sentinel 事件。 您可以使用 Microsoft 安全规则作为模板来创建具有类似逻辑的新规则。 有关安全规则的详细信息，请参阅 [从 Microsoft 安全警报自动创建事件](create-incidents-from-alerts.md)。

- **合成** 

    基于合成技术，Azure 中的高级多阶段攻击检测使用可缩放的机器学习算法，可将多个产品之间的许多低保真警报和事件关联到高保真且可操作的事件中。 默认情况下启用合成。 由于逻辑是隐藏的，因此不可自定义，因此只能用此模板创建一个规则。

- **机器学习行为分析**

    这些模板基于专有的 Microsoft 机器学习算法，因此无法看到它们的工作方式和运行时间的内部逻辑。 由于逻辑是隐藏的，因此不可自定义，因此只能为此类型的每个模板创建一个规则。

- **计划**

    计划分析规则基于 Microsoft 安全专家编写的内置查询。 您可以查看查询逻辑并对其进行更改。 您可以使用 &quot;计划的规则" 选项卡中。

    有关如何在规则创建向导中自定义规则的详细信息，请参阅 [教程：创建自定义分析规则以检测威胁](tutorial-detect-threats-custom.md)。

## <a name="next-steps"></a>后续步骤

在本教程中，已学习如何使用 Azure Sentinel 来检测威胁。 

若要了解如何自动响应威胁，请 [在 Azure Sentinel 中设置自动威胁响应](tutorial-respond-threats-playbook.md)。

