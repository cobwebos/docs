---
title: "使用 Postman 描述自定义 API 和连接器 - Azure 逻辑应用 | Microsoft Docs"
description: "创建 Postman 集合用于描述、分组和组织自定义 API 与连接器"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 072d544e5d29c4abb3d69651e53cfb33d5e0c9e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="describe-custom-apis-and-custom-connectors-with-postman"></a>使用 Postman 描述自定义 API 和自定义连接器

若要更快、更轻松地开发[自定义 API](../logic-apps/logic-apps-create-api-app.md) 和[自定义连接器](../logic-apps/custom-connector-overview.md)，可以创建 [Postman](https://www.getpostman.com/) 集合（而不是 OpenAPI 文档）用于描述 API 和连接器。 Postman 集合可帮助组织和分组相关的 API 请求。 例如，在创建 Azure 逻辑应用、Microsoft Flow 或 Microsoft PowerApps 的连接器时，可以使用 Postman。 

本教程以 [Azure 认知服务文本分析](https://azure.microsoft.com/services/cognitive-services/text-analytics/)中的[检测语言 API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) 为例，介绍如何创建 [Postman 集合](https://www.getpostman.com/docs/postman/collections/creating_collections)。 此 API 可识别在文本中传递给 API 的语言、情绪和关键短语。

## <a name="prerequisites"></a>先决条件

* Postman 初学者请[安装 Postman 应用](https://www.getpostman.com/apps)。

* Azure 订阅。 如果没有订阅，可以从[免费的 Azure 帐户](https://azure.microsoft.com/free/)着手。 或者，也可注册[即用即付订阅](https://azure.microsoft.com/pricing/purchase-options/)。

* 如果有 Azure 订阅，请完成[此处所述的任务 1](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-signup.md) 来注册文本分析 API。 

## <a name="create-a-postman-collection"></a>创建 Postman 集合

在创建集合之前，请先为 API 终结点创建一个 HTTP 请求。 

### <a name="create-an-http-request-for-your-api"></a>为 API 创建 HTTP 请求

1. 打开 Postman 应用，以便可为 API 终结点创建 [HTTP 请求](https://www.getpostman.com/docs/postman/sending_api_requests/requests)。 有关详细信息，请参阅 Postman 的[请求文档](https://www.getpostman.com/docs/postman/sending_api_requests/requests)。

   1. 在“生成器”选项卡上，选择 HTTP 方法，输入 API 终结点的请求 URL，并选择授权协议（如果有）。 
   准备就绪后，选择“参数”。

      在本教程中，可以使用以下示例中的设置：

      ![创建请求：“HTTP 方法”、“请求 URL”、“授权”](./media/custom-connector-api-postman-collection/01-create-api-http-request.png)

      | 参数 | 建议的值 | 
      | --------- | --------------- | 
      | **HTTP 方法** | POST | 
      | **请求 URL** | `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages` | | 
      | **授权** | “无身份验证” | | 
      ||| 

   2. 现在可以输入键值对，用作请求 URL 中的查询或路径参数。 Postman 会将这些项合并成为一个查询字符串。
   完成后，选择“标头”。

      ![继续创建请求：“参数”](./media/custom-connector-api-postman-collection/02-create-api-http-request-params.png)

      | 参数 | 建议的值 | 
      | --------- | --------------- | 
      | **参数** | **键**：“numberOfLanguagesToDetect” </br>**值**：“1” | 
      ||| 

   3. 输入请求标头的键值对。 
   对于标头名称，请输入所需的任何字符串。 对于常见的 HTTP 标头，可从下拉列表中选择。 完成后，选择“正文”。 
   
      ![继续创建请求：“标头”](./media/custom-connector-api-postman-collection/03-create-api-http-request-header.png)

      | 参数 | 值 | 
      | --------- | ----- | 
      | **标头** | **键**：“Ocp-Apim-Subscription-Key” </br>**值**：*your-API-subscription-key*，可在认知服务帐户中找到 <p>**键**：“Content-Type” </br> **值**：“application/json” | 
      ||| 

   4. 输入想要在请求正文中发送的内容。 
   若要通过获取响应来检查请求是否正常工作，请选择“发送”。 
   
      ![继续创建请求：“正文”](./media/custom-connector-api-postman-collection/04-create-api-http-request-body.png)

      | 参数 | 建议的值 | 
      | --------- | --------------- |    
      | **正文** | ```{"documents": [{ "id": "1", "text": "Hello World"}]}``` | 
      ||| 

      响应字段包含来自 API 的完整响应，其中包括结果，如果发生了任何错误，则还包括错误。

      ![获取请求响应](./media/custom-connector-api-postman-collection/05-create-api-http-request-response.png)

2. 经检查请求可正常工作后，请将请求保存到 Postman 集合。 

   1. 选择“保存”。 
      
      ![选择“保存”](./media/custom-connector-api-postman-collection/06a-save-request.png)
 
   2. 在“保存请求”下，提供“请求名称”，并选择性地提供“请求说明”。 

      > [!NOTE]
      > 自定义连接器稍后会使用这些值。 因此，请确保提供与稍后要用于自定义 API 操作摘要和说明相同的值。

   3. 选择“+ 创建集合”并提供集合名称。 
   选中创建集合文件夹对应的复选框，并选择“保存到 <Postman 集合名称>”。

      ![保存请求](./media/custom-connector-api-postman-collection/06b-save-request.png)

### <a name="save-the-request-response"></a>保存请求响应

保存请求后，可以保存响应。 这样，稍后在加载请求时，响应会显示为示例。

1. 在响应窗口的上方，选择“保存响应”。 

   ![保存响应](./media/custom-connector-api-postman-collection/07-create-api-http-request-save-response.png)

   自定义连接器仅支持每个请求的一个响应。 
   如果为每个请求保存了多个响应，只会使用第一个响应。

2. 为示例提供名称，选择“保存示例”。

3. 使用任何其他请求和响应继续生成 Postman 集合。

### <a name="export-your-postman-collection"></a>导出 Postman 集合

1. 完成后，将集合导出到 JSON 文件。

   ![导出集合](./media/custom-connector-api-postman-collection/08-export-http-request.png)

2. 选择“集合 v1”导出格式，浏览到要将 JSON 文件保存到的位置。

   > [!NOTE]
   > 目前，V1 仅适用于自定义连接器。

   ![选择导出格式：“集合 v1”](./media/custom-connector-api-postman-collection/09-export-format.png)
   
现在，可以使用此 Postman 集合来创建自定义 API 和连接器。 导出集合后，可将该 JSON 文件导入逻辑应用、Flow 或 PowerApps。

> [!IMPORTANT]
> 如果从 Postman 集合创建自定义连接器，请确保从操作和触发器中删除 `Content-type` 标头。 目标服务（例如 Flow）会自动添加此标头。 另外，请从操作和触发器中删除 `securityDefintions` 节中的身份验证标头。

## <a name="next-steps"></a>后续步骤

* [逻辑应用：注册自定义连接器](../logic-apps/logic-apps-custom-connector-register.md)
* [Flow：注册连接器](https://ms.flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector)
* [PowerApps：注册连接器](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector)
* [自定义连接器常见问题解答](../logic-apps/custom-connector-faq.md)
