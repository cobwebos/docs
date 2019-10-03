---
title: 从 Azure 逻辑应用连接到 REST 终结点
description: 使用 Azure 逻辑应用监视自动化的任务、 流程和工作流中的 REST 终结点
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: f0410ed7a98e4838e41407868cf26b5254811ae3
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541592"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用调用 REST 终结点

与[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和内置 HTTP + Swagger 连接器，你可以自动执行定期调用通过任何 REST 终结点的工作流[Swagger 文件](https://swagger.io)通过构建逻辑应用。 HTTP + Swagger 触发器和操作与相同[HTTP 触发器和操作](connectors-native-http.md)，但通过公开的 API 结构和 Swagger 文件描述的输出提供在逻辑应用设计器更好的体验。 若要实现轮询触发器，请按照中所述的轮询模式[创建自定义 Api 以从逻辑应用中调用其他 Api、 服务和系统](../logic-apps/logic-apps-create-api-app.md#polling-triggers)。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 描述目标 REST 终结点的 Swagger 文件 URL

  通常情况下，REST 终结点必须满足此条件连接器才能正常：

  * Swagger 文件必须驻留在可公开访问的 HTTPS URL。

  * Swagger 文件必须具有[跨域资源共享 (CORS)](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)启用。

  若要引用未托管或者不满足安全和跨源要求的 Swagger 文件，你可以[Swagger 文件上传到 Azure 存储帐户中的 blob 容器](#host-swagger)，并因此在该存储帐户上启用 CORS可以引用该文件。

  在本主题使用示例[认知服务人脸 API](https://docs.microsoft.com/azure/cognitive-services/face/overview)，这要求[认知服务帐户和访问密钥](../cognitive-services/cognitive-services-apis-create-account.md)。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识。 如果你不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

* 逻辑应用从你想要调用的目标终结点。 开始使用 HTTP + Swagger 触发器，请[创建空白逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 为使用 HTTP + Swagger 操作，使用所需的任何触发器启动逻辑应用。 此示例使用 HTTP + Swagger 触发器为第一步。

## <a name="add-an-http--swagger-trigger"></a>添加 HTTP + Swagger 触发器

此内置的触发器将 HTTP 请求发送到 Swagger 文件，用于描述 REST API 并返回响应，其中包含该文件的内容的 URL。

1. 登录到 [Azure 门户](https://portal.azure.com)。 在逻辑应用设计器中打开空白逻辑应用。

1. 在设计器中，在搜索框中，输入作为筛选器的"swagger"。 从**触发器**列表中，选择**HTTP + Swagger**触发器。

   ![选择 HTTP + Swagger 触发器](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. 在中**SWAGGER 终结点 URL** ，输入的 URL 的 Swagger 文件，并选择**下一步**。

   此示例使用位于美国西部区域中的 Swagger URL[认知服务人脸 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![输入 Swagger 终结点 URL](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. 当在设计器显示由 Swagger 文件描述的操作时，选择你想要使用的操作。

   ![Swagger 文件中的操作](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. 对于触发器参数，而有所不同基于所选的操作，你想要包含终结点调用中提供的值。 设置你希望多久触发器的重复调用的终结点。

   此示例将重命名触发器绑定到"HTTP + Swagger 触发器：人脸-检测"，以便具有更具描述性的名称。

   ![操作详细信息](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. 若要添加其他可用参数，请打开**添加新参数**列表，然后选择所需的参数。

   HTTP + Swagger 可用的身份验证类型的详细信息，请参阅[进行身份验证的 HTTP 触发器和操作](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)。

1. 继续使用触发器激发时运行的操作生成逻辑应用的工作流。

1. 完成后，请记住保存逻辑应用。 在设计器工具栏上，选择**保存**。

## <a name="add-an-http--swagger-action"></a>添加 HTTP + Swagger 操作

此内置操作到 Swagger 文件，用于描述 REST API 并返回响应，其中包含该文件的内容的 URL 发出 HTTP 请求。

1. 登录到 [Azure 门户](https://portal.azure.com)。 在逻辑应用设计器中打开逻辑应用。

1. 在你想要添加 HTTP + Swagger 中的步操作中，选择**新步骤**。

   若要在步骤之间添加操作，请将鼠标指针移到步骤之间的箭头上。 选择加号 ( **+** ) 的出现，并选择**添加操作**。

1. 在设计器中，在搜索框中，输入作为筛选器的"swagger"。 从**操作**列表中，选择**HTTP + Swagger**操作。

    ![选择 HTTP + Swagger 操作](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. 在中**SWAGGER 终结点 URL** ，输入的 URL 的 Swagger 文件，并选择**下一步**。

   此示例使用位于美国西部区域中的 Swagger URL[认知服务人脸 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![输入 Swagger 终结点 URL](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. 当在设计器显示由 Swagger 文件描述的操作时，选择你想要使用的操作。

   ![Swagger 文件中的操作](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. 为操作参数，而有所不同基于所选的操作，你想要包含终结点调用中提供的值。

   此示例中没有任何参数，但重命名操作保存到"HTTP + Swagger 操作：人脸-识别"，以便具有更具描述性的名称。

   ![操作详细信息](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. 若要添加其他可用参数，请打开**添加新参数**列表，然后选择所需的参数。

   HTTP + Swagger 可用的身份验证类型的详细信息，请参阅[进行身份验证的 HTTP 触发器和操作](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)。

1. 完成后，请记住保存逻辑应用。 在设计器工具栏上，选择**保存**。

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>在 Azure 存储托管 Swagger

您可以引用未托管或者不满足安全和跨源要求通过将该文件上传到 Azure 存储帐户中 blob 容器并在该存储帐户上启用 CORS 的 Swagger 文件。 若要创建、 设置，并存储在 Azure 存储中的 Swagger 文件，请按照下列步骤：

1. [创建 Azure 存储帐户](../storage/common/storage-create-storage-account.md)。

1. 现在为 blob 中启用 CORS。 在存储帐户的菜单中选择**CORS**。 上**Blob 服务**选项卡上，指定这些值，然后选择**保存**。

   | 属性 | 值 |
   |----------|-------|
   | **允许的来源** | `*` |
   | **允许的方法** | `GET`、`HEAD`、`PUT` |
   | **允许的标头** | `*` |
   | **公开的标头** | `*` |
   | **最大期限**（以秒为单位） | `200` |
   |||

   虽然此示例使用[Azure 门户](https://portal.azure.com)，可以使用一种工具，如[Azure 存储资源管理器](https://storageexplorer.com/)，或使用此示例自动配置此设置[PowerShell 脚本](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1).

1. [创建 blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md)。 在容器上**概述**窗格中，选择**更改访问级别**。 从**公共访问级别**列表中，选择**Blob （仅限对 blob 的匿名读取访问）** ，然后选择**确定**。

1. [将 Swagger 文件上传到 blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob)，而是通过[Azure 门户](https://portal.azure.com)或[Azure 存储资源管理器](https://storageexplorer.com/)。

1. 若要引用的 blob 容器中的文件，请使用遵循此格式，这是区分大小写的 HTTPS 链接：

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<swagger-file-name>`

## <a name="connector-reference"></a>连接器参考

以下是详细信息的输出的 HTTP + Swagger 触发器或操作。 HTTP + Swagger 调用将返回此信息：

| 属性名称 | Type | 描述 |
|---------------|------|-------------|
| headers | object | 从请求标头 |
| body | object | JSON 对象 | 具有从请求的正文内容的对象 |
| 状态代码 | int | 请求中的状态代码 |
|||

| 状态代码 | 描述 |
|-------------|-------------|
| 200 | OK |
| 202 | 已接受 |
| 400 | 错误的请求 |
| 401 | 未授权 |
| 403 | 禁止 |
| 404 | 未找到 |
| 500 | 内部服务器错误。 发生未知错误。 |
|||

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)