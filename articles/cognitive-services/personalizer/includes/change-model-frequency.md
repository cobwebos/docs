---
title: include 文件
description: include 文件
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 01/15/2020
ms.author: diberry
ms.openlocfilehash: 7ef219e6b5f7547029612ec3898efec51abd4712
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122742"
---
## <a name="change-the-model-update-frequency"></a>更改模型更新频率

在 Azure 门户的“配置”页上的个性化体验创建服务资源中，将“模型更新频率”更改为 10 秒   。 此短暂持续时间可快速训练服务，使你可以看到顶部操作如何针对每次迭代而变化。

![更改模型更新频率](../media/settings/configure-model-update-frequency-settings.png)

首次实例化个性化体验创建服务循环时，由于没有奖励 API 调用可供训练，因此没有模型。 排名调用将为每个项返回相等的概率。 你的应用程序仍应始终使用 RewardActionId 的输出对内容进行排名。