---
title: 教程 - 使用 Azure 容器注册表自动生成容器映像
description: 本教程介绍如何对生成任务进行配置，使其在你向 Git 存储库提交源代码时在云中自动触发容器映像生成。
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 4accbcb61e57d58100b6a4c06142dd3dc633f7f4
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
ms.locfileid: "34057666"
---
# <a name="tutorial-automate-container-image-builds-with-azure-container-registry-build"></a>教程：使用 Azure 容器注册表自动生成容器映像

除[快速生成](container-registry-tutorial-quick-build.md)之外，ACR 生成还支持通过生成任务自动执行 Docker 容器映像生成。 此教程介绍如何使用 Azure CLI 创建一个生成任务，你将源代码提交到 Git 存储库时，该任务会在云中自动触发映像生成。

本教程（教程系列的第二部分）的内容包括：

> [!div class="checklist"]
> * 创建生成任务
> * 测试生成任务
> * 查看生成状态
> * 使用代码提交触发生成任务

本教程假设你已完成[前面教程](container-registry-tutorial-quick-build.md)中的任务。 如果尚未完成，请先完成前面教程[先决条件](container-registry-tutorial-quick-build.md#prerequisites)部分中的步骤，再继续操作。

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果想在本地使用 Azure CLI，必须安装 2.0.32 或更高版本的 Azure CLI。 运行 `az --version` 即可查找版本。 如果需要安装或升级 CLI，请参阅[安装 Azure CLI 2.0][azure-cli]。

## <a name="prerequisites"></a>先决条件

### <a name="get-sample-code"></a>获取示例代码

本教程假设你已完成[前面教程](container-registry-tutorial-quick-build.md)中的步骤，并且已经创建分支和克隆示例存储库。 如果尚未完成，请先完成前面教程[先决条件](container-registry-tutorial-quick-build.md#prerequisites)部分中的步骤，再继续操作。

### <a name="container-registry"></a>容器注册表

Azure 订阅中必须具有 Azure 容器注册表才能完成此教程。 如果需要注册表，请参阅[前面教程](container-registry-tutorial-quick-build.md)，或[快速入门：使用 Azure CLI 创建容器注册表](container-registry-get-started-azure-cli.md)。

## <a name="build-task"></a>生成任务

生成任务定义自动生成的属性，包括容器映像源代码的位置和触发生成的事件。 生成任务中定义的事件发生时（例如向 Git 存储库提交内容），ACR 生成会在云中启动容器映像生成。 默认情况下，它之后会将成功生成的映像推送给任务中指定的 Azure 容器注册表。

ACR 生成目前支持以下生成任务触发事件：

* 向 Git 存储库提交内容
* 更新基础映像

## <a name="create-a-build-task"></a>创建生成任务

此部分中首先创建一个供 ACR 生成使用的 GitHub 个人访问令牌 (PAT)。 然后创建生成任务，向存储库分支提交代码时，该任务会触发生成。

### <a name="create-a-github-personal-access-token"></a>创建 GitHub 个人访问令牌

要在向 Git 存储库提交内容时触发生成，ACR 生成需要一个访问存储库的个人访问令牌 (PAT)。 请按照以下步骤进行操作，在 GitHub 中生成 PAT：

1. 导航到 GitHub 上的 PAT 创建页面 https://github.com/settings/tokens/new
1. 输入关于令牌的简短说明，例如“ACR 生成任务演示”
1. 在“存储库”下方，启用“存储库:状态”和“public_repo”

   ![GitHub 中个人访问令牌生成页面的屏幕截图][build-task-01-new-token]

1. 选择“生成令牌”按钮（可能会要求你确认密码）
1. 将生成的令牌复制并保存到安全位置（后面定义生成任务时会使用此令牌）

   ![GitHub 中已生成的个人访问令牌的屏幕截图][build-task-02-generated-token]

### <a name="create-the-build-task"></a>创建生成任务

现在已完成启用 ACR 生成以读取提交状态和在存储库中创建 Webhook 所需的步骤，可以创建生成任务，向存储库提交内容时触发容器映像生成。

首先，使用适用于环境的值填充这些 shell 环境变量。 这一步并非必须执行的步骤，但它能让在此教程中执行多个 Azure CLI 命令更容易。 如果没有填充这些环境变量，示例命令中出现时则必须手动替换每个值。

```azurecli-interactive
ACR_NAME=mycontainerregistry # The name of your Azure container registry
GIT_USER=gituser             # Your GitHub user account name
GIT_PAT=personalaccesstoken  # The PAT you generated in the previous section
```

现在可通过执行以下 [az acr build-task create][az-acr-build-task-create] 命令创建生成任务：

```azurecli-interactive
az acr build-task create \
    --registry $ACR_NAME \
    --name buildhelloworld \
    --image helloworld:{{.Build.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node \
    --branch master \
    --git-access-token $GIT_PAT
```

此生成任务指定向 `--context` 指定的主要分支存储库提交代码时，ACR 生成将根据该分支中的代码生成容器映像。 `--image` 参数为映像标记的版本部分指定参数化的 `{{.Build.ID}}` 值，确保生成映像与特定生成关联且被唯一标记。

成功的 [az acr build-task create][az-acr-build-task-create] 命令产生的输出应如下所示：

```console
$ az acr build-task create \
>     --registry $ACR_NAME \
>     --name buildhelloworld \
>     --image helloworld:{{.Build.ID}} \
>     --context https://github.com/$GIT_USER/acr-build-helloworld-node \
>     --branch master \
>     --git-access-token $GIT_PAT
{
  "additionalProperties": {},
  "alias": "buildhelloworld",
  "creationDate": "2018-05-10T19:34:48.086776+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/mycontainerregistry/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry/buildTasks/buildhelloworld",
  "location": "eastus",
  "name": "buildhelloworld",
  "platform": {
    "additionalProperties": {},
    "cpu": 1,
    "osType": "Linux"
  },
  "properties": {
    "additionalProperties": {
      "imageName": null
    },
    "baseImageDependencies": null,
    "baseImageTrigger": "Runtime",
    "branch": "master",
    "buildArguments": [],
    "contextPath": null,
    "dockerFilePath": "Dockerfile",
    "imageNames": [
      "helloworld:{{.Build.ID}}"
    ],
    "isPushEnabled": true,
    "noCache": false,
    "provisioningState": "Succeeded",
    "type": "Docker"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "mycontainerregistry",
  "sourceRepository": {
    "additionalProperties": {},
    "isCommitTriggerEnabled": true,
    "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node",
    "sourceControlAuthProperties": null,
    "sourceControlType": "GitHub"
  },
  "status": "Enabled",
  "tags": null,
  "timeout": 3600,
  "type": "Microsoft.ContainerRegistry/registries/buildTasks"
}
```

## <a name="test-the-build-task"></a>测试生成任务

现在你已具有定义生成的生成任务。 若要测试生成定义，请通过执行 [az acr build-task run][az-acr-build-task-run] 命令手动触发生成：

```azurecli-interactive
az acr build-task run --registry $ACR_NAME --name buildhelloworld
```

默认情况下，执行此命令时，`az acr build-task run` 命令会将日志流式传输到控制台。 此处输出会显示生成 aa2 已排队和已生成。

```console
$ az acr build-task run --registry $ACR_NAME --name buildhelloworld
Queued a build with build ID: aa2
Waiting for a build agent...
time="2018-05-10T19:37:17Z" level=info msg="Running command git clone https://x-access-token:*************@github.com/gituser/acr-build-helloworld-node /root/acr-builder/src"
Cloning into '/root/acr-builder/src'...
time="2018-05-10T19:37:17Z" level=info msg="Running command git checkout master"
Already on 'master'
Your branch is up to date with 'origin/master'.
920f16cfafa36d0bc3f397c3dd48185a03499404
time="2018-05-10T19:37:17Z" level=info msg="Running command git rev-parse --verify HEAD"
time="2018-05-10T19:37:17Z" level=info msg="Running command docker build --pull -f Dockerfile -t mycontainerregistry.azurecr.io/helloworld:aa2 ."
Sending build context to Docker daemon  209.9kB
Step 1/5 : FROM node:9-alpine
9-alpine: Pulling from library/node
Digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3
Status: Image is up to date for node:9-alpine
 ---> 7af437a39ec2
Step 2/5 : COPY . /src
 ---> 48a7735fa94e

[...]

26b0c207c4a9: Pushed
917e7cdebc8b: Pushed
aa2: digest: sha256:6975f01e2e202c084581e676acbe6047788fbe616836328b0b31ce8c58e9fc89 size: 1367
time="2018-05-10T19:37:57Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" mycontainerregistrtyy.azurecr.io/helloworld:aa2"
"["mycontainerregistrtyy.azurecr.io/helloworld@sha256:6975f01e2e202c084581e676acbe6047788fbe616836328b0b31ce8c58e9fc89"]"
time="2018-05-10T19:37:57Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" node:9-alpine"
"["node@sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3"]"
ACR Builder discovered the following dependencies:
- image:
    registry: mycontainerregistrtyy.azurecr.io
    repository: helloworld
    tag: aa2
    digest: sha256:6975f01e2e202c084581e676acbe6047788fbe616836328b0b31ce8c58e9fc89
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3
  git:
    git-head-revision: 920f16cfafa36d0bc3f397c3dd48185a03499404

Build complete
Build ID: aa2 was successful after 46.491407373s
```

## <a name="view-build-status"></a>查看生成状态

有时查看未手动触发的正在进行的生成的状态很有用。 例如，故障排除由源代码提交触发的生成时。 本部分中将触发一个手动生成，但阻止将生成日志流式传输到控制台的默认行为。 然后使用 `az acr build-task logs` 命令来监视正在进行的生成。

首先，像之前一样手动触发一个生成，但要指定 `--no-logs` 参数阻止将日志传输到控制台：

```azurecli-interactive
az acr build-task run --registry $ACR_NAME --name buildhelloworld --no-logs
```

接下来使用 `az build-task logs` 命令查看当前正在运行的生成的日志：

```azurecli-interactive
az acr build-task logs --registry $ACR_NAME
```

当前正在运行的生成的日志已流式传输到控制台，应与以下输出类似（此处的显示内容已截断）：

```console
$ az acr build-task logs --registry $ACR_NAME
Showing logs for the last updated build
Build ID: aa3

[...]

Build complete
Build ID: aa3 was successful after 1m14.26397548s
```

## <a name="trigger-a-build-with-a-commit"></a>使用命令触发生成

通过手动运行生成任务对其进行测试后，可通过更改源代码手动触发该生成任务。

首先，确保目录中包含[存储库][sample-repo]的本地克隆：

```azurecli-interactive
cd acr-build-helloworld-node
```

接下来执行以下命令，创建新文件，并将其提交和推送给你在 GitHub 上的存储库分支：

```azurecli-interactive
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Build"
git push origin master
```

执行 `git push` 命令时可能需要提供 GitHub 凭据。 提供 GitHub 用户名并输入之前为密码创建的个人访问令牌 (PAT)。

```console
$ git push origin master
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

将提交推送至存储库后，ACR 生成所创建的 Webhook 便会在 Azure 容器注册表中触发并启动一个生成。 显示当前正在运行的生成的生成日志，验证和监视生成进度：

```azurecli-interactive
az acr build-task logs --registry $ACR_NAME
```

输出结果类似于以下内容，显示当前执行（或最近执行）的生成：

```console
$ az acr build-task logs --registry $ACR_NAME
Showing logs for the last updated build
Build ID: aa4

[...]

Build complete
Build ID: aa4 was successful after 39.164385024s
```

## <a name="list-builds"></a>生成列表

若要查看 ACR 生成为注册表完成的生成列表，请运行 [az acr build-task list-builds][az-acr-build-task-list-builds] 命令：

```azurecli-interactive
az acr build-task list-builds --registry $ACR_NAME --output table
```

该命令产生的输出应如下所示。 会显示 ACR 生成已执行的生成，并在最新生成的 TRIGGER 列中显示“Git Commit”：

```console
$ az acr build-task list-builds --registry $ACR_NAME --output table
BUILD ID    TASK             PLATFORM    STATUS     TRIGGER     STARTED               DURATION
----------  ---------------  ----------  ---------  ----------  --------------------  ----------
aa4         buildhelloworld  Linux       Succeeded  Git Commit  2018-05-10T19:49:40Z  00:00:45
aa3         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T19:41:50Z  00:01:20
aa2         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T19:37:11Z  00:00:50
aa1                          Linux       Succeeded  Manual      2018-05-10T19:10:14Z  00:00:55
```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何使用生成任务，在你向 Git 存储库提交源代码时在 Azure 中自动触发容器映像生成。 请转到下一教程，了解如何创建生成任务，使其在更新容器映像的基础映像时触发生成。

> [!div class="nextstepaction"]
> [在更新基础映像时自动生成](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build-task]: /cli/azure/acr#az-acr-build-task
[az-acr-build-task-create]: /cli/azure/acr#az-acr-build-task-create
[az-acr-build-task-run]: /cli/azure/acr#az-acr-build-task-run
[az-acr-build-task-list-builds]: /cli/azure/acr#az-acr-build-task-list-build

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png
