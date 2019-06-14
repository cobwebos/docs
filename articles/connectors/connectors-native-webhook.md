---
title: 创建基于事件的工作流或操作 - Azure 逻辑应用 | Microsoft Docs
description: 使用 Webhook 和 Azure 逻辑应用自动执行基于事件的工作流或操作
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.assetid: 71775384-6c3a-482c-a484-6624cbe4fcc7
ms.topic: article
tags: connectors
ms.date: 07/21/2016
ms.openlocfilehash: c3047000843e054e71ec1a80313118a25e7c4905
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60447179"
---
# <a name="create-event-based-workflows-or-actions-by-using-webhooks-and-azure-logic-apps"></a>使用 Webhook 和 Azure 逻辑应用创建基于事件的工作流或操作

借助 Webhook 操作和触发器，可启动、暂停和恢复流以执行以下任务：

* 收到项目时从 [Azure 事件中心](https://github.com/logicappsio/EventHubAPI)触发
* 在继续工作流之前等待批准

深入了解[如何创建支持 webhook 的自定义 API](../logic-apps/logic-apps-create-api-app.md)。

## <a name="use-the-webhook-trigger"></a>使用 Webhook 触发器

[触发器  ](../connectors/apis-list.md)是一个会启动逻辑应用工作流的事件。 Webhook 触发器是基于事件的这不依赖于轮询新项目。 当使用 webhook 触发器保存逻辑应用或逻辑应用从禁用到更改时启用 webhook 触发器*订阅*到指定的服务或通过注册的终结点*回调 URL*与该服务或终结点。 然后，该触发器使用该 URL 来根据需要运行逻辑应用。 像[请求触发器](connectors-native-reqres.md)，立即预期的事件发生时，会触发逻辑应用。 触发器*取消订阅*如果删除的触发器并保存逻辑应用，或者当您更改为已禁用已启用从逻辑应用。

以下示例演示如何在逻辑应用设计器中设置 HTTP 触发器。 这些步骤假设之前已部署或正在访问遵循[逻辑应用中的 webhook 订阅和取消订阅模式](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)的 API。 

**添加 Webhook 触发器**

1. 作为逻辑应用中的第一步，添加 **HTTP Webhook** 触发器。
2. 为 webhook 订阅和取消订阅调用填写参数。

   此步骤遵循与 [HTTP 操作](connectors-native-http.md)格式相同的模式。

     ![HTTP 触发器](./media/connectors-native-webhook/using-trigger.png)

3. 至少添加一个操作。
4. 单击“保存”  以发布逻辑应用。 此步骤将使用触发此逻辑应用所需的回调 URL 调用订阅终结点。
5. 每当服务向回调 URL 发送 `HTTP POST` 请求时，逻辑应用都会触发，并包含传递到请求中的任何数据。

## <a name="use-the-webhook-action"></a>使用 Webhook 操作

[*操作*](../connectors/apis-list.md)是一种定义操作和逻辑应用的工作流运行。 逻辑应用的 webhook 操作，该操作的运行时*订阅*到指定的服务或通过注册的终结点*回调 URL*与该服务或终结点。 Webhook 操作然后等待，直到服务调用逻辑应用继续运行之前的 URL。 逻辑应用从服务或终结点在这些情况下取消订阅： 

* Webhook 操作成功完成
* 如果等待响应时取消逻辑应用运行
* 之前的逻辑应用超时

例如， [**发送审批电子邮件**](connectors-create-api-office365-outlook.md)操作是采用这种模式的 webhook 操作的示例。 可通过 Webhook 操作将此模式扩展到任何服务中。 

以下示例演示如何在逻辑应用设计器中设置 webhook 操作。 这些步骤假设之前已部署或正在访问遵循[逻辑应用中使用的 webhook 订阅和取消订阅模式](../logic-apps/logic-apps-create-api-app.md#webhook-actions)的 API。 

**添加 webhook 操作**

1. 选择“新步骤”   > “添加操作”  。

2. 在搜索框中，键入“webhook”，查找 **HTTP Webhook** 操作。

    ![选择查询操作](./media/connectors-native-webhook/using-action-1.png)

3. 为 Webhook 订阅和取消订阅调用填写参数

   此步骤遵循与 [HTTP 操作](connectors-native-http.md)格式相同的模式。

     ![完成查询操作](./media/connectors-native-webhook/using-action-2.png)
   
   在运行时，逻辑应用在进行到该步骤后调用订阅终结点。

4. 单击“保存”  以发布逻辑应用。

## <a name="technical-details"></a>技术详细信息

以下是有关 webhook 支持的触发器和操作的详细信息。

## <a name="webhook-triggers"></a>Webhook 触发器

| 操作 | 描述 |
| --- | --- |
| HTTP Webhook |将回调 URL 订阅到可调用该 URL 的服务以按需触发逻辑应用。 |

### <a name="trigger-details"></a>触发器详细信息

#### <a name="http-webhook"></a>HTTP Webhook

将回调 URL 订阅到可调用该 URL 的服务以按需触发逻辑应用。
\* 表示必填字段。

| 显示名称 | 属性名 | 描述 |
| --- | --- | --- |
| 订阅方法* |method |要用于订阅请求的 HTTP 方法 |
| 订阅 URI* |uri |要用于订阅请求的 HTTP URI |
| 取消订阅方法* |method |要用于取消订阅请求的 HTTP 方法 |
| 取消订阅 URI* |uri |要用于取消订阅请求的 HTTP URI |
| 订阅正文 |body |订阅的 HTTP 请求正文 |
| 订阅标头 |headers |订阅的 HTTP 请求标头 |
| 订阅身份验证 |authentication |要用于订阅的 HTTP 身份验证。 有关详细信息，[请参阅 HTTP 连接器](connectors-native-http.md#authentication) |
| 取消订阅正文 |body |取消订阅的 HTTP 请求正文 |
| 取消订阅标头 |headers |取消订阅的 HTTP 请求标头 |
| 取消订阅身份验证 |authentication |要用于取消订阅的 HTTP 身份验证。 有关详细信息，[请参阅 HTTP 连接器](connectors-native-http.md#authentication) |

**输出详细信息**

Webhook 请求

| 属性名 | 数据类型 | 描述 |
| --- | --- | --- |
| 标头 |对象 |Webhook 请求标头 |
| 正文 |对象 |Webhook 请求对象 |
| 状态代码 |int |Webhook 请求状态代码 |

## <a name="webhook-actions"></a>Webhook 操作

| 操作 | 描述 |
| --- | --- |
| HTTP Webhook |将回调 URL 订阅到可调用该 URL 的服务以按需恢复工作流步骤。 |

### <a name="action-details"></a>操作详细信息

#### <a name="http-webhook"></a>HTTP Webhook

将回调 URL 订阅到可调用该 URL 的服务以按需恢复工作流步骤。
\* 表示必填字段。

| 显示名称 | 属性名 | 描述 |
| --- | --- | --- |
| 订阅方法* |method |要用于订阅请求的 HTTP 方法 |
| 订阅 URI* |uri |要用于订阅请求的 HTTP URI |
| 取消订阅方法* |method |要用于取消订阅请求的 HTTP 方法 |
| 取消订阅 URI* |uri |要用于取消订阅请求的 HTTP URI |
| 订阅正文 |body |订阅的 HTTP 请求正文 |
| 订阅标头 |headers |订阅的 HTTP 请求标头 |
| 订阅身份验证 |authentication |要用于订阅的 HTTP 身份验证。 有关详细信息，[请参阅 HTTP 连接器](connectors-native-http.md#authentication) |
| 取消订阅正文 |body |取消订阅的 HTTP 请求正文 |
| 取消订阅标头 |headers |取消订阅的 HTTP 请求标头 |
| 取消订阅身份验证 |authentication |要用于取消订阅的 HTTP 身份验证。 有关详细信息，[请参阅 HTTP 连接器](connectors-native-http.md#authentication) |

**输出详细信息**

Webhook 请求

| 属性名 | 数据类型 | 描述 |
| --- | --- | --- |
| 标头 |对象 |Webhook 请求标头 |
| 正文 |对象 |Webhook 请求对象 |
| 状态代码 |int |Webhook 请求状态代码 |

## <a name="next-steps"></a>后续步骤

* [创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [查找其他连接器](apis-list.md)
