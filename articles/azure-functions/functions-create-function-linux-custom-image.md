---
title: 在 Linux 上使用自定义映像创建函数（预览）| Microsoft Docs
description: 了解如何创建在自定义 Linux 映像中运行的 Azure Functions。
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 10/19/2018
ms.topic: tutorial
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: aa3c72c7ff2aa5e25fbff9fc38c33fd2dda34ecd
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2018
ms.locfileid: "49985074"
---
# <a name="create-a-function-on-linux-using-a-custom-image-preview"></a>在 Linux 上使用自定义映像创建函数（预览）

使用 Azure Functions 可将函数托管在 Linux 上的自己的自定义容器中。 还可以[托管在默认的 Azure 应用服务容器中](functions-create-first-azure-function-azure-cli-linux.md)。 此功能目前为预览版，并且需要 [Functions 2.0 运行时](functions-versions.md)。

本教程介绍如何将函数作为自定义 Docker 映像部署到 Azure。 需要自定义内置的应用服务容器映像时，此模式很有用。 当函数需要特定的语言版本，或者需要内置映像中未预配的特定依赖项或配置时，可以使用自定义映像。

本教程逐步讲解如何使用 Azure Functions Core Tools 在自定义 Linux 映像中创建函数。 请将此映像发布到 Azure 中的函数应用，该应用是使用 Azure CLI 创建的。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 使用 Core Tools 创建函数应用和 Dockerfile。
> * 使用 Docker 生成自定义映像。
> * 将自定义映像发布到容器注册表。
> * 创建 Azure 存储帐户。
> * 创建 Linux 应用服务计划。
> * 从 Docker 中心部署函数应用。
> * 将应用程序设置添加到函数应用。

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

* `dotnet`：创建一个 .NET 类库项目 (.csproj)。
* `node`：创建一个 JavaScript 项目。

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

[!INCLUDE [functions-update-function-code](../../includes/functions-update-function-code.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

## <a name="build-the-image-from-the-docker-file"></a>从 Docker 文件生成映像

查看项目根文件夹中的 _Dockerfile_。 此文件描述在 Linux 上运行函数应用所需的环境。 以下示例是一个用于创建容器的 Dockerfile，该容器在 JavaScript (Node.js) 辅助角色运行时上运行函数应用： 

```docker
FROM mcr.microsoft.com/azure-functions/node:2.0

ENV AzureWebJobsScriptRoot=/home/site/wwwroot
COPY . /home/site/wwwroot
```

> [!NOTE]
> 在专用容器注册表中托管映像时，应使用 Dockerfile 中的 **ENV** 变量将连接设置添加到函数应用。 由于本教程无法保证使用的是专用注册表，因此连接设置是[在部署后使用 Azure CLI 添加的](#configure-the-function-app)，这是一种最佳安全做法。

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

## <a name="create-a-linux-app-service-plan"></a>创建 Linux 应用服务计划

消耗计划目前不支持在 Linux 上托管 Functions。 必须在 Linux 应用服务计划中托管 Linux 容器应用。 若要了解有关托管的详细信息，请参阅 [Azure Functions 托管计划比较](functions-scale.md)。

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-and-deploy-the-custom-image"></a>创建并部署自定义映像

函数应用托管函数的执行环境。 使用 [az functionapp create](/cli/azure/functionapp#az-functionapp-create) 命令基于 Docker 中心映像创建一个函数应用。

在以下命令中，请将 `<app_name>` 占位符替换成唯一函数应用名称，将 `<storage_name>` 替换为存储帐户名。 `<app_name>` 将用作 Function App 的默认 DNS 域，因此，该名称需要在 Azure 中的所有应用之间保持唯一。 如前所述，`<docker-id>` 是你的 Docker 帐户名称。

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myAppServicePlan --deployment-container-image-name <docker-id>/mydockerimage:v1.0.0
```

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

_deployment-container-image-name_ 参数表示 Docker 中心托管的、用于创建函数应用的映像。

## <a name="configure-the-function-app"></a>配置函数应用

该函数需要使用连接字符串连接到默认的存储帐户。 将自定义映像发布到专用容器帐户时，应该使用 [ENV 指令](https://docs.docker.com/engine/reference/builder/#env)或类似的命令将这些应用程序设置指定为 Dockerfile 中的环境变量。

在本例中，`<storage_account>` 是所创建的存储帐户的名称。 使用 [az storage account show-connection-string](/cli/azure/storage/account#show-connection-string) 命令获得连接字符串。 使用 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) 命令将这些应用程序设置添加到函数应用。

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <storage_account> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings AzureWebJobsDashboard=$storageConnectionString \
AzureWebJobsStorage=$storageConnectionString
```

现在，可以测试 Azure 中的 Linux 上运行的函数。

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 使用 Core Tools 创建函数应用和 Dockerfile。
> * 使用 Docker 生成自定义映像。
> * 将自定义映像发布到容器注册表。
> * 创建 Azure 存储帐户。
> * 创建 Linux 应用服务计划。
> * 从 Docker 中心部署函数应用。
> * 将应用程序设置添加到函数应用。

了解如何启用内置于核心应用服务平台中的持续集成功能。 可以配置函数应用，以便在更新 Docker 中心内的映像时重新部署容器。

> [!div class="nextstepaction"] 
> [使用用于容器的 Web 应用进行持续部署](../app-service/containers/app-service-linux-ci-cd.md)
