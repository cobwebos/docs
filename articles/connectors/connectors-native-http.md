---
title: 调用 HTTP 和 HTTPS 终结点-Azure 逻辑应用
description: 使用 Azure 逻辑应用将传出请求发送到 HTTP 和 HTTPS 终结点
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: 4f585778808b133fe5c731ab6a3189594cbfc0f9
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72804228"
---
# <a name="send-outgoing-calls-to-http-or-https-endpoints-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用发送对 HTTP 或 HTTPS 终结点的传出调用

通过[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和内置 HTTP 触发器或操作，你可以创建定期将请求发送到任何 HTTP 或 HTTPS 终结点的自动化任务和工作流。 若要接收和响应传入的 HTTP 或 HTTPS 调用，请使用内置[请求触发器或响应操作](../connectors/connectors-native-reqres.md)。

例如，可以通过按指定计划检查终结点来监视网站的服务终结点。 当某个事件发生在该终结点（如网站关闭）时，该事件会触发逻辑应用的工作流，并运行指定的操作。

若要按定期计划检查或*轮询*终结点，可以使用 HTTP 触发器作为工作流的第一步。 每次检查时，该触发器会向该终结点发送调用或请求。 该终结点的响应确定了逻辑应用的工作流是否运行。 触发器将响应中的任何内容传递到逻辑应用中的操作。

可将 HTTP 操作用作工作流中的另一个步骤，用于调用所需的终结点。 该终结点的响应确定了工作流剩余操作的运行方式。

根据目标终结点的功能，HTTP 连接器支持传输层安全（TLS）版本1.0、1.1 和1.2。 逻辑应用与端点协商，使用的支持的最高版本。 例如，如果端点支持1.2，连接器将首先使用1.2。 否则，连接器将使用下一个支持的最高版本。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 要调用的目标终结点的 URL

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识。 如果你不熟悉逻辑应用，请参阅[什么是 Azure 逻辑应用](../logic-apps/logic-apps-overview.md)？

* 要从中调用目标终结点的逻辑应用。 若要开始使用 HTTP 触发器，请[创建一个空白逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 HTTP 操作，请使用所需的任何触发器启动逻辑应用。 此示例使用 HTTP 触发器作为第一步。

## <a name="add-an-http-trigger"></a>添加 HTTP 触发器

此内置触发器对终结点的指定 URL 进行 HTTP 调用并返回响应。

1. 登录到 [Azure 门户](https://portal.azure.com)。 在逻辑应用设计器中打开空白逻辑应用。

1. 在 "**选择操作**" 下的 "搜索" 框中，输入 "http" 作为筛选器。 从 "**触发器**" 列表中，选择**HTTP**触发器。

   ![选择 HTTP 触发器](./media/connectors-native-http/select-http-trigger.png)

   此示例将触发器重命名为 "HTTP 触发器"，以使该步骤具有更具描述性的名称。 此外，该示例稍后会添加 HTTP 操作，并且这两个名称必须是唯一的。

1. 提供要包含在对目标终结点的调用中的[HTTP 触发器参数](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger)的值。 为希望触发器检查目标终结点的频率设置重复周期。

   如果选择 "**无**" 以外的身份验证类型，则身份验证设置会根据你的选择而有所不同。 有关详细信息，请参阅[将身份验证添加到出站调用](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)。

   ![输入 HTTP 触发器参数](./media/connectors-native-http/http-trigger-parameters.png)

1. 若要添加其他可用参数，请打开 "**添加新参数**" 列表，然后选择所需的参数。

1. 继续使用触发器激发时运行的操作生成逻辑应用的工作流。

1. 完成后，请记得保存逻辑应用。 在设计器工具栏上，选择“保存”。

## <a name="add-an-http-action"></a>添加 HTTP 操作

此内置操作对终结点的指定 URL 进行 HTTP 调用并返回响应。

1. 登录到 [Azure 门户](https://portal.azure.com)。 在逻辑应用设计器中打开逻辑应用。

   此示例使用 HTTP 触发器作为第一步。

1. 在要添加 HTTP 操作的步骤下，选择 "**新建步骤**"。

   若要在步骤之间添加操作，请将鼠标指针移到步骤之间的箭头上。 选择出现的加号（ **+** ），然后选择 "**添加操作**"。

1. 在 "**选择操作**" 下的 "搜索" 框中，输入 "http" 作为筛选器。 从 "**操作**" 列表中，选择**HTTP**操作。

   ![选择“HTTP”操作](./media/connectors-native-http/select-http-action.png)

   此示例将操作重命名为 "HTTP action"，以使该步骤具有更具描述性的名称。

1. 提供要包含在对目标终结点的调用中的[HTTP 操作参数](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action)的值。

   如果选择 "**无**" 以外的身份验证类型，则身份验证设置会根据你的选择而有所不同。 有关详细信息，请参阅[将身份验证添加到出站调用](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)。

   ![输入 HTTP 操作参数](./media/connectors-native-http/http-action-parameters.png)

1. 若要添加其他可用参数，请打开 "**添加新参数**" 列表，然后选择所需的参数。

1. 完成后，请记得保存逻辑应用。 在设计器工具栏上，选择“保存”。

## <a name="content-with-multipartform-data-type"></a>具有多部分/窗体数据类型的内容

若要处理 `multipart/form-data` 类型在 HTTP 请求中的内容，可以使用此格式向 HTTP 请求的正文添加包含 `$content-type` 和 `$multipart` 属性的 JSON 对象。

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

例如，假设有一个逻辑应用，该应用使用该站点的 API （支持 `multipart/form-data` 类型）将 HTTP POST 请求发送到网站。 下面是此操作的可能外观：

![多部分窗体数据](./media/connectors-native-http/http-action-multipart.png)

下面是在基础工作流定义中显示 HTTP 操作的 JSON 定义的示例：

```json
{
   "HTTP_action": {
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

## <a name="connector-reference"></a>连接器参考

有关触发器和操作参数的详细信息，请参阅以下部分：

* [HTTP 触发器参数](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger)
* [HTTP 操作参数](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action)

### <a name="output-details"></a>输出详细信息

下面是有关 HTTP 触发器或操作的输出的详细信息，返回以下信息：

| 属性名称 | Type | 描述 |
|---------------|------|-------------|
| headers | 对象 | 请求中的标头 |
| body | 对象 | JSON 对象 | 具有来自请求的正文内容的对象 |
| 状态代码 | int | 请求的状态代码 |
|||

| 状态代码 | 描述 |
|-------------|-------------|
| 200 | 确定 |
| 202 | 已接受 |
| 400 | 错误的请求 |
| 401 | 未授权 |
| 403 | 禁止 |
| 404 | 未找到 |
| 500 | 内部服务器错误。 发生未知错误。 |
|||

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
