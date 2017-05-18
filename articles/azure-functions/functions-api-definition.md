---
title: "Azure Functions 中的 OpenAPI 元数据 | Microsoft Docs"
description: "Azure Functions 中的 OpenAPI 支持概述"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: erikre
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/23/2017
ms.author: alkarche
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: aac362f5123038cc39e0d2b32c10f7747a702cfe
ms.contentlocale: zh-cn
ms.lasthandoff: 05/16/2017


---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Azure Functions 中的 OpenAPI 2.0 元数据支持（预览版）
此预览功能允许在 Function App 中编写 OpenAPI 2.0（以前称为 Swagger）定义，以及使用 Function App 托管该文件。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

### <a name="what-is-openapi-metadata"></a>什么是 OpenAPI 元数据？
[OpenAPI 元数据](http://swagger.io/)允许托管 REST API 的函数供各种其他软件使用。 诸如 PowerApps 和 [API 应用](https://docs.microsoft.com/azure/app-service-api/app-service-api-dotnet-get-started#a-idcodegena-generate-client-code-for-the-data-tier)之类的第一方产品到如 [Postman](https://www.getpostman.com/docs/importing_swagger) 这样的第三方开发人员工具，以及[更多软件包](http://swagger.io/tools/)。

>[!TIP]
>建议先从[入门教程](./functions-api-definition-getting-started.md)开始，然后返回到本文档，以了解有关特定功能的详细信息。

## <a name="enable"></a>启用 OpenAPI 定义支持
* 可以在 Function App 设置的 `API Definition (preview)` 页中配置所有 OpenAPI 设置。
* 通过将 `API definition source` 设置为 `Function`，可以启用托管的 OpenAPI 定义和快速定义生成。
  * `External URL` 允许函数使用托管在其他位置的 OpenAPI 定义。

## <a name="generate-definition"></a>通过函数元数据生成 Swagger 框架
使用模板来开始编写第一个 OpenAPI 定义是一个不错的方法。 定义模板功能使用 function.json 中的所有元数据为每个 HTTP 触发器函数创建稀疏的 OpenAPI 定义。 **将需要按 [OpenAPI 规范](http://swagger.io/specification/)填写有关 API 的详细信息，如请求和响应模板。**

[查看入门教程中的分步说明](./functions-api-definition-getting-started.md)

### <a name="templates"></a>可用模板

|Name| 说明 |
|:-----|:-----|
|生成的定义|一个 OpenAPI 定义，内含可以从 Function 的现有元数据中推断出的大量信息|

### <a name="quickstart-details"></a>生成的定义中包含的元数据

下表显示了门户设置和 function.json 中的相应数据（function.json 映射到生成的 Swagger 框架时）。

|Swagger.json|门户 UI|Function.json|
|:----|:-----|:-----|
|[主机](http://swagger.io/specification/#fixed-fields-15)|`Function app settings` > `Go to App Service Settings` > `Overview` > `URL`|*不存在*
|[路径](http://swagger.io/specification/#paths-object-29)|`Integrate` > `Selected HTTP methods`|绑定：路由
|[路径项](http://swagger.io/specification/#path-item-object-32)|`Integrate` > `Route template`|绑定︰方法
|[安全性](http://swagger.io/specification/#security-scheme-object-112)|密钥|*不存在*|
|operationID*|路由 + 允许的动作|路由 + 允许的动作|

\*仅与 PowerApps + 流程集成才需要操作 ID
> [!NOTE]
>  x-ms-summary 在逻辑应用、流程和 PowerApps 中提供显示名称。
>
> 有关详细信息，请参阅[自定义 PowerApps 的 Swagger 定义](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/)。

## <a name="CICD"></a>使用 CI/CD 设置 API 定义

 必须先在门户中启用 API 定义托管，然后源代码管理才能修改其中的 API 定义。 请根据以下说明操作。

1. 在 Function App 设置中导航到 `API Definition (preview)`。
  1. 将 `API definition source` 设置为 `Function`
  1. 单击 `Generate API definition template`，然后单击 `Save` 来创建模板定义供以后修改。
  1. 记下 `API definition URL` 和 `key`
1. [设置 CI/CD](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment#continuous-deployment-requirements)
2. 在 `\site\wwwroot\.azurefunctions\swagger\swagger.json` 中修改源代码管理中的 `swagger.json`

现在针对存储库中 `swagger.json` 的更改由 Function App 托管在步骤 1.3 中记下的 `API definition URL` 和 `key` 中

## <a name="next-steps"></a>后续步骤
* [入门教程](functions-api-definition-getting-started.md)
  * 请尝试我们的演练以查看运行的 OpenAPI 定义！
* [Azure Functions Github 存储库](https://github.com/Azure/Azure-Functions/)
  * 查看 Functions Github，向我们提供有关 API 定义支持预览的反馈！ 为想要更新的任何内容制作一个 Github 发布。
* [Azure Functions 开发人员参考](functions-reference.md)  
  * ，用于编码函数和定义触发器及绑定的程序员参考。

