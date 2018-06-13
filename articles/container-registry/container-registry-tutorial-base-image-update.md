---
title: 教程 - 使用 Azure 容器注册表生成在基础映像更新时自动化容器映像生成
description: 在此教程中，将介绍在更新基础映像时，如何配置生成任务以自动触发云中的容器映像生成。
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 531aeaacf0bd70521d70afb45d141fc3296ebb04
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
ms.locfileid: "34058108"
---
# <a name="tutorial-automate-image-builds-on-base-image-update-with-azure-container-registry-build"></a>教程：使用 Azure 容器注册表生成在基础映像更新时自动化映像生成

ACR 生成支持在容器基础映像更新时自动化的生成执行，例如在某个基础映像中修补 OS 或应用程序框架时。 在本教程中，将介绍当容器基础映像已推送到注册表时，如何在 ACR 生成中创建在云中触发生成的生成任务。

本教程（教程系列的最后一部分）的内容包括：

> [!div class="checklist"]
> * 生成基础映像
> * 创建应用程序映像生成任务
> * 更新基础映像以触发应用程序映像生成
> * 显示已触发的生成
> * 验证已更新的应用程序映像

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果想本地使用 Azure CLI，则必须已安装 Azure CLI 版本 2.0.32 或更高版本。 运行 `az --version` 即可查找版本。 如果需要安装或升级 CLI，请参阅[安装 Azure CLI 2.0][azure-cli]。

## <a name="prerequisites"></a>先决条件

### <a name="complete-previous-tutorials"></a>完成上一个教程

本教程假定你已完成本系列中前两个教程中的步骤，其中包括：

* 创建 Azure 容器注册表
* 创建示例存储库分支
* 克隆示例存储库
* 创建 GitHub 个人访问令牌

如果尚未完成以上步骤，请在继续之前先完成前两个教程步骤：

[使用 Azure 容器注册表生成在云中生成容器映像](container-registry-tutorial-quick-build.md)

[使用 Azure 容器注册表生成自动化容器映像生成](container-registry-tutorial-build-task.md)

### <a name="configure-environment"></a>配置环境

使用适用于环境的值填充这些 shell 环境变量。 这一步并非必须执行的步骤，但它能让在此教程中执行多个 Azure CLI 命令更容易。 如果没有填充这些环境变量，示例命令中出现时则必须手动替换每个值。

```azurecli-interactive
ACR_NAME=mycontainerregistry # The name of your Azure container registry
GIT_USER=gituser             # Your GitHub user account name
GIT_PAT=personalaccesstoken  # The PAT you generated in the second tutorial
```

## <a name="base-images"></a>基础映像

定义大部分容器映像的 Dockerfile 指定它所基于的父级映像，通常称为它的基础映像。 基础映像通常包含操作系统，例如：[Alpine Linux][base-alpine] 或 [Windows Nano Server][base-windows]，其余的容器层应用在这些操作系统上。 它们可能还包括应用程序框架，例如 [Node.js][base-node] 或 [.NET Core][base-dotnet]。

### <a name="base-image-updates"></a>基础映像更新

基础映像通常通过映像维护程序更新，以将 OS 或框架的新功能或改进添加进该映像中。 安全补丁是更新基础映像的另一常见原因。

基础映像更新时，将提示需要重新生成注册表中基于该映像的任何容器映像，以添加新功能和修补。 更新容器的基础映像时，ACR 生成能够自动生成映像。

### <a name="base-image-update-scenario"></a>基础映像更新方案

本教程将指导完成基础映像更新方案。 [代码示例][code-sample]包括两个 Dockerfile：一个应用程序映像和一个它指定为其基础的映像。 在后续部分中，你将创建一个 ACR 生成任务，它会在新版本基础映像推送到容器注册表时自动触发应用程序映像的生成。

[Dockerfile-app][dockerfile-app]：小型 Node.js Web 应用程序，它呈现一个静态 Web 页面，该页面显示该应用程序所基于的 Node.js 版本。 该版本字符串是模拟的，在该页面上显示了基础映像中定义的环境变量和 `NODE_VERSION` 的内容。

[Dockerfile-base][dockerfile-base]：`Dockerfile-app` 指定为其基础的映像。 它本身基于[节点][base-node]映像，并且包括 `NODE_VERSION` 环境变量。

在以下部分中，将会创建生成任务，在基础映像 Dockerfile 中更新 `NODE_VERSION` 值，并使用 ACR 生成来生成基础映像。 ACR 生成将新基础映像推送到注册表时，它会自动触发应用程序映像的生成。 可选择本地运行应用程序容器映像，在生成的映像中查看不同版本字符串。

## <a name="build-base-image"></a>生成基础映像

首先使用 ACR 生成的快速生成来生成基础映像。 如本系列[第一个教程](container-registry-tutorial-quick-build.md)所述，如果生成成功，则不仅生成映像，还会将其推送到容器注册表。

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

## <a name="create-build-task"></a>创建生成任务

接下来，使用 [az acr build-task create][az-acr-build-task-create] 创建生成任务：

```azurecli-interactive
az acr build-task create \
    --registry $ACR_NAME \
    --name buildhelloworld \
    --image helloworld:{{.Build.ID}} \
    --build-arg REGISTRY_NAME=$ACR_NAME.azurecr.io \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node \
    --file Dockerfile-app \
    --branch master \
    --git-access-token $GIT_PAT
```

该生成任务类似于[上一教程](container-registry-tutorial-build-task.md)中创建的任务。 将提交推送到 `--context` 指定的存储库时，生成任务指示 ACR 生成触发映像生成。

它的不同之处还在于它的行为，在其基础映像更新时，它还会触发映像生成。 `--file` 参数、[Dockerfile-app][dockerfile-app] 指定的 Dockerfile 支持从其基础映像所在的注册表指定映像。

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
```

运行生成任务时，ACR 生成检测到映像的依赖项。 如果 `FROM` 语句中指定的基础映像驻留在同一注册表中，则该基础映像将添加挂钩，以确保它的基础映像更新时会重新生成该映像。

> [!NOTE]
> 在预览版中，仅当基础映像和引用该基础映像的映像处于同一 Azure 容器注册表中时，ACR 生成才支持触发派生的映像生成。

## <a name="build-application-container"></a>生成应用程序容器

若要启用 ACR 生成以确定并跟踪容器映像的依赖项（包含其基础映像），首先必须触发其生成任务至少一次。

使用 [az acr build-task run][az-acr-build-task-run] 手动触发生成任务并生成应用程序映像：

```azurecli-interactive
az acr build-task run --registry $ACR_NAME --name buildhelloworld
```

生成完成后，如果还要完成以下可选步骤，请记下“生成 ID”（例如，“aa6”）。

### <a name="optional-run-application-container-locally"></a>可选：本地运行应用程序容器

如果在本地运行（而不是在 Cloud Shell）并且已安装 Docker，运行容器，查看呈现在 Web 浏览器中的应用程序，并重新生成其基础映像。 如果正在使用 Cloud Shell，可跳过此部分（Cloud Shell 不支持 `az acr login` 或 `docker run`）。

首先，通过 [az acr login][az-acr-login] 登录到容器注册表：

```azurecli
az acr login --name $ACR_NAME
```

然后，使用 `docker run` 在本地运行容器。 将 \<build-id\> 替换为上一步骤的输出（例如，“aa6”）中找到的生成 ID。

```azurecli
docker run -d -p 8080:80 $ACR_NAME.azurecr.io/helloworld:<build-id>
```

在浏览器中导航到 http://localhost:8080，应看见呈现在 Web 页面中的 Node.js 版本号，如下所示。 在稍后的步骤中，会通过向版本字符串中添加“a”来提升版本。

![浏览器中呈现示例应用程序的屏幕截图][base-update-01]

## <a name="list-builds"></a>生成列表

接下来，列出 ACR 生成已为注册表完成的生成：

```azurecli-interactive
az acr build-task list-builds --registry $ACR_NAME --output table
```

如果已完成之前的教程（并且没有删除注册表），应看到如下所示的输出。 记下生成编号和最后一次的生成 ID，以便在后续部分中更新基础映像后对比输出。

```console
$ az acr build-task list-builds --registry $ACR_NAME --output table
BUILD ID    TASK             PLATFORM    STATUS     TRIGGER     STARTED               DURATION
----------  ---------------  ----------  ---------  ----------  --------------------  ----------
aa6         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T20:00:12Z  00:00:50
aa5                          Linux       Succeeded  Manual      2018-05-10T19:57:35Z  00:00:55
aa4         buildhelloworld  Linux       Succeeded  Git Commit  2018-05-10T19:49:40Z  00:00:45
aa3         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T19:41:50Z  00:01:20
aa2         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T19:37:11Z  00:00:50
aa1                          Linux       Succeeded  Manual      2018-05-10T19:10:14Z  00:00:55
```

## <a name="update-base-image"></a>更新基础映像

在这里模拟基础映像中的框架补丁。 编辑“Dockerfile-base”，并在 `NODE_VERSION` 中定义的版本号后面添加一个“a”：

```Dockerfile
ENV NODE_VERSION 9.11.1a
```

在 ACR 生成中运行快速生成以生成修改后的基础映像。 请记下输出中的“生成 ID”。

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

生成完成并且 ACR 生成将新基础映像推送到注册表后，它会触发应用程序映像的生成。 之前创建的 ACR 生成任务触发应用程序映像生成可能需要一些时间，因为它必须检测新完成和推送的基础映像。

## <a name="list-builds"></a>生成列表

现在已更新基础映像，再次列出生成与之前的列表进行对比。 如果开始时输出没有区别，可定期运行命令以查看出现在列表中的新生成。

```azurecli-interactive
az acr build-task list-builds --registry $ACR_NAME --output table
```

输出如下所示。 最后执行的生成的触发器应为“映像更新”，指示生成任务是通过基础映像的快速生成启动的。

```console
$ az acr build-task list-builds --registry $ACR_NAME --output table
BUILD ID    TASK             PLATFORM    STATUS     TRIGGER       STARTED               DURATION
----------  ---------------  ----------  ---------  ------------  --------------------  ----------
aa8         buildhelloworld  Linux       Succeeded  Image Update  2018-05-10T20:09:52Z  00:00:45
aa7                          Linux       Succeeded  Manual        2018-05-10T20:09:17Z  00:00:40
aa6         buildhelloworld  Linux       Succeeded  Manual        2018-05-10T20:00:12Z  00:00:50
aa5                          Linux       Succeeded  Manual        2018-05-10T19:57:35Z  00:00:55
aa4         buildhelloworld  Linux       Succeeded  Git Commit    2018-05-10T19:49:40Z  00:00:45
aa3         buildhelloworld  Linux       Succeeded  Manual        2018-05-10T19:41:50Z  00:01:20
aa2         buildhelloworld  Linux       Succeeded  Manual        2018-05-10T19:37:11Z  00:00:50
aa1                          Linux       Succeeded  Manual        2018-05-10T19:10:14Z  00:00:55
```

如果想要执行以下可选步骤运行新生成的容器，以查看更新的版本号，请记下映像更新触发的生成的“生成 ID”值（在之前的输出中为“aa8”）。

### <a name="optional-run-newly-built-image"></a>可选：运行新生成的映像

如果正在本地运行（而不是在 Cloud Shell 中运行）并且已安装 Docker，则可在新应用程序映像的生成完成后运行它。 将 `<build-id>` 替换为上一步中获取的生成 ID。 如果正在使用 Cloud Shell，可跳过此部分（Cloud Shell 不支持 `docker run`）。

```bash
docker run -d -p 8081:80 $ACR_NAME.azurecr.io/helloworld:<build-id>
```

在浏览器中导航到 http://localhost:8081，应看见网页上显示有更新后的 Node.js 版本编号（带有“a”）：

![浏览器中呈现示例应用程序的屏幕截图][base-update-02]

请务必注意，使用新版本号更新“基础”映像，但是最后生成的“应用程序”映像显示新版本。 ACR 生成选取对基础映像的更改，并自动重新生成应用程序映像。

## <a name="clean-up-resources"></a>清理资源

若要删除本系列教程中创建的所有资源，包括容器注册表、容器实例、密钥保管库和服务主体，请运行以下命令：

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>后续步骤

在此教程中，介绍了在映像的基础映像更新后，如何使用生成任务自动触发容器映像生成。 现在，继续了解适用于容器注册表的身份验证。

> [!div class="nextstepaction"]
> [Azure 容器注册表中身份验证](container-registry-authentication.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[code-sample]: https://github.com/Azure-Samples/acr-build-helloworld-node
[dockerfile-app]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-app
[dockerfile-base]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-base

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build-run
[az-acr-build-task-create]: /cli/azure/acr#az-acr-build-task-create
[az-acr-build-task-run]: /cli/azure/acr#az-acr-build-task-run
[az-acr-login]: /cli/azure/acr#az-acr-login

<!-- IMAGES -->
[base-update-01]: ./media/container-registry-tutorial-base-image-update/base-update-01.png
[base-update-02]: ./media/container-registry-tutorial-base-image-update/base-update-02.png