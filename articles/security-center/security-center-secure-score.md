---
title: Azure 安全中心中的安全评分 |Microsoft Docs
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
ms.openlocfilehash: 79154a13722654ef5cbbe7ac99bb67d4b761fe60
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76903443"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>提高 Azure 安全中心的安全评分

> [!NOTE]
> 预览中提供了增强的安全评分。 增强的安全分数最终将取代现有的安全分数，但在这段时间内，它们会并行运行以简化转换。
>
> 有关增强安全分数权益的详细信息，请参阅[此处](secure-score-security-controls.md)。
>
> 若要参与预览，请打开 Azure 门户，启动 "Azure 安全中心"，然后选择 "安全分数"。 在这里，你将在页面顶部看到一个横幅，提供新的安全评分体验。 或者单击[此处](https://aka.ms/ascnewscore)。

由于有这么多的服务提供了安全性优势，因此通常很难知道首先要采取哪些措施来保护和强化工作负荷。 Azure 安全分数查看你的安全建议，并为你确定其优先级，因此你知道首先要执行哪些建议。 这可以帮助你找到最严重的安全漏洞，以便确定调查的优先级。 安全分数是一个工具，可帮助您评估工作负荷的安全状况。

## <a name="secure-score-calculation"></a>安全分数计算

安全中心模拟安全分析师的工作、查看安全建议，以及应用高级算法来确定每个建议的重要程度。
Azure 安全中心会不断地查看活动的建议，并基于这些建议计算安全分数，推荐分数派生自其严重性和安全最佳方案，它们会影响工作负荷的安全性。

安全中心还提供**总体安全分数**。 

**整体安全分数**是所有建议评分的累积。 你可以在订阅或管理组中查看总体安全分数，具体取决于你选择的内容。 分数会根据所选订阅和这些订阅的活动建议而有所不同。

若要检查哪些建议会影响你的安全评分，你可以在 "安全中心" 仪表板中查看最常见的三个有影响力建议，也可以在 "建议列表" 边栏选项卡中使用 "**安全分数影响**" 列对建议进行排序。

查看整体安全分数：

1. 在 Azure 仪表板中，单击 "**安全中心**"，然后单击 "**安全分数**"。

2. 在顶部，可以看到安全分数突出显示：
   - **总体安全分数**表示每个策略每个选定订阅的分数
   - **按类别安全分数**显示需要关注的资源
   - **安全评分影响的热门建议**为您提供了一系列建议，这些建议可在您实现这些建议的同时提高安全分数。
 
   ![安全分数](./media/security-center-secure-score/secure-score-dashboard.png)

3. 在下表中，可以看到每个订阅和每个订阅的整体安全分数。

   > [!NOTE]
   > 每个订阅的安全分数之和不等于整体安全分数。 安全分数是根据你的正常资源与每个建议的资源总量之间的比率进行计算，而不是在你的订阅中的安全分数之和。 
   >
4. 单击 "**查看建议**" 查看对该订阅的建议，你可以修正这些建议以提高安全分数。
4. 在建议列表中，可以看到，对于每个建议，都存在一个表示**安全分数影响**的列。 如果遵循建议，则此数字表示总体安全分数将提高的程度。 例如，在下面的屏幕中，如果**修正容器安全配置中的漏洞**，安全分数将增加35点。

   ![安全分数](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>单个安全分数

此外，若要查看单个安全分数，可以在 "建议" 边栏选项卡中找到这些分数。  

**建议的安全评分**是基于正常资源和资源总量之间的比率的计算。 如果正常运行的资源数等于资源总数，则会获得建议50的最大安全分数。 若要尝试使安全分数接近最大分数，请遵循这些建议，修复不正常的资源。

如果应用建议步骤，则**建议的影响**使你可以了解安全分数的提高程度。 例如，如果安全分数为42，**建议影响**为 + 3，则执行建议中所述的步骤可将评分提高到45。

建议显示如果未采取更正步骤，你的工作负荷所公开的威胁。

![单个建议安全分数](./media/security-center-secure-score/indiv-recommendation-secure-score.png)







## <a name="next-steps"></a>后续步骤
本文介绍如何使用 Azure 安全中心的**安全评分**来改善安全状况。 若要了解有关安全中心的详细信息，请参阅：

* [Azure 安全中心常见问题解答](security-center-faq.md)-查找有关使用服务的常见问题。
* [Azure 安全中心的安全运行状况监视](security-center-monitoring.md)-了解如何监视 azure 资源的运行状况。


