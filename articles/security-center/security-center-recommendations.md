---
title: Azure 安全中心的安全建议
description: 本文档介绍 Azure 安全中心中的建议如何帮助你保护 Azure 资源并保持符合安全策略。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 7f6c0f2a311590219fb59bfe1ec63831c03e8af2
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91314430"
---
# <a name="security-recommendations-in-azure-security-center"></a>Azure 安全中心的安全建议 
本主题说明如何查看和了解 Azure 安全中心内的建议，以帮助你保护 Azure 资源。

> [!NOTE]
> 本文档将使用示例部署介绍该服务。  本文档不是一份分步指南。
>

## <a name="what-are-security-recommendations"></a>安全建议是什么？

建议是为了保护资源而要采取的措施。

安全中心会定期分析 Azure 资源的安全状态，以识别潜在的安全漏洞。 然后会提供有关如何消除这些安全漏洞的建议。

每项建议都提供：

- 问题简述。
- 为实施建议而要执行的补救步骤。
- 受影响的资源。

## <a name="monitor-recommendations"></a>监视建议 <a name="monitor-recommendations"></a>

安全中心将分析资源的安全状态，以识别潜在的漏洞。 “概述”下的“建议”磁贴显示了安全中心列出的建议总数。

![安全中心概述](./media/security-center-recommendations/asc-overview.png)

1. 选择“概述”下的“建议”磁贴。 这会打开“建议”列表。

1. 建议会被分组到各项安全控制中。

      ![建议会按安全控制分组](./media/security-center-recommendations/view-recommendations.png)

1. 展开一项控制并选择特定的建议，以查看建议页。

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="建议详细信息页。" lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    该页面包括：

    - 受支持的建议的 "**强制**" 和 "**拒绝**" 按钮 (参阅[阻止强制/拒绝建议的配置](prevent-misconfigurations.md)错误) 
    - 严重性指标
    - 刷新间隔（如果相关） 
    - 描述 - 问题简述
    - 修正步骤 - 修正受影响资源的安全问题时所需的手动步骤的说明。 对于带有“快速修复”的建议，可以先选择“查看修正逻辑”，然后再为资源应用建议的修补程序。 
    - 受影响的资源 - 资源会分组到不同的选项卡中：
        - **正常资源** –不受影响的相关资源，或已修正问题的相关资源。
        - 不**正常资源**–仍会受到确定问题影响的资源。
        - **不适用的资源** –建议无法提供明确答案的资源。 “不适用”选项卡还会为每个资源提供原因。 

            :::image type="content" source="./media/security-center-recommendations/recommendations-not-applicable-reasons.png" alt-text="不适用的资源及其原因。":::

## <a name="preview-recommendations"></a>预览建议

计算安全分数时不包括标记为“预览”的建议。

仍应尽可能按这些建议修正，以便在预览期结束时，它们会有助于提升评分。

预览建议示例如下：

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="带有预览标志的建议":::
 
## <a name="next-steps"></a>后续步骤

在本文档中，已向你介绍安全中心的安全建议。 若要了解如何按建议修正，请参阅：

- [按建议修正](security-center-remediate-recommendations.md) -- 了解如何为 Azure 订阅和资源组配置安全策略。
- [防止对强制/拒绝建议的配置](prevent-misconfigurations.md)错误。
