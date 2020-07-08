---
title: 从 Azure 逻辑应用连接到 REST 终结点
description: 使用 Azure 逻辑应用监视自动化任务、流程和工作流中的 REST 终结点
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/01/2019
tags: connectors
ms.openlocfilehash: a5c00dc64dd39ba2fdbb734f4e9749fbe42e246e
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2020
ms.locfileid: "83831919"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用调用 REST 终结点

借助 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md) 和内置的 HTTP + Swagger 连接器，你可生成逻辑应用，对通过 [Swagger 文件](https://swagger.io)定期调用任何 REST 终结点的工作流进行自动化处理。 HTTP + Swagger 触发器和操作与 [HTTP 触发器和操作](connectors-native-http.md)的功能相同，但通过公开 Swagger 文件所述的 API 结构和输出，在逻辑应用设计器中提供更好的体验。 若要实现轮询触发器，请遵循[创建用于调用逻辑应用的其他 API、服务和系统的自定义 API](../logic-apps/logic-apps-create-api-app.md#polling-triggers) 中所述的轮询模式。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 描述目标 REST 终结点的 Swagger（非 OpenAPI）文件的 URL

  通常，REST 终结点必须满足此条件，连接器才能工作：

  * Swagger 文件必须托管在可公开访问的 HTTPS URL 上。

  * Swagger 文件必须启用[跨域资源共享 (CORS)](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)。

  若要引用未托管或不满足安全性和跨域要求的 Swagger 文件，可[将 Swagger 文件上传到 Azure 存储帐户中的 Blob 容器](#host-swagger)，并在该存储帐户上启用 CORS，以便可以引用该文件。

  本主题中的示例使用[认知服务人脸 API](https://docs.microsoft.com/azure/cognitive-services/face/overview)，这需要[认知服务帐户和访问密钥](../cognitive-services/cognitive-services-apis-create-account.md)。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识。 如果你不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

* 要从中调用目标终结点的逻辑应用。 要开始使用 HTTP + Swagger 触发器，请[创建空白的逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 要使用 HTTP + Swagger 操作，请使用所需的任意触发器启动逻辑应用。 此示例将 HTTP + Swagger 触发器用作第一步。

## <a name="add-an-http--swagger-trigger"></a>添加 HTTP + Swagger 触发器

此内置触发器将 HTTP 请求发送到描述 REST API 的 Swagger 文件的 URL，并返回包含该文件内容的响应。

1. 登录 [Azure 门户](https://portal.azure.com)。 在逻辑应用设计器中打开空白逻辑应用。

1. 在设计器上的搜索框中，输入“swagger”作为筛选器。 从“触发器”列表中，选择“HTTP + Swagger”触发器 。

   ![选择 HTTP + Swagger 触发器](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. 在“SWAGGER ENDPOINT URL”框中，输入 Swagger 文件的 URL，然后选择“下一步” 。

   此示例将位于美国西部区域的 Swagger URL 用于[认知服务人脸 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)：

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![输入 Swagger 终结点的 URL](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. 当设计器显示 Swagger 文件描述的操作时，请选择要使用的操作。

   ![Swagger 文件中的操作](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. 提供要包含在终结点调用中的触发器参数的值，这些参数因所选操作而异。 设置希望触发器调用终结点的频率。

   此示例将触发器重命名为“HTTP + Swagger 触发器：人脸 - 检测”，以便该步骤的名称更具描述性。

   ![操作详细信息](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. 要添加其他可用参数，请打开“添加新参数”列表，然后选择所需参数。

   要详细了解可用于 HTTP + Swagger 的身份验证类型，请参阅[向出站调用添加身份验证](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)。

1. 继续使用触发器激发时运行的操作生成逻辑应用的工作流。

1. 完成后，请记得保存逻辑应用。 在设计器工具栏上，选择“保存”。

## <a name="add-an-http--swagger-action"></a>添加 HTTP + Swagger 操作

此内置操作向描述 REST API 的 Swagger 文件的 URL 发出 HTTP 请求，并返回包含该文件内容的响应。

1. 登录 [Azure 门户](https://portal.azure.com)。 在逻辑应用设计器中打开逻辑应用。

1. 在要添加 HTTP + Swagger 操作的步骤下，选择“新建步骤”。

   若要在步骤之间添加操作，请将鼠标指针移到步骤之间的箭头上。 选择出现的加号 (+)，然后选择“添加操作” 。

1. 在设计器上的搜索框中，输入“swagger”作为筛选器。 从“操作”列表中，选择“HTTP + Swagger”操作 。

    ![选择 HTTP + Swagger 操作](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. 在“SWAGGER ENDPOINT URL”框中，输入 Swagger 文件的 URL，然后选择“下一步” 。

   此示例将位于美国西部区域的 Swagger URL 用于[认知服务人脸 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)：

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![输入 Swagger 终结点的 URL](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. 当设计器显示 Swagger 文件描述的操作时，请选择要使用的操作。

   ![Swagger 文件中的操作](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. 提供要包含在终结点调用中的操作参数的值，这些参数因所选操作而异。

   此示例没有参数，但将操作重命名为“HTTP + Swagger 操作：人脸 - 识别”，以便该步骤的名称更具描述性。

   ![操作详细信息](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. 要添加其他可用参数，请打开“添加新参数”列表，然后选择所需参数。

   要详细了解可用于 HTTP + Swagger 的身份验证类型，请参阅[向出站调用添加身份验证](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)。

1. 完成后，请记得保存逻辑应用。 在设计器工具栏上，选择“保存”。

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>在 Azure 存储中托管 Swagger

可将未托管或不满足安全性和跨域要求的 Swagger 文件上传到 Azure 存储帐户中的 Blob 容器，并在该存储帐户上启用 CORS 来引用该文件。 若要在 Azure 存储中创建、设置和存储 Swagger 文件，请遵循以下步骤：

1. [创建 Azure 存储帐户](../storage/common/storage-create-storage-account.md)。

1. 现在为 Blob 启用 CORS。 在存储帐户的菜单中选择“CORS”。 在“Blob 服务”选项卡上，指定以下值，然后选择“保存” 。

   | 属性 | 值 |
   |----------|-------|
   | **允许的源** | `*` |
   | **允许的方法** | `GET`、`HEAD`、`PUT` |
   | **允许的标头** | `*` |
   | **公开的标头** | `*` |
   | **最长时间**（以秒为单位） | `200` |
   |||

   虽然此示例使用 [Azure 门户](https://portal.azure.com)，但你也可使用工具（例如 [Azure 存储资源管理器](https://storageexplorer.com/)），或使用此示例 [PowerShell 脚本](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1)自动配置此设置。

1. [创建 Blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md)。 在容器的“概述”窗格中，选择“更改访问级别” 。 从“公共访问级别”列表中，选择“Blob (仅限 Blob 的匿名读取访问)”，然后选择“确定”  。

1. 通过 [Azure 门户](https://portal.azure.com)或 [Azure 存储资源管理器](https://storageexplorer.com/)[将 Swagger 文件上传到 Blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob)。

1. 若要在 Blob 容器中引用该文件，请从 Azure 存储资源管理器获取采用以下格式（区分大小写）的 HTTPS URL：

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<complete-swagger-file-name>?<query-parameters>`

## <a name="connector-reference"></a>连接器参考

下面是有关 HTTP + Swagger 触发器或操作的输出的详细信息。 HTTP + Swagger 调用会返回以下信息：

| 属性名称 | 类型 | 说明 |
|---------------|------|-------------|
| 标头 | 对象 | 请求中的标头 |
| 正文 | 对象 | JSON 对象 | 具有请求正文内容的对象 |
| 状态代码 | int | 请求的状态代码 |
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

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
