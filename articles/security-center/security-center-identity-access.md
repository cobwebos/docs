---
title: 在 Azure 安全中心监视标识和访问 | Microsoft Docs
description: 了解如何使用 Azure 安全中心的标识和访问功能监视用户的访问活动和标识相关问题。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2019
ms.author: memildin
ms.openlocfilehash: 6b262baddd10c9d0dff4b196b733972b97d99872
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552978"
---
# <a name="monitor-identity-and-access-preview"></a>监视标识和访问（预览）
在安全中心识别出潜在的安全漏洞时，它会创建一些建议，指导完成配置所需控件以强化和保护资源的过程。

本文介绍 Azure 安全中心的 "资源安全性" 部分的 "**标识和访问**" 页。

有关此页上可能会看到的建议的完整列表，请参阅[身份验证和访问建议](recommendations-reference.md#recs-identity)。

> [!NOTE]
> 监视标识和访问的功能以预览版提供，仅适用于安全中心的标准层。 若要详细了解安全中心的定价层，请参阅[定价](security-center-pricing.md)。
>

标识应为企业的控制平面，保护标识应是最高优先级。 安全边界已从网络边界演进成标识边界。 安全性越来越少地与如何保护网络相关，而更多地与如何保护数据，以及如何管理应用和用户的安全性相关。 而现在，随着越来越多的数据和应用移到云中，标识成为新的外围。

监视标识活动即可在事件发生前主动采取措施，或事后采取针对性措施来阻止攻击尝试。 “标识和访问”仪表板提供如下建议：

- 为订阅中的特权帐户启用 MFA
- 从订阅中删除具有写入权限的外部帐户
- 从订阅中删除具有特权的外部帐户

> [!NOTE]
> 如果订阅包含 600 多个帐户，则安全中心无法针对订阅运行标识建议。 下面的 "不可用评估" 下列出了未运行的建议。
安全中心无法针对云解决方案提供商 (CSP) 合作伙伴的管理代理运行标识建议。
>

## <a name="monitor-identity-and-access"></a>监视标识和访问

通过从安全中心侧栏（在 "**资源**" 下）或 "概述" 页中选择 "**标识" & 访问**，打开已标识标识和访问问题的列表。 

“标识和访问”下面有两个选项卡：

- **概述**：安全中心识别的建议。
- **订阅**：订阅列表和每个订阅的当前安全状态。

[![标识 & 访问](./media/security-center-identity-access/identity-dashboard.png)](./media/security-center-identity-access/identity-dashboard.png#lightbox)

### <a name="overview-section"></a>“概述”部分
“概述”下面提供了建议列表。 第一列列出了建议。 第二列显示受该建议影响的订阅总数。 第三列显示问题的严重性。

1. 选择一条建议。 此时将打开并显示 "建议" 窗口：

   - 建议的说明
   - 不正常和正常的订阅列表
   - 由于评估失败，或者资源位于免费层上运行的订阅下且未评估，而未扫描的资源列表

    [![推荐窗口](./media/security-center-identity-access/select-subscription.png)](./media/security-center-identity-access/select-subscription.png#lightbox)

1. 在列表中选择一个订阅以查看更多详细信息。

### <a name="subscriptions-section"></a>“订阅”部分
“订阅”下面提供了订阅列表。 第一列列出订阅。 第二列显示每个订阅的建议总数。 第三列显示问题的严重性。

[![订阅 "选项卡](./media/security-center-identity-access/subscriptions.png)](./media/security-center-identity-access/subscriptions.png#lightbox)

1. 选择一个订阅。 此时会打开摘要视图，其中包含三个选项卡：

   - **建议**：基于安全中心执行的失败的评估。
   - **已通过的评估**：安全中心执行的、已通过的评估列表。
   - **不可用的评估**：由于出错或者订阅包含 600 个以上的帐户，而未能运行的评估列表。

   “建议”下面是选定订阅的建议列表，以及每条建议的严重性。

   [选择订阅的 ![建议](./media/security-center-identity-access/recommendations.png)](./media/security-center-identity-access/recommendations.png#lightbox)

1. 选择某个建议可以查看该建议的说明、不正常和正常订阅的列表，以及未扫描的资源列表。

   [建议 ![说明](./media/security-center-identity-access/designate.png)](./media/security-center-identity-access/designate.png#lightbox)

   “已通过的评估”下面是已通过的评估列表。  这些评估的严重性始终为绿色。

   [![通过的评估](./media/security-center-identity-access/passed-assessments.png)](./media/security-center-identity-access/passed-assessments.png#lightbox)

1. 在列表中选择一个已通过的评估可以查看该评估的说明，以及正常订阅的列表。 不正常的订阅也有一个选项卡，其中列出了所有失败的订阅。

   [![通过的评估](./media/security-center-identity-access/remove.png)](./media/security-center-identity-access/remove.png#lightbox)

> [!NOTE]
> 如果创建的条件性访问策略要求使用 MFA 但包含了排除集，则安全中心 MFA 建议评估会将策略视为不符合，因为这样可以使某些用户无需 MFA 即可登录到 Azure。

## <a name="next-steps"></a>后续步骤
若要详细了解适用于其他 Azure 资源类型的建议，请参阅以下文章：

- [在 Azure 安全中心保护计算机和应用程序](security-center-virtual-machine-protection.md)
- [保护 Azure 安全中心中的网络](security-center-network-recommendations.md)
- [在 Azure 安全中心保护 Azure SQL 服务和数据](security-center-sql-service-recommendations.md)