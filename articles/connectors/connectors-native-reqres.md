---
title: "使用请求和响应的操作 |Microsoft 文档"
description: "请求和响应的触发器和操作在 Azure 逻辑应用程序概述"
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: 566924a4-0988-4d86-9ecd-ad22507858c0
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan
ms.openlocfilehash: e45b07d709927af64cfba28dfb0d8ee9cb8893b3
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-the-request-and-response-components"></a>要开始使用的请求和响应的组件
逻辑应用中的请求和响应组件，你可以响应实时的事件。

例如，你能够：

* 从本地数据库通过逻辑的应用响应 HTTP 请求的数据。
* 触发从外部 webhook 事件的逻辑应用。
* 调用包含请求和响应操作从，在另一个逻辑应用内的逻辑应用程序。

若要开始使用逻辑应用的请求和响应的操作，请参阅[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。

## <a name="use-the-http-request-trigger"></a>使用 HTTP 请求触发器
触发器是可以用于启动工作流中的逻辑应用定义的事件。 [了解有关触发器](connectors-overview.md)。

下面是如何设置逻辑应用程序设计器中的 HTTP 请求的示例序列。

1. 添加触发器**请求-接收时 HTTP 请求**逻辑在应用程序中。 你可以根据需要提供的 JSON 架构 (通过使用之类的工具[JSONSchema.net](http://jsonschema.net)) 请求正文。 这样设计器生成 HTTP 请求中的属性的令牌。
2. 添加另一项操作，以便你可以保存逻辑应用。
3. 在保存后逻辑应用，你可以从请求卡获取 HTTP 请求 URL。
4. HTTP POST (你可以使用之类的工具[Postman](https://www.getpostman.com/)) 到的 URL 将触发逻辑应用。

> [!NOTE]
> 如果你未定义响应操作，`202 ACCEPTED`响应立即返回到调用方。 响应操作可用于自定义响应。
> 
> 

![响应触发器](./media/connectors-native-reqres/using-trigger.png)

## <a name="use-the-http-response-action"></a>使用 HTTP 响应操作
通过发出 HTTP 请求来触发工作流中使用时，HTTP 响应操作才有效。 如果你未定义响应操作，`202 ACCEPTED`响应立即返回到调用方。  在工作流中的任何步骤，可以添加响应操作。 逻辑应用，只保留传入请求打开一个分钟的响应。  在一分钟，如果没有响应发送从工作流 （和响应操作存在于定义） 之后,`504 GATEWAY TIMEOUT`返回到调用方。

下面介绍了如何添加 HTTP 响应操作：

1. 选择**新步骤**按钮。
2. 选择**添加操作**。
3. 在操作搜索框中，键入**响应**列出响应操作。
   
    ![选择响应操作](./media/connectors-native-reqres/using-action-1.png)
4. 添加所需的 HTTP 响应消息的任何参数。
   
    ![完成响应操作](./media/connectors-native-reqres/using-action-2.png)
5. 单击工具栏上，若要保存，左上角和逻辑应用会同时将保存并发布 （激活）。

## <a name="request-trigger"></a>请求触发器
以下是此连接器支持的触发器的详细信息。 没有单个请求触发器。

| 触发器 | 描述 |
| --- | --- |
| 请求 |当收到 HTTP 请求时发生 |

## <a name="response-action"></a>响应操作
以下是此连接器支持的操作的详细信息。 没有可以仅当请求触发器一起出现时使用的单个响应操作。

| 操作 | 描述 |
| --- | --- |
| 响应 |返回对关联的 HTTP 请求的响应 |

### <a name="trigger-and-action-details"></a>触发器和操作的详细信息
下表描述了触发器和操作的输入的字段和相应输出详细信息。

#### <a name="request-trigger"></a>请求触发器
下面是从传入的 HTTP 请求的触发器的输入的字段。

| 显示名称 | 属性名 | 描述 |
| --- | --- | --- |
| JSON 架构 |架构 |HTTP 请求正文的 JSON 架构 |

<br>

**输出详细信息**

以下是输出请求的详细信息。

| 属性名 | 数据类型 | 描述 |
| --- | --- | --- |
| 标头 |对象 |请求标头 |
| 正文 |对象 |请求对象 |

#### <a name="response-action"></a>响应操作
以下是有关 HTTP 响应操作的输入的字段。 A * 这是必填的字段。

| 显示名称 | 属性名 | 描述 |
| --- | --- | --- |
| 状态代码 * |statusCode |HTTP 状态代码 |
| 标头 |标头 |任何响应标头，以包含一个 JSON 对象 |
| 正文 |正文 |响应正文 |

## <a name="next-steps"></a>后续步骤
现在，试用的平台和[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。 你可以通过查看浏览逻辑应用中的其他可用连接器我们[Api 列表](apis-list.md)。

