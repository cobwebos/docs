---
title: Azure 顾问的建议摘要
description: 获取活动建议的定期摘要
ms.topic: article
ms.date: 03/16/2020
ms.author: sagupt
ms.openlocfilehash: 98f1bfd42a486e005cd1e777a83f5b615a7c8304
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "79502459"
---
# <a name="configure-periodic-summary-for-recommendations"></a>配置建议的定期摘要

顾问**建议摘要**提供一种简单而主动的方式，可在不同的类别中随时了解你的活动建议。 利用此功能，可以在不同的类别中配置定期通知，以获得所有活动的建议摘要。 可以使用操作组选择所需的频道，如电子邮件、sms 或其他通知。 本文介绍如何为顾问建议设置**建议摘要**。


## <a name="setting-up-your-recommendation-digest"></a>设置建议摘要 

**建议摘要**创建体验可帮助你配置摘要。 你可以为配置选择以下参数：
1. 类别：我们提供了建议类别，如成本、高可用性、性能和运营绩效卓越。 此功能尚不可用于安全建议。
2. 摘要的频率：摘要通知的频率可以是每周、每两周、每月一次。
3. 操作组：可以选择现有操作组，也可以创建新的操作组。 若要了解有关操作组的详细信息，请参阅[创建和管理操作组](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)。
4. 摘要语言
5. 建议摘要名称：可以使用用户友好的字符串来更好地跟踪和监视摘要。

## <a name="steps-to-create-recommendation-digest-in-azure-portal"></a>在 Azure 门户中创建建议摘要的步骤

下面是创建**建议摘要**的步骤：
* **步骤1：** 在 Azure 门户中，单击 " **Advisor** "，然后在 "**监视**" 部分选择 "**建议摘要**" 

   ![建议摘要入口点](./media/digest-0.png)

* **步骤2：** 从顶部栏中选择 "**新建议摘要**"，如下所示：

   ![创建建议摘要](./media/digest-5.png)

* **步骤3：** 在 "**作用域**" 部分中，选择摘要的**订阅**

   ![提供建议摘要输入](./media/digest-1.png)

* **步骤4：** 在 "**条件**" 部分中，选择**类别**、**频率**和**语言**等配置

   ![提供建议摘要输入条件](./media/digest-2.png)

* **步骤5：** 在 "**操作组**" 部分中，选择摘要的**操作组**。 可在此处了解详细信息-[创建和管理操作组](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)

   ![提供建议摘要输入操作组](./media/digest-3.png)

* **步骤6：** 在此最后一节中，对于**摘要详细信息**，可以为建议摘要指定名称和状态。 按 "**创建建议摘要**" 完成设置。
   ![完成建议摘要创建](./media/digest-4.png)

## <a name="next-steps"></a>后续步骤

有关顾问建议的详细信息，请参阅以下资源：
* [Azure 顾问简介](advisor-overview.md)
* [顾问入门](advisor-get-started.md)
* [顾问成本建议](advisor-cost-recommendations.md)
* [顾问性能建议](advisor-performance-recommendations.md)
* [顾问安全性建议](advisor-security-recommendations.md)
* [顾问操作优秀建议](advisor-operational-excellence-recommendations.md)
* [顾问 REST API](https://docs.microsoft.com/rest/api/advisor/)
