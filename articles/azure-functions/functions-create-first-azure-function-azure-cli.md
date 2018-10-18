---
title: 使用 Azure CLI 创建第一个函数
description: 了解如何使用 Azure CLI 和 Azure Functions Core Tools 创建第一个可无服务器执行的 Azure Function。
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 09/10/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: ef5459b2b31b67afe187612ffc1ab079a5045a8c
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114904"
---
# <a name="create-your-first-function-from-the-command-line"></a>通过命令行创建第一个函数

本快速入门主题逐步讲解如何使用命令行或终端创建第一个函数。 使用 Azure CLI 创建函数应用（托管函数的[无服务器](https://azure.microsoft.com/solutions/serverless/)基础结构）。 此函数代码项目通过 [Azure Functions Core Tools](functions-run-local.md) 从模板生成，该工具也用于将函数应用项目部署到 Azure。

可以使用 Mac、Windows 或 Linux 计算机执行以下步骤。

## <a name="prerequisites"></a>先决条件

运行此示例之前，必须做好以下准备：

+ 安装 [Azure Core Tools 2.x 版](functions-run-local.md#v2)。

+ 安装 [Azure CLI]( /cli/azure/install-azure-cli)。 本文需要 Azure CLI 2.0 或更高版本。 运行 `az --version` 即可确定你拥有的版本。 也可使用 [Azure Cloud Shell](https://shell.azure.com/bash)。

+ 一个有效的 Azure 订阅。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>创建本地函数应用项目

从命令行运行以下命令，以便在当前本地目录的 `MyFunctionProj` 文件夹中创建一个函数应用项目。 也会在 `MyFunctionProj` 中创建一个 GitHub 存储库。

```bash
func init MyFunctionProj
```

当系统提示时，请使用箭头键从下面的语言选项中选择一个辅助角色运行时：

+ `dotnet`：创建一个 .NET 类库项目 (.csproj)。
+ `node`：创建一个 JavaScript 项目。

执行命令后，看到的内容如以下输出所示：

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Initialized empty Git repository in C:/functions/MyFunctionProj/.git/
```

## <a name="create-a-function"></a>创建函数

以下命令导航到新项目，并创建一个名为 `MyHtpTrigger` 的 HTTP 触发的函数。

```bash
cd MyFunctionProj
func new --name MyHttpTrigger --template "HttpTrigger"
```

执行命令后，看到的内容如以下输出所示，是一个 JavaScript 函数：

```output
Writing C:\functions\MyFunctionProj\MyHttpTrigger\index.js
Writing C:\functions\MyFunctionProj\MyHttpTrigger\sample.dat
Writing C:\functions\MyFunctionProj\MyHttpTrigger\function.json
```

## <a name="edit-the-function"></a>编辑此函数

默认情况下，模板会创建一个函数，该函数在进行请求时需要功能键。 为了便于在 Azure 中测试此函数，需更新此函数，使之允许匿名访问。 进行此更改的方式取决于函数项目语言。

### <a name="c"></a>C\#

打开 MyHttpTrigger.cs 代码文件（你的新函数），并将函数定义中的 **AuthorizationLevel** 属性更新为值 `anonymous`，然后保存所做的更改。

```csharp
[FunctionName("MyHttpTrigger")]
        public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous, 
            "get", "post", Route = null)]HttpRequest req, ILogger log)
```

### <a name="javascript"></a>JavaScript

打开新函数的 function.json 文件（在文本编辑器中打开），将 **bindings.httpTrigger** 中的 **authLevel** 属性更新为 `anonymous`，然后保存所做的更改。

```json
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
```

现在可以在 Azure 中调用此函数，不需提供功能键。 在本地运行时，从不需要功能键。

## <a name="run-the-function-locally"></a>在本地运行函数

以下命令用于启动函数应用。 此应用使用 Azure 中的 Azure Functions 运行时来运行。

```bash
func host start --build
```

若要编译 C# 项目，`--build` 选项是必需的。 不需对 JavaScript 项目使用此选项。

Functions 主机启动时，会写入类似于以下输出的内容。为了可读性，这些输出已经被截断：

```output

                  %%%%%%
                 %%%%%%
            @   %%%%%%    @
          @@   %%%%%%      @@
       @@@    %%%%%%%%%%%    @@@
     @@      %%%%%%%%%%        @@
       @@         %%%%       @@
         @@      %%%       @@
           @@    %%      @@
                %%
                %

...

Content root path: C:\functions\MyFunctionProj
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

...

Http Functions:

        HttpTrigger: http://localhost:7071/api/HttpTrigger

[8/27/2018 10:38:27 PM] Host started (29486ms)
[8/27/2018 10:38:27 PM] Job host started
```

从运行时输出中复制 `HTTPTrigger` 函数的 URL，将其粘贴到浏览器的地址栏中。 将查询字符串 `?name=<yourname>` 追加到此 URL 并执行请求。 下面演示浏览器中本地函数返回的对 GET 请求的响应：

![在浏览器中进行本地测试](./media/functions-create-first-azure-function-azure-cli/functions-test-local-browser.png)

在本地运行函数以后，即可在 Azure 中创建函数应用和其他必需的资源。

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app"></a>创建函数应用

必须使用 Function App 托管函数的执行。 Function App 提供一个环境，以便在不使用服务器的情况下执行函数代码。 它可让你将函数分组为一个逻辑单元，以便更轻松地管理、部署和共享资源。 使用 [az functionapp create](/cli/azure/functionapp#az-functionapp-create) 命令创建 Function App。 

在以下命令中，请将 `<app_name>` 占位符替换成唯一函数应用名称，将 `<storage_name>` 替换为存储帐户名。 `<app_name>` 将用作 Function App 的默认 DNS 域，因此，该名称需要在 Azure 中的所有应用之间保持唯一。 _deployment-source-url_ 参数是 GitHub 中包含“Hello World”HTTP 触发函数的示例存储库。

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westeurope \
--name <app_name> --storage-account  <storage_name>  
```

设置 _consumption-plan-location_ 参数意味着函数应用将在消耗托管计划中托管。 在此无服务器计划中，将根据函数需要动态添加资源，你只在函数运行时付费。 有关详细信息，请参阅[选择适当的托管计划](functions-scale.md)。

创建 Function App 后，Azure CLI 会显示类似于以下示例的信息：

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

## <a name="configure-the-function-app"></a>配置函数应用

Core Tools 2.x 版创建项目时使用的模板适用于 Azure Functions 2.x 运行时。 因此，需确保 2.x 版运行时在 Azure 中使用。 将 `FUNCTIONS_WORKER_RUNTIME` 应用程序设置为 `~2` 会将函数应用固定到最新的 2.x 版本。 使用 [az functionapp config appsettings set](https://docs.microsoft.com/cli/azure/functionapp/config/appsettings#set) 命令设置应用程序设置。

在以下 Azure CLI 命令中，<app_name> 是函数应用的名称。

```azurecli-interactive
az functionapp config appsettings set --name <app_name> \
--resource-group myResourceGroup \
--settings FUNCTIONS_WORKER_RUNTIME=~2
```

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]
