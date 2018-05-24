---
title: 在 Azure 安全中心管理安全警报 | Microsoft 文档
description: 本文档旨在帮助使用 Azure 安全中心功能来管理和响应安全警报。
services: security-center
documentationcenter: na
author: terrylan
manager: mbaldwin
editor: ''
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/30/2017
ms.author: yurid
ms.openlocfilehash: d088223aa2ea40d5bb9cf0e492e87ef054d86348
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365350"
---
# <a name="managing-and-responding-to-security-alerts-in-azure-security-center"></a>管理和响应 Azure 安全中心的安全警报
本文档旨在帮助使用 Azure 安全中心来管理和响应安全警报。

> [!NOTE]
> 若要启用高级检测，请升级到 Azure 安全中心标准版。 可免费试用 60 天。 要升级，请选择 [安全策略](security-center-policies.md)中的“定价层”。 请参阅 [Azure 安全中心定价](security-center-pricing.md)，了解详细信息。
>
>

## <a name="what-are-security-alerts"></a>什么是安全警报？
安全中心会自动收集、分析以及整合 Azure 资源、网络和所连合作伙伴解决方案（如，防火墙和终结点保护解决方案）的日志数据，检测真正的威胁并减少误报。 安全中心显示了一系列安全警报（按严重程度排序），并显示了快速调查问题所需的信息以及修复攻击的建议。


> [!NOTE]
> 有关安全中心检测功能工作原理的详细信息，请参阅 [Azure 安全中心检测功能](security-center-detection-capabilities.md)。
>
>

## <a name="managing-security-alerts"></a>管理安全警报
可以通过查看“安全警报”  磁贴查看当前警报。 按照以下步骤查看每个警报的更多详细信息：

1. 在安全中心仪表板中，可以看到“安全警报”磁贴。

    ![安全中心的“安全警报”磁贴](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1-ga.png)

2. 单击该磁贴打开“安全警报”，查看有关警报的更多详细信息。

   ![安全中心的“安全警报”](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2-ga.png)

此页底部是每个警报的详细信息。 要进行排序，请单击按其排序的列。 下面给出了每个列的定义：

* **说明**：警报的简要说明。
* **计数**：在特定日期检测到的此特定类型的所有警报的列表。
* **检测程序**：负责触发警报的服务。
* **日期**：事件发生的日期。
* **状态**：该警报的当前状态。 状态有两种：
  * **活动**：已检测到安全警报。
* **严重性**：严重性级别，分为高、中和低。

> [!NOTE]
> 安全中心生成的安全警报也会显示在“Azure 活动日志”下。 若要深入了解如何访问 Azure 活动日志，请阅读[查看活动日志以审核对资源的操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)。
>

### <a name="filtering-alerts"></a>筛选警报
可以根据日期、状态和严重性筛选警报。 对于需要缩小安全警报显示范围的情况，筛选警报非常有用。 例如，假设正在调查系统中的潜在危害，需要处理过去 24 小时内发生的安全警报。

1. 单击“安全警报”上的“筛选器”。 此时会打开“筛选器”，请选择要查看的日期、状态和严重性值。

    ![筛选安全中心的警报](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3-2017.png)

### <a name="respond-to-security-alerts"></a>响应安全警报
选择某个安全警报，了解触发该警报的事件详细信息以及修复攻击所需的步骤（如有）。 安全警报按类型和日期进行分组。 单击某个安全警报会打开一个页面，其中包含一个分组警报列表。

![响应 Azure 安全中心的安全警报](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5-ga.png)

在这种情况下，触发的警报指的是可疑的远程桌面协议 (RDP) 活动。 第一列显示受到攻击的资源；第二列显示该资源受到攻击的次数；第三列显示攻击的时间；第四列显示警报的状态；第五列显示攻击的严重性。 查看此信息后，请单击受攻击的资源。

![关于如何处理 Azure 安全中心安全警报的建议](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6-ga.png)

在“说明”字段可以找到此事件的更多详细信息。 通过这些额外的详细信息，可深入了解是什么触发了该安全警报、其目标资源、源 IP 地址（如果适用）以及修复建议。  在某些情况下，源 IP 地址为空（不可用），因为并不是所有 Windows 安全事件日志都包含 IP 地址。

安全中心提供的修正建议因安全警报而异。 在某些情况下，可能需要使用其他 Azure 功能来实现建议的修复操作。 例如，此攻击的修复方法是，使用[网络 ACL](../virtual-network/virtual-networks-acl.md) 或[网络安全组](../virtual-network/security-overview.md#security-rules)规则将生成此攻击的 IP 地址加入阻止名单。 有关不同类型的警报的详细信息，请参阅 [Azure 安全中心的安全警报（按类型）](security-center-alerts-type.md)。

> [!NOTE]
> 安全中心在功能有限的预览版中发布了新的一组检测。这些检测利用审核记录这种常用审核框架，检测 Linux 计算机上的恶意行为。 若要体验预览版，请向[我们](mailto:ASC_linuxdetections@microsoft.com)发送包含订阅 ID 的电子邮件。


## <a name="see-also"></a>另请参阅
本文档中已经介绍了如何在安全中心配置安全策略。 要了解有关安全中心的详细信息，请参阅以下内容：

* [处理 Azure 安全中心的安全事件](security-center-incident.md)
* [Azure 安全中心检测功能](security-center-detection-capabilities.md)
* [Azure 安全中心规划和操作指南](security-center-planning-and-operations-guide.md)
* [Azure 安全中心常见问题解答](security-center-faq.md) — 查找有关使用该服务的常见问题解答。
* [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/) - 查找关于 Azure 安全性及合规性的博客文章。
