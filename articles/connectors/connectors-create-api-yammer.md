---
title: 在 Azure 逻辑应用中添加 Yammer 连接器 | Microsoft Docs
description: 使用 REST API 参数的 Yammer 连接器概述
services: logic-apps
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: b5ae0827-fbb3-45ec-8f45-ad1cc2e7eccc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 7f1758e9b95f534b23188f427ae0edaddbb29a48
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-yammer-connector"></a>Yammer 连接器入门
连接到 Yammer 以在企业网络中访问对话。 借助 Yammer，可以：

* 根据从 Yammer 中获取的数据生成业务流。 
* 针对组或关注的源中有新消息的情况使用触发器。
* 使用操作发布消息、获取所有消息等。 这些操作可获得响应，并使输出可用于其他操作。 例如，出现新消息时，可使用 Office 365 发送电子邮件。

若要立即开始创建逻辑应用，请参阅[创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="create-a-connection-to-yammer"></a>创建到 Yammer 的连接
要使用 Yammer 连接器，首先创建**连接**，然后为以下属性提供详细信息： 

| 属性 | 必选 | 说明 |
| --- | --- | --- |
| 令牌 |是 |提供 Yammer 凭据 |

> [!INCLUDE [Steps to create a connection to Yammer](../../includes/connectors-create-api-yammer.md)]
> 

## <a name="connector-specific-details"></a>特定于连接器的详细信息

在[连接器详细信息](/connectors/yammer/)中查看在 Swagger 中定义的触发器和操作，并查看限制。

## <a name="more-connectors"></a>更多连接器
返回到 [API 列表](apis-list.md)。