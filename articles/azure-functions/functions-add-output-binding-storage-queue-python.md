---
title: 将 Azure 存储队列绑定添加到 Python 函数
description: 了解如何使用 Azure CLI 和 Functions Core Tools 将 Azure 存储队列输出绑定添加到 Python 函数。
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 04/24/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: 92ee9b0a8a0906bca31d7dcb1730c3464d0d6cbc
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839179"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>将 Azure 存储队列绑定添加到 Python 函数

无需编写自己的集成代码，即可使用 Azure Functions 将 Azure 服务和其他资源连接到函数。 这些绑定表示输入和输出，在函数定义中声明。  绑定中的数据作为参数提供给函数。 触发器是一种特殊类型的输入绑定。  尽管一个函数只有一个触发器，但它可以有多个输入和输出绑定。 有关详细信息，请参阅 [Azure Functions 触发器和绑定的概念](functions-triggers-bindings.md)。

本文介绍如何将[前一篇快速入门文章](functions-create-first-function-python.md)中创建的函数与 Azure 存储队列相集成。 添加到此函数的输出绑定会将 HTTP 请求中的数据写入到队列中的消息。

大多数绑定都需要一个存储的连接字符串，函数将使用该字符串来访问绑定的服务。 为便于建立此连接，请使用连同函数应用一起创建的存储帐户。 与此帐户建立的连接已存储在名为 `AzureWebJobsStorage` 的应用设置中。  

## <a name="prerequisites"></a>先决条件

在开始学习本文之前，请完成 [Python 快速入门第 1 部分](functions-create-first-function-python.md)中的步骤。

[!INCLUDE [functions-cloud-shell-note](../../includes/functions-cloud-shell-note.md)]

## <a name="download-the-function-app-settings"></a>下载函数应用设置

[!INCLUDE [functions-app-settings-download-local-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>启用扩展捆绑包

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

现在，可将存储输出绑定添加到项目。

## <a name="add-an-output-binding"></a>添加输出绑定

在 Functions 中，每种类型的绑定都需要一个 `direction`、`type`，以及要在 function.json 文件中定义的唯一 `name`。 定义这些属性的方式取决于函数应用的语言。

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

## <a name="add-code-that-uses-the-output-binding"></a>添加使用输出绑定的代码

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

使用输出绑定时，无需使用 Azure 存储 SDK 代码进行身份验证、获取队列引用或写入数据。 Functions 运行时和队列输出绑定将为你执行这些任务。

## <a name="run-the-function-locally"></a>在本地运行函数

如前所述，使用以下命令在本地启动 Functions 运行时：

```bash
func host start
```

> [!NOTE]  
> 由于在前一篇快速入门中，你已在 host.json 中启用扩展捆绑包，因此在启动期间已下载并安装[存储绑定扩展](functions-bindings-storage-blob.md#packages---functions-2x)以及其他 Microsoft 绑定扩展。

从运行时输出中复制 `HttpTrigger` 函数的 URL，将其粘贴到浏览器的地址栏中。 将查询字符串 `?name=<yourname>` 追加到此 URL 并运行请求。 与在前一篇文章中一样，浏览器中应会显示相同的响应。

这一次，输出绑定还会在存储帐户中创建名为 `outqueue` 的队列，并添加包含此字符串的消息。

接下来，使用 Azure CLI 查看新队列，并检查是否已添加了一条消息。 也可以使用 [Microsoft Azure 存储资源管理器][Azure Storage Explorer]或 [Azure 门户](https://portal.azure.com)查看队列。

### <a name="set-the-storage-account-connection"></a>设置存储帐户连接

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

### <a name="query-the-storage-queue"></a>查询存储队列

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

现在，可将更新的函数应用重新发布到 Azure。

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

同样，可以使用 cURL 或浏览器来测试已部署的函数。 与前面一样，请将查询字符串 `&name=<yourname>` 追加到 URL，如以下示例所示：

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

可以[检查存储队列消息](#query-the-storage-queue)，以再次确认输出绑定是否在队列中生成了新的消息。

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>后续步骤

现已更新 HTTP 触发的函数，使其将数据写入存储队列。 若要详细了解如何使用 Python 开发 Azure Functions，请参阅 [Azure Functions Python 开发人员指南](functions-reference-python.md)及 [Azure Functions 触发器和绑定](functions-triggers-bindings.md)。 有关 Python 中完整 Function 项目的示例，请参阅 [Python Functions 示例](/samples/browse/?products=azure-functions&languages=python)。 

接下来，应为函数应用启用 Application Insights 监视：

> [!div class="nextstepaction"]
> [启用 Application Insights 集成](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/