---
title: "在 Azure Functions 中使用代理 |Microsoft 文档"
description: "有关如何使用 Azure Functions 代理的概述"
services: functions
documentationcenter: 
author: mattchenderson
manager: erikre
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 02/19/2017
ms.author: mahender
translationtype: Human Translation
ms.sourcegitcommit: facdd2abbb90a85535b8936ea655a824134c4c8b
ms.openlocfilehash: 3e8b8f9908cf24baf7a5d70521c79dbd470001f8
ms.lasthandoff: 02/22/2017


---
# <a name="working-with-azure-functions-proxies-preview"></a>使用 Azure Functions 代理（预览版）

> [!Note] 
> Azure Functions 代理当前为预览版。 在预览版阶段它是免费的，但标准版 Functions 将针对代理执行收费。 有关详细信息，请参阅 [Azure Functions 定价](https://azure.microsoft.com/pricing/details/functions/)。

本文介绍了如何配置和使用 Azure Functions 代理。 此功能允许你在 Function App 上指定由其他资源实现的终结点。 可以使用这些代理将大型 API 拆分到多个 Function App 中（与在微服务体系结构中一样），同时对客户端仍然呈现为单个 API 接口。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]


## <a name="a-nameenableaenabling-azure-functions-proxies"></a><a name="enable"></a>启用 Azure Functions 代理

默认情况下未启用这些代理。 在该功能处于禁用状态时可以创建代理，但它们不会执行。 以下步骤将展示如何启用代理：

1. 打开 [Azure 门户]并导航到你的 Function App。
2. 选择“Function App 设置”。
3. 将**启用 Azure Functions 代理(预览版)** 切换为开启。

当新功能可用时还可以返回到此处来更新代理运行时。


## <a name="creating-a-proxy"></a>创建代理

本部分将展示如何在 Functions 门户中创建代理。

1. 打开 [Azure 门户]并导航到你的 Function App。
2. 在左侧导航栏中，单击“新建代理”。
3. 为你的代理提供一个名称。
4. 通过指定**路由模板**和 **HTTP 方法**配置在此 Function App 上公开的终结点。 这些参数的行为取决于 [HTTP 触发器]的规则。
5. 将“后端 URL”设置为另一个终结点。 这可以是其他 Function App 中的函数，也可以是任何其他 API。
6. 单击“创建”。

代理现在已作为新终结点存在于 Function App 上。 从客户端角度来看，它等同于 Azure Functions 中的 HttpTrigger。 可以通过复制代理 URL 并使用你最喜欢的 HTTP 客户端对其进行测试来试验新代理。


## <a name="a-namemodify-requestsamodifying-backend-requests"></a><a name="modify-requests"></a>修改后端请求

后端 URL 参数不需要是静态的。 可以从请求或应用程序设置中将其设为输入中的条件。


### <a name="using-request-parameters"></a>使用请求参数

路由模板中使用的参数可以用作后端 URL 属性的输入。 值通过名称进行引用，括在花括号“{}”中。

例如，如果代理具有诸如 `/pets/{petId}` 的路由模板，则后端 URL 可以包括 `{petId}` 的值，就像在 `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}` 中那样。 如果路由模板以通配符结尾，例如 `/api/{*restOfPath}`，则值 `{restOfPath}` 将是传入请求中的其余路径段的字符串表示形式。


### <a name="using-application-settings"></a>使用应用程序设置

还可以通过将设置名称包围在百分号“%”之间来引用[应用程序设置](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#develop)。

例如，后端 URL `https://%ORDER_PROCESSING_HOST%/api/orders` 会将“%ORDER_PROCESSING_HOST%”替换为 ORDER_PROCESSING_HOST 设置的值。

> [!TIP] 
> 当有多个部署或测试环境时，请为后端主机使用应用程序设置。 这样可以确保始终与该环境的正确后端进行通信。



## <a name="deployment-methods"></a>部署方法

你配置的代理存储在一个 proxies.json 文件中，此文件位于 Function App 目录的根目录中。 当使用 Functions 支持的任意[部署方法](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment)时，可以手动编辑此文件并将其部署为你的应用的一部分。

必须启用此功能才能处理此文件。 可以根据[启用 Azure Functions 代理](#enable)中的说明启用此功能。

Proxies.json 是由一个代理对象定义的，包括已命名的代理及其定义。 示例文件可能如下例所示：

```json
{
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

每个代理都有一个友好名称，例如上例中的“proxy1”。 对应的代理定义对象是由以下属性定义的：

* **matchCondition**：必需 - 一个对象，它定义了将触发此代理执行的请求。 它包含两个与 [HTTP 触发器]共享的属性：
    * _methods_：这是代理将响应的 HTTP 方法的数组。 如果未指定，则代理将响应路由上的所有 HTTP 方法。
    * _route_：必需 - 它定义了路由模板，控制代理将响应哪些请求 URL。 与在 HTTP 触发器中不同，此处没有默认值。
* **backendUri**：应当通过代理将请求发送到的后端资源的 URL。 此值可以模板化，如[修改后端请求](#modify-requests)中所述。 如果未包括此属性，则 Azure Functions 将以 HTTP 200 OK 进行响应。

> [!Note] 
> route 属性“Azure Functions 代理”不接受 Functions 主机配置的 routePrefix 属性。 如果希望包括诸如 /api 的前缀，必须将其包括在 route 属性中。



[Azure 门户]: https://portal.azure.com
[HTTP 触发器]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook#http-trigger
