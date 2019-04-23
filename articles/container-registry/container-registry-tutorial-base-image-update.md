---
title: 教程 - 在基础映像更新时自动化容器映像生成 - Azure 容器注册表任务
description: 本教程介绍在更新基础映像时，如何配置 Azure 容器注册表任务以自动触发云中的容器映像生成。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: a5d89051ef479cf9d87ca8f921e05c6d0be12b8c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "58892171"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-an-azure-container-registry"></a>教程：在 Azure 容器注册表中更新基础映像时自动化容器映像生成 

ACR 任务支持在容器基础映像更新时自动化的生成执行，例如在某个基础映像中修补 OS 或应用程序框架时。 本教程介绍当容器基础映像已推送到注册表时，如何在 ACR 任务中创建在云中触发生成的任务。

本教程（教程系列的最后一部分）的内容包括：

> [!div class="checklist"]
> * 生成基础映像
> * 创建应用程序映像生成任务
> * 更新基础映像以触发应用程序映像任务
> * 显示已触发的任务
> * 验证已更新的应用程序映像

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果想本地使用 Azure CLI，则必须已安装 Azure CLI 版本 **2.0.46** 或更高版本。 运行 `az --version` 即可查找版本。 如果需要安装或升级 CLI，请参阅[安装 Azure CLI][azure-cli]。

## <a name="prerequisites"></a>先决条件

### <a name="complete-the-previous-tutorials"></a>完成前一篇教程

本教程假定你已完成本系列中前两个教程中的步骤，其中包括：

* 创建 Azure 容器注册表
* 创建示例存储库分支
* 克隆示例存储库
* 创建 GitHub 个人访问令牌

如果尚未完成以上步骤，请在继续之前先完成前两个教程步骤：

[使用 Azure 容器注册表任务在云中生成容器映像](container-registry-tutorial-quick-task.md)

[使用 Azure 容器注册表任务自动化容器映像生成](container-registry-tutorial-build-task.md)

### <a name="configure-the-environment"></a>配置环境

使用适用于环境的值填充这些 shell 环境变量。 此步骤并非必须执行的步骤，但它能让在此教程中执行多个 Azure CLI 命令更容易。 如果没有填充这些环境变量，示例命令中出现时则必须手动替换每个值。

```azurecli-interactive
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the second tutorial
```

## <a name="base-images"></a>基础映像

定义大部分容器映像的 Dockerfile 指定它所基于的父级映像，通常称为它的基础映像。 基础映像通常包含操作系统，例如：[Alpine Linux][base-alpine] 或 [Windows Nano Server][base-windows]，其余的容器层应用在这些操作系统上。 它们可能还包括应用程序框架，例如 [Node.js][base-node] 或 [.NET Core][base-dotnet]。

### <a name="base-image-updates"></a>基础映像更新

基础映像通常通过映像维护程序更新，以将 OS 或框架的新功能或改进添加进该映像中。 安全补丁是更新基础映像的另一常见原因。

基础映像更新时，将提示需要重新生成注册表中基于该映像的任何容器映像，以添加新功能和修补。 更新容器的基础映像时，ACR 任务能够自动生成映像。

### <a name="base-image-update-scenario"></a>基础映像更新方案

本教程将指导完成基础映像更新方案。 [代码示例][code-sample]包括两个 Dockerfile：一个应用程序映像和一个它指定为其基础的映像。 在后续部分，我们将创建一个 ACR 任务，它会在新版本基础映像推送到容器注册表时自动触发应用程序映像的生成。

[Dockerfile-app][dockerfile-app]：小型 Node.js Web 应用程序，它呈现一个静态 Web 页面，其中显示该应用程序所基于的 Node.js 版本。 该版本字符串是模拟的：显示基础映像中定义的环境变量和 `NODE_VERSION` 的内容。

[Dockerfile-base][dockerfile-base]：`Dockerfile-app` 指定为其基础的映像。 它本身基于[节点][base-node]映像，并且包括 `NODE_VERSION` 环境变量。

在以下部分，我们将创建一个任务，在基础映像 Dockerfile 中更新 `NODE_VERSION` 值，并使用 ACR 任务来生成基础映像。 ACR 任务将新基础映像推送到注册表时，它会自动触发应用程序映像的生成。 可选择本地运行应用程序容器映像，在生成的映像中查看不同版本字符串。

在本教程中，ACR 任务会生成并推送在 Dockerfile 中指定的单一容器映像。 ACR 任务也可运行[多步骤任务](container-registry-tasks-multi-step.md)，使用 YAML 文件来定义相关步骤，以便生成并推送多个容器，并可选择对其进行测试。

## <a name="build-the-base-image"></a>生成基础映像

首先使用 ACR 任务的快速任务来生成基础映像。 如本系列教程的[第一篇教程](container-registry-tutorial-quick-task.md)中所述，如果生成成功，则此过程不仅会生成映像，还会将其推送到容器注册表。

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

## <a name="create-a-task"></a>创建任务

接下来，使用 [az acr task create][az-acr-task-create] 创建一个任务：

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name taskhelloworld \
    --image helloworld:{{.Run.ID}} \
    --arg REGISTRY_NAME=$ACR_NAME.azurecr.io \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file Dockerfile-app \
    --branch master \
    --git-access-token $GIT_PAT
```

> [!IMPORTANT]
> 如果以前在预览期使用 `az acr build-task` 创建了任务，则需要使用 [az acr task][az-acr-task] 命令重新创建这些任务。

此任务类似于[上一篇教程](container-registry-tutorial-build-task.md)中创建的快速任务。 将提交内容推送到 `--context` 指定的存储库时，生成任务会指示 ACR 任务触发映像生成。

它的不同之处还在于它的行为，在其基础映像更新时，它还会触发映像生成。 `--file` 参数、[Dockerfile-app][dockerfile-app] 指定的 Dockerfile 支持从其基础映像所在的注册表指定映像。

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
```

运行任务时，ACR 任务会检测映像的依赖项。 如果 `FROM` 语句中指定的基础映像驻留在同一注册表中或公共 Docker 中心存储库中，则该基础映像将添加挂钩，以确保它的基础映像更新时会重新生成该映像。

## <a name="build-the-application-container"></a>生成应用程序容器

若要启用 ACR 任务来确定并跟踪容器映像的依赖项（包含其基础映像），首先**必须**触发其任务**至少一次**。

使用 [az acr task run][az-acr-task-run] 手动触发任务并生成应用程序映像：

```azurecli-interactive
az acr task run --registry $ACR_NAME --name taskhelloworld
```

任务完成后，如果还要完成以下可选步骤，请记下“运行 ID”（例如“da6”）。

### <a name="optional-run-application-container-locally"></a>可选：本地运行应用程序容器

如果在本地运行（而不是在 Cloud Shell）并且已安装 Docker，运行容器，查看呈现在 Web 浏览器中的应用程序，并重新生成其基础映像。 如果正在使用 Cloud Shell，可跳过此部分（Cloud Shell 不支持 `az acr login` 或 `docker run`）。

首先，通过 [az acr login][az-acr-login] 登录到容器注册表：

```azurecli
az acr login --name $ACR_NAME
```

然后，使用 `docker run` 在本地运行容器。 将 **\<run-id\>** 替换为上一步骤的输出中的“运行 ID”（例如“da6”）。

```azurecli
docker run -d -p 8080:80 $ACR_NAME.azurecr.io/helloworld:<run-id>
```

在浏览器中导航到 `http://localhost:8080`，应看见呈现在 Web 页面中的 Node.js 版本号，如下所示。 在稍后的步骤中，会通过向版本字符串中添加“a”来提升版本。

![浏览器中呈现示例应用程序的屏幕截图][base-update-01]

## <a name="list-the-builds"></a>列出生成

接下来，使用 [az acr task list-runs][az-acr-task-list-runs] 命令列出 ACR 任务对注册表完成的任务运行：

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

如果已完成之前的教程（并且没有删除注册表），应看到如下所示的输出。 记下任务运行的数目以及最新的运行 ID，以便在后续部分中更新基础映像后对比输出。

```console
$ az acr task list-runs --registry $ACR_NAME --output table

RUN ID    TASK            PLATFORM    STATUS     TRIGGER     STARTED               DURATION
--------  --------------  ----------  ---------  ----------  --------------------  ----------
da6       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T23:07:22Z  00:00:38
da5                       Linux       Succeeded  Manual      2018-09-17T23:06:33Z  00:00:31
da4       taskhelloworld  Linux       Succeeded  Git Commit  2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual      2018-09-17T22:29:59Z  00:00:57
```

## <a name="update-the-base-image"></a>更新基础映像

在这里模拟基础映像中的框架补丁。 编辑“Dockerfile-base”，并在 `NODE_VERSION` 中定义的版本号后面添加一个“a”：

```Dockerfile
ENV NODE_VERSION 9.11.2a
```

运行快速任务来生成修改后的基础映像。 记下输出中的“运行 ID”。

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

生成完成并且 ACR 任务将新基础映像推送到注册表后，它会触发应用程序映像的生成。 之前创建的任务触发应用程序映像生成可能需要一些时间，因为它必须检测新生成和推送的基础映像。

## <a name="list-updated-build"></a>列出已更新的生成

更新基础映像后，请再次列出任务运行，以便与之前的列表进行比较。 如果开始时输出没有区别，可定期运行该命令以查看出现在列表中的新任务运行。

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

输出如下所示。 最后执行的生成的触发器应为“映像更新”，指示任务是通过基础映像的快速任务启动的。

```console
$ az acr task list-builds --registry $ACR_NAME --output table

Run ID    TASK            PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  --------------  ----------  ---------  ------------  --------------------  ----------
da8       taskhelloworld  Linux       Succeeded  Image Update  2018-09-17T23:11:50Z  00:00:33
da7                       Linux       Succeeded  Manual        2018-09-17T23:11:27Z  00:00:35
da6       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T23:07:22Z  00:00:38
da5                       Linux       Succeeded  Manual        2018-09-17T23:06:33Z  00:00:31
da4       taskhelloworld  Linux       Succeeded  Git Commit    2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual        2018-09-17T22:29:59Z  00:00:57
```

如果想要执行以下可选步骤运行新生成的容器，以查看更新的版本号，请记下映像更新触发的生成的“运行 ID”值（在之前的输出中为“da8”）。

### <a name="optional-run-newly-built-image"></a>可选：运行新生成的映像

如果正在本地运行（而不是在 Cloud Shell 中运行）并且已安装 Docker，则可在新应用程序映像的生成完成后运行它。 将 `<run-id>` 替换为上一步骤中获取的“运行 ID”。 如果正在使用 Cloud Shell，可跳过此部分（Cloud Shell 不支持 `docker run`）。

```bash
docker run -d -p 8081:80 $ACR_NAME.azurecr.io/helloworld:<run-id>
```

在浏览器中导航到 http://localhost:8081，应看见网页上显示有更新后的 Node.js 版本编号（带有“a”）：

![浏览器中呈现示例应用程序的屏幕截图][base-update-02]

请务必注意，使用新版本号更新“基础”映像，但是最后生成的“应用程序”映像显示新版本。 ACR 任务选取了对基础映像的更改，并自动重新生成了应用程序映像。

## <a name="clean-up-resources"></a>清理资源

若要删除本系列教程中创建的所有资源，包括容器注册表、容器实例、密钥保管库和服务主体，请运行以下命令：

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>后续步骤

在本教程中，我们已了解在更新映像的基础映像后，如何使用任务来自动触发容器映像生成。 现在，继续了解适用于容器注册表的身份验证。

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
[az-acr-task-create]: /cli/azure/acr
[az-acr-task-run]: /cli/azure/acr#az-acr-run
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-task-list-runs]: /cli/azure/acr
[az-acr-task]: /cli/azure/acr

<!-- IMAGES -->
[base-update-01]: ./media/container-registry-tutorial-base-image-update/base-update-01.png
[base-update-02]: ./media/container-registry-tutorial-base-image-update/base-update-02.png
