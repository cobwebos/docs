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
ms.openlocfilehash: 408b0f020be72b8e6b10dd6c97298afda1b91360
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78394634"
---
# <a name="security-recommendations-in-azure-security-center"></a>Azure 安全中心的安全建议 
本主题介绍如何查看和了解 Azure 安全中心中的建议，以帮助你保护 Azure 资源。

> [!NOTE]
> 本文档将使用示例部署介绍该服务。  本文档不是一份分步指南。
>

## <a name="what-are-security-recommendations"></a>安全建议是什么？

建议是为了保护资源而要采取的措施。

安全中心会定期分析 Azure 资源的安全状态，以识别潜在的安全漏洞。 然后向您提供有关如何删除它们的建议。

每项建议都提供：

- 建议的简短说明。
- 为了实现此建议，要执行的补救步骤。 <!-- In some cases, Quick Fix remediation is available. -->
- 需要对这些资源执行建议的操作。
- **安全分数影响**，即，如果实现此建议，安全分数将会达到的程度。

## 监视建议<a name="monitor-recommendations"></a>

安全中心将分析资源的安全状态，以识别潜在的漏洞。 "**概述**" 下的 "**建议**" 磁贴显示安全中心标识的建议的总数。

![安全中心概述](./media/security-center-recommendations/asc-overview.png)

1. 选择 "**概述**" 下的 "**建议" 磁贴**。 此时将打开**建议**列表。

      ![查看建议](./media/security-center-recommendations/view-recommendations.png)

    可筛选建议。 要筛选建议，请选择“建议”边栏选项卡上的“筛选器”。 此时会打开“筛选器”边栏选项卡，选择要查看严重性和状态值。

   * **建议**：建议。
   * **安全分数影响**：安全中心使用安全建议生成的分数，以及应用高级算法来确定每个建议的重要程度。 有关详细信息，请参阅[安全分数计算](security-center-secure-score.md#secure-score-calculation)。
   * **资源**：列出了此建议适用的资源。
   * **状态栏**：描述该特定建议的严重性：
       * **高（红色）** ：有意义的资源（例如应用程序、VM 或网络安全组）存在漏洞，需要引起注意。
       * **中（橙色）** ：存在漏洞，需要执行非关键或附加步骤以消除它或完成一个过程。
       * **Low （蓝色）** ：应该解决的漏洞，但不需要立即引起注意。 （默认情况下，不显示严重性低的建议，但如果用户需要查看这些建议，可以将其筛选出来。） 
       * **正常（绿色）** ：
       * 不可**用（灰色）** ：

1. 若要查看每个建议的详细信息，请单击 "建议"。

    ![建议详细信息](./media/security-center-recommendations/recommendation-details.png)

>[!NOTE] 
> 请参阅 Azure 资源的[经典部署模型和资源管理器部署模型](../azure-classic-rm.md)。
 
## <a name="next-steps"></a>后续步骤

在本文档中，已向你介绍安全中心的安全建议。 了解如何修正建议：

* [修正建议](security-center-remediate-recommendations.md)-了解如何配置 Azure 订阅和资源组的安全策略。
