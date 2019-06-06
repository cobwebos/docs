---
title: 在 Azure 安全中心监视标识和访问 | Microsoft Docs
description: 了解如何使用 Azure 安全中心的标识和访问功能监视用户的访问活动和标识相关问题。
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2018
ms.author: monhaber
ms.openlocfilehash: 16548ae75567fa3ba6f8c9135d61945bd28d2db8
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428416"
---
# <a name="monitor-identity-and-access-in-azure-security-center-preview"></a>在 Azure 安全中心监视标识和访问（预览版）
本文介绍如何使用 Azure 安全中心监视用户的标识和访问活动。

> [!NOTE]
> "视图*经典*标识和访问"链接将于 2019 年 7 月 31 日停用。 单击[此处](security-center-features-retirement-july2019.md#menu_classicidentity)若要了解其他服务上。

> [!NOTE]
> 监视标识和访问的功能以预览版提供，仅适用于安全中心的标准层。 若要详细了解安全中心的定价层，请参阅[定价](security-center-pricing.md)。
>

标识应为企业的控制平面，保护标识应为头等大事。 安全边界已从网络边界演进成标识边界。 安全性越来越少地与如何保护网络相关，而更多地与如何保护数据，以及如何管理应用和用户的安全性相关。 而现在，随着越来越多的数据和应用移到云中，标识成为新的外围。

监视标识活动即可在事件发生前主动采取措施，或事后采取针对性措施来阻止攻击尝试。 “标识和访问”仪表板提供如下建议：

- 为订阅中的特权帐户启用 MFA
- 从订阅中删除具有写入权限的外部帐户
- 从订阅中删除具有特权的外部帐户

> [!NOTE]
> 如果订阅包含 600 多个帐户，则安全中心无法针对订阅运行标识建议。 未运行的建议列在“不可用的评估”下面，下文将介绍此类建议。
安全中心无法针对云解决方案提供商 (CSP) 合作伙伴的管理代理运行标识建议。
>

有关安全中心提供的标识和访问建议的列表，请参阅[建议](security-center-identity-access.md#recommendations)。

## <a name="monitoring-security-health"></a>监视安全运行状况
可以在“安全中心 - 概述”仪表板上监视资源的安全状态。  “资源”部分指示运行状况，其中显示了每种资源类型的严重性。 

可以选择“建议”来查看所有问题的列表。  在“资源”下，可以查看特定于计算和应用、数据安全性、网络或标识和访问的问题列表。  有关如何应用建议的详细信息，请参阅[在 Azure 安全中心实施安全建议](security-center-recommendations.md)。

有关标识和访问建议的完整列表，请参阅[建议](security-center-identity-access.md#recommendations)。

若要继续，请选择“资源”或安全中心主菜单中的“标识和访问”。  

![安全中心仪表板][1]

## <a name="monitor-identity-and-access"></a>监视标识和访问
“标识和访问”下面有两个选项卡： 

- **概述**：安全中心识别的建议。
- **订阅**：订阅列表和每个订阅的当前安全状态。

![标识和访问][2]

### <a name="overview-section"></a>“概述”部分
“概述”下面提供了建议列表。  第一列列出了建议。 第二列显示受该建议影响的订阅总数。 第三列显示问题的严重性。

1. 选择一条建议。 此时会打开该建议的窗口，其中显示：

   - 建议的说明
   - 不正常和正常的订阅列表
   - 由于评估失败，或者资源位于免费层上运行的订阅下且未评估，而未扫描的资源列表

   ![建议的窗口][3]

1. 在列表中选择一个订阅以查看更多详细信息。

### <a name="subscriptions-section"></a>“订阅”部分
“订阅”下面提供了订阅列表。  第一列列出订阅。 第二列显示每个订阅的建议总数。 第三列显示问题的严重性。

![订阅的选项卡][4]

1. 选择一个订阅。 此时会打开摘要视图，其中包含三个选项卡：

   - **建议**：基于安全中心执行的失败的评估。
   - **已通过的评估**：安全中心执行的、已通过的评估列表。
   - **不可用的评估**：由于出错或者订阅包含 600 个以上的帐户，而未能运行的评估列表。

   “建议”下面是选定订阅的建议列表，以及每条建议的严重性。 

   ![选定订阅的建议][5]

1. 选择某个建议可以查看该建议的说明、不正常和正常订阅的列表，以及未扫描的资源列表。

   ![建议的说明][6]

   “已通过的评估”下面是已通过的评估列表。   这些评估的严重性始终为绿色。

   ![已通过的评估][7]

1. 在列表中选择一个已通过的评估可以查看该评估的说明，以及正常订阅的列表。 不正常的订阅也有一个选项卡，其中列出了所有失败的订阅。

   ![已通过的评估][8]

## <a name="recommendations"></a>建议
参考下表来帮助了解可用的标识和访问建议，以及应用每条建议时的效果。

|资源类型|安全功能分数|建议|描述|
|----|----|----|----|
|订阅|50|应与你的订阅的所有者权限在帐户上启用 MFA|为具有管理员权限的所有订阅帐户启用多重身份验证 (MFA)，以防止破坏帐户或资源。|
|订阅|40|应在你具有写入权限的订阅帐户启用 MFA|为具有写入特权的所有订阅帐户启用多重身份验证 (MFA)，以防止破坏帐户或资源。|
|订阅|30|应从订阅中删除拥有所有者权限的外部帐户|从订阅中删除具有所有者权限的外部帐户，以防止发生未受监视的访问。|
|订阅|30|在你订阅的帐户具有读取权限，应启用 MFA|为具有读取特权的所有订阅帐户启用多重身份验证 (MFA)，以防止破坏帐户或资源。|
|订阅|25|外部帐户具有写入权限应从订阅中删除|从订阅中删除具有写入权限的外部帐户，以防止发生未受监视的访问。 |
|订阅|20|应从订阅中删除弃用的帐户拥有所有者权限|从订阅中删除拥有所有者权限的已弃用帐户。|
|订阅|5|应从订阅中删除弃用的帐户|从订阅中删除已弃用的帐户，以便只允许当前用户访问。 |
|订阅|5|应该有多个所有者分配给你的订阅|指定多个订阅所有者，以实现管理员访问权限冗余。|
|订阅|5|最多 3 个所有者应被指定为你的订阅|指定少于 3 个订阅所有者，以减少已遭入侵的所有者做出违规行为的可能性。|
|密钥保管库|5|应启用了密钥保管库中的诊断日志|启用日志并将其保留长达一年。 这样便可以在发生安全事件或网络遭泄露时，重新创建活动线索用于调查目的。 |
|订阅|15|应从订阅中删除拥有读取权限的外部帐户|从订阅中删除具有读取特权的外部帐户，以防止发生未受监视的访问。| 

> [!NOTE]
> 如果创建要求 MFA，但具有排除项设置的条件性访问策略，安全中心 MFA 建议评估会考虑该策略不符合，因为它使某些用户能够登录到 Azure 而无需 MFA。

## <a name="next-steps"></a>后续步骤
若要了解有关适用于其他 Azure 资源类型的建议的详细信息，请参阅以下内容：

- [在 Azure 安全中心保护计算机和应用程序](security-center-virtual-machine-recommendations.md)
- [保护 Azure 安全中心中的网络](security-center-network-recommendations.md)
- [在 Azure 安全中心保护 Azure SQL 服务和数据](security-center-sql-service-recommendations.md)

若要了解有关安全中心的详细信息，请参阅以下文章：
* [管理和响应 Azure 安全中心的安全警报](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)。 了解如何管理警报并响应安全中心的安全事件。
* [了解 Azure 安全中心的安全警报](https://docs.microsoft.com/azure/security-center/security-center-alerts-type)。 了解不同类型的安全警报。
* [Azure Security Center FAQ](security-center-faq.md)（Azure 安全中心常见问题）。 查找安全中心使用方面的常见问题解答。


<!--Image references-->
[1]: ./media/security-center-identity-access/overview.png
[2]: ./media/security-center-identity-access/identity-dashboard.png
[3]: ./media/security-center-identity-access/select-subscription.png
[4]: ./media/security-center-identity-access/subscriptions.png
[5]: ./media/security-center-identity-access/recommendations.png
[6]: ./media/security-center-identity-access/designate.png
[7]: ./media/security-center-identity-access/passed-assessments.png
[8]: ./media/security-center-identity-access/remove.png
