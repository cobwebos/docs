---
title: 在 Linux 上使用自定义映像创建 Azure Functions
description: 了解如何创建在自定义 Linux 映像中运行的 Azure Functions。
ms.date: 09/27/2019
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 5a7fbecca2dc7585ff7110d53deccbbbbf23087c
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551482"
---
# <a name="create-a-function-on-linux-using-a-custom-image"></a>在 Linux 上使用自定义映像创建函数

使用 Azure Functions 可将函数托管在 Linux 上的自己的自定义容器中。 还可以[托管在默认的 Azure 应用服务容器中](functions-create-first-azure-function-azure-cli-linux.md)。 此功能要求使用 [Functions 2.x 运行时](functions-versions.md)。

本教程介绍如何将函数作为自定义 Docker 映像部署到 Azure。 需要自定义内置的容器映像时，此模式很有用。 当函数需要特定的语言版本，或者需要内置映像中未预配的特定依赖项或配置时，可以使用自定义映像。 Azure Functions 支持的基础映像可以在 [Azure Functions 基础映像存储库](https://hub.docker.com/_/microsoft-azure-functions-base)中找到。 

本教程逐步讲解如何使用 Azure Functions Core Tools 在自定义 Linux 映像中创建函数。 请将此映像发布到 Azure 中的函数应用，该应用是使用 Azure CLI 创建的。 稍后，将更新函数以连接到 Azure 队列存储。 还可以启用。  

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 使用 Core Tools 创建函数应用和 Dockerfile。
> * 使用 Docker 生成自定义映像。
> * 将自定义映像发布到容器注册表。
> * 创建 Azure 存储帐户。
> * 创建高级托管计划。
> * 从 Docker 中心部署函数应用。
> * 将应用程序设置添加到函数应用。
> * 启用持续部署。
> * 与容器建立 SSH 连接。
> * 添加队列存储输出绑定。 

支持在 Mac、Windows 或 Linux 计算机上执行以下步骤。 

## <a name="prerequisites"></a>必备条件

运行此示例之前，必须做好以下准备：

* 安装 [Azure Core Tools 2.x 版](functions-run-local.md#v2)。

* 安装 [Azure CLI]( /cli/azure/install-azure-cli)。 本文需要 Azure CLI 2.0 或更高版本。 运行 `az --version` 即可确定你拥有的版本。  
你也可使用 [Azure Cloud Shell](https://shell.azure.com/bash)。

* 一个有效的 Azure 订阅。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-cloud-shell-note](../../includes/functions-cloud-shell-note.md)]

## <a name="create-the-local-project"></a>创建本地项目

从命令行运行以下命令，以便在当前本地目录的 `MyFunctionProj` 文件夹中创建一个函数应用项目。 对于 Python 项目，你[必须在虚拟环境中运行](functions-create-first-function-python.md#create-and-activate-a-virtual-environment)。

```bash
func init MyFunctionProj --docker
```

包括 `--docker` 选项时，会为项目生成 dockerfile。 此文件用于创建一个可在其中运行项目的自定义容器。 所用基础映像取决于所选辅助角色运行时语言。  

当系统提示时，请从下面的语言中选择一个辅助角色运行时：

* `dotnet`：创建一个 .NET Core 类库项目 (.csproj)。
* `node`：创建一个 JavaScript 项目。
* `python`：创建 Python 项目。  

使用以下命令导航到新的 `MyFunctionProj` 项目文件夹。

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

## <a name="build-from-the-docker-file"></a>从 Docker 文件生成

查看项目根文件夹中的 _Dockerfile_。 此文件描述在 Linux 上运行函数应用所需的环境。 以下示例是一个用于创建容器的 Dockerfile，该容器在 JavaScript (Node.js) 辅助角色运行时上运行函数应用： 

```Dockerfile
FROM mcr.microsoft.com/azure-functions/node:2.0

ENV AzureWebJobsScriptRoot=/home/site/wwwroot
COPY . /home/site/wwwroot
```

> [!NOTE]
> Azure Functions 支持的基础映像的完整列表可以在 [Azure Functions 基础映像页](https://hub.docker.com/_/microsoft-azure-functions-base)中找到。

### <a name="run-the-build-command"></a>运行 `build` 命令

在根文件夹中运行 [docker build](https://docs.docker.com/engine/reference/commandline/build/) 命令，并提供名称 `mydockerimage` 和标记 `v1.0.0`。 将 `<docker-id>` 替换为 Docker 中心帐户 ID。 此命令为容器生成 Docker 映像。

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

该命令完成后，可在本地运行新容器。

### <a name="run-the-image-locally"></a>在本地运行映像
通过在本地容器中运行生成的 Docker 映像，验证该映像是否正常工作。 发布 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令并将映像的名称和标记传递给它。 确保使用 `-p` 参数指定端口。

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

使用本地 Docker 容器中运行的自定义映像，通过浏览到 <http://localhost:8080> 验证函数应用和容器是否正常运行。

![在本地运行函数应用。](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

> [!NOTE]
> 此时，尝试调用特定 HTTP 函数时，会收到 HTTP 401 错误响应。 这是因为函数在本地容器中的运行方式与在 Azure 中的运行方式相同，这意味着需要功能键。 由于尚未将容器发布到函数应用，因此没有可用的功能键。 稍后你将看到，当你使用核心工具发布容器时，将显示功能键。 如果要测试在本地容器中运行的函数，可以将[授权密钥](functions-bindings-http-webhook.md#authorization-keys)更改为 `anonymous`。 

验证容器中的函数应用后，请停止执行。 现在，可将自定义映像推送到 Docker 中心帐户。

## <a name="push-to-docker-hub"></a>推送到 Docker Hub

注册表是一种托管映像、提供服务映像和容器服务的应用程序。 若要共享映像，必须先将其推送至注册表。 Docker 中心是一种用于 Docker 映像的注册表，使用该注册表，用户能够托管自己的存储库，无论是公共的还是专用的均可。

推送映像之前，必须先使用 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 命令登录到 Docker 中心。 请将 `<docker-id>` 替换为自己的帐户名，并在控制台中的提示符下键入密码。 有关其他 Docker 中心密码选项，请参阅 [docker login 命令的文档](https://docs.docker.com/engine/reference/commandline/login/)。

```bash
docker login --username <docker-id>
```

如果看到“登录成功”消息，说明登录成功。 登录后，使用 [docker push](https://docs.docker.com/engine/reference/commandline/push/) 命令将映像推送到 Docker 中心。

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

推送成功后，可以在 Azure 中将此映像用作新函数应用的部署源。

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-premium-plan"></a>创建高级计划

Linux 托管[专用（应用服务）计划](functions-scale.md#app-service-plan)和[高级计划](functions-premium-plan.md#features)支持的自定义 Functions 容器。 本教程使用高级计划，该计划可以根据需要进行缩放。 若要了解有关托管的详细信息，请参阅 [Azure Functions 托管计划比较](functions-scale.md)。

以下示例在“弹性高级”  定价层 (`--sku EP1`)、美国西部区域 (`-location WestUS`) 和 Linux 容器 (`--is-linux`) 中创建一个名为 `myPremiumPlan` 的高级计划。

```azurecli-interactive
az functionapp plan create --resource-group myResourceGroup --name myPremiumPlan \
--location WestUS --number-of-workers 1 --sku EP1 --is-linux
```

## <a name="create-an-app-from-the-image"></a>从映像创建应用

函数应用管理托管计划中函数的执行。 使用 [az functionapp create](/cli/azure/functionapp#az-functionapp-create) 命令基于 Docker 中心映像创建一个函数应用。

在以下命令中，请将 `<app_name>` 占位符替换成唯一函数应用名称，将 `<storage_name>` 替换为存储帐户名。 `<app_name>` 将用作 Function App 的默认 DNS 域，因此，该名称需要在 Azure 中的所有应用之间保持唯一。 如前所述，`<docker-id>` 是你的 Docker 帐户名称。

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myPremiumPlan --deployment-container-image-name <docker-id>/mydockerimage:v1.0.0
```

_deployment-container-image-name_ 参数表示 Docker 中心托管的、用于创建函数应用的映像。 若要查看用于部署的映像的相关信息，请使用 [az functionapp config container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) 命令。 若要从另一映像进行部署，请使用 [az functionapp config container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) 命令。

## <a name="configure-the-function-app"></a>配置函数应用

该函数需要使用连接字符串连接到默认的存储帐户。 将自定义映像发布到专用容器帐户时，应该使用 [ENV 指令](https://docs.docker.com/engine/reference/builder/#env)或类似的命令将这些应用程序设置指定为 Dockerfile 中的环境变量。

在本例中，`<storage_name>` 是所创建的存储帐户的名称。 使用 [az storage account show-connection-string](/cli/azure/storage/account) 命令获得连接字符串。 使用 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) 命令将这些应用程序设置添加到函数应用。

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <storage_name> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <app_name> \
--resource-group myResourceGroup \
--settings AzureWebJobsStorage=$storageConnectionString
```

> [!NOTE]
> 如果容器为专用，则还需设置以下应用程序设置  
> - DOCKER_REGISTRY_SERVER_USERNAME  
> - DOCKER_REGISTRY_SERVER_PASSWORD  
>
> 需停止函数应用，然后再将其启动，以便这些值生效

## <a name="verify-your-functions"></a>验证函数

<!-- we should replace this with a CLI or API-based approach, when we get something better than REST -->

调用终结点时，创建的 HTTP 触发的函数需要[函数密钥](functions-bindings-http-webhook.md#authorization-keys)。 目前，获取函数 URL（包括密钥）的最简单方法是在 [Azure 门户]中获取。 

> [!TIP]
> 也可以使用[密钥管理 API](https://github.com/Azure/azure-functions-host/wiki/Key-management-API) 获取函数密钥，但这需要提供[用于身份验证的持有者令牌](/cli/azure/account#az-account-get-access-token)。

在 [Azure 门户]中找到新的函数应用，方法是在页面顶部的“搜索”框中键入函数应用名称，然后选择“应用服务”资源。  

选择“MyHttpTrigger”函数，然后选择“</> 获取函数 URL” > “默认(函数密钥)” > “复制”。    

![从 Azure 门户复制函数 URL](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key.png)

在此 URL 中，函数密钥是 `code` 查询参数。 

> [!NOTE]  
> 由于函数应用将部署为容器，因此无法在门户中对函数代码进行更改。 必须改为更新本地容器中的项目，然后将其重新发布到 Azure。

将函数 URL 粘贴到浏览器的地址栏中。 将查询字符串值 `&name=<yourname>` 添加到该 URL 尾部，然后按键盘上的 `Enter` 键来执行请求。 此时会看到函数返回的响应显示在浏览器中。

以下示例显示了浏览器中的响应：

![浏览器中的函数响应。](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

请求 URL 包含通过 HTTP 访问函数默认所需的密钥。 

## <a name="enable-continuous-deployment"></a>启用持续部署

使用容器的好处之一是能够为持续部署提供支持。 当容器在注册表中更新后，可以使用 Functions 来自动部署更新。 使用 [az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) 命令启用持续部署。

```azurecli-interactive
az functionapp deployment container config --enable-cd \
--query CI_CD_URL --output tsv \
--name <app_name> --resource-group myResourceGroup
```

此命令在启用持续部署后返回部署 Webhook URL。 也可使用 [az functionapp deployment container show-cd-url](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) 命令返回该 URL。 

复制部署 URL 并浏览到 DockerHub 存储库，选择“Webhook”选项卡，  键入 Webhook 的 **Webhook 名称**，将 URL 粘贴到“Webhook URL”中，  然后选择加号 ( **+** )。

![将 Webhook 添加到 DockerHub 存储库中](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

设置 Webhook 以后，只要 DockerHub 中的链接映像进行了更新，函数应用就会下载并安装最新映像。

## <a name="enable-ssh-connections"></a>启用 SSH 连接

SSH 实现容器和客户端之间的安全通信。 启用 SSH 后，可以使用应用服务高级工具 (Kudu) 连接到容器。 为了便于使用 SSH 连接到容器，Functions 提供了已启用 SSH 的基础映像。 

### <a name="change-the-base-image"></a>更改基础映像

在 dockerfile 中，将字符串 `-appservice` 追加到 `FROM` 指令中的基础映像。在 JavaScript 项目中，更新的内容如下所示。

```docker
FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
```

在两个基础映像中做出修改后，可以通过 SSH 连接到容器。 [此应用服务教程](../app-service/containers/tutorial-custom-docker-image.md#enable-ssh-connections)详细描述了这些差异。

### <a name="rebuild-and-redeploy-the-image"></a>重新生成并重新部署映像

在根文件夹中，根据如前所述再次运行 [docker build](https://docs.docker.com/engine/reference/commandline/build/) 命令（请将 `<docker-id>` 替换为你的 Docker Hub 帐户 ID）。 

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

将更新的映像推回到 Docker Hub。

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

更新的映像将重新部署到函数应用。

### <a name="connect-to-your-container-in-azure"></a>连接到 Azure 中的容器

在浏览器中，导航到函数应用容器的以下高级工具 (Kudu) `scm.` 终结点（请将 `<app_name>` 替换为函数应用的名称）。

```
https://<app_name>.scm.azurewebsites.net/
```

登录到你的 Azure 帐户，然后选择“SSH”选项卡来与容器建立 SSH 连接。 

建立连接后，运行 `top` 命令查看当前正在运行的进程。 

![在 SSH 会话中运行的 Linux top 命令。](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-queue-storage"></a>写入队列存储

无需编写自己的集成代码，即可使用 Functions 将 Azure 服务和其他资源连接到函数。 这些绑定表示输入和输出，在函数定义中声明。  绑定中的数据作为参数提供给函数。 触发器是一种特殊类型的输入绑定。  尽管一个函数只有一个触发器，但它可以有多个输入和输出绑定。 有关详细信息，请参阅 [Azure Functions 触发器和绑定的概念](functions-triggers-bindings.md)。

本部分介绍如何将函数与 Azure 存储队列集成。 添加到此函数的输出绑定会将 HTTP 请求中的数据写入到队列中的消息。

### <a name="download-the-function-app-settings"></a>下载函数应用设置

[!INCLUDE [functions-app-settings-download-local-cli](../../includes/functions-app-settings-download-local-cli.md)]

### <a name="enable-extension-bundles"></a>启用扩展捆绑包

由于你使用队列存储输出绑定，因此在运行项目之前，必须安装存储绑定扩展。 


# <a name="javascript--pythontabnodejspython"></a>[JavaScript/Python](#tab/nodejs+python)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

除了 HTTP 和计时器触发器，绑定将实现为扩展包。 在终端窗口中运行以下 [dotnet add package](/dotnet/core/tools/dotnet-add-package) 命令，将存储扩展包添加到项目中。

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

> [!TIP]
> 使用 Visual Studio 时，还可以使用 NuGet 包管理器来添加此包。

---

现在，可将存储输出绑定添加到项目。

### <a name="add-an-output-binding"></a>添加输出绑定

在 Functions 中，每种类型的绑定都需要一个 `direction`、`type`，以及要在 function.json 文件中定义的唯一 `name`。 定义这些属性的方式取决于函数应用的语言。

# <a name="javascript--pythontabnodejspython"></a>[JavaScript/Python](#tab/nodejs+python)

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

---

### <a name="add-code-that-uses-the-output-binding"></a>添加使用输出绑定的代码

定义绑定后，可以使用绑定的 `name`，将其作为函数签名中的属性进行访问。 使用输出绑定时，无需使用 Azure 存储 SDK 代码进行身份验证、获取队列引用或写入数据。 Functions 运行时和队列输出绑定将为你执行这些任务。

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]

# <a name="pythontabpython"></a>[Python](#tab/python)

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

---

### <a name="update-the-hosted-container"></a>更新托管容器

在根文件夹中再次运行 [docker build](https://docs.docker.com/engine/reference/commandline/build/) 命令，但这次请将标记中的版本更新为 `v1.0.2`。 如前所述，将 `<docker-id>` 替换为你的 Docker Hub 帐户 ID。 

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

将更新的映像推回到存储库。

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

### <a name="verify-the-updates-in-azure"></a>验证 Azure 中的更新

在浏览器中使用前面所述的同一 URL 来触发函数。 应会看到相同的响应。 但这一次，作为 `name` 参数传递的字符串已写入到 `outqueue` 存储队列。

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>后续步骤

成功将自定义容器部署到 Azure 中的函数应用后，接下来请考虑阅读有关以下主题的详细信息：

+ [监视函数](functions-monitoring.md)
+ [缩放和托管选项](functions-scale.md)
+ [基于 Kubernetes 的无服务器托管](functions-kubernetes-keda.md)

[Azure 门户]: https://portal.azure.com
