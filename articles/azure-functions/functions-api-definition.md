---
title: Azure Functions 中的 OpenAPI 元数据 | Microsoft Docs
description: Azure Functions 中的 OpenAPI 支持概述
services: functions
documentationcenter: ''
author: alexkarcher-msft
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 03/23/2017
ms.author: alkarche
ms.openlocfilehash: b6aacc536e589a2036aba5a0784a4ba71641a59e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
ms.locfileid: "22991672"
---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Azure Functions 中的 OpenAPI 2.0 元数据支持（预览版）
Azure Functions 中的 OpenAPI 2.0（以前称为 Swagger）元数据支持一项预览版功能，可用于在 Function App 中编写 OpenAPI 2.0 定义。 随后可使用 Function App 托管该文件。

通过 [OpenAPI 元数据](http://swagger.io/)，大量其他软件可使用托管 REST API 的函数。 此软件包括 Microsoft 产品/服务（如 PowerApps 和 [Azure 应用服务的 API 应用功能](../app-service/app-service-web-overview.md)）、第三方开发人员工具（如 [Postman](https://www.getpostman.com/docs/importing_swagger)，以及[更多大量的程序包](http://swagger.io/tools/)。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

>[!TIP]
>建议先从[入门教程](./functions-api-definition-getting-started.md)开始，然后返回到本文档，了解有关特定功能的详细信息。

## <a name="enable"></a>启用 OpenAPI 定义支持
可在函数应用的“平台功能”的“API 定义”页中配置所有 OpenAPI 设置。

要生成托管的 OpenAPI 定义和快速入门定义，请将“API 定义源”设置为“函数(预览版)”。 外部 URL 允许函数使用托管在其他位置的 OpenAPI 定义。

## <a name="generate-definition"></a>通过函数元数据生成 Swagger 框架
模板可帮助你开始编写第一个 OpenAPI 定义。 定义模板功能通过使用 function.json 文件中的所有元数据，为每个 HTTP 触发器函数创建稀疏的 OpenAPI 定义。 将需要按 [OpenAPI 规范](http://swagger.io/specification/)填写 API 详细信息，如请求和响应模板。

有关分步说明，请参阅[入门教程](./functions-api-definition-getting-started.md)。

### <a name="templates"></a>可用模板

|名称| 说明 |
|:-----|:-----|
|生成的定义|一个 OpenAPI 定义，内含可从函数的现有元数据中推断出的大量信息。|

### <a name="quickstart-details"></a>生成的定义中包含的元数据

下表显示了 Azure 门户设置，并显示 function.json 在映射到生成的 Swagger 框架时包含的相应数据。

|Swagger.json|门户 UI|Function.json|
|:----|:-----|:-----|
|[主机](http://swagger.io/specification/#fixed-fields-15)|“Function App 设置” > “应用服务设置” > “概述” > “URL”|*不存在*
|[路径](http://swagger.io/specification/#paths-object-29)|“集成” > “选择 HTTP 方法”|绑定：路由
|[路径项](http://swagger.io/specification/#path-item-object-32)|“集成” > “路由模板”|绑定︰方法
|[安全性](http://swagger.io/specification/#security-scheme-object-112)|密钥|*不存在*|
|operationID*|路由 + 允许的动作|路由 + 允许的动作|

\*仅与 PowerApps 和 Flow 集成才需要操作 ID。
> [!NOTE]
> x-ms-summary 扩展名在逻辑应用、PowerApps 和 Flow 中提供显示名称。
>
> 有关详细信息，请参阅[自定义 PowerApps 的 Swagger 定义](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/)。

## <a name="CICD"></a>使用 CI/CD 设置 API 定义

 必须先在门户中启用 API 定义托管，然后源控件才能修改其中的 API 定义。 请按照以下说明操作：

1. 浏览到 Function App 设置中的“API 定义(预览)”。
  1. 将“API 定义源”设置为“函数”。
  1. 单击“生成 API 定义模板”，然后单击“保存”以创建模板定义供稍后修改。
  1. 记下 API 定义 URL 和密钥。
1. [设置持续集成/持续部署 (CI/CD)](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment#continuous-deployment-requirements)。
2. 在 \site\wwwroot\.azurefunctions\swagger\swagger.json 处的源控件中修改 swagger.json。

现在，存储库中对 swagger.json 的更改就由 Function App 通过步骤 1.c 中记录的 API 定义 URL 和密码进行托管。

## <a name="next-steps"></a>后续步骤
* [入门教程](functions-api-definition-getting-started.md)。 请尝试一下演练，查看运行中的 OpenAPI 定义。
* [Azure Functions GitHub 存储库](https://github.com/Azure/Azure-Functions/)。 查看 Functions 存储库，针对 API 定义支持预览提供反馈。 为要更新的任何内容制作一个 GitHub 发布。
* [Azure Functions 开发人员参考](functions-reference.md)。 了解如何编码函数以及如何定义触发器和绑定。
