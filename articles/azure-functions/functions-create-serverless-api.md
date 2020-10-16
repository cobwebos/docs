---
title: 在 Azure Functions 中自定义 HTTP 终结点
description: 了解如何在 Azure Functions 中自定义 HTTP 触发器终结点
author: mattchenderson
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: mahender
ms.custom: mvc
ms.openlocfilehash: d683ef92c4e8d11e9defbed5454e5849211bf8f7
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92104744"
---
# <a name="customize-an-http-endpoint-in-azure-functions"></a>在 Azure Functions 中自定义 HTTP 终结点

本文介绍如何使用 Azure Functions 构建高度可缩放的 API。 Azure Functions 附带了一组内置的 HTTP 触发器和绑定，方便你使用各种语言（包括 Node.js、C# 等等）创建终结点。 在本文中，将自定义一个 HTTP 触发器来处理 API 设计中的特定操作。 此外，还要通过将 API 与 Azure Functions 代理集成并设置模拟 API，来准备扩展 API。 这些任务会在 Functions 无服务器计算环境的顶层完成，因此，不需要考虑如何缩放资源，只需专注于自己的 API 逻辑。

## <a name="prerequisites"></a>先决条件 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

本文的余下内容将使用生成的函数。

## <a name="sign-in-to-azure"></a>登录 Azure

使用 Azure 帐户登录到 [Azure 门户](https://portal.azure.com)。

## <a name="customize-your-http-function"></a>自定义 HTTP 函数

默认情况下，HTTP 触发器函数已配置为接受任何 HTTP 方法。 也可以使用默认 URL `http://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>`。 在本部分，需要修改函数，以便只响应包含 `/api/hello` 的 GET 请求。 

1. 在 Azure 门户中导航到该函数。 在左侧菜单中选择“集成”****，然后在“触发器”**** 下选择“HTTP (请求)”****。

    :::image type="content" source="./media/functions-create-serverless-api/customizing-http.png" alt-text="自定义 HTTP 函数":::

1. 使用下表中指定的 HTTP 触发器设置。

    | 字段 | 示例值 | 说明 |
    |---|---|---|
    | 路由模板 | /hello | 确定可以使用哪个路由来调用此函数 |
    | 授权级别 | 匿名 | 可选：无需 API 密钥便可访问函数 |
    | 选定的 HTTP 方法 | GET | 只允许使用选定的 HTTP 方法来调用此函数 |

    未在路由模板中包含 `/api` 基路径前缀，因为其由某个全局设置处理。

1. 选择“保存” ****。

有关自定义 HTTP 函数的详细信息，请参阅 [Azure Functions HTTP 绑定](./functions-bindings-http-webhook.md)。

### <a name="test-your-api"></a>测试 API

接下来，请测试函数，了解它与新 API 外围应用的配合使用情况：
1. 在“函数”页，从左侧菜单中选择“代码 + 测试”****。

1. 从顶部菜单中选择“获取函数 URL”****，然后复制 URL。 确认它现在使用 `/api/hello` 路径。
 
1. 将 URL 复制到新的浏览器标签页或偏好的 REST 客户端中。 

   浏览器默认使用 GET。
 
1. 将参数添加到 URL 中的查询字符串。 

   例如，`/api/hello/?name=John`。
 
1. 按 Enter 确认其正常工作。 应当可以看到响应“Hello John”**。

1. 也可以尝试使用其他 HTTP 方法调用终结点，确认是否未执行该函数。 为此，请使用 REST 客户端，例如 cURL、Postman 或 Fiddler。

## <a name="proxies-overview"></a>代理概述

在下一部分，将通过代理呈现 API。 Azure Functions 代理可将请求转发到其他资源。 定义 HTTP 终结点的过程与定义 HTTP 触发器类似。 但调用终结点时不能写入要执行的代码，而要提供远程实现的 URL。 这样便可以将多个 API 源组合到可方便客户端使用的单个 API 外围应用中，在想要以微服务形式生成 API 的情况下，这很有用。

代理可以指向任何 HTTP 资源，例如：
- Azure Functions 
- [Azure 应用服务](../app-service/overview.md)中的 API 应用
- [Linux 上的应用服务](../app-service/overview.md#app-service-on-linux)中的 Docker 容器
- 其他任何托管 API

若要了解有关代理的详细信息，请参阅[使用 Azure Functions 代理]。

## <a name="create-your-first-proxy"></a>创建第一个代理

在本部分，将创建充当整个 API 的前端的新代理。 

### <a name="setting-up-the-frontend-environment"></a>设置前端环境

重复[创建 Function App](./functions-create-first-azure-function.md#create-a-function-app) 中的步骤，创建要在其中创建代理的新 Function App。 此新应用的 URL 将充当 API 的前端，之前编辑的函数应用将充当后端。

1. 在门户中导航到新的前端 Function App。
1. 选择“平台功能”，并选择“应用程序设置”**** ****。
1. 向下滚动到存储键/值对的“应用程序设置”，然后使用键 `HELLO_HOST` 创建新设置****。 将其值设置为后端 Function App 的主机，例如 `<YourBackendApp>.azurewebsites.net`。 此值是前面在测试 HTTP 函数时复制的 URL 的一部分。 稍后会在配置中引用此设置。

    > [!NOTE] 
    > 建议在主机配置中使用应用设置，以防止对代理的环境依赖关系进行硬编码。 使用应用设置意味着可以在环境之间移动代理配置，并应用特定于环境的应用设置。

1. 选择“保存” 。

### <a name="creating-a-proxy-on-the-frontend"></a>在前端上创建代理

1. 在门户中导航回到前端函数应用。

1. 在左侧菜单中，选择“代理”****，然后选择“添加”****。 

1. 在“新建代理”**** 页，使用下表中的设置，然后选择“创建”****。

    | 字段 | 示例值 | 说明 |
    |---|---|---|
    | 名称 | HelloProxy | 仅用于管理的友好名称 |
    | 路由模板 | /api/remotehello | 确定可以使用哪个路由来调用此代理 |
    | 后端 URL | https://%HELLO_HOST%/api/hello | 指定请求应代理的终结点 |

    
    :::image type="content" source="./media/functions-create-serverless-api/creating-proxy.png" alt-text="自定义 HTTP 函数":::

    Azure Functions 代理不提供 `/api` 基路径前缀，该前缀必须包含在路由模板中。 `%HELLO_HOST%` 语法引用前面创建的应用设置。 解析的 URL 将指向原始函数。

1. 可通过复制代理 URL 并在浏览器中或使用偏好的 HTTP 客户端对其进行测试来试用新代理：
    - 对于匿名函数，请使用：`https://YOURPROXYAPP.azurewebsites.net/api/remotehello?name="Proxies"`。
    - 对于具有授权的函数，请使用：`https://YOURPROXYAPP.azurewebsites.net/api/remotehello?code=YOURCODE&name="Proxies"`。

## <a name="create-a-mock-api"></a>创建模拟 API

接下来，使用代理为解决方案创建模拟 API。 此代理让客户端开发可以继续进行，而无需完全实现后端。 在开发的稍后阶段，可以创建新的函数应用来支持此逻辑并将代理重定向到新的函数应用。

为了创建此模拟 API，我们将创建一个新代理，但这一次使用的是[应用服务编辑器](https://github.com/projectkudu/kudu/wiki/App-Service-Editor)。 要开始，请在门户中导航到 Function App。 选择“平台功能”并在“开发工具”下找到“应用服务编辑器”**** **** ****。 应用服务编辑器在新选项卡中打开。

在左侧导航栏中选择 `proxies.json`。 此文件存储所有代理配置。 如果使用某个 [函数部署方法](./functions-continuous-deployment.md)，则在源代码管理中维护此文件。 若要详细了解此文件，请参阅[代理高级配置](./functions-proxies.md#advanced-configuration)。

到目前为止，proxies.json 应如下所示：

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

接下来，将添加模拟 API。 将 proxies.json 文件替换为以下代码：

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

此代码添加没有 `backendUri` 属性的新代理 `GetUserByName`。 此代理不会调用另一个资源，而是使用响应重写来修改代理的默认响应。 也可以将请求和响应重写与后端 URL 结合使用。 在代理到需要修改标头、查询参数等元素的旧系统时，此方法特别有用。 若要详细了解请求和响应重写，请参阅[修改代理中的请求和响应](./functions-proxies.md)。

通过使用浏览器或偏好的 REST 客户端调用 `<YourProxyApp>.azurewebsites.net/api/users/{username}` 终结点来测试模拟 API。 请务必将 _{username}_ 替换为表示用户名的字符串值。

## <a name="next-steps"></a>后续步骤

本文介绍了如何在 Azure Functions 中构建和自定义 API。 此外，还介绍了如何将多个 API（包括模拟 API）合并成统一的 API 图面。 可以使用这些方法构建具有不同复杂性的 API，所有这些 API 都可在 Azure Functions 提供的无服务器计算模型上运行。

以下参考文档可以帮助进一步开发 API：

- [Azure Functions HTTP 绑定](./functions-bindings-http-webhook.md)
- [使用 Azure Functions 代理]
- [记录 Azure Functions API（预览版）](./functions-openapi-definition.md)


[Create your first function]: ./functions-create-first-azure-function.md
[使用 Azure Functions 代理]: ./functions-proxies.md