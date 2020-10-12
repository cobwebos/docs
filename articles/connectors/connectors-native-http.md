---
title: 使用 HTTP 或 HTTPS 调用服务终结点
description: 从 Azure 逻辑应用向服务终结点发送出站 HTTP 或 HTTPS 请求
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 09/14/2020
tags: connectors
ms.openlocfilehash: f005bdfa5643ea187fb2973cac065563c4cc2ee6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91292449"
---
# <a name="call-service-endpoints-over-http-or-https-from-azure-logic-apps"></a>从 Azure 逻辑应用通过 HTTP 或 HTTPS 调用服务终结点

通过 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md) 和内置 HTTP 触发器或操作，你可以创建可通过 HTTP 或 HTTPS 将出站请求发送到其他服务和系统上的终结点的自动化任务和工作流。 若要接收和响应入站 HTTPS 调用，请使用内置 [请求触发器和响应操作](../connectors/connectors-native-reqres.md)。

例如，可以通过按特定计划检查终结点来监视网站的服务终结点。 当该终结点上发生特定的事件（例如网站关闭）时，该事件会触发逻辑应用的工作流并运行该工作流中的操作。

* 若要按定期计划检查或轮询某个终结点，可[添加 HTTP 触发器](#http-trigger)作为工作流中的第一个步骤。 每次触发器检查终结点时，触发器都会调用该终结点或向该终结点发送请求。 该终结点的响应确定了逻辑应用的工作流是否运行。 触发器将终结点响应中的任何内容传递到逻辑应用中的操作。

* 若要从工作流中的任何其他位置调用终结点，请[添加 HTTP 操作](#http-action)。 该终结点的响应确定了工作流剩余操作的运行方式。

本文介绍如何使用 HTTP 触发器和 HTTP 操作，以便逻辑应用可以将出站调用发送到其他服务和系统。

有关逻辑应用的出站调用的加密、安全和授权（例如 [传输层安全性 (TLS) ](https://en.wikipedia.org/wiki/Transport_Layer_Security)，以前称为安全套接字层 (SSL) 、自签名证书或 [Azure Active Directory 开放式身份验证 (Azure AD OAuth) ](../active-directory/develop/index.yml)），请参阅对 [其他服务和系统的出站调用的安全访问和数据访问](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests)。

## <a name="prerequisites"></a>先决条件

* Azure 帐户和订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 要调用的目标终结点的 URL

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识。 如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

* 要从中调用目标终结点的逻辑应用。 若要从 HTTP 触发器开始，请[创建一个空白逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 HTTP 操作，请使用所需的任何触发器启动逻辑应用。 此示例的第一步是使用 HTTP 触发器。

<a name="http-trigger"></a>

## <a name="add-an-http-trigger"></a>添加 HTTP 触发器

此内置触发器对终结点的指定 URL 发出 HTTP 调用，并返回响应。

1. 登录到 [Azure 门户](https://portal.azure.com)。 在逻辑应用设计器中打开空白逻辑应用。

1. 在设计器的搜索框下，选择“内置”。 在搜索框中，输入 `http` 作为筛选器。 在“触发器”列表中，选择“HTTP”触发器。 

   ![选择 HTTP 触发器](./media/connectors-native-http/select-http-trigger.png)

   此示例将触发器重命名为“HTTP trigger”，使步骤的名称更具描述性。 此外，该示例稍后会添加 HTTP 操作，因此，两个名称必须唯一。

1. 提供要包含在目标终结点调用中的 [HTTP 触发器参数](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)的值。 设置重复周期，以确定触发器检查目标终结点的频率。

   ![输入 HTTP 触发器参数](./media/connectors-native-http/http-trigger-parameters.png)

   如果选择的身份验证类型不是“None”，则身份验证设置将根据你的选择而有所不同。 有关 HTTP 可用的身份验证类型的详细信息，请参阅以下主题：

   * [向出站调用添加身份验证](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [使用托管标识对资源访问者进行身份验证](../logic-apps/create-managed-service-identity.md)

1. 若要添加其他可用参数，请打开“添加新参数”列表，并选择所需的参数。

1. 继续使用触发器激发时运行的操作生成逻辑应用的工作流。

1. 完成后，请记得保存逻辑应用。 在设计器工具栏上选择“保存”。

<a name="http-action"></a>

## <a name="add-an-http-action"></a>添加 HTTP 操作

此内置操作对终结点的指定 URL 发出 HTTP 调用，并返回响应。

1. 登录到 [Azure 门户](https://portal.azure.com)。 在逻辑应用设计器中打开逻辑应用。

   此示例的第一步是使用 HTTP 触发器。

1. 在要添加 HTTP 操作的步骤下，选择“新建步骤”。

   若要在步骤之间添加操作，请将鼠标指针移到步骤之间的箭头上。 选择出现的加号 ( **+** )，然后选择“添加操作”。

1. 在“选择操作”下，选择“内置”。  在搜索框中，输入 `http` 作为筛选器。 在“操作”列表中，选择“HTTP”操作。 

   ![选择“HTTP”操作](./media/connectors-native-http/select-http-action.png)

   此示例将操作重命名为“HTTP action”，使步骤的名称更具描述性。

1. 提供要包含在目标终结点调用中的 [HTTP 操作参数](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)的值。

   ![输入 HTTP 操作参数](./media/connectors-native-http/http-action-parameters.png)

   如果选择的身份验证类型不是“None”，则身份验证设置将根据你的选择而有所不同。 有关 HTTP 可用的身份验证类型的详细信息，请参阅以下主题：

   * [向出站调用添加身份验证](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [使用托管标识对资源访问者进行身份验证](../logic-apps/create-managed-service-identity.md)

1. 若要添加其他可用参数，请打开“添加新参数”列表，并选择所需的参数。

1. 完成后，请记得保存逻辑应用。 在设计器工具栏上选择“保存”。

## <a name="trigger-and-action-outputs"></a>触发器和操作输出

下面是有关 HTTP 触发器或操作的输出的详细信息，输出中将返回以下信息：

| 属性 | 类型 | 说明 |
|----------|------|-------------|
| `headers` | JSON 对象 | 请求中的标头 |
| `body` | JSON 对象 | 包含请求中正文内容的对象 |
| `status code` | Integer | 请求中的状态代码 |
|||

| 状态代码 | 说明 |
|-------------|-------------|
| 200 | 确定 |
| 202 | 已接受 |
| 400 | 错误的请求 |
| 401 | 未授权 |
| 403 | 禁止 |
| 404 | 未找到 |
| 500 | 内部服务器错误。 发生未知错误。 |
|||

## <a name="content-with-multipartform-data-type"></a>具有多部分/表单数据类型的内容

若要处理 HTTP 请求中具有 `multipart/form-data` 类型的内容，可以使用此格式向 HTTP 请求的正文添加包含 `$content-type` 和 `$multipart` 属性的 JSON 对象。

```json
"body": {
   "$content-type": "multipart/form-data",
   "$multipart": [
      {
         "body": "<output-from-trigger-or-previous-action>",
         "headers": {
            "Content-Disposition": "form-data; name=file; filename=<file-name>"
         }
      }
   ]
}
```

例如，假设你有一个逻辑应用，它使用该站点的 API（支持 `multipart/form-data` 类型）向网站发送对 Excel 文件的 HTTP POST 请求。 下面是此操作的可能外观：

![多部分表单数据](./media/connectors-native-http/http-action-multipart.png)

以下是在基础工作流定义中显示 HTTP 操作的 JSON 定义的同一示例：

```json
"HTTP_action": {
   "inputs": {
      "body": {
         "$content-type": "multipart/form-data",
         "$multipart": [
            {
               "body": "@trigger()",
               "headers": {
                  "Content-Disposition": "form-data; name=file; filename=myExcelFile.xlsx"
               }
            }
         ]
      },
      "method": "POST",
      "uri": "https://finance.contoso.com"
   },
   "runAfter": {},
   "type": "Http"
}
```

## <a name="content-with-applicationx-www-form-urlencoded-type"></a>包含应用程序的内容/x-url 编码类型

若要在 HTTP 请求的正文中提供 url 编码数据，必须指定数据具有 `application/x-www-form-urlencoded` 内容类型。 在 HTTP 触发器或操作中，添加 `content-type` 标头。 将标头值设置为 `application/x-www-form-urlencoded` 。

例如，假设有一个逻辑应用，该应用将 HTTP POST 请求发送到支持该类型的网站 `application/x-www-form-urlencoded` 。 下面是此操作的可能外观：

![显示 HTTP 请求并将 "content-type" 标头设置为 "application/x-url 编码" 的屏幕截图](./media/connectors-native-http/http-action-urlencoded.png)

<a name="asynchronous-pattern"></a>

## <a name="asynchronous-request-response-behavior"></a>异步请求-响应行为

默认情况下，Azure 逻辑应用中所有基于 HTTP 的操作都遵循标准[异步操作模式](/azure/architecture/patterns/async-request-reply)。 该模式指定在 HTTP 操作调用某个终结点、服务、系统或 API 或向其发送请求后，接收方立即返回[“202 已接受”](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.3)响应。 此代码确认接收方已接受请求，但尚未完成处理。 响应可以包括一个指定了 URL 和刷新 ID 的 `location` 标头，调用方可以使用该标头来轮询或检查异步请求的状态，直到接收方停止处理并返回[“200 正常”](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.1)成功响应或其他非 202 响应。 但是，调用方不必等待请求完成处理即可继续运行下一操作。 有关详细信息，请参阅[异步微服务集成强制实施微服务自治](/azure/architecture/microservices/design/interservice-communication#synchronous-versus-asynchronous-messaging)。

* 在逻辑应用设计器中，HTTP 操作（而不是触发器）有一个默认启用的**异步模式**设置。 此设置指定调用方不等待处理完成即可继续执行下一操作，但需继续检查状态直到处理停止。 如果禁用，则此设置指定调用方需等待处理完成才能继续执行下一操作。

  若要查找此设置，请执行以下步骤：

  1. 在 HTTP 操作的标题栏上，选择省略号 ( **...** ) 按钮，这将打开操作的设置。

  1. 找到“异步模式”设置。

     ![“异步模式”设置](./media/connectors-native-http/asynchronous-pattern-setting.png)

* HTTP 操作的基础 JavaScript 对象表示法 (JSON) 定义隐式遵循异步操作模式。

<a name="disable-asynchronous-operations"></a>

## <a name="disable-asynchronous-operations"></a>禁用异步操作

有时，你在特定场景下可能需要 HTTP 操作的异步行为，例如，当你希望实现以下目的时：

* [避免长时间运行的任务发生 HTTP 超时](#avoid-http-timeouts)
* [禁止检查位置标头](#disable-location-header-check)

<a name="turn-off-asynchronous-pattern-setting"></a>

### <a name="turn-off-asynchronous-pattern-setting"></a>关闭“异步模式”设置

1. 在逻辑应用设计器的 HTTP 操作的标题栏上，选择省略号 ( **...** ) 按钮，这将打开操作的设置。

1. 找到“异步模式”设置，关闭此设置（如果已启用），然后选择“完成”。

   ![禁用“异步模式”设置](./media/connectors-native-http/disable-asynchronous-pattern-setting.png)

<a name="add-disable-async-pattern-option"></a>

### <a name="disable-asynchronous-pattern-in-actions-json-definition"></a>在操作的 JSON 定义中禁用异步模式

在 HTTP 操作的基础 JSON 定义中，向操作的定义[添加 `"DisableAsyncPattern"` 操作选项](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options)，使操作改为遵循同步操作模式。 有关详细信息，另请参阅[在同步操作模式下运行操作](../logic-apps/logic-apps-workflow-actions-triggers.md#disable-asynchronous-pattern)。

<a name="avoid-http-timeouts"></a>

## <a name="avoid-http-timeouts-for-long-running-tasks"></a>避免长时间运行的任务发生 HTTP 超时

HTTP 请求有一个[超时限制](../logic-apps/logic-apps-limits-and-config.md#http-limits)。 如果有长时间运行的 HTTP 操作由于此限制而超时，则可使用以下选项：

* [禁用 HTTP 操作的异步操作模式](#disable-asynchronous-operations)，使该操作不会持续轮询或检查请求的状态， 而是等待接收方在请求完成处理后以状态和结果做出响应。

* 将 HTTP 操作替换为 [HTTP Webhook 操作](../connectors/connectors-native-webhook.md)，后者会等待接收方在请求完成处理后以状态和结果做出响应。

<a name="disable-location-header-check"></a>

## <a name="disable-checking-location-headers"></a>禁止检查位置标头

某些终结点、服务、系统或 API 会返回没有 `location` 标头的“202 已接受”响应。 若要避免 HTTP 操作在 `location` 标头不存在时不断检查请求状态，可以使用以下选项：

* [禁用 HTTP 操作的异步操作模式](#disable-asynchronous-operations)，使该操作不会持续轮询或检查请求的状态， 而是等待接收方在请求完成处理后以状态和结果做出响应。

* 将 HTTP 操作替换为 [HTTP Webhook 操作](../connectors/connectors-native-webhook.md)，后者会等待接收方在请求完成处理后以状态和结果做出响应。

## <a name="known-issues"></a>已知问题

<a name="omitted-headers"></a>

### <a name="omitted-http-headers"></a>忽略了 HTTP 标头

如果 HTTP 触发器或操作包含这些标头，则逻辑应用会从生成的请求消息中删除这些标头，且不显示任何警告或错误：

* `Accept-*` 标头除外 `Accept-version`
* `Allow`
* `Content-*` 中含以下例外：`Content-Disposition`、`Content-Encoding` 和 `Content-Type`
* `Cookie`
* `Expires`
* `Host`
* `Last-Modified`
* `Origin`
* `Set-Cookie`
* `Transfer-Encoding`

尽管逻辑应用不会阻止保存此类逻辑应用（使用具有这些标头的 HTTP 触发器或操作的逻辑应用），但逻辑应用会忽略这些标头。

## <a name="connector-reference"></a>连接器参考

有关触发器和操作参数的详细信息，请参阅以下部分：

* [HTTP 触发器参数](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)
* [HTTP 操作参数](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

## <a name="next-steps"></a>后续步骤

* [对其他服务和系统的出站调用进行安全访问和数据访问](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests)
* [适用于逻辑应用的连接器](../connectors/apis-list.md)
