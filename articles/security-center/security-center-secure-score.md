---
title: Azure 安全中心中的安全评分 | Microsoft Docs
description: " 使用 Azure 安全中心中的安全评分确定安全建议的优先级。 "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: v-mohabe
ms.openlocfilehash: c23c9a2b9af1947c397b96431ae3c3bcd0e30aaa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65968297"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>提高 Azure 安全中心的安全评分


在有很多服务提供安全权益的情况下，通常很难知道首先要采取哪些措施来保护并强化工作负荷。 Azure 安全评分评审你的安全建议并确定其优先级，以便你知道要首先执行哪些建议。 这可帮助你找到最严重的安全漏洞，因此你可以确定调查优先级。 安全评分是一个可帮助你评估工作负荷安全状况的工具。

## <a name="secure-score-calculation"></a>安全评分的计算

安全中心模仿安全分析师的工作，审查安全建议并应用高级算法来确定每项建议的重要性。
Azure 安全中心会不断检查活动建议并根据它们计算安全评分，建议的分数源自其严重性和对工作负荷安全性影响最大的最佳安全实践。

安全中心还提供**整体安全评分**。 

**整体安全评分**是所有建议评分的累计评分。 可以查看订阅或管理组的整体安全评分，具体取决于所选择的内容。 分数将根据所选订阅和这些订阅上的活动建议而有所不同。

 
若要检查哪些建议对安全评分影响最大，可以在安全中心仪表板中查看最具影响力的前三个建议，或者可以使用“安全分数影响”列对建议列表边栏选项卡中的建议进行排序  。


若要查看整体安全评分：

1. 在 Azure 安全中心仪表板中，单击“安全中心”，然后单击“安全功能分数”   。
2. 可以在顶部看见安全功能分数突出显示：
   - 总体安全功能分数表示每个策略、每个所选订阅的分数 
   - 按类别的安全功能分数显示哪些资源需要最多的关注 
   - 安全评分影响的最重要建议提供了一个建议列表，这些建议可以在你实施它们时最大程度提高安全评分。 
 
   ![安全评分](./media/security-center-secure-score/secure-score-dashboard.png)

3. 在下表中可以看到每个订阅和及其总体安全功能分数。

   > [!NOTE]
   > 每个订阅的安全功能分数总和不等于总体安全功能分数。 安全功能分数是基于健康资源与每个建议的总资源之间的比率计算的，而不是基于订阅的安全功能分数的总和。 
   >
4. 单击“查看建议”以查看可以修复的订阅建议，提高安全功能分数  。
4. 在建议列表中，可在其中看到每个建议都有表示“安全功能分数影响”的一列  。 此数字表示遵循建议能提高的整体安全评分。 例如，在下面的屏幕中，如果“修正容器安全配置中的漏洞”，安全评分将增加 35 分  。

   ![安全评分](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>单项安全评分

此外，若要查看单项安全评分，可以在单个建议边栏选项卡中找到这些分数。  

**建议安全评分**计算基于正常运行的资源与总资源之间的比率。 如果正常运行的资源数量等于资源总数，则将获得建议的最高安全评分 50。 为了让安全评分接近最高分，请按建议修复运行不正常的资源。

**建议影响**表示在应用推荐步骤时安全评分会提升多少。 例如，如果你的安全评分为 42 且“建议影响”为 +3，则执行建议中列出的步骤可以将安全评分提高到 45  。

该建议显示不执行修正步骤时工作负荷所面临的威胁。

![单项建议安全评分](./media/security-center-secure-score/indiv-recommendation-secure-score.png)







## <a name="next-steps"></a>后续步骤
本介绍了如何使用 Azure 安全中心中的“安全评分”来改进安全状况  。 若要了解有关安全中心的详细信息，请参阅：

* [Azure 安全中心常见问题解答](security-center-faq.md)-- 查找有关使用服务的常见问题。
* [Azure 安全中心的安全性运行状况监视](security-center-monitoring.md) - 了解如何监视 Azure 资源的运行状况。


