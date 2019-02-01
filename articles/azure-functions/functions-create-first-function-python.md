---
title: 在 Azure 中创建你的第一个 Python 函数
description: 了解如何使用 Azure Functions Core Tools 和 Azure CLI 在 Azure 中创建你的第一个 Python 函数。
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 08/29/2018
ms.topic: quickstart
ms.service: functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: 631fc4e613311df9386084408ead625f6d018151
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2019
ms.locfileid: "54911884"
---
# <a name="create-your-first-python-function-in-azure-preview"></a>在 Azure 中创建你的第一个 Python 函数（预览版）

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

本快速入门文章指导你使用 Azure CLI 创建你的第一个在 Linux 上运行的[无服务器](https://azure.com/serverless) Python 函数应用。 函数代码在本地创建，然后使用 [Azure Functions Core Tools](functions-run-local.md) 部署到 Azure。 若要了解有关在 Linux 上运行函数应用的预览版注意事项的更多信息，请参阅[此 Linux 上的 Functions 文章](https://aka.ms/funclinux)。

支持在 Mac、Windows 或 Linux 计算机上执行以下步骤。

## <a name="prerequisites"></a>先决条件

若要在本地进行构建和测试，你需要：

+ 安装 [Python 3.6](https://www.python.org/downloads/)。

+ 安装 [Azure Functions Core Tools](functions-run-local.md#v2) 2.2.70 或更高版本（需要 .NET Core 2.x SDK）。

若要在 Azure 中发布和运行：

+ 安装 [Azure CLI]( /cli/azure/install-azure-cli) 版本 2.x 或更高版本。

+ 需要一个活动 Azure 订阅。
  [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment"></a>创建并激活虚拟环境

若要创建 Functions 项目，你需要在 Python 3.6 虚拟环境中工作。 运行以下命令来创建并激活一个名为 `.env` 的虚拟环境。

```bash
# In Bash
python3.6 -m venv .env
source .env/bin/activate

# In PowerShell
py -3.6 -m venv .env
.env\scripts\activate
```

## <a name="create-a-local-functions-project"></a>创建本地 Functions 项目

现在可以创建一个本地 Functions 项目。 此目录相当于 Azure 中的一个函数应用。 它可以包含共享同一本地和托管配置的多个函数。

在终端窗口中或从命令提示符下，运行以下命令：

```bash
func init MyFunctionProj
```

选取 **python** 作为所需的运行时。

```output
Select a worker runtime:
1. dotnet
2. node
3. python
```

可以看到类似于以下输出的内容。

```output
Installing wheel package
Installing azure-functions package
Installing azure-functions-worker package
Running pip freeze
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing /MyFunctionProj/.vscode/extensions.json
```

创建了一个名为 _MyFunctionProj_ 的新文件夹。 若要继续，将目录更改到此文件夹。

```bash
cd MyFunctionProj
```

## <a name="create-a-function"></a>创建函数

若要创建函数，请运行以下命令：

```bash
func new
```

选择 `HTTP Trigger` 作为模板并提供 `HttpTrigger` 作为**函数名称**。

```output
Select a template:
1. Blob trigger
2. Cosmos DB trigger
3. Event Grid trigger
4. Event Hub trigger
5. HTTP trigger
6. Queue trigger
7. Service Bus Queue trigger
8. Service Bus Topic trigger
9. Timer trigger

Choose option: 5
Function name: HttpTrigger
```

可以看到类似于以下输出的内容。

```output
Writing /MyFunctionProj/HttpTrigger/sample.dat
Writing /MyFunctionProj/HttpTrigger/__init__.py
Writing /MyFunctionProj/HttpTrigger/function.json
The function "HttpTrigger" was created successfully from the "HTTP trigger" template.
```

创建了一个名为 _HttpTrigger_ 的子文件夹。 其中包含 `__init__.py`（这是主脚本文件）和 `function.json` 文件（它描述了函数使用的触发器和绑定）。 若要详细了解编程模型，可以参考 [Azure Functions Python 开发人员指南](functions-reference-python.md)。

## <a name="run-the-function-locally"></a>在本地运行函数

使用以下命令在本地运行 Functions 主机。

```bash
func host start
```

当 Functions 主机启动时，它会输出 HTTP 触发的函数的 URL。 （请注意，为了提高可读性，已截断了整个输出。）

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
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.
...

Http Functions:

        HttpTrigger: http://localhost:7071/api/HttpTrigger
```

从输出中复制函数的 URL，将其粘贴到浏览器的地址栏中。 将查询字符串 `?name=<yourname>` 追加到此 URL 并执行请求。

    http://localhost:7071/api/HttpTrigger?name=<yourname>

下面的屏幕截图显示了当从浏览器触发该函数时来自函数的响应：

![测试](./media/functions-create-first-function-python/function-test-local-browser.png)

现在，可以创建函数应用以及发布到 Azure 时所需的其他资源了。

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-function-app-in-azure"></a>在 Azure 中创建 Linux 函数应用

函数应用提供了用于执行函数代码的环境。 可以使用它将函数分组为一个逻辑单元，以便更轻松地管理、部署和共享资源。 使用 [az functionapp create](/cli/azure/functionapp#az_functionapp_create) 命令创建**在 Linux 上运行的 Python 函数应用**。

运行以下命令，并使用唯一的函数应用名称替换 `<app_name>` 占位符，使用存储帐户名称替换 `<storage_name>`。 `<app_name>` 也是函数应用的默认 DNS 域。 此名称在 Azure 的所有应用中必须独一无二。

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <app_name> --storage-account  <storage_name>
```

> [!NOTE]
> 如果现有资源组名为 `myResourceGroup` 且其中包含非 Linux 应用服务应用，则必须使用另一资源组。 不能在同一资源组中托管 Windows 和 Linux 应用。  

创建函数应用后，你会看到以下消息：

```output
Your serverless Linux function app 'myfunctionapp' has been successfully created.
To active this function app, publish your app content using Azure Functions Core Tools or the Azure portal.
```

现在，可以将本地函数项目发布到 Azure 中的函数应用了。

## <a name="deploy-the-function-app-project-to-azure"></a>将函数应用项目部署到 Azure

使用 Azure Functions Core Tools，运行以下命令。 将 `<app_name>` 替换为上一步中你的应用的名称。

```bash
func azure functionapp publish <app_name>
```

你将看到如下输出，为了提高可读性，已经截断了这些输出。

```output
Getting site publishing info...

...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
```

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>后续步骤

详细了解如何使用 Python 开发 Azure 函数。

> [!div class="nextstepaction"]
> [Azure Functions Python 开发人员指南](functions-reference-python.md)
> [Azure Functions 触发器和绑定](functions-triggers-bindings.md)
