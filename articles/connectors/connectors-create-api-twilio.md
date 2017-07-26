---
title: "在 Azure 逻辑应用中添加 Twilio 连接器 | Microsoft Docs"
description: "使用 REST API 参数的 Twilio 连接器概述"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 43116187-4a2f-42e5-9852-a0d62f08c5fc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/19/2016
ms.author: mandia; ladocs
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: a790ac51b0fea7e3fa379d20e0e094e7ce0d7696
ms.contentlocale: zh-cn
ms.lasthandoff: 05/26/2017


---
# <a name="get-started-with-the-twilio-connector"></a>Twilio 连接器入门
连接到 Twilio 以发送和接收全球短信、彩信和 IP 消息。 借助 Twilio，可以：

* 根据从 Twilio 中获取的数据生成业务流。 
* 使用获取消息、列出消息等的操作。 这些操作可获得响应，然后使输出可用于其他操作。 例如，收到新 Twilio 消息时，可接受此消息并在服务总线工作流中使用它。 

若要开始创建逻辑应用，请参阅[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="create-a-connection-to-twilio"></a>创建到 Twilio 的连接
将此连接器添加到逻辑应用时，输入以下 Twilio 值：

| 属性 | 必选 | 说明 |
| --- | --- | --- |
| 帐户 ID |是 |输入 Twilio 帐户 ID |
| 访问令牌 |是 |输入 Twilio 访问令牌 |

> [!INCLUDE [Steps to create a connection to Twilio](../../includes/connectors-create-api-twilio.md)]
> 
> 

如果不具有 Twilio 访问令牌，请参阅[用户标识和访问令牌](https://www.twilio.com/docs/api/chat/guides/identity)。

## <a name="connector-specific-details"></a>特定于连接器的详细信息

在[连接器详细信息](/connectors/twilio/)中查看在 Swagger 中定义的触发器和操作，并查看限制。

## <a name="more-connectors"></a>更多连接器
返回到 [API 列表](apis-list.md)。
