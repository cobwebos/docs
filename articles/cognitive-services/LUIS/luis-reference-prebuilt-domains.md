---
title: 预构建的域参考
titleSuffix: Azure
description: 预构建的域参考，这些参考是语言理解智能服务 (LUIS) 中意向和实体的预构建集合。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 3265477108b7e74d65050408add6c5d5c94b4852
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233894"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>LUIS 应用的预构建的域参考
此参考提供有关[预构建的域](luis-how-to-use-prebuilt-domains.md)的信息，这些参考是 LUIS 提供的意向和实体的预构建集合。

相比之下，[自定义域](luis-how-to-start-new-app.md)从无意向和模型开始。 可将任何预构建的域意向和实体添加到自定义模型中。

# <a name="supported-domains-across-cultures"></a>各个区域支持的域

唯一支持的区域性为英语。 

<!--

The table below summarizes the currently supported domains. Support for English is usually more complete than others.


| Entity Type       | EN-US      | ZH-CN   | DE    | FR     | ES    | IT      | PT-BR |  JP  |      KO |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:| 
| Calendar    | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -      | -    | -    | -     | -  |
| Communication   | ✓    | -       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Email           | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| HomeAutomation           | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Notes      | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Places    | ✓    | -       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| RestaurantReservation   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| ToDo     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| ToDo_IPA        | ✓    | ✓       | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Utilities          | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Weather        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Web    | ✓    | -        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
||||||||||||| 

-->

<br><br>

|实体类型|description|
|--|--|
|Calendar|日历涉及到个人会议和约会，而不涉及到公共活动，例如世界杯日程安排、西雅图赛事日历或普通日历（例如今天的日期、秋季在哪一天开始、劳动节是哪一天）。 |
|通信|请求通话、发送短信或即时消息、查找并添加联系人，以及各种其他通信相关的请求（通常为传出请求）。 仅限联系人姓名的查询不属于通信域。|
|Email|电子邮件是通信域的子域。 它主要包含通过电子邮件发送和接收消息的请求。|
|家庭自动化|家庭自动化域提供与控制智能家居设备相关的意向和实体。 它主要支持与灯光和空调相关的控制命令，但对于其他电器提供某些通用化功能。|
|说明|请注意，域提供意向和实体用于创建笔记以及记下用户的项。|
|场所|场所包括企业、机构、餐馆、公共场所和地址。 该域支持位置查找以及询问有关公共场所的信息，例如其位置、营业时间和距离。|
|餐位预订|餐位预订域支持用于处理餐位预订的意向。|
|待办事项|待办事项域提供任务列表的类型，让用户添加、标记和删除其待办事项。|
|实用程序|公用设施域是所有 LUIS 预生成模型的总域，其中包含不同方案中的通用意向和言语。|
|天气|天气域侧重于检查天气状况和提供有关位置与时间的建议，或按天气状况检查时间。|
|Web|Web 域提供用于搜索网站的意向和实体。|
