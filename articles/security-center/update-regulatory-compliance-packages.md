---
title: 如何在 Azure 安全中心规章符合性仪表板中更新为动态规章相容性监视 |Microsoft Docs
description: 更新法规符合性包
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
ms.openlocfilehash: 7a017f9485ec847c0a2c045e4c511b6a68db0cb2
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82889913"
---
# <a name="customizing-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>自定义合规性仪表板中的标准集

Azure 安全中心会不断地将资源的配置与行业标准、法规和基准中的要求进行比较。 **规章相容性仪表板**根据你如何满足特定的符合性控制和要求，提供对符合性状态的见解。


## <a name="overview-of-compliance-packages"></a>符合性包概述

在安全中心中，行业标准、法规标准和基准均以*符合性包*的形式表示。  每个包都是在 Azure 策略中定义的一个计划。 若要查看在仪表板中映射为评估的符合性数据，请从**安全策略**页中将符合性包添加到管理组或订阅。 （了解有关 Azure 策略的详细信息以及使用[安全策略](tutorial-security-policy.md)的方案。）

如果已将标准或基准载入到所选作用域，则标准将显示在你的符合性仪表板中，并将所有关联的符合性数据映射为评估。 你还可以为任何已载入的标准下载汇总报表。

Microsoft 还会自行跟踪法规标准，并随着时间的推移，自动改进其在某些包中的覆盖范围。 当 Microsoft 发布新的计划内容（映射到标准中的更多控件的新策略）时，其他内容会自动显示在仪表板中。

> [!TIP]
> 一种标准的改进，因为 Microsoft 发布新内容是**AZURE CIS 1.1.0 （新）** （更正式地说， [Cis Microsoft Azure 地基基准版本 1.1.0](https://www.cisecurity.org/benchmark/azure/)）。 需要将其添加到仪表板中的 "Azure CIS 1.1.0"，这是默认情况下在每个安全中心环境中配置的 Azure CIS 的表示形式。 该包依赖于一组静态规则。 较新的包包括更多策略，并将随时间自动更新。 更新到如下所述的新动态包。


## <a name="available-packages"></a>可用包

可以添加标准，如 NIST SP 800-53 R4、SWIFT CSP CSCF-v2020、英国官方和英国 NHS、加拿大联邦 PBMM 和 Azure CIS 1.1.0 （新）-Azure CIS 1.1.0 的更完整表示形式。 

此外，还可以根据常见的符合性框架，为安全和符合性最佳实践添加**Azure 安全基准**，其中包括 Microsoft 创作的特定于 azure 的准则。 （[了解有关 Azure 安全性基准的详细信息](https://docs.microsoft.com/azure/security/benchmarks/introduction)。）

在仪表板可用时，将支持该仪表板中的其他标准。 


## <a name="adding-a-regulatory-standard-to-your-dashboard"></a>向仪表板添加法规标准

以下步骤说明如何添加包以监视符合性，其中一种受支持的法规标准。

> [!NOTE]
> 只有作为所有者或策略参与者的用户具有添加符合性标准所必需的权限。 

1. 在安全中心的边栏中，选择 "**符合**性"，以打开 "合规性" 仪表板。 可在此处查看当前已分配给当前所选订阅的符合性标准。   

1. 在页面顶部，选择 "**管理符合性策略**"。 "策略管理" 页将出现。

1. 选择要为其管理法规符合性状况的订阅或管理组。 

    > [!TIP]
    > 建议选择标准适用的最高作用域，以便为所有嵌套资源聚合和跟踪符合性数据。 

1. 若要添加与组织相关的标准，请单击 "**添加更多标准**"。 

1. 从 "**添加合规性标准**" 页，可以搜索任何可用标准的程序包。 可用的一些标准包括：

    - **Azure 安全基准**
    - **NIST SP 800-53 R4**
    - **SWIFT CSP CSCF-v2020**
    - **UKO 和英国 NHS**
    - **加拿大 PBMM**
    
    ![将规章包添加到 Azure 安全中心的符合性仪表板](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. 从安全中心的边栏中，再次选择 "**规章遵从性**" 以返回到 "合规性" 仪表板。
    * 你的新标准将显示在行业 & 规章标准的列表中。 
    * 如果已添加**AZURE cis 1.1.0 （新）**，则 azure cis 1.1.0 相容性的原始*静态*视图也会保留在其上。 将来可能会自动删除它。

    > [!NOTE]
    > 新添加的标准可能需要几个小时才能显示在 "符合性" 仪表板中。

    [![显示旧的和新的 Azure CIS 的规章相容性仪表板](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark-small.png)](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark.png#lightbox)

## <a name="next-steps"></a>后续步骤

本文介绍了如何**添加符合性包**来监视与其他标准的符合性。 

有关其他相关材料，请参阅以下文章： 

- [Azure 安全基准](https://docs.microsoft.com/azure/security/benchmarks/introduction)
- [安全中心规章相容性仪表板](security-center-compliance-dashboard.md)
- [使用安全策略](tutorial-security-policy.md)