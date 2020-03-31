---
title: 如何在 Azure 安全中心法规合规性仪表板中更新到动态法规遵从性监视 |微软文档
description: 更新法规遵从性包（预览版）
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 4080825bbb1f6c274f5b5aafd28e8c672148b98f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159279"
---
# <a name="update-to-dynamic-compliance-packages-in-your-regulatory-compliance-dashboard-preview"></a>更新到法规遵从性仪表板中的动态合规性包（预览版）

Azure 安全中心不断将资源的配置与行业标准、法规和基准方面的要求进行比较。 **法规遵从性仪表板**基于您满足特定合规性控制和要求的实现方式，提供对合规性状况的见解。

可以跟踪合规性状态的一个标准是[Azure CIS 1.1.0（](https://www.cisecurity.org/benchmark/azure/)更正式地说，"CIS 微软 Azure 基础基准版本 1.1.0"）。 

最初出现在合规性仪表板中的 Azure CIS 表示依赖于安全中心随附的静态规则集。

借助**动态合规性包（预览）** 功能，Security Center 会随着时间的推移自动提高行业标准的覆盖范围。 合规性包本质上是 Azure 策略中定义的计划。 它们可以分配给您选择的作用域（订阅、管理组等）。 要查看仪表板中映射为评估的合规性数据，请从安全策略中向管理组或订阅添加合规性包。 添加合规性包可有效地将法规遵从性计划分配给您选择的范围。 通过这种方式，您可以在仪表板中跟踪新发布的法规计划作为合规性标准。 当 Microsoft 发布计划的新内容（映射到标准中更多控件的新策略）时，其他内容会自动显示在仪表板中。

Azure CIS 基准的动态合规性包**Azure CIS 1.1.0（新）** 在原始*静态*版本上改进：：

* 包括更多政策
* 添加新覆盖范围时自动更新 

更新到新的动态包，如下所述。

## <a name="adding-a-dynamic-compliance-package"></a>添加动态合规性包

以下步骤说明如何添加动态包，以监视是否符合 Azure CIS 基准 v1.1.0。   

### <a name="update-to-the-azure-cis-110-new-dynamic-compliance-package"></a>更新到 Azure CIS 1.1.0（新）动态合规性包 

1. 打开**安全策略**页面。 此页显示管理组、订阅、工作区和管理组结构的数量。

1. 选择要为其管理法规遵从性状态的订阅或管理组。 我们建议选择标准适用的最高范围，以便聚合和跟踪所有嵌套资源的合规性数据。 

1. 在行业&法规标准（预览）部分中，您将看到 Azure CIS 1.1.0 可以针对新内容进行更新。 单击 **"立即更新**"。 

1. 可选，单击"**添加更多标准**"以打开"**添加法规遵从性标准"** 页。 在那里，您可以手动搜索 Azure **CIS 1.1.0 （新）** 和动态包，查找其他合规性标准，如**NIST SP 800-53 R4、SWIFT** **CSP CSCF-v2020、UKO****和英国 NHS**以及**加拿大 PBMM**。
    
    > [!TIP]
    > 只有所有者或策略参与者的用户才具有添加合规性标准的必要权限。 

    ![将法规包添加到 Azure 安全中心的法规遵从性仪表板](./media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-additional-standards.png)


1. 从安全中心的侧边栏中，选择**法规遵从性**以打开法规遵从性仪表板。 
    * Azure CIS 1.1.0 （New） 现在显示在行业&监管标准列表中。 
    * Azure CIS 1.1.0 合规性的原始*静态*视图也将保留在它旁边。 将来可能会自动删除它。

    > [!NOTE]
    > 新添加的标准可能需要几个小时才能显示在合规性仪表板中。


    [![显示新旧 Azure CIS 的法规遵从性仪表板](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png)](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png#lightbox)


## <a name="next-steps"></a>后续步骤

在本文中，我们已了解到：

* 如何将法规遵从仪表板中显示的**标准升级到**新的*动态*包
* 如何**添加合规性包**来监视您对其他标准的遵守情况。 

有关其他相关材料，请参阅以下文章： 

- [安全中心法规合规性仪表板](security-center-compliance-dashboard.md)
- [使用安全策略](tutorial-security-policy.md)
- [管理 Azure 安全中心安全建议](security-center-recommendations.md) - 了解如何使用 Azure 安全中心的建议来保护 Azure 资源。