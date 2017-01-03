
---
title: "在逻辑应用中添加 HTTP + Swagger 操作 | Microsoft 文档"
description: "HTTP + Swagger 操作和操作的概述"
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: eccfd87c-c5fe-4cf7-b564-9752775fd667
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 04b081d265c53ddcf77c79bf604296a0d44be4ed


---
# <a name="get-started-with-the-http--swagger-action"></a>HTTP + Swagger 操作入门
借助 HTTP + Swagger 操作，可以通过 [Swagger 文档](https://swagger.io)向任意 REST 终结点创建一流连接器。 还可以扩展逻辑应用以调用带有一流逻辑应用设计器体验的任何 REST 终结点。

若要开始在逻辑应用中使用 HTTP + Swagger 操作，请参阅[创建新逻辑应用](../app-service-logic/app-service-logic-create-a-logic-app.md)。

- - -
## <a name="use-http--swagger-as-a-trigger-or-an-action"></a>将 HTTP + Swagger 用作触发器或操作
HTTP + Swagger 触发器和操作与 [HTTP 操作](connectors-native-http.md)功能相同，但通过在设计器中显示来自 [Swagger 元数据](https://swagger.io)的 API 和输出形状提供更好的设计体验。 此外，可以将 HTTP + Swagger 用作触发器。 如果要实现轮询触发器，它应遵循[创建用于逻辑应用的自定义 API](../app-service-logic/app-service-logic-create-api-app.md#polling-triggers) 中所述的轮询模式。

[了解有关逻辑应用触发器和操作的详细信息。](connectors-overview.md)

以下示例演示如何在逻辑应用中将 HTTP + Swagger 操作用作工作流中的操作。

1. 选择“新步骤”按钮。
2. 选择“添加操作”。
3. 在操作搜索框中，键入“swagger”列出 HTTP + Swagger 操作。
   
    ![选择 HTTP + Swagger 操作](./media/connectors-native-http-swagger/using-action-1.png)
4. 键入 Swagger 文档的 URL：
   
   * 若要从逻辑应用设计工作，URL 必须是 HTTPS 终结点并已启用 CORS。
   * 如果 Swagger 文档不满足此要求，可以使用[启用 CORS 的 Azure 存储](#hosting-swagger-from-storage)存储该文档。
5. 单击“下一步”从 Swagger 文档读取并呈现。
6. 添加 HTTP 调用所需的任何参数。
   
    ![完成 HTTP 操作](./media/connectors-native-http-swagger/using-action-2.png)
7. 单击工具栏左上角的“保存”，逻辑应用将保存并发布（激活）。

### <a name="host-swagger-from-azure-storage"></a>从 Azure 存储托管 Swagger
可能要引用未托管或者不满足设计器的安全和跨源要求的 Swagger 文档。 若要解决此问题，可以将 Swagger 文档存储在 Azure 存储中，并使 CORS 引用该文档。  

下面是在 Azure 存储中创建、配置和存储 Swagger 文档的步骤：

1. [使用 Azure Blob 存储中创建 Azure 存储帐户](../storage/storage-create-storage-account.md)。 （若要执行此操作，请将权限设置为**公共访问**。）
2. 在 blob 上启用 CORS。 可使用[此 PowerShell 脚本](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1)自动配置该设置。
3. 将 Swagger 文件上传到 blob 中。 可通过 [Azure 门户](https://portal.azure.com)或 [Azure 存储资源管理器](http://storageexplorer.com/)之类的工具执行此操作。
4. 在 Azure Blob 存储中引用文档的 HTTPS 链接。 （该连接采用格式 `https://*storageAccountName*.blob.core.windows.net/*container*/*filename*`。）

## <a name="technical-details"></a>技术详细信息
下面是此 HTTP + Swagger 连接器支持的触发器和操作的详细信息。

## <a name="http--swagger-triggers"></a>HTTP + Swagger 触发器
触发器是用于启动在逻辑应用中定义的工作流的事件。 [了解有关触发器的详细信息](connectors-overview.md)。 HTTP + Swagger 连接器具有一个触发器。

| 触发器 | 说明 |
| --- | --- |
| HTTP + Swagger |进行 HTTP 调用并返回响应内容 |

## <a name="http--swagger-actions"></a>HTTP + Swagger 操作
操作是由在逻辑应用中定义的工作流执行的操作。 [了解有关操作的详细信息。](connectors-overview.md) HTTP + Swagger 连接器具有一个可能的操作。

| 操作 | 说明 |
| --- | --- |
| HTTP + Swagger |进行 HTTP 调用并返回响应内容 |

### <a name="action-details"></a>操作详细信息
HTTP + Swagger 连接器附带一个可能的操作。 下面是每个操作的相关信息、其必填和选填输入字段以及与其用途相关联的对应输出详细信息。

#### <a name="http--swagger"></a>HTTP + Swagger
在 Swagger 元数据的协助下发出 HTTP 出站请求。
星号 (*) 表示必填字段。

| 显示名称 | 属性名称 | 说明 |
| --- | --- | --- |
| 方法* |方法 |要使用的 HTTP 谓词。 |
| URI* |uri |HTTP 请求的 URI。 |
| 标头 |headers |要包含的 HTTP 标头的 JSON 对象。 |
| 正文 |body |HTTP 请求正文。 |
| 身份验证 |authentication |要用于请求的身份验证。 [有关详细信息，请参阅 HTTP](connectors-native-http.md#authentication)。 |

**输出详细信息**

HTTP 响应

| 属性名称 | 数据类型 | 说明 |
| --- | --- | --- |
| 标头 |对象 |响应标头 |
| 正文 |对象 |响应对象 |
| 状态代码 |int |HTTP 状态代码 |

### <a name="http-responses"></a>HTTP 响应
对各种操作进行调用时，可能得到特定响应。 下表概述对应的响应和说明。

| Name | 说明 |
| --- | --- |
| 200 |确定 |
| 202 |已接受 |
| 400 |错误的请求 |
| 401 |未授权 |
| 403 |禁止 |
| 404 |未找到 |
| 500 |内部服务器错误。 发生未知错误。 |

- - -
## <a name="next-steps"></a>后续步骤
现在试用平台并[创建逻辑应用](../app-service-logic/app-service-logic-create-a-logic-app.md)。 通过查看 [API 列表](apis-list.md)了解逻辑应用中的其他可用连接器。




<!--HONumber=Dec16_HO5-->


