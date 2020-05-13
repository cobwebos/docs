---
title: 在 Azure Functions 中自定义 HTTP 终结点
description: 了解如何在 Azure Functions 中自定义 HTTP 触发器终结点
author: mattchenderson
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: mahender
ms.custom: mvc
ms.openlocfilehash: 5607a737fa4616d4eda3d174144c1717125f4181
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83122761"
---
# <a name="customize-an-http-endpoint-in-azure-functions"></a>在 Azure Functions 中自定义 HTTP 终结点

本文介绍如何使用 Azure Functions 构建高度可缩放的 API。 Azure Functions 附带了一组内置的 HTTP 触发器和绑定，方便你使用各种语言（包括 Node.js、C# 等等）创建终结点。 本文介绍如何自定义 HTTP 触发器，以处理 API 设计中的特定操作。 你还可以通过将 API 与 Azure Functions 代理和设置模拟 Api 来进行集成，为扩展 API 做好准备。 这些任务是在 "无服务器" 计算环境的基础上完成的，因此无需担心缩放资源，只需专注于 API 逻辑即可。

## <a name="prerequisites"></a>先决条件 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

本文的余下内容将使用生成的函数。

## <a name="sign-in-to-azure"></a>登录 Azure

使用 Azure 帐户登录到 [Azure 门户](https://portal.azure.com)。

## <a name="customize-your-http-function"></a>自定义 HTTP 函数

默认情况下，HTTP 触发器函数配置为接受任何 HTTP 方法。 你还可以使用默认 URL， `http://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>` 。 在本部分中，将修改函数，使其仅对获取请求 `/api/hello` 。 

1. 在 Azure 门户中导航到该函数。 在左侧菜单中选择 "**集成**"，然后在 "**触发器**" 下选择 " **HTTP （请求）** "。

    :::image type="content" source="./media/functions-create-serverless-api/customizing-http.png" alt-text="自定义 HTTP 函数":::

1. 使用下表中指定的 HTTP 触发器设置。

    | 字段 | 示例值 | 说明 |
    |---|---|---|
    | 路由模板 | /hello | 确定可以使用哪个路由来调用此函数 |
    | 授权级别 | 匿名 | 可选：无需 API 密钥便可访问函数 |
    | 选定的 HTTP 方法 | GET | 只允许使用选定的 HTTP 方法来调用此函数 |

    不会在 `/api` 路由模板中包含基路径前缀，因为它是由全局设置来处理的。

1. 选择“保存”  。

有关自定义 HTTP 函数的详细信息，请参阅[AZURE FUNCTIONS http 绑定](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook)。

### <a name="test-your-api"></a>测试 API

接下来，请测试你的函数，以了解它如何与新的 API 图面配合工作：
1. 在 "函数" 页上，从左侧菜单中选择 "**代码 + 测试**"。

1. 从顶部菜单中选择 "**获取函数 URL** " 并复制 URL。 确认它现在使用 `/api/hello` 路径。
 
1. 将 URL 复制到新的浏览器标签页或偏好的 REST 客户端中。 

   默认情况下，浏览器使用 GET。
 
1. 将参数添加到 URL 中的查询字符串。 

   例如，`/api/hello/?name=John`。
 
1. 按 Enter 以确认它是否正常工作。 应该会看到响应 "*Hello John*"。

1. 还可以尝试用其他 HTTP 方法调用终结点，以确认不执行该函数。 为此，请使用 REST 客户端，如卷曲、Postman 或 Fiddler。

## <a name="proxies-overview"></a>代理概述

在下一部分中，你将通过代理来呈现你的 API。 Azure Functions 代理可将请求转发到其他资源。 像使用 HTTP 触发器一样定义 HTTP 终结点。 但是，您可以向远程实现提供 URL，而不是在调用终结点时编写要执行的代码。 这样一来，你就可以将多个 API 源组合到单个 API 图面中，这对客户端而言很容易使用，如果你希望将 API 构建为微服务，这非常有用。

代理可以指向任何 HTTP 资源，例如：
- Azure Functions 
- [Azure 应用服务](https://docs.microsoft.com/azure/app-service/overview)中的 API 应用
- [Linux 上的应用服务](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro)中的 Docker 容器
- 其他任何托管 API

若要了解有关代理的详细信息，请参阅[使用 Azure Functions 代理]。

## <a name="create-your-first-proxy"></a>创建第一个代理

在本部分中，将创建一个新的代理，它充当整个 API 的前端。 

### <a name="setting-up-the-frontend-environment"></a>设置前端环境

重复[创建 Function App](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function#create-a-function-app) 中的步骤，创建要在其中创建代理的新 Function App。 此新应用的 URL 充当 API 的前端，你之前编辑的 function app 将充当后端。

1. 在门户中导航到新的前端 Function App。
1. 选择“平台功能”，并选择“应用程序设置”********。
1. 向下滚动到 "**应用程序设置**"，其中存储了键/值对，并使用键创建新的设置 `HELLO_HOST` 。 将其值设置为后端 Function App 的主机，例如 `<YourBackendApp>.azurewebsites.net`。 此值是你之前在测试 HTTP 函数时复制的 URL 的一部分。 稍后会在配置中引用此设置。

    > [!NOTE] 
    > 建议在主机配置中使用应用设置，以防止对代理的环境依赖关系进行硬编码。 使用应用设置意味着可以在环境之间移动代理配置，并应用特定于环境的应用设置。

1. 选择“保存”  。

### <a name="creating-a-proxy-on-the-frontend"></a>在前端上创建代理

1. 在门户中导航回到前端函数应用。

1. 在左侧菜单中选择 "**代理**"，然后选择 "**添加**"。 

1. 在 "**新建代理**" 页上，使用下表中的设置，然后选择 "**创建**"。

    | 字段 | 示例值 | 说明 |
    |---|---|---|
    | 名称 | HelloProxy | 仅用于管理的友好名称 |
    | 路由模板 | /api/remotehello | 确定可以使用哪个路由来调用此代理 |
    | 后端 URL | https://%HELLO_HOST%/api/hello | 指定请求应代理的终结点 |

    
    :::image type="content" source="./media/functions-create-serverless-api/creating-proxy.png" alt-text="创建代理":::

    Azure Functions 代理不提供 `/api` 必须包含在路由模板中的基本路径前缀。 `%HELLO_HOST%`语法引用之前创建的应用设置。 解析的 URL 将指向原始函数。

1. 通过复制代理 URL 并在浏览器中或使用喜爱的 HTTP 客户端对其进行测试，尝试新的代理：
    - 对于匿名函数，请使用： `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?name="Proxies"` 。
    - 对于授权使用的函数： `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?code=YOURCODE&name="Proxies"` 。

## <a name="create-a-mock-api"></a>创建模拟 API

接下来，你将使用代理为解决方案创建模拟 API。 此代理可让客户端进行开发，而无需完全实现后端。 以后在开发时，可以创建新的 function app，它支持此逻辑并将代理重定向到此逻辑。

若要创建此模拟 API，我们将创建一个新代理，这次使用[应用服务编辑器](https://github.com/projectkudu/kudu/wiki/App-Service-Editor)。 要开始，请在门户中导航到 Function App。 选择 "**平台功能**"，然后在 "**开发工具**" 下查找**应用服务编辑器**。 应用服务编辑器在新选项卡中打开。

在左侧导航栏中选择 `proxies.json`。 此文件存储所有代理的配置。 如果使用某个[函数部署方法](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment)，则在源代码管理中维护此文件。 若要详细了解此文件，请参阅[代理高级配置](https://docs.microsoft.com/azure/azure-functions/functions-proxies#advanced-configuration)。

如果已按照目前的步骤进行操作，则你的代理应如下所示：

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/remotehello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        }
    }
}
```

接下来，将添加模拟 API。 将代理 json 文件替换为以下代码：

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/remotehello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        },
        "GetUserByName" : {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/users/{username}"
            },
            "responseOverrides": {
                "response.statusCode": "200",
                "response.headers.Content-Type" : "application/json",
                "response.body": {
                    "name": "{username}",
                    "description": "Awesome developer and master of serverless APIs",
                    "skills": [
                        "Serverless",
                        "APIs",
                        "Azure",
                        "Cloud"
                    ]
                }
            }
        }
    }
}
```

此代码将添加新的代理， `GetUserByName` 而不包含 `backendUri` 属性。 此代理不会调用另一个资源，而是使用响应重写来修改代理的默认响应。 也可以将请求和响应重写与后端 URL 结合使用。 当代理到旧系统时，此方法特别有用，你可能需要修改标头、查询参数等。 若要详细了解请求和响应重写，请参阅[修改代理中的请求和响应](https://docs.microsoft.com/azure/azure-functions/functions-proxies)。

通过使用浏览器或偏好的 REST 客户端调用 `<YourProxyApp>.azurewebsites.net/api/users/{username}` 终结点来测试模拟 API。 请务必将 _{username}_ 替换为表示用户名的字符串值。

## <a name="next-steps"></a>后续步骤

本文介绍了如何在 Azure Functions 中构建和自定义 API。 此外，还介绍了如何将多个 API（包括模拟 API）合并成统一的 API 图面。 可以使用这些方法构建具有不同复杂性的 API，所有这些 API 都可在 Azure Functions 提供的无服务器计算模型上运行。

以下参考文档可以帮助进一步开发 API：

- [Azure Functions HTTP 绑定](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook)
- [使用 Azure Functions 代理]
- [记录 Azure Functions API（预览版）](https://docs.microsoft.com/azure/azure-functions/functions-api-definition-getting-started)


[Create your first function]: https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function
[使用 Azure Functions 代理]: https://docs.microsoft.com/azure/azure-functions/functions-proxies
