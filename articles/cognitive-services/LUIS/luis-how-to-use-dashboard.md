---
title: 适用于 LUIS 应用的应用程序仪表板 | Microsoft Docs
description: 了解应用程序仪表板，它是一种可视化报表工具，可让你一目了然地监视应用。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/01/2018
ms.author: diberry
ms.openlocfilehash: 518227d9f4165a08fafefa357de255d97c710f61
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224494"
---
# <a name="application-dashboard"></a>应用程序仪表板
应用仪表板可让你一目了然地监视应用。 通过单击“我的应用”页中的应用程序名称打开应用，然后选择顶部面板中的“仪表板”，仪表板将随即显示。 

> [!CAUTION]
> 如果希望使用 LUIS 的最新指标，则需要：
> * 使用在 Azure 中创建的 LUIS [终结点密钥](luis-how-to-azure-subscription.md)
> * 对所有终结点请求（包括 LUIS [API](https://aka.ms/luis-endpoint-apis) 和机器人）使用 LUIS 终结点密钥
> * 对每个 LUIS 应用使用不同的终结点密钥。 不要对所有应用使用一个终结点密钥。 终结点密钥跟踪在密钥级别进行，而非在应用级别进行。  

“仪表板”页提供 LUIS 应用的概述，包括当前的模型状态和随时间变化的[终结点](luis-glossary.md#endpoint)使用情况。 <!--The following image shows the **Dashboard** page.-->

<!-- TBD: Get a working screen shot
![The Dashboard](./media/luis-how-to-use-dashboard/dashboard.png)
-->

<!-- TBD: IS THIS STILL TRUE?
At the top of the **Dashboard** page, a contextual notification bar constantly displays notifications to update you on the required or recommended actions appropriate for the current state of your app. It also provides useful tips and alerts as needed. A detailed description of the data reported on the **Dashboard** page follows.
-->
  
## <a name="app-status"></a>应用状态
仪表板显示应用程序的培训和发布状态，包括上次培训和发布应用的日期和时间。  

![仪表板 - 应用状态](./media/luis-how-to-use-dashboard/app-state.png)

## <a name="model-data-statistics"></a>模型数据统计信息
仪表板显示应用中现有意向、实体和已标记表述的总数。 

![应用数据统计信息](./media/luis-how-to-use-dashboard/app-model-count.png)

## <a name="endpoint-hits"></a>终结点命中数
仪表板将显示 LUIS 应用接收到的终结点命中总数，还可显示指定时间段内的命中数。 显示的命中总数是使用[终结点密钥](./luis-concept-keys.md#endpoint-key)的终结点命中数和使用[创作密钥](./luis-concept-keys.md#authoring-key)的终结点命中数的总和。

<!-- TBD: this image is old but I don't have a new one based on usage -->
![终结点命中数](./media/luis-how-to-use-dashboard/dashboard-endpointhits.png)

> [!NOTE] 
> 最新的终结点命中次数位于 Azure 门户中的 LUIS 服务概述上。 
 
### <a name="total-endpoint-hits"></a>终结点命中总数
从应用创建至当前日期，应用接收到的终结点命中总数。

### <a name="endpoint-hits-per-period"></a>一个时间段内的终结点命中数
过去某个时间段内接收到的命中数（按天显示）。 开始和结束日期之间的点数表示此时间段内的天数。 将鼠标指针悬停在各个点上可查看此时间段内每一天的命中次数。 

选择一个时间段以在图表上查看：
 
1. 单击“其他设置”![“其他设置”按钮](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png)以访问时间段列表。 可选择的时间段范围为一周到一年（最长时间段）。 

    ![一个时间段内的终结点命中数](./media/luis-how-to-use-dashboard/timerange.png)

2. 从列表中选择一个时间段，然后单击返回箭头 ![返回箭头](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) 以显示图表。

### <a name="key-usage"></a>密钥使用情况
应用程序的终结点密钥使用的命中次数。 有关终结点密钥的详细信息，请参阅 [LUIS 中的密钥](luis-concept-keys.md)。 
  
## <a name="intent-breakdown"></a>意向细分
“意向细分”显示基于已标记表述数或终结点命中数的意向细分。 此摘要图显示应用中每个意向的相对重要性。 将鼠标指针悬停在某个扇形上时，可看到意向名称和此意向占已标记表述数/终结点命中数总数的百分比。 

![意向细分](./media/luis-how-to-use-dashboard/intent-breakdown.png)

若要控制是否基于已标记表述数或终结点命中数进行细分：

1. 单击“其他设置”![“其他设置”按钮](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png)以访问列表，如下图所示：

    ![意向细分列表](./media/luis-how-to-use-dashboard/intent-breakdown-based-on.png)
2. 从列表中选择一个值，然后单击返回箭头 ![返回箭头](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) 以显示图表。

## <a name="entity-breakdown"></a>实体细分
仪表板显示基于已标记表述数或终结点命中数的实体细分。 此摘要图显示应用中每个实体的相对重要性。 将鼠标指针悬停在切片上时，可看到实体名称及其占已标记表述数/终结点命中数的百分比。 

![实体细分](./media/luis-how-to-use-dashboard/entity-breakdown.png)

若要控制是否基于已标记表述数或终结点命中数进行细分：

1. 单击“其他设置”![“其他设置”按钮](./media/luis-how-to-use-dashboard/Dashboard-Settings-btn.png)以访问列表，如下图所示：

    ![实体细分列表](./media/luis-how-to-use-dashboard/entity-breakdown-based-on.png)
2. 从列表中选择一个值，然后单击返回箭头 ![返回箭头](./media/luis-how-to-use-dashboard/Dashboard-backArrow.png) 以显示相应图表。
