---
title: 如何在 Azure 安全中心监管合规仪表板中更新为动态监管合规监视 | Microsoft Docs
description: 更新监管合规包
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: adac0d460753f24d06bffc72f9b2b38c2a3306d7
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078096"
---
# <a name="customizing-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>在监管合规仪表板中自定义标准集

Azure 安全中心会不断地将资源的配置与行业标准、法规和基准中的要求进行比较。 监管合规仪表板基于你满足特定合规控制和要求的情况来提供合规态势的见解。


## <a name="overview-of-compliance-packages"></a>合规包概述

行业标准、监管标准和基准在安全中心中表示为合规包。  每个包都是在 Azure 策略中定义的计划。 若要查看在仪表板中映射为评估的合规数据，请从“安全策略”页中将合规包添加到管理组或订阅。 （可在[使用安全策略](tutorial-security-policy.md)中详细了解 Azure Policy 和计划。）

将标准或基准加入到所选范围后，它会将计划分配给该范围，标准会出现在监管合规仪表板中，所有关联合规数据都映射为评估。 还可以为任何已加入的标准下载摘要报表。

Microsoft 还会自行跟踪监管标准，并随时间推移自动改进其在某些包中的覆盖范围。 当 Microsoft 为计划发布新内容（映射到标准中的更多控制的新策略）时，附加内容会自动显示在仪表板中。

> [!TIP]
> 一种会随着 Microsoft 发布新内容，随时间推移而改进的标准是 Azure CIS 1.1.0（新）（更正式的名称是 [CIS Microsoft Azure 基础基准版本 1.1.0](https://www.cisecurity.org/benchmark/azure/)）。 需要将此标准添加到仪表板中的“Azure CIS 1.1.0”（这是默认情况下在每个安全中心环境中配置的 Azure CIS 表示形式）旁。 该包依赖于一组静态规则。 较新的包中包含更多策略，会随时间推移自动更新。 按如下所述更新到新动态包。


## <a name="available-packages"></a>可用包

可以添加标准，如 NIST SP 800-53 R4、SWIFT CSP CSCF-v2020、UK Official and UK NHS、Canada Federal PBMM 以及 Azure CIS 1.1.0（新）- Azure CIS 1.1.0 的更完整表示形式。 

此外，可以添加 Azure 安全基准，它是由 Microsoft 创作的特定于 Azure 的准则，适用于基于常见合规框架的安全与合规最佳做法。 （[详细了解 Azure 安全基准](https://docs.microsoft.com/azure/security/benchmarks/introduction)。）

其他标准会在可用时在仪表板中受支持。 


## <a name="add-a-regulatory-standard-to-your-dashboard"></a>向仪表板添加法规标准

以下步骤说明如何添加包以监视是否符合与一种支持的监管标准。

> [!NOTE]
> 只有作为所有者或策略参与者的用户才拥有添加合规标准所需的权限。 

1. 从安全中心的边栏中，选择“监管合规”以打开监管合规仪表板。 可在此处查看当前分配给当前所选订阅的合规标准。   

1. 在页面顶部，选择“管理合规策略”。 “策略管理”页将出现。

1. 选择要为其管理监管合规态势的订阅或管理组。 

    > [!TIP]
    > 建议选择标准所适用的最高范围，以便为所有嵌套资源聚合和跟踪合规数据。 

1. 若要添加与组织相关的标准，请单击“添加更多标准”。 

1. 从“添加监管合规标准”页中，可以搜索任何可用标准的包。 可用的一些标准包括：

    - Azure 安全基准
    - **NIST SP 800-53 R4**
    - SWIFT CSP CSCF-v2020
    - UKO and UK NHS
    - Canada PBMM
    
    ![将监管包添加到 Azure 安全中心的监管合规仪表板](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. 选择 " **添加** "，然后输入特定计划的所有必要详细信息，如范围、参数和修正。

1. 从安全中心的边栏中，再次选择“监管合规”以返回到监管合规仪表板。
    * 新标准会出现在行业和监管标准的列表中。 
    * 如果添加了 Azure CIS 1.1.0（新），则 Azure CIS 1.1.0 合规的原始静态视图也会保留在它旁边。 将来可能会自动删除它。

    > [!NOTE]
    > 新添加的标准可能需要几个小时才能出现在合规仪表板中。

    [![显示旧的和新的 Azure CIS 的监管合规仪表板](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark-small.png)](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark.png#lightbox)


## <a name="removing-a-standard-from-your-dashboard"></a>从仪表板中删除标准

如果任何提供的法规标准与您的组织不相关，则只需将其从 UI 中删除即可简单。 这样，你便可以进一步自定义合规性仪表板，仅关注适用于你的标准。

删除标准：

1. 从安全中心的菜单中，选择 " **安全策略**"。

1. 选择要从中删除标准的相关订阅。

    > [!NOTE]
    > 你可以从订阅中删除标准，但不能从管理组中删除。 

    此时会打开 "安全策略" 页。 对于所选订阅，它会显示默认策略、行业和法规标准以及您创建的任何自定义计划。

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard.png" alt-text="从 Azure 安全中心的规章相容性仪表板中删除法规标准&quot;:::

1. 对于要删除的标准，选择 " **禁用**"。 此时将显示一个确认窗口。

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard-confirm.png" alt-text="从 Azure 安全中心的规章相容性仪表板中删除法规标准&quot;:::

1. 对于要删除的标准，选择 ":::

1. 请选择“是”。 将删除此标准。 


## <a name="next-steps"></a>后续步骤

在本文中，你了解了如何添加合规包以监视是否符合附加标准。 

如需其他相关材料，请参阅以下文章： 

- [Azure 安全基准](https://docs.microsoft.com/azure/security/benchmarks/introduction)
- [安全中心监管合规仪表板](security-center-compliance-dashboard.md)
- [使用安全策略](tutorial-security-policy.md)