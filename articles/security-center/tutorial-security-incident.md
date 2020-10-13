---
title: 警报响应教程 - Azure 安全中心
description: 在本教程中，你将了解如何会审安全警报，并确定警报的根本原因和范围。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 181e3695-cbb8-4b4e-96e9-c4396754862f
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2020
ms.author: memildin
ms.openlocfilehash: a04f94f5ebc7c1fdaf7b95e71dc8549e19863b39
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91614120"
---
# <a name="tutorial-triage-investigate-and-respond-to-security-alerts"></a>教程：会审、调查和响应安全警报
安全中心使用高级分析和威胁智能来持续分析混合云工作负荷，在存在恶意活动时发出警报。 你也可以将其他安全产品和服务中的警报集成到安全中心，并根据自己的指示器或智能源创建自定义警报。 生成警报后，需采取快速行动进行调查和修正。 

在本教程中，您将学习如何执行以下操作：

> [!div class="checklist"]
> * 会审安全警报
> * 调查安全警报以确定根本原因
> * 响应安全警报并缓解该根本原因

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件
若要逐步执行本教程中介绍的功能，需要启用 Azure Defender。 可以免费试用 Azure Defender。 若要了解详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/security-center/)。 可通过快速入门内容“[安全中心入门](security-center-get-started.md)”了解如何升级。


## <a name="triage-security-alerts"></a>会审安全警报
安全中心提供所有安全警报的统一视图。 安全警报根据检测到的活动的严重性进行排序。 

从“安全警报”页中会审警报：

:::image type="content" source="./media/tutorial-security-incident/alerts-list.png" alt-text="安全警报列表页面" lightbox="./media/tutorial-security-incident/alerts-list.png":::

使用此页面查看环境中活动的安全警报，以确定要首先调查哪个警报。

会审安全警报时，根据警报严重性确定警报优先级，方法是先对优先级较高的警报进行寻址。 有关警报严重性的详细信息，请参阅[如何对警报进行分类](security-center-alerts-overview.md#how-are-alerts-classified)？

> [!TIP]
> 可以将 Azure 安全中心连接到包括 Azure Sentinel 在内的大多数流行的 SIEM 解决方案，并从你选择的工具中使用警报。 有关详细信息，请参阅[将警报导出到 SIEM](continuous-export.md)。


## <a name="investigate-a-security-alert"></a>调查安全警报

确定要首先调查的警报后：

1. 选择所需的警报。
1. 从“警报概述”页中，选择要首先调查的资源。
1. 从左窗格开始调查，该窗格显示有关安全警报的基本信息。

    :::image type="content" source="./media/tutorial-security-incident/alert-details-left-pane.png" alt-text="安全警报列表页面":::

    此窗格显示：
    - 警报严重性、状态和活动时间
    - 解释检测到的精确活动的说明
    - 受影响的资源
    - 基于 MITRE ATT&CK 矩阵的活动的终止链意向

1. 有关有助于调查可疑活动的更多详细信息，请查看“警报详细信息”选项卡。

1. 查看完此页上的信息后，你可能有足够的时间来继续响应。 如果需要更多详细信息，请执行以下操作：

    - 联系资源所有者，以验证检测到的活动是否为误报。
    - 调查受攻击资源生成的原始日志

## <a name="respond-to-a-security-alert"></a>响应安全警报
调查警报并了解其范围后，可以从 Azure 安全中心内响应安全警报：

1.  打开“执行操作”选项卡以查看建议的响应。

    :::image type="content" source="./media/tutorial-security-incident/alert-details-take-action.png" alt-text="安全警报列表页面" lightbox="./media/tutorial-security-incident/alert-details-take-action.png":::

1.  查看“缓解威胁”部分，了解缓解该问题所需的手动调查步骤。
1.  若要强化资源并防止将来出现此类攻击，请在“防止将来的攻击”部分中修正安全建议。
1.  若要使用自动响应步骤触发逻辑应用，请使用“触发自动响应”部分。
1.  如果检测到的活动不是恶意行为，则可以使用“禁止类似警报”部分来禁止以后显示此类警报。

1.  如果已完成对警报的调查，并以适当的方式做出了响应，请将状态更改为“已解除”。

    :::image type="content" source="./media/tutorial-security-incident/set-status-dismissed.png" alt-text="安全警报列表页面":::

    这会从主警报列表中删除该警报。 可以使用“警报列表”页中的筛选器查看所有处于“已解除”状态的警报。

1.  还可以根据需要向 Microsoft 提供有关警报的反馈：
    1. 将警报标记为“有用”或“无用”并提供反馈 
    1. 选择原因并添加注释。

        :::image type="content" source="./media/tutorial-security-incident/alert-feedback.png" alt-text="安全警报列表页面":::

    > [!TIP]
    > 我们会查看你的反馈，以改进算法并提供更好的安全警报。

## <a name="end-the-tutorial"></a>结束教程

本系列中的其他快速入门和教程是在本快速入门的基础上制作的。 如果打算继续学习后续的快速入门和教程，请让自动预配和 Azure Defender 保持启用状态。 

如果不打算继续，或想要禁用以下功能之一，请执行以下操作：

1. 返回到“安全中心”主菜单，选择“定价和设置”。
1. 选择相关订阅。
1. 若要降级，请选择“Azure Defender”。
1. 若要禁用自动预配功能，请打开“数据收集”页，并将“自动预配”设置为“关”  。
4. 选择“保存”。

>[!NOTE]
> 禁用自动预配不会从已有 Log Analytics 代理的 Azure VM 中删除该代理。 禁用自动设置会限制对资源的安全监视。
>

## <a name="next-steps"></a>后续步骤
本教程介绍了响应安全警报时需使用的安全中心功能。 如需查看相关材料，请参阅：

- [响应适用于 Key Vault 的 Azure Defender 警报](defender-for-key-vault-usage.md)
- [安全警报 - 参考指南](alerts-reference.md)
- [Azure Defender 简介](azure-defender.md)