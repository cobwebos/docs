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
ms.date: 07/29/2019
ms.author: memildin
ms.openlocfilehash: 6363100c844d071a3bb47521cec6ff7e988f6af8
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263194"
---
# <a name="security-recommendations-in-azure-security-center"></a>Azure 安全中心的安全建议 
本主题说明如何查看和了解 Azure 安全中心内的建议，以帮助你保护 Azure 资源。

> [!NOTE]
> 本文档将使用示例部署介绍该服务。  本文档不是一份分步指南。
>

## <a name="what-are-security-recommendations"></a>安全建议是什么？

建议是为了保护资源而要采取的措施。

安全中心会定期分析 Azure 资源的安全状态，以识别潜在的安全漏洞。 然后，提供有关如何修正这些漏洞的建议。

每项建议都提供：

- 问题的简短说明。
- 为实施建议而要执行的补救步骤。
- 受影响的资源。

## <a name="monitor-recommendations"></a>监视建议 <a name="monitor-recommendations"></a>

安全中心将分析资源的安全状态，以识别潜在的漏洞。 “概述”下的“建议”磁贴显示了安全中心列出的建议总数。

![安全中心概述](./media/security-center-recommendations/asc-overview.png)

1. 选择“概述”下的“建议”磁贴。 这会打开“建议”列表。

1. 建议分组到安全控件中。

      ![按安全控制分组的建议](./media/security-center-recommendations/view-recommendations.png)

1. 展开控件并选择特定的建议，以查看 "建议" 页。

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="建议详细信息页。" lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    页面包括：

    - **严重性指标**
    - **刷新间隔**  (相关)  
    - **说明** -问题的简短说明
    - **修正步骤** -说明修正受影响资源上的安全问题所需的手动步骤。 有关 "快速修复" 的建议，可以在将建议的修补程序应用于资源之前选择 " **查看修正逻辑** "。 
    - **受影响的资源** -将资源分组到选项卡：
        - **正常资源** –不受影响的相关资源，或已修正问题的相关资源。
        - 不**正常资源**–仍会受到确定问题影响的资源。
        - **不适用的资源** –建议无法提供明确答案的资源。 "不适用" 选项卡还包括每个资源的原因。 

            :::image type="content" source="./media/security-center-recommendations/recommendations-not-applicable-reasons.png" alt-text="由于原因而不适用的资源。":::


 
## <a name="next-steps"></a>后续步骤

在本文档中，已向你介绍安全中心的安全建议。 了解如何修正建议：

* [修正建议](security-center-remediate-recommendations.md) -了解如何配置 Azure 订阅和资源组的安全策略。
