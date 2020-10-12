---
title: Azure 顾问的建议摘要
description: 获取活动建议的定期摘要
ms.topic: article
ms.date: 03/16/2020
ms.author: sagupt
ms.openlocfilehash: e446fca11e029e28e44ada884efd071f3142514c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86518073"
---
# <a name="configure-periodic-summary-for-recommendations"></a>针对建议配置定期摘要

使用 Azure 顾问的**建议摘要**可以轻松主动地了解不同类别中的活动建议。 可以通过此功能针对不同类别的所有活动建议的摘要配置定期通知。 可以使用操作组为电子邮件、短信之类的通知选择所需通道。 本文介绍了如何为 Azure 顾问建议设置**建议摘要**。


## <a name="setting-up-your-recommendation-digest"></a>设置建议摘要 

可以借助**建议摘要**创建体验来配置摘要。 可选择以下参数进行配置：
1. 类别：建议类别包括成本、高可用性、性能、卓越运营等。 此功能尚不可用于安全建议。
2. 摘要频率：摘要通知的频率可以是每周、每两周和每月。
3. 操作组：可以选择现有的操作组，也可以创建新的操作组。 若要详细了解操作组，请参阅[创建和管理操作组](../azure-monitor/platform/action-groups.md)。
4. 摘要语言
5. 建议摘要名称：可以使用用户易记的字符串来更好地跟踪和监视摘要。

## <a name="steps-to-create-recommendation-digest-in-azure-portal"></a>在 Azure 门户中创建建议摘要的步骤

下面是创建**建议摘要**的步骤：
* **步骤 1：** 在 Azure 门户中，转到“顾问”，然后在“监视”部分下选择“建议摘要”   

   ![建议摘要入口点](./media/digest-0.png)

* **步骤 2：** 从顶部栏中选择“新建建议摘要”，如下所示：

   ![创建建议摘要](./media/digest-5.png)

* **步骤 3：** 在“范围”部分中选择摘要的“订阅”

   ![提供建议摘要输入](./media/digest-1.png)

* **步骤 4：** 在“条件”部分中选择配置，例如“类别”、“频率”和“语言”   

   ![提供建议摘要输入条件](./media/digest-2.png)

* **步骤 5：** 在“操作组”部分中，选择摘要的“操作组”。 可以在此文中了解详细信息：[创建和管理操作组](../azure-monitor/platform/action-groups.md)

   ![提供建议摘要输入操作组](./media/digest-3.png)

* **步骤 6：** 这是**摘要详细信息**的最后一部分，可以在其中为建议摘要指定名称和状态。 按“创建建议摘要”按钮以完成设置。
   ![完成建议摘要创建](./media/digest-4.png)

## <a name="next-steps"></a>后续步骤

有关顾问建议的详细信息，请参阅以下资源：
* [Azure 顾问简介](advisor-overview.md)
* [顾问入门](advisor-get-started.md)
* [顾问成本建议](advisor-cost-recommendations.md)
* [顾问性能建议](advisor-performance-recommendations.md)
* [顾问安全性建议](advisor-security-recommendations.md)
* [顾问卓越运营建议](advisor-operational-excellence-recommendations.md)
* [顾问 REST API](/rest/api/advisor/)
