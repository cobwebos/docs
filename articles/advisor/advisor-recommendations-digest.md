---
title: Azure 顾问的建议摘要
description: 获取活动建议的定期摘要
ms.topic: article
ms.date: 03/16/2020
ms.author: sagupt
ms.openlocfilehash: 98f1bfd42a486e005cd1e777a83f5b615a7c8304
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502459"
---
# <a name="configure-periodic-summary-for-recommendations"></a>为建议配置定期摘要

顾问**建议摘要**提供了一种简单而主动的方式，可以跨不同类别查看您的主动建议。 此功能提供了为跨不同类别的所有活动建议摘要配置定期通知的功能。 您可以使用操作组为电子邮件、短信或其他通知选择所需的频道。 本文介绍如何为顾问建议设置**建议摘要**。


## <a name="setting-up-your-recommendation-digest"></a>设置您的推荐摘要 

**建议摘要**创建体验可帮助您配置摘要。 您可以为配置选择以下参数：
1. 类别：我们有推荐类别，如成本、高可用性、性能和卓越运营。 此功能尚不适用于安全建议。
2. 摘要频率：摘要通知的频率可以是每周、每两周和每月。
3. 操作组：您可以选择现有操作组或创建新操作组。 要了解有关操作组的更多详细信息，请参阅[创建和管理操作组](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)。
4. 摘要语言
5. 建议摘要名称：您可以使用用户友好的字符串更好地跟踪和监视摘要。

## <a name="steps-to-create-recommendation-digest-in-azure-portal"></a>在 Azure 门户中创建建议摘要的步骤

以下是创建**建议摘要的步骤：**
* **第 1 步：** 在 Azure 门户中，转到 **"顾问"，** 在 **"监视"** 部分下，选择 **"建议摘要"** 

   ![建议摘要入口点](./media/digest-0.png)

* **第 2 步：** 从顶部栏中选择 **"新建建议摘要**"，如下所示：

   ![创建建议摘要](./media/digest-5.png)

* **第 3 步：** 在 **"范围"** 部分中，选择摘要的**订阅**

   ![提供建议摘要输入](./media/digest-1.png)

* **第 4 步：** 在**条件**部分中，选择**类别**、**频率**和**语言**等配置

   ![提供建议摘要输入条件](./media/digest-2.png)

* **第 5 步：** 在**操作组**部分中，选择摘要**的操作组**。 您可以在此处了解更多信息 -[创建和管理操作组](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)

   ![提供建议摘要输入操作组](./media/digest-3.png)

* **第 6 步：** 在摘要**详细信息**的最后一节中，您可以将名称和状态分配给建议摘要。 按**创建建议摘要**以完成设置。
   ![完整的建议摘要创建](./media/digest-4.png)

## <a name="next-steps"></a>后续步骤

有关顾问建议的详细信息，请参阅以下资源：
* [Azure 顾问简介](advisor-overview.md)
* [顾问入门](advisor-get-started.md)
* [顾问成本建议](advisor-cost-recommendations.md)
* [顾问性能建议](advisor-performance-recommendations.md)
* [顾问安全性建议](advisor-security-recommendations.md)
* [顾问卓越运营建议](advisor-operational-excellence-recommendations.md)
* [顾问 REST API](https://docs.microsoft.com/rest/api/advisor/)
