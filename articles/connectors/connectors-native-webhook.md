---
title: "逻辑应用 webhook 连接器 | Microsoft Docs"
description: "用于执行“筛选数组”等操作的 Webhook 操作和触发器概述。"
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: 71775384-6c3a-482c-a484-6624cbe4fcc7
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/21/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: c0edbe421050ad46f6d31fd6416df4b344b233ad
ms.openlocfilehash: 2c24699f6253fc7952b331ebcce4b937f1c83603
ms.lasthandoff: 01/20/2017


---
# <a name="get-started-with-the-webhook-connector"></a>Webhook 连接器入门
借助 Webhook 操作和触发器，可触发、暂停和恢复流以完成：

* 收到项目时立即从 [Azure Event Hub](https://github.com/logicappsio/EventHubAPI) 触发
* 在继续工作流之前等待批准

有关创建支持 Webhook 订阅的 API 的信息可在[有关创建逻辑应用连接器的本文中](../logic-apps/logic-apps-create-api-app.md)找到。

- - -
## <a name="use-the-webhook-trigger"></a>使用 Webhook 触发器
触发器是用于启动在逻辑应用中定义的工作流的事件。 [了解有关触发器的详细信息](connectors-overview.md)。  Webhook 触发器尤其有用，因为它不依赖于轮询新项目，和[请求触发器](connectors-native-reqres.md)一样，逻辑应用将在发生事件时立即触发。  这通过将一个*回调 URL* 注册到可用于按需触发逻辑应用的服务来实现。

下面是如何在逻辑应用设计器中设置 HTTP 触发器的示例序列。  这假设之前已部署或正在访问遵循[逻辑应用中使用的 Webhook 订阅和取消订阅模式](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)的 API。  每当使用新 Webhook 保存逻辑应用或将逻辑应用从禁用切换到启用时，都进行订阅调用。  每当删除和保存逻辑应用或将逻辑应用从启用切换到禁用时，都进行取消订阅调用。

1. 添加 **HTTP Webhook** 触发器作为逻辑应用中的第一步
2. 为 Webhook 订阅和取消订阅调用填写参数

   * 这遵循与 [HTTP 操作](connectors-native-http.md)格式相同的模式

     ![HTTP 触发器](./media/connectors-native-webhook/using-trigger.png)
3. 添加至少一个操作
4. 单击保存发布逻辑应用，这将使用触发此逻辑应用所需的回调 URL 调用订阅终结点
5. 每当服务对回调 URL 进行 `HTTP POST` 时，逻辑应用都将触发（并包含请求中传递的任何数据）

## <a name="use-the-webhook-action"></a>使用 Webhook 操作
操作是指在逻辑应用中定义的由工作流执行的操作。 [了解有关操作的详细信息。](connectors-overview.md)  Webhook 操作尤其有用，因为它将*调用 URL* 注册到服务，并在恢复前等待该 URL 被调用。  [“发送批准电子邮件”](connectors-create-api-office365-outlook.md)是遵循此模式的连接器示例。  可通过 Webhook 操作将此模式扩展到任何服务中。  这假设之前已部署或正在访问遵循[逻辑应用中使用的 Webhook 订阅和取消订阅模式](../logic-apps/logic-apps-create-api-app.md#webhook-actions)的 API。  每当逻辑应用执行 Webhook 操作时，都进行订阅调用。  每当在等待响应时或在逻辑应用运行超时之前取消运行时，都进行取消订阅调用。

若要添加 Webhook 操作：

1. 选择“新步骤”按钮
2. 选择“添加操作”
3. 在操作搜索框中，键入“Webhook”列出 **HTTP Webhook** 操作

    ![选择查询操作](./media/connectors-native-webhook/using-action-1.png)
4. 为 Webhook 订阅和取消订阅调用填写参数

   * 这遵循与 [HTTP 操作](connectors-native-http.md)格式相同的模式

     ![完成查询操作](./media/connectors-native-webhook/using-action-2.png)
   * 在运行时，逻辑应用将在到达步骤后调用订阅终结点
5. 单击工具栏左上角的“保存”，逻辑应用将保存并发布（激活）

- - -
## <a name="technical-details"></a>技术详细信息
下面是 Webhook 支持的触发器和操作的详细信息。

## <a name="webhook-triggers"></a>Webhook 触发器
触发器是用于启动工作流的操作。 [了解有关触发器的详细信息](connectors-overview.md)。 此连接器具有 1 个触发器。

| 操作 | 说明 |
| --- | --- |
| HTTP Webhook |将回调 URL 订阅到可调用该 URL 的服务以按需触发逻辑应用。 |

### <a name="trigger-details"></a>触发器详细信息
Webhook 连接器附带 1 个可能的触发器。 下面是该操作的相关信息、其必填和选填输入字段以及与其用途相关联的对应输出详细信息。

#### <a name="http-webhook"></a>HTTP Webhook
将回调 URL 订阅到可调用该 URL 的服务以按需触发逻辑应用。
* 表示必填字段。

| 显示名称 | 属性名称 | 说明 |
| --- | --- | --- |
| 订阅方法* |method |要用于订阅请求的 HTTP 方法 |
| 订阅 URI* |uri |要用于订阅请求的 HTTP URI |
| 取消订阅方法* |method |要用于取消订阅请求的 HTTP 方法 |
| 取消订阅 URI* |uri |要用于取消订阅请求的 HTTP URI |
| 订阅正文 |body |订阅的 HTTP 请求正文 |
| 订阅标头 |headers |订阅的 HTTP 请求标头 |
| 订阅身份验证 |身份验证 |要用于订阅的 HTTP 身份验证。 有关详细信息，[请参阅 HTTP 连接器](connectors-native-http.md#authentication) |
| 取消订阅正文 |body |取消订阅的 HTTP 请求正文 |
| 取消订阅标头 |headers |取消订阅的 HTTP 请求标头 |
| 取消订阅身份验证 |authentication |要用于取消订阅的 HTTP 身份验证。 有关详细信息，[请参阅 HTTP 连接器](connectors-native-http.md#authentication) |

<br>

**输出详细信息**

Webhook 请求

| 属性名称 | 数据类型 | 说明 |
| --- | --- | --- |
| 标头 |对象 |Webhook 请求标头 |
| 正文 |对象 |Webhook 请求对象 |
| 状态代码 |int |Webhook 请求状态代码 |

## <a name="webhook-actions"></a>Webhook 操作
操作是指在逻辑应用中定义的由工作流执行的操作。 [了解有关操作的详细信息。](connectors-overview.md) 连接器具有 1 个可能的操作。

| 操作 | 说明 |
| --- | --- |
| HTTP Webhook |将回调 URL 订阅到可调用该 URL 的服务以按需恢复工作流步骤。 |

### <a name="action-details"></a>操作详细信息
Webhook 连接器附带 1 个可能的操作。 下面有该操作的相关信息、其必填和选填输入字段以及与其用途相关联的对应输出详细信息。

#### <a name="http-webhook"></a>HTTP Webhook
将回调 URL 订阅到可调用该 URL 的服务以按需恢复工作流步骤。
* 表示必填字段。

| 显示名称 | 属性名称 | 说明 |
| --- | --- | --- |
| 订阅方法* |method |要用于订阅请求的 HTTP 方法 |
| 订阅 URI* |uri |要用于订阅请求的 HTTP URI |
| 取消订阅方法* |method |要用于取消订阅请求的 HTTP 方法 |
| 取消订阅 URI* |uri |要用于取消订阅请求的 HTTP URI |
| 订阅正文 |body |订阅的 HTTP 请求正文 |
| 订阅标头 |headers |订阅的 HTTP 请求标头 |
| 订阅身份验证 |身份验证 |要用于订阅的 HTTP 身份验证。 有关详细信息，[请参阅 HTTP 连接器](connectors-native-http.md#authentication) |
| 取消订阅正文 |body |取消订阅的 HTTP 请求正文 |
| 取消订阅标头 |headers |取消订阅的 HTTP 请求标头 |
| 取消订阅身份验证 |authentication |要用于取消订阅的 HTTP 身份验证。 有关详细信息，[请参阅 HTTP 连接器](connectors-native-http.md#authentication) |

<br>

**输出详细信息**

Webhook 请求

| 属性名称 | 数据类型 | 说明 |
| --- | --- | --- |
| 标头 |对象 |Webhook 请求标头 |
| 正文 |对象 |Webhook 请求对象 |
| 状态代码 |int |Webhook 请求状态代码 |

- - -
## <a name="next-steps"></a>后续步骤
下面是有关如何继续推进逻辑应用和我们的社区的详细信息。

## <a name="create-a-logic-app"></a>创建逻辑应用
现在试用平台并[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)。 通过查看 [API 列表](apis-list.md)了解逻辑应用中的其他可用连接器。

