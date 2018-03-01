---
title: "注册自定义连接器 - Azure 逻辑应用 | Microsoft Docs"
description: "设置在 Azure 逻辑应用中使用的自定义连接器"
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
ms.openlocfilehash: 722b82aabe796367d906e9338355b83a1a8b1e1c
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2018
---
# <a name="register-custom-connectors-in-azure-logic-apps"></a>在 Azure 逻辑应用中注册自定义连接器

在生成 REST API、设置身份验证并获取 OpenAPI 定义文件或 Postman 集合后，即可开始注册连接器。 

## <a name="prerequisites"></a>先决条件

若要注册自定义连接器，需要以下各项：

* 在 Azure 中用于注册连接器的详细信息，例如名称、Azure 订阅、Azure 资源组以及想要使用的位置

* 描述 API 的 OpenAPI (Swagger) 文件或 Postman 集合

  在本教程中可使用 [Azure 资源管理器 OpenAPI 示例文件](http://pwrappssamples.blob.core.windows.net/samples/AzureResourceManager.json)。

* 表示连接器的图标

* 连接器的简短说明

* API 的主机位置

## <a name="1-create-your-connector"></a>1.创建连接器

1. 在 Azure 门户的 Azure 主菜单上，选择“创建资源”。 在搜索框中，输入“逻辑应用连接器”作为筛选条件，然后按 Enter。

   ![“新建”，搜索“逻辑应用连接器”](./media/logic-apps-custom-connector-register/create-logic-apps-connector.png)

2. 从结果列表中选择“逻辑应用连接器” > “创建”。

   ![创建逻辑应用连接器](./media/logic-apps-custom-connector-register/choose-logic-apps-connector.png)

3. 如表中所示，提供用于注册连接器的详细信息。 完成后，请选择“固定到仪表板” > “创建”。

   ![逻辑应用自定义连接器详细信息](./media/logic-apps-custom-connector-register/logic-apps-connector-details.png)

   | 属性 | 建议的值 | 说明 | 
   | -------- | --------------- | ----------- | 
   | **Name** | custom-connector-name | 提供连接器的名称。 | 
   | **订阅** | *Azure-subscription-name* | 选择 Azure 订阅。 | 
   | **资源组** | *Azure-resource-group-name* | 创建或选择用于整理 Azure 资源的 Azure 组。 | 
   | **位置** | *deployment-region* | 选择连接器的部署区域。 | 
   |||| 

   Azure 部署连接器后，自定义连接器菜单将自动打开。 
   如果没有打开，请从 Azure 仪表板中选择自定义连接器。

## <a name="2-define-your-connector"></a>2.定义连接器

现在可以指定用于创建连接器的 OpenAPI 文件或 Postman 集合、连接器使用的身份验证、自定义连接器提供的操作和触发器，以及操作和触发器可使用的参数。

### <a name="2a-specify-the-openapi-file-or-postman-collection-for-your-connector"></a>2a. 为连接器指定 OpenAPI 文件或 Postman 集合

1. 在连接器的菜单中，请选择“逻辑应用连接器”（如果尚未选择）。 在工具栏中，选择“编辑”。

   ![编辑自定义连接器](./media/logic-apps-custom-connector-register/edit-custom-connector.png)

2. 选择“常规”，以此在这些表中提供用于为自定义连接器创建、保护和定义操作和触发器的详细信息。

   1. 对于“自定义连接器”，请选择一个选项，以此提供描述 API 的 OpenAPI (Swagger) 文件。

      ![为 API 提供 OpenAPI 文件](./media/logic-apps-custom-connector-register/provide-openapi-file.png)

      | 选项 | 格式 |说明 | 
      | ------ | ------ | ----------- | 
      | **上传 OpenAPI 文件** | OpenAPI (Swagger)-json-file | 浏览到 OpenAPI 文件的位置并选择该文件。 | 
      | **使用 OpenAPI URL** | http://path-to-swagger-json-file | 为 API 的 OpenAPI 文件提供 URL。 | 
      | **上传 Postman 集合 V1** | exported-Postman-collection-V1-file | 浏览到以 V1 格式导出的 Postman 集合的位置。 | 
      |||| 

   2. 对于“常规信息”，请上传连接器的图标。 
   通常情况下，“说明”、“主机”和“基 URL”字段会通过 OpenAPI 文件自动被填充。 
   但如果这些信息没有被填充，请如表中所示进行添加并且选择“继续”。 

      ![连接器详细信息](./media/logic-apps-custom-connector-register/add-connector-details.png)

      | 选项或设置 | 格式 | 说明 | 
      | ----------------- | ------ | ----------- | 
      | **上传图标** | png-or-jpg-file-under-1-MB | 表示连接器的图标 <p>颜色：最好是有色背景衬托的白色徽标。 <p>尺寸：230 像素方框内约 160 像素的徽标 | 
      | **图标背景色** | icon-brand-color-hexadecimal-code | <p>与图标文件中的背景色匹配的图标背景色。 <p>格式：十六进制。 例如，#007ee5 表示蓝色。 | 
      | **说明** | connector-description | 提供连接器的简短说明。 | 
      | **主机** | connector-host | 为 Web API 提供主机域。 | 
      | **基 URL** | connector-base-URL | 为 Web API 提供基 URL。 | 
      |||| 

### <a name="2b-describe-the-authentication-that-your-connector-uses"></a>2b. 描述连接器使用的身份验证

1. 现在选择“安全性”，以此审查或描述连接器使用的身份验证。 身份验证可确保用户的标识在你的服务和任何客户端之间正确地流动。

   ![身份验证类型](./media/logic-apps-custom-connector-register/security.png)

   * 如果上传 OpenAPI 文件，注册向导会自动检测 Web API 使用的身份验证类型，并根据该文件中的 `securityDefinitions` 对象自动填充向导中的“安全性”部分。 例如，以下是一个使用 OAuth2.0 指定的部分：

     ``` json
     "securityDefinitions": {
       "AAD": {
       "type": "oauth2",
       "flow": "accessCode",
       "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
       "tokenUrl": "https://login.windows.net/common/oauth2/token",
       "scopes": {}
       }
     },
     ```

   * 如果 OpenAPI 文件未填充身份验证类型和属性，请选择“编辑”以添加此信息。 
   
     例如，如果之前[在本教程中设置了 Azure AD 身份验证](../logic-apps/custom-connector-azure-active-directory-authentication.md)，则已创建了用于保护 Web API 和连接器的 Azure AD 应用。 
     因此，现在可提供之前保存的应用程序 ID 和客户端密钥：
    
     | 设置 | 建议的值 | 说明 | 
     | ------- | --------------- | ----------- | 
     | **身份验证类型** | OAuth 2.0 | | 
     | **标识提供者** | Azure Active Directory | | 
     | **客户端 ID** | application-ID-for-connector-Azure-AD-app | 之前为连接器的 Azure AD 应用保存的应用程序 ID | 
     | **客户端机密** | client-key-for-connector-Azure-AD-app | 用于连接器的 Azure AD 应用的客户端密钥 | 
     | **登录 URL** | `https://login.windows.net` | | 
     | **资源 URL** | `https://management.core.windows.net/` | 确保严格按照指定添加 URL，包括尾部反斜杠。 | 
     |||| 

   * 一个 Postman 集合，它会自动生成 OpenAPI 文件，并且仅当你使用 OAuth 2.0 或 Basic 等受支持的身份验证类型时才会自动填充身份验证类型。

2. 若要在输入安全性信息后保存连接器，请在页面顶部选择“更新连接器”，然后选择“继续”。 

### <a name="2c-review-update-or-define-actions-and-triggers-for-your-connector"></a>2c. 审查、更新或定义连接器的操作和触发器

1. 现在选择“定义”，以此审查、编辑或定义用户可添加到其工作流的新操作和触发器。

   操作和触发器基于 OpenAPI 文件或 Postman 集合中定义的操作，这会自动填充“定义”页面并包含请求和响应值。 因此，如果所需操作已显示在此处，可转到注册流程中的下一步，而无需在此页面上进行更改。

   ![连接器定义](./media/logic-apps-custom-connector-register/definition.png)

2. （可选）如果想编辑现有操作和触发器或者添加新的，请继续执行这些步骤。

<a name="add-action-or-trigger"></a> 

#### <a name="edit-or-add-actions-for-your-connector"></a>编辑或添加连接器的操作

1. 若要编辑现有操作，请选择此操作相应的编号。 若要添加 OpenAPI 文件或 Postman 集合中不存在的操作，请在“操作”下选择“新建操作”。

2. 在“常规”下，为此操作提供或编辑以下信息：
   
   | 设置 | 建议的值 | 说明 | 
   | ------- | --------------- | ----------- | 
   | **摘要** | operation-name | 此操作的标题 | 
   | **说明** | operation-description | 此操作的说明。 <p>**提示**：确保说明以句点结尾。 |
   | **操作 ID** | operation-identifier | 用于标识此操作的唯一名称。 使用混合大小写，例如：“GetPullRequest” | 
   |**可见性**| “无”、“高级”、“内部”或“重要” | 此操作面向用户的可见性。 有关详细信息，请参阅 [OpenAPI 扩展](../logic-apps/custom-connector-openapi-extensions.md#visibility)。 | 
   |||| 

3. 现在，定义此操作的请求。
  
   1. 在“请求”部分中，选择“从示例导入”。 

   2. 在“从示例导入”页面上，粘贴示例请求。 

      API 文档通常会提供示例请求，可在该文档中获取“谓词”、“URL”、“标头”和“正文”字段的信息。 如需相关示例，请参阅[文本分析 API 文档](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)。

      ![导入示例请求](./media/logic-apps-custom-connector-register/import-sample-operation-request.png)

      > [!IMPORTANT]
      > 如果从 Postman 集合创建连接器，请确保从操作和触发器中删除 `Content-type` 标头。 逻辑应用会自动添加此标头。 此外，从操作和触发器中删除在 `Security` 部分中定义的身份验证标头。

      有关高级 OpenAPI 功能，请参阅[自定义连接器的 OpenAPI 扩展](../logic-apps/custom-connector-openapi-extensions.md)。

   3. 若要完成请求定义，请选择“导入”。

4. 现在，定义此操作的响应。

   1. 可在“响应”下指定默认响应。 
   选择“添加默认响应”。

   2. 在“从示例导入”页面上，粘贴示例响应，然后选择“导入”。

5. 最后在“验证”下审查和修复针对此操作确定的任何潜在问题。

#### <a name="edit-or-add-triggers-for-your-connector"></a>编辑或添加连接器的触发器

1. 若要编辑现有触发器，请选择此触发器相应的编号。 若要添加 OpenAPI 文件或 Postman 集合中不存在的触发器，请在“触发器”下选择“新建触发器”。

2. 在“常规”下，为此触发器提供或编辑以下信息：

   | 设置 | 建议的值 | 说明 | 
   | ------- | --------------- | ----------- | 
   | **摘要** | operation-name | 此触发器的标题 | 
   | **说明** | operation-description | 此触发器的说明。 <p>**提示**：确保说明以句点结尾。 | 
   | **操作 ID** | operation-identifier | 用于标识此触发器的唯一名称。 使用混合大小写，例如：“TriggerOnGitHubPushEvent” | 
   |**可见性**| “无”、“高级”、“内部”或“重要” | 此触发器面向用户的可见性。 有关详细信息，请参阅 [OpenAPI 扩展](../logic-apps/custom-connector-openapi-extensions.md#visibility)。 | 
   | **触发器类型** | “Webhook”或“轮询” | 此触发器的类型。 例如，webhook 触发器在触发前会等待特定事件发生。 轮询触发器会基于指定的时间间隔和频率定期检查服务终结点。 <p>有关 webhook 和轮询触发器模式的详细信息，请参阅[创建自定义 API](../logic-apps/logic-apps-create-api-app.md)。 | 
   |||| 

3. 现在，定义创建此触发器的请求。 

   1. 在“请求”部分中，选择“从示例导入”。

   2. 在“从示例导入”页面上，粘贴示例请求。 

      API 文档通常会提供示例请求，可在该文档中获取“谓词”、“URL”、“标头”和“正文”字段的信息。 如需相关示例，请参阅[文本分析 API 文档](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)。

      ![导入示例请求](./media/logic-apps-custom-connector-register/import-sample-operation-request.png)

      > [!IMPORTANT]
      > 如果从 Postman 集合创建连接器，请确保从操作和触发器中删除 `Content-type` 标头。 逻辑应用会自动添加此标头。 此外，从操作和触发器中删除在 `Security` 部分中定义的身份验证标头。

      有关高级 OpenAPI 功能，请参阅[自定义连接器的 OpenAPI 扩展](../logic-apps/custom-connector-openapi-extensions.md)。

   3. 若要完成请求定义，请选择“导入”。 

4. 现在，定义此触发器的响应。 在“响应”部分中，根据触发器的类型执行下列步骤：

   **Webhook 触发器**
   1. 在“Webhook 响应”中，选择“从示例导入”。 

   2. 在“从示例导入”页面上，粘贴示例响应，然后选择“导入”。 若要查看示例响应，请参阅[用于创建 webhook 的 GitHub API 参考](https://developer.github.com/v3/repos/hooks/#create-a-hook)。

   3. 在“触发器配置”下，选择要用于 webhook 创建请求的参数。 逻辑应用使用此参数值填充由服务使用的回调 URL 来与触发器进行通信。

   **轮询触发器**
   1. 可在“响应”下指定默认响应。 
   选择“添加默认响应”。

   2. 在“从示例导入”页面上，粘贴示例响应，然后选择“导入”。

   3. 在“触发器配置”下指定查询参数（要为参数传递的值）和“触发器提示”（为下一请求指定正确的轮询时间间隔）。

5. 最后在“验证”下审查和修复针对此触发器确定的任何潜在问题。

#### <a name="review-reference-definitions-for-your-connector"></a>审查连接器的引用定义

“引用”部分会自动通过 API 说明进行填充并描述操作和触发器可引用的任何参数字段。 

1. 在“引用”下选择想审查的引用定义的编号。

2. 在“名称”下，审查或更新引用定义名称。

3. 在“验证”下审查和修复针对此引用定义确定的任何潜在问题。

## <a name="3-finish-creating-your-connector"></a>3.完成创建连接器

准备就绪后，选择“创建”即可部署连接器。 如果要更新现有连接器，请选择“更新连接器”。

祝贺你！ 现在，当创建逻辑应用时，可以在逻辑应用设计器中找到你的连接器，并将该连接器添加到逻辑应用中。

![在逻辑应用设计器中，找到连接器](./media/logic-apps-custom-connector-register/custom-connector-created.png)

## <a name="share-your-connector-with-other-logic-apps-users"></a>与其他逻辑应用用户共享连接器

已注册但未认证的自定义连接器与 Microsoft 托管连接器的工作方式类似，但前者仅对该连接器的创建者以及在部署逻辑应用的区域中对这些应用具有同一 Azure Active Directory 租户和 Azure 订阅的用户可见，并且仅可供他们使用。 尽管共享是可选操作，但也可能存在想要与其他用户共享连接器的情况。 

> [!IMPORTANT]
> 如果共享一个连接器，其他人可能会开始依赖该连接器。 
> 删除连接器会删除到该连接器的所有连接。
 
若要与这些边界外的外部用户共享连接器（例如与所有逻辑应用、Microsoft Flow 和 PowerApps 用户共享），请[提交连接器以进行 Microsoft 认证](../logic-apps/custom-connector-submit-certification.md)。

## <a name="faq"></a>常见问题

**问：**自定义连接器是否存在任何限制？ </br>
**答：**是，请参阅[此处的自定义连接器限制](../logic-apps/logic-apps-limits-and-config.md#custom-connector-limits)。

## <a name="get-support"></a>获取支持

* 有关开发和载入支持，或者要请求注册向导中没有提供的功能，请发送电子邮件至 [condevhelp@microsoft.com](mailto:condevhelp@microsoft.com)。Microsoft 会监视此帐户以了解开发人员的问题和疑问，并将他们路由到相应的团队。

* 若要提问或解答问题或者了解其他 Azure 逻辑应用用户的活动，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。

* 为帮助改进逻辑应用，敬请在[逻辑应用用户反馈网站](http://aka.ms/logicapps-wish)上投票或发表看法。 

## <a name="next-steps"></a>后续步骤

* [可选：认证连接器](../logic-apps/custom-connector-submit-certification.md)
* [自定义连接器常见问题解答](../logic-apps/custom-connector-faq.md)