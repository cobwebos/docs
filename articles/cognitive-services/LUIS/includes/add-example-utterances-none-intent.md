---
title: include 文件
description: include 文件
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 11/05/2019
ms.author: diberry
ms.openlocfilehash: bbe40083159d433c0b5746834e1c530b23b03851
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648087"
---
客户端应用程序需要知道某个话语是否对应用程序没有意义或不合适。 在创建过程中，“None”  意向将添加到每个应用程序，以确定客户端应用程序是否无法回答某个话语。

如果 LUIS 对于某个话语返回“None”  意向，客户端应用程序可以询问用户是否要结束聊天或提供更多指示以继续聊天。 

如果将“无”  意向留空，则将在现有主题域意向之一中预测应在主题域之外预测的言语。 因此，客户端应用程序（如聊天机器人）将基于错误的预测执行错误的操作。 

1. 在左侧面板中选择“意向”。 

1. 选择“None”意向。  添加用户可能会输入的、但与应用无关的三条陈述：

    |`None` 示例言语|
    |--|
    |`Barking dogs are annoying`|
    |`Order a pizza for me`|
    |`Penguins in the ocean`|
