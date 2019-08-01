---
title: 在 Linux 上使用自定义映像创建 Azure Functions
description: 了解如何创建在自定义 Linux 映像中运行的 Azure Functions。
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 06/25/2019
ms.topic: tutorial
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: 66effb095edf7927a38fca53b2ff317e9bad468e
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619612"
---
# <a name="create-a-function-on-linux-using-a-custom-image"></a>在 Linux 上使用自定义映像创建函数

使用 Azure Functions 可将函数托管在 Linux 上的自己的自定义容器中。 还可以[托管在默认的 Azure 应用服务容器中](functions-create-first-azure-function-azure-cli-linux.md)。 此功能要求使用 [Functions 2.x 运行时](functions-versions.md)。

本教程介绍如何将函数作为自定义 Docker 映像部署到 Azure。 需要自定义内置的容器映像时，此模式很有用。 当函数需要特定的语言版本，或者需要内置映像中未预配的特定依赖项或配置时，可以使用自定义映像。 Azure Functions 支持的基础映像可以在 [Azure Functions 基础映像存储库](https://hub.docker.com/_/microsoft-azure-functions-base)中找到。 [Python 支持](functions-reference-python.md)目前为预览版。

本教程逐步讲解如何使用 Azure Functions Core Tools 在自定义 Linux 映像中创建函数。 请将此映像发布到 Azure 中的函数应用，该应用是使用 Azure CLI 创建的。 稍后，将更新函数以连接到 Azure 队列存储。 还可以启用。  

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 使用 Core Tools 创建函数应用和 Dockerfile。
> * 使用 Docker 生成自定义映像。
> * 将自定义映像发布到容器注册表。
> * 创建 Azure 存储帐户。
> * 创建高级托管计划。
> * 从 Docker 中心部署函数应用。
> * 将应用程序设置添加到函数应用。
> * 启用持续部署。
> * 添加 Application Insights 监视。

支持在 Mac、Windows 或 Linux 计算机上执行以下步骤。 

## <a name="prerequisites"></a>先决条件

运行此示例之前，必须做好以下准备：

* 安装 [Azure Core Tools 2.x 版](functions-run-local.md#v2)。

* 安装 [Azure CLI]( /cli/azure/install-azure-cli)。 本文需要 Azure CLI 2.0 或更高版本。 运行 `az --version` 即可确定你拥有的版本。  
你也可使用 [Azure Cloud Shell](https://shell.azure.com/bash)。

* 一个有效的 Azure 订阅。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>创建本地函数应用项目

从命令行运行以下命令，以便在当前本地目录的 `MyFunctionProj` 文件夹中创建一个函数应用项目。

```bash
func init MyFunctionProj --docker
```

包括 `--docker` 选项时，会为项目生成 dockerfile。 此文件用于创建一个可在其中运行项目的自定义容器。 所用基础映像取决于所选辅助角色运行时语言。  

当系统提示时，请从下面的语言中选择一个辅助角色运行时：

* `dotnet`：创建一个 .NET Core 类库项目 (.csproj)。
* `node`：创建一个 JavaScript 项目。
* `python`：创建 Python 项目。

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

执行命令后，看到的内容如以下输出所示：

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing Dockerfile
```

使用以下命令导航到新的 `MyFunctionProj` 项目文件夹。

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

## <a name="build-the-image-from-the-docker-file"></a>从 Docker 文件生成映像

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

执行命令后，看到的内容如以下输出所示，在此示例中适用于 JavaScript 辅助角色运行时：

```bash
Sending build context to Docker daemon  17.41kB
Step 1/3 : FROM mcr.microsoft.com/azure-functions/node:2.0
2.0: Pulling from azure-functions/node
802b00ed6f79: Pull complete
44580ea7a636: Pull complete
73eebe8d57f9: Pull complete
3d82a67477c2: Pull complete
8bd51cd50290: Pull complete
7bd755353966: Pull complete
Digest: sha256:480e969821e9befe7c61dda353f63298f2c4b109e13032df5518e92540ea1d08
Status: Downloaded newer image for mcr.microsoft.com/azure-functions/node:2.0
 ---> 7c71671b838f
Step 2/3 : ENV AzureWebJobsScriptRoot=/home/site/wwwroot
 ---> Running in ed1e5809f0b7
Removing intermediate container ed1e5809f0b7
 ---> 39d9c341368a
Step 3/3 : COPY . /home/site/wwwroot
 ---> 5e196215935a
Successfully built 5e196215935a
Successfully tagged <docker-id>/mydockerimage:v1.0.0
```

### <a name="test-the-image-locally"></a>在本地测试映像
通过在本地容器中运行生成的 Docker 映像，验证该映像是否正常工作。 发布 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令并将映像的名称和标记传递给它。 确保使用 `-p` 参数指定端口。

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

使用本地 Docker 容器中运行的自定义映像，通过浏览到 <http://localhost:8080> 验证函数应用和容器是否正常运行。

![在本地测试函数应用。](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

可以选择再次测试函数，这一次请使用以下 URL 在本地容器中进行：

`http://localhost:8080/api/myhttptrigger?name=<yourname>`

验证容器中的函数应用后，请停止执行。 现在，可将自定义映像推送到 Docker 中心帐户。

## <a name="push-the-custom-image-to-docker-hub"></a>将自定义映像推送到 Docker 中心

注册表是一种托管映像、提供服务映像和容器服务的应用程序。 若要共享映像，必须先将其推送至注册表。 Docker 中心是一种用于 Docker 映像的注册表，使用该注册表，用户能够托管自己的存储库，无论是公共的还是专用的均可。

推送映像之前，必须先使用 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 命令登录到 Docker 中心。 请将 `<docker-id>` 替换为自己的帐户名，并在控制台中的提示符下键入密码。 有关其他 Docker 中心密码选项，请参阅 [docker login 命令的文档](https://docs.docker.com/engine/reference/commandline/login/)。

```bash
docker login --username <docker-id>
```

如果看到“登录成功”消息，说明登录成功。 登录后，使用 [docker push](https://docs.docker.com/engine/reference/commandline/push/) 命令将映像推送到 Docker 中心。

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

可通过检查命令的输出来验证推送是否成功。

```bash
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
24d81eb139bf: Pushed
fd9e998161c9: Mounted from <docker-id>/mydockerimage
e7796c35add2: Mounted from <docker-id>/mydockerimage
ae9a05b85848: Mounted from <docker-id>/mydockerimage
45c86e20670d: Mounted from <docker-id>/mydockerimage
v1.0.0: digest: sha256:be080d80770df71234eb893fbe4d... size: 1796
```

现在，可在 Azure 中将此映像用作新函数应用的部署源。

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-premium-plan"></a>创建高级计划

Linux 托管[专用（应用服务）计划](functions-scale.md#app-service-plan)和[高级计划](functions-premium-plan.md#features)支持的自定义 Functions 容器。 本教程使用高级计划，该计划可以根据需要进行缩放。 若要了解有关托管的详细信息，请参阅 [Azure Functions 托管计划比较](functions-scale.md)。

以下示例在“弹性高级”  定价层 (`--sku EP1`)、美国西部区域 (`-location WestUS`) 和 Linux 容器 (`--is-linux`) 中创建一个名为 `myPremiumPlan` 的高级计划。

```azurecli-interactive
az functionapp plan create --resource-group myResourceGroup --name myPremiumPlan \
--location WestUS --number-of-workers 1 --sku EP1 --is-linux
```

## <a name="create-and-deploy-the-custom-image"></a>创建并部署自定义映像

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
--settings AzureWebJobsDashboard=$storageConnectionString \
AzureWebJobsStorage=$storageConnectionString
```

> [!NOTE]
> 如果容器为专用，则还需设置以下应用程序设置  
> - DOCKER_REGISTRY_SERVER_USERNAME  
> - DOCKER_REGISTRY_SERVER_PASSWORD  
>
> 需停止函数应用，然后再将其启动，以便这些值生效

现在，可以测试 Azure 中的 Linux 上运行的函数。

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

## <a name="enable-continuous-deployment"></a>启用持续部署

使用容器的一大好处是，在注册表中更新容器时可以自动部署更新。 使用 [az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) 命令启用持续部署。

```azurecli-interactive
az functionapp deployment container config --enable-cd \
--query CI_CD_URL --output tsv \
--name <app_name> --resource-group myResourceGroup
```

此命令在启用持续部署后返回部署 Webhook URL。 也可使用 [az functionapp deployment container show-cd-url](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) 命令返回该 URL。 

复制部署 URL 并浏览到 DockerHub 存储库，选择“Webhook”选项卡，  键入 Webhook 的 **Webhook 名称**，将 URL 粘贴到“Webhook URL”中，  然后选择加号 ( **+** )。

![将 Webhook 添加到 DockerHub 存储库中](media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

设置 Webhook 以后，只要 DockerHub 中的链接映像进行了更新，函数应用就会下载并安装最新映像。

## <a name="enable-application-insights"></a>启用 Application Insights

监视函数执行的建议方法是将函数应用与 Azure Application Insights 集成。 在 Azure 门户中创建函数应用时，默认情况下会为你完成此集成。 但是，当你使用 Azure CLI 创建函数应用时，Azure 的函数应用中的集成并未完成。

若要为函数应用启用 Application Insights，请执行以下操作：

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

若要了解详细信息，请参阅[监视 Azure Functions](functions-monitoring.md)。

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 使用 Core Tools 创建函数应用和 Dockerfile。
> * 使用 Docker 生成自定义映像。
> * 将自定义映像发布到容器注册表。
> * 创建 Azure 存储帐户。
> * 创建一个 Linux 高级计划。
> * 从 Docker 中心部署函数应用。
> * 将应用程序设置添加到函数应用。
> * 启用持续部署。
> * 添加 Application Insights 监视。

> [!div class="nextstepaction"] 
> [详细了解用于将函数部署到 Azure 的选项](functions-deployment-technologies.md)
