---
title: 从 Azure 逻辑应用连接到 REST 终结点
description: 使用 Azure 逻辑应用监视自动化任务、进程和工作流中的 REST 终结点
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: 663ef16511269dd61a6567d6570f3445b7da6447
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72804239"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用调用 REST 终结点

使用[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和内置 HTTP + Swagger 连接器，可以通过生成逻辑应用，自动执行定期调用任何 REST 终结[点的工作](https://swagger.io)流。 HTTP + Swagger 触发器和操作与[http 触发器和操作](connectors-native-http.md)相同，但在逻辑应用设计器中提供了更好的体验，方法是公开 Swagger 文件描述的 API 结构和输出。 若要实现轮询触发器，请遵循通过创建自定义 Api 中所述的轮询模式[来调用逻辑应用中的其他 api、服务和系统](../logic-apps/logic-apps-create-api-app.md#polling-triggers)。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 用于描述目标 REST 终结点的 Swagger 文件的 URL

  通常，REST 终结点必须满足此条件，连接器才能工作：

  * Swagger 文件必须托管在可公开访问的 HTTPS URL 上。

  * Swagger 文件必须启用[跨域资源共享（CORS）](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) 。

  若要引用未托管的 Swagger 文件或不满足安全要求和跨域要求的 Swagger 文件，可以将[swagger 文件上传到 Azure 存储帐户中的 blob 容器](#host-swagger)，并在该存储帐户上启用 CORS，以便可以引用文件。

  本主题中的示例使用[认知服务人脸 API](https://docs.microsoft.com/azure/cognitive-services/face/overview)，这需要[认知服务帐户和访问密钥](../cognitive-services/cognitive-services-apis-create-account.md)。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识。 如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

* 要从中调用目标终结点的逻辑应用。 若要开始使用 HTTP + Swagger 触发器，请[创建一个空白逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 HTTP + Swagger 操作，请使用所需的任何触发器启动逻辑应用。 此示例使用 HTTP + Swagger 触发器作为第一步。

## <a name="add-an-http--swagger-trigger"></a>添加 HTTP + Swagger 触发器

此内置触发器将 HTTP 请求发送到 Swagger 文件的 URL，该文件描述 REST API 并返回包含该文件内容的响应。

1. 登录到 [Azure 门户](https://portal.azure.com)。 在逻辑应用设计器中打开空白逻辑应用。

1. 在设计器的搜索框中，输入 "swagger" 作为筛选器。 从 "**触发器**" 列表中，选择 " **HTTP + Swagger** " 触发器。

   ![选择 HTTP + Swagger 触发器](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. 在 " **SWAGGER 终结点 url** " 框中，输入 swagger 文件的 URL，然后选择 "**下一步**"。

   此示例使用位于美国西部区域的 Swagger URL 来[人脸 API 认知服务](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)：

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![输入 Swagger 终结点的 URL](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. 当设计器显示 Swagger 文件描述的操作时，请选择要使用的操作。

   ![Swagger 文件中的操作](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. 根据所选操作，提供要包含在终结点调用中的触发器参数的值。 为触发器调用终结点的频率设置重复周期。

   此示例将触发器重命名为 "HTTP + Swagger 触发器：面部检测"，以便该步骤具有更具描述性的名称。

   ![操作详细信息](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. 若要添加其他可用参数，请打开 "**添加新参数**" 列表，然后选择所需的参数。

   有关可用于 HTTP + Swagger 的身份验证类型的详细信息，请参阅[将身份验证添加到出站调用](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)。

1. 继续使用触发器激发时运行的操作生成逻辑应用的工作流。

1. 完成后，请记得保存逻辑应用。 在设计器工具栏上，选择“保存”。

## <a name="add-an-http--swagger-action"></a>添加 HTTP + Swagger 操作

此内置操作向用于描述 REST API 的 Swagger 文件的 URL 发出 HTTP 请求，并返回包含该文件内容的响应。

1. 登录到 [Azure 门户](https://portal.azure.com)。 在逻辑应用设计器中打开逻辑应用。

1. 在要添加 HTTP + Swagger 操作的步骤下，选择 "**新建步骤**"。

   若要在步骤之间添加操作，请将鼠标指针移到步骤之间的箭头上。 选择出现的加号（ **+** ），然后选择 "**添加操作**"。

1. 在设计器的搜索框中，输入 "swagger" 作为筛选器。 从 "**操作**" 列表中，选择**HTTP + Swagger**操作。

    ![选择 HTTP + Swagger 操作](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. 在 " **SWAGGER 终结点 url** " 框中，输入 swagger 文件的 URL，然后选择 "**下一步**"。

   此示例使用位于美国西部区域的 Swagger URL 来[人脸 API 认知服务](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)：

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![输入 Swagger 终结点的 URL](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. 当设计器显示 Swagger 文件描述的操作时，请选择要使用的操作。

   ![Swagger 文件中的操作](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. 根据所选操作，提供要包含在终结点调用中的操作参数的值。

   此示例没有参数，但将操作重命名为 "HTTP + Swagger 操作：面部识别"，以便步骤具有更具描述性的名称。

   ![操作详细信息](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. 若要添加其他可用参数，请打开 "**添加新参数**" 列表，然后选择所需的参数。

   有关可用于 HTTP + Swagger 的身份验证类型的详细信息，请参阅[将身份验证添加到出站调用](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)。

1. 完成后，请记得保存逻辑应用。 在设计器工具栏上，选择“保存”。

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>在 Azure 存储中托管 Swagger

可以通过将该文件上传到 Azure 存储帐户中的 blob 容器，并在该存储帐户上启用 CORS，来引用未托管的 Swagger 文件，或不满足安全和跨域要求的 Swagger 文件。 若要在 Azure 存储中创建、设置和存储 Swagger 文件，请执行以下步骤：

1. [创建 Azure 存储帐户](../storage/common/storage-create-storage-account.md)。

1. 现在为 blob 启用 CORS。 在存储帐户的菜单中，选择 " **CORS**"。 在 " **Blob 服务**" 选项卡上，指定这些值，然后选择 "**保存**"。

   | properties | Value |
   |----------|-------|
   | **允许的来源** | `*` |
   | **允许的方法** | `GET`、`HEAD`、`PUT` |
   | **允许的标头** | `*` |
   | **公开的标头** | `*` |
   | **最大期限**（秒） | `200` |
   |||

   虽然此示例使用[Azure 门户](https://portal.azure.com)，但你可以使用[Azure 存储资源管理器](https://storageexplorer.com/)之类的工具，也可以使用此示例[PowerShell 脚本](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1)自动配置此设置。

1. [创建 blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md)。 在容器的 "**概述**" 窗格上，选择 "**更改访问级别**"。 从 "**公共访问级别**" 列表中，选择 " **Blob （仅限 blob 的匿名读取访问）** "，然后选择 **"确定"** 。

1. 通过[Azure 门户](https://portal.azure.com)或[Azure 存储资源管理器](https://storageexplorer.com/)将[Swagger 文件上传到 blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob)。

1. 若要在 blob 容器中引用该文件，请使用遵循此格式的 HTTPS 链接，这区分大小写：

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<swagger-file-name>`

## <a name="connector-reference"></a>连接器参考

下面是有关 HTTP + Swagger 触发器或操作的输出的详细信息。 HTTP + Swagger 调用返回以下信息：

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