---
title: 教程 - 通过基础映像更新触发映像生成
description: 在本教程中，你将配置一个 Azure 容器注册表任务，以便在更新另一个专用 Azure 容器注册表中的基础映像时，在云中自动触发容器映像生成。
ms.topic: tutorial
ms.date: 01/22/2020
ms.openlocfilehash: e8aae8a91288d470c801dc4d82cfa6b44369d832
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617680"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-another-private-container-registry"></a>教程：在更新另一个专用 Azure 容器注册表中的基础映像时自动化容器映像生成 

ACR 任务支持在容器的[基础映像更新](container-registry-tasks-base-images.md)时自动化映像生成，例如在某个基础映像中修补 OS 或应用程序框架时。 

本教程介绍在将容器的基础映像推送到另一个 Azure 容器注册表时，如何创建在云中触发生成的 ACR 任务。 你还可以尝试在某篇教程中学习如何创建 ACR 任务，以便在将基本映像推送到[同一 Azure 容器注册表](container-registry-tutorial-base-image-update.md)时触发映像生成。

本教程的内容：

> [!div class="checklist"]
> * 在基础注册表中生成基础映像
> * 在另一注册表中创建应用程序生成任务以跟踪基础映像 
> * 更新基础映像以触发应用程序映像任务
> * 显示已触发的任务
> * 验证已更新的应用程序映像

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果想要在本地使用 Azure CLI，必须已安装 Azure CLI 版本 **2.0.68** 或更高版本。 运行 `az --version` 即可查找版本。 如果需要安装或升级 CLI，请参阅[安装 Azure CLI][azure-cli]。

## <a name="prerequisites"></a>必备条件

### <a name="complete-the-previous-tutorials"></a>完成前一篇教程

本教程假定你已完成本系列中前两个教程中的步骤，其中包括：

* 创建 Azure 容器注册表
* 创建示例存储库分支
* 克隆示例存储库
* 创建 GitHub 个人访问令牌

在继续之前，请先完成以下教程（如果尚未完成）：

[使用 Azure 容器注册表任务在云中生成容器映像](container-registry-tutorial-quick-task.md)

[使用 Azure 容器注册表任务自动化容器映像生成](container-registry-tutorial-build-task.md)

除了为前面的教程创建的容器注册表以外，还需要创建一个注册表来存储基础映像。 如果需要，请在与原始注册表不同的位置创建第二个注册表。

### <a name="configure-the-environment"></a>配置环境

使用适用于环境的值填充这些 shell 环境变量。 此步骤并非必须执行的步骤，但它能让在此教程中执行多个 Azure CLI 命令更容易。 如果未填充这些环境变量，则每当示例命令中出现每个值，都必须手动替换该值。

```azurecli-interactive
BASE_ACR=<base-registry-name>   # The name of your Azure container registry for base images
ACR_NAME=<registry-name>        # The name of your Azure container registry for application images
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the second tutorial
```

### <a name="base-image-update-scenario"></a>基础映像更新方案

本教程将指导完成基础映像更新方案。 此方案反映了在其他注册表中创建应用程序映像时，在团队共用的专用容器注册表中管理基础映像的开发工作流。 该基础映像可以指定团队共用的操作系统和框架，甚至可以指定共用的服务组件。

例如，在自己注册表中开发应用程序映像的开发人员可以访问共用基础注册表中维护的一组基础映像。 基础注册表可以位于另一区域，甚至可以异地复制。

[代码示例][code-sample]包括两个 Dockerfile：一个应用程序映像和一个它指定为其基础的映像。 在以下部分，你将创建一个 ACR 任务，用于在将新版基础映像推送到不同 Azure 容器注册表时自动触发应用程序映像的生成。

* [Dockerfile-app][dockerfile-app]：小型 Node.js Web 应用程序，它呈现一个静态 Web 页面，其中显示该应用程序所基于的 Node.js 版本。 该版本字符串是模拟的：显示基础映像中定义的环境变量和 `NODE_VERSION` 的内容。

* [Dockerfile-base][dockerfile-base]：`Dockerfile-app` 指定为其基础的映像。 它本身基于[节点][base-node]映像，并且包括 `NODE_VERSION` 环境变量。

在以下部分，我们将创建一个任务，在基础映像 Dockerfile 中更新 `NODE_VERSION` 值，并使用 ACR 任务来生成基础映像。 ACR 任务将新基础映像推送到注册表时，它会自动触发应用程序映像的生成。 可选择本地运行应用程序容器映像，在生成的映像中查看不同版本字符串。

在本教程中，ACR 任务会生成并推送在 Dockerfile 中指定的应用程序容器映像。 ACR 任务也可运行[多步骤任务](container-registry-tasks-multi-step.md)，使用 YAML 文件来定义相关步骤，以便生成并推送多个容器，并可选择对其进行测试。

## <a name="build-the-base-image"></a>生成基础映像

首先使用 [az acr build][az-acr-build] 通过 ACR 任务“快速任务”来生成基础映像。  如本系列教程的[第一篇教程](container-registry-tutorial-quick-task.md)中所述，如果生成成功，则此过程不仅会生成映像，还会将其推送到容器注册表。 在此示例中，映像将推送到基础映像注册表。

```azurecli-interactive
az acr build --registry $BASE_ACR --image baseimages/node:9-alpine --file Dockerfile-base .
```

## <a name="create-a-task-to-track-the-private-base-image"></a>创建一个任务来跟踪专用基础映像

接下来，使用 [az acr task create][az-acr-task-create] 在应用程序映像注册表中创建一个启用[托管标识](container-registry-tasks-authentication-managed-identity.md)的任务。 在后续步骤中将使用该托管标识，使任务能够在基础映像注册表中进行身份验证。 

此示例使用系统分配的标识，但你可以为特定的方案创建并启用用户分配的托管标识。 有关详细信息，请参阅[使用 Azure 托管标识在 ACR 任务中进行跨注册表的身份验证](container-registry-tasks-cross-registry-authentication.md)。

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name taskhelloworld \
    --image helloworld:{{.Run.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file Dockerfile-app \
    --git-access-token $GIT_PAT \
    --arg REGISTRY_NAME=$BASE_ACR.azurecr.io \
    --assign-identity
```


此任务类似于[上一篇教程](container-registry-tutorial-build-task.md)中创建的任务。 将提交内容推送到 `--context` 指定的存储库时，生成任务会指示 ACR 任务触发映像生成。 在前一篇教程用于生成映像的 Dockerfile 指定了公共基础映像 (`FROM node:9-alpine`)，而此任务中的 Dockerfile [Dockerfile-app][dockerfile-app] 指定的是基础映像注册表中的基础映像：

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
```

使用此配置可以轻松模拟稍后在本教程中对基础映像进行的框架修补。

## <a name="give-identity-pull-permissions-to-base-registry"></a>为标识授予对基础注册表的提取权限

若要为任务的托管标识授予从基础映像注册表提取映像的权限，请先运行 [az acr task show][az-acr-task-show] 获取标识的服务主体 ID。 然后运行 [az acr show][az-acr-show] 获取基础注册表的资源 ID：

```azurecli-interactive
# Get service principal ID of the task
principalID=$(az acr task show --name taskhelloworld --registry $ACR_NAME --query identity.principalId --output tsv) 

# Get resource ID of the base registry
baseregID=$(az acr show --name $BASE_ACR --query id --output tsv) 
```
 
运行 [az role assignment create][az-role-assignment-create]，为托管标识分配对注册表的提取权限：

```azurecli-interactive
az role assignment create \
  --assignee $principalID \
  --scope $baseregID --role acrpull 
```

## <a name="add-target-registry-credentials-to-the-task"></a>将目标注册表凭据添加到任务

运行 [az acr task credential add][az-acr-task-credential-add] 将凭据添加到任务。 传递 `--use-identity [system]` 参数，指示任务的系统分配的托管标识可以访问凭据。

```azurecli-interactive
az acr task credential add \
  --name taskhelloworld \
  --registry $ACR_NAME \
  --login-server $BASE_ACR.azurecr.io \
  --use-identity [system] 
```

## <a name="manually-run-the-task"></a>手动运行任务

使用 [az acr task run][az-acr-task-run] 手动触发任务并生成应用程序映像。 此步骤是必需的，这样该任务才能跟踪应用程序映像对基础映像的依赖性。

```azurecli-interactive
az acr task run --registry $ACR_NAME --name taskhelloworld
```

任务完成后，如果还要完成以下可选步骤，请记下“运行 ID”（例如“da6”）  。

### <a name="optional-run-application-container-locally"></a>可选：本地运行应用程序容器

如果在本地运行（而不是在 Cloud Shell）并且已安装 Docker，运行容器，查看呈现在 Web 浏览器中的应用程序，并重新生成其基础映像。 如果正在使用 Cloud Shell，可跳过此部分（Cloud Shell 不支持 `az acr login` 或 `docker run`）。

首先，使用 [az acr login][az-acr-login] 在容器注册表中进行身份验证：

```azurecli
az acr login --name $ACR_NAME
```

然后，使用 `docker run` 在本地运行容器。 将 **\<run-id\>** 替换为上一步骤的输出中的“运行 ID”（例如“da6”）。 此示例将容器命名为 `myapp`，并包含 `--rm` 参数，以便在停止容器后将其删除。

```bash
docker run -d -p 8080:80 --name myapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

在浏览器中导航到 `http://localhost:8080`，应看见呈现在 Web 页面中的 Node.js 版本号，如下所示。 在稍后的步骤中，会通过向版本字符串中添加“a”来提升版本。

![浏览器中呈现示例应用程序的屏幕截图][base-update-01]

若要停止并删除容器，请运行以下命令：

```bash
docker stop myapp
```

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

在这里模拟基础映像中的框架补丁。 编辑“Dockerfile-base”，并在 `NODE_VERSION` 中定义的版本号后面添加一个“a”  ：

```Dockerfile
ENV NODE_VERSION 9.11.2a
```

运行快速任务来生成修改后的基础映像。 记下输出中的“运行 ID”  。

```azurecli-interactive
az acr build --registry $BASE_ACR --image baseimages/node:9-alpine --file Dockerfile-base .
```

生成完成并且 ACR 任务将新基础映像推送到注册表后，它会触发应用程序映像的生成。 之前创建的任务触发应用程序映像生成可能需要一些时间，因为它必须检测新生成和推送的基础映像。

## <a name="list-updated-build"></a>列出已更新的生成

更新基础映像后，请再次列出任务运行，以便与之前的列表进行比较。 如果开始时输出没有区别，可定期运行该命令以查看出现在列表中的新任务运行。

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

输出如下所示。 最后执行的生成的触发器应为“映像更新”，指示任务是通过基础映像的快速任务启动的。

```console
$ az acr task list-runs --registry $ACR_NAME --output table

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

如果想要执行以下可选步骤运行新生成的容器，以查看更新的版本号，请记下映像更新触发的生成的“运行 ID”值（在之前的输出中为“da8”）  。

### <a name="optional-run-newly-built-image"></a>可选：运行新生成的映像

如果正在本地运行（而不是在 Cloud Shell 中运行）并且已安装 Docker，则可在新应用程序映像的生成完成后运行它。 将 `<run-id>` 替换为上一步骤中获取的“运行 ID”。 如果正在使用 Cloud Shell，可跳过此部分（Cloud Shell 不支持 `docker run`）。

```bash
docker run -d -p 8081:80 --name updatedapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

在浏览器中导航到 http://localhost:8081 ，应看见网页上显示有更新后的 Node.js 版本编号（带有“a”）：

![浏览器中呈现示例应用程序的屏幕截图][base-update-02]

请务必注意，使用新版本号更新“基础”映像，但是最后生成的“应用程序”映像显示新版本   。 ACR 任务选取了对基础映像的更改，并自动重新生成了应用程序映像。

若要停止并删除容器，请运行以下命令：

```bash
docker stop updatedapp
```

## <a name="next-steps"></a>后续步骤

在本教程中，我们已了解在更新映像的基础映像后，如何使用任务来自动触发容器映像生成。 现在，请转到下一个教程学习如何按照定义的计划触发任务。

> [!div class="nextstepaction"]
> [按计划运行任务](container-registry-tasks-scheduled.md)

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
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task]: /cli/azure/acr#az-acr-task
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create

<!-- IMAGES -->
[base-update-01]: ./media/container-registry-tutorial-base-image-update/base-update-01.png
[base-update-02]: ./media/container-registry-tutorial-base-image-update/base-update-02.png
