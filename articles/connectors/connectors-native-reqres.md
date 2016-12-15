---
title: "使用请求和响应操作 | Microsoft Docs"
description: "Azure 逻辑应用中的请求和响应触发器和操作的概述"
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
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 48c9453ac0435a6180f8b322897439bf1964fae9


---
# <a name="get-started-with-the-request-and-response-components"></a>请求和响应组件入门
借助逻辑应用中的请求和响应组件，可以实时响应事件。

例如，你可以：

* 通过逻辑应用使用来自本地数据库的数据响应 HTTP 请求。
* 从外部 Webhook 事件触发逻辑应用。
* 从另一个逻辑应用内使用请求和响应操作调用逻辑应用。

若要开始在逻辑应用中使用请求和响应操作，请参阅[创建逻辑应用](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="use-the-http-request-trigger"></a>使用 HTTP 请求触发器
触发器是用于启动在逻辑应用中定义的工作流的事件。 [了解有关触发器的详细信息](connectors-overview.md)。

下面是如何在逻辑应用设计器中设置 HTTP 请求的示例序列。

1. 在逻辑应用中添加触发器“请求 - 收到 HTTP 请求时”。 可以选择为请求正文提供 JSON 架构（通过使用 [JSONSchema.net](http://jsonschema.net) 之类的工具）。 这允许设计人员为 HTTP 请求中的属性生成令牌。
2. 添加另一个操作，以便保存逻辑应用。
3. 保存逻辑应用后，可以从请求卡获取 HTTP 请求 URL。
4. 对该 URL 的 HTTP POST（可使用 [Postman](https://www.getpostman.com/) 之类的工具）触发逻辑应用。

> [!NOTE]
> 如果未定义响应操作，`202 ACCEPTED` 响应将立即返回到调用方。 可使用响应操作自定义响应。
> 
> 

![响应触发器](./media/connectors-native-reqres/using-trigger.png)

## <a name="use-the-http-response-action"></a>使用 HTTP 响应操作
HTTP 请求操作仅在由 HTTP 请求触发的工作流中使用时才有效。 如果未定义响应操作，`202 ACCEPTED` 响应将立即返回到调用方。  可在工作流内的任意步骤中添加响应操作。 逻辑应用使传入请求仅为一个响应保持打开状态一分钟。  一分钟后，如果未从工作流中发送任何响应（并且定义中存在响应操作），则 `504 GATEWAY TIMEOUT` 将返回到调用方。

下面是添加 HTTP 响应操作的方法：

1. 选择“新步骤”按钮。
2. 选择“添加操作”。
3. 在操作搜索框中，键入**响应**列出响应操作。
   
    ![选择响应操作](./media/connectors-native-reqres/using-action-1.png)
4. 添加 HTTP 响应消息所需的任何参数。
   
    ![完成响应操作](./media/connectors-native-reqres/using-action-2.png)
5. 单击工具栏左上角保存，逻辑应用将保存并发布（激活）。

## <a name="request-trigger"></a>请求触发器
下面是此连接器支持的触发器的详细信息。 存在单个请求触发器。

| 触发器 | 说明 |
| --- | --- |
| 请求 |在收到 HTTP 请求时发生 |

## <a name="response-action"></a>响应操作
下面是此连接器支持的操作的详细信息。 存在只能在附带请求触发器时使用的单个响应操作。

| 操作 | 说明 |
| --- | --- |
| 响应 |向相关 HTTP 请求返回响应 |

### <a name="trigger-and-action-details"></a>触发器和操作详细信息
下表介绍触发器和操作的输入字段以及对应的输出详细信息。

#### <a name="request-trigger"></a>请求触发器
下面是来自传入 HTTP 请求的触发器的输入字段。

| 显示名称 | 属性名称 | 说明 |
| --- | --- | --- |
| JSON 架构 |schema |HTTP 请求正文的 JSON 架构 |

<br>

**输出详细信息**

下面是请求的输出详细信息。

| 属性名称 | 数据类型 | 说明 |
| --- | --- | --- |
| 标头 |对象 |请求标头 |
| 正文 |对象 |请求对象 |

#### <a name="response-action"></a>响应操作
下面是 HTTP 请求操作的输入字段。 * 表示它是必填字段。

| 显示名称 | 属性名称 | 说明 |
| --- | --- | --- |
| 状态代码* |statusCode |HTTP 状态代码 |
| 标头 |headers |要包含的任何响应标头的 JSON 对象 |
| 正文 |body |响应正文 |

## <a name="next-steps"></a>后续步骤
现在，试用平台并[创建逻辑应用](../app-service-logic/app-service-logic-create-a-logic-app.md)。 通过查看 [API 列表](apis-list.md)了解逻辑应用中的其他可用连接器。




<!--HONumber=Nov16_HO3-->


