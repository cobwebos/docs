---
title: Azure 安全中心的安全分数 |微软文档
description: " 使用 Azure 安全中心中的安全分数确定安全建议的优先级。 "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/15/2019
ms.author: memildin
ms.openlocfilehash: 30405ce5cc875144fcd1cf83d4a3f883a0304989
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415768"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>提高 Azure 安全中心的安全评分

> [!NOTE]
> 本文是关于安全分数的早期版本。 此安全分数体验仍可从 UI 获得，但随着时间的推移将逐渐消除。 两种安全分数体验并排运行，以实现更平滑的过渡。
>
> 有关较新的安全分数的详细信息，请参阅[此处](secure-score-security-controls.md)。
>

在有很多服务提供安全权益的情况下，通常很难知道首先要采取哪些措施来保护并强化工作负荷。 安全分数会审核您的安全建议并为其设置优先级，以便您知道首先执行哪些建议。 这可帮助你找到最严重的安全漏洞，因此你可以确定调查优先级。 安全分数是帮助您评估工作负载安全状况的工具。

## <a name="secure-score-calculation"></a>安全分数计算

安全中心模仿安全分析师的工作，审查安全建议并应用高级算法来确定每项建议的重要性。
Azure 安全中心会不断审核您的活动建议并基于它们计算安全分数，建议的分数源自其严重性和安全最佳实践，这些最佳实践对工作负荷安全性的影响最大。

安全中心还为您提供**整体安全分数**。 

**总体安全分数**是您所有推荐分数的累积。 您可以跨订阅或管理组查看总体安全分数，具体取决于您选择的内容。 分数将根据所选订阅和这些订阅上的活动建议而有所不同。

要检查哪些建议对安全分数的影响最大，您可以在安全中心仪表板中查看影响最大的三个建议，也可以使用 **"安全分数影响**"列对建议列表边栏中的建议进行排序。

要查看您的整体安全分数：

1. 在 Azure 仪表板中，单击 **"安全中心**"，然后单击 **"安全分数**"。

2. 在顶部，您可以看到安全分数亮点：
   - **总体安全分数**表示每个策略的分数，每个选定的订阅
   - **按类别进行安全分数**显示哪些资源最需要关注
   - **安全分数影响的顶级建议**为您提供了建议列表，这些建议将在实施时最提高您的安全分数。
 
   ![安全功能分数](./media/security-center-secure-score/secure-score-dashboard.png)

3. 在下表中，您可以看到每个订阅以及每个订阅的总体安全分数。

   > [!NOTE]
   > 每个订阅的安全分数总和不等于总体安全分数。 安全分数是根据您的健康资源和每个建议的总资源之间的比率计算的，而不是整个订阅的安全分数的总和。 
   >
4. 单击 **"查看建议**"以查看该订阅的建议，您可以修复这些建议以提高安全分数。
4. 在建议列表中，您可以看到，对于每个建议，都有一个表示**安全分数影响的**列。 此数字表示如果您遵循建议，您的总体安全分数将提高多少。 例如，在下面的屏幕中，如果**修复容器安全配置中的漏洞**，您的安全分数将增加 35 分。

   ![安全功能分数](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>个人安全得分

此外，要查看单个安全分数，您可以在单个推荐边栏选项卡中找到这些分数。  

**建议安全分数**是基于您的健康资源和总资源之间的比率的计算。 如果健康资源的数量等于资源总数，您将获得建议 50 的最大安全分数。 要尝试使安全分数更接近最高分数，请按照建议修复不正常的资源。

通过 **"建议"影响**，您可以了解应用建议步骤时的安全分数会提高多少。 例如，如果安全分数为 42，**并且"建议影响"为**+3，则执行建议中概述的步骤会提高您的分数变为 45。

该建议显示不执行修正步骤时工作负荷所面临的威胁。

![单项建议安全评分](./media/security-center-secure-score/indiv-recommendation-secure-score.png)


## <a name="next-steps"></a>后续步骤
本文介绍如何使用 Azure 安全中心**中的安全分数**改进安全状况。 若要了解有关安全中心的详细信息，请参阅：

* [Azure 安全中心常见问题解答](security-center-faq.md)-- 查找有关使用服务的常见问题。
* [Azure 安全中心的安全性运行状况监视](security-center-monitoring.md) - 了解如何监视 Azure 资源的运行状况。
