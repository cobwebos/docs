---
title: 教程 - 在代码提交时构建映像
description: 本教程介绍如何配置一个 Azure 容器注册表任务，以便在向 Git 存储库提交源代码时在云中自动触发容器映像生成。
ms.topic: tutorial
ms.date: 05/04/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: 6882cb683e0bd8b76bb1207e628e43f24c7b5987
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252122"
---
# <a name="tutorial-automate-container-image-builds-in-the-cloud-when-you-commit-source-code"></a>教程：提交源代码时，在云中自动化容器映像生成

除了[快速任务](container-registry-tutorial-quick-task.md)之外，ACR 任务还支持在将源代码提交到 Git 存储库时自动在云中生成 Docker 容器映像。 ACR 任务支持的 Git 上下文包括公共或专用 GitHub 或 Azure Repos。

> [!NOTE]
> 目前，ACR 任务不支持 GitHub Enterprise 存储库中的提交或拉取请求触发器。

在本教程中，在你将源代码提交到 Git 存储库时，ACR 任务会生成并推送在 Dockerfile 中指定的单一容器映像。 要创建[多步骤任务](container-registry-tasks-multi-step.md)并让其使用 YAML 文件来定义相关步骤，以便在提交代码时生成、推送和测试（可选）多个容器，请参阅[教程：提交源代码时在云中运行多步骤容器工作流](container-registry-tutorial-multistep-task.md)。 有关 ACR 任务的概述，请参阅[使用 ACR 任务自动执行 OS 和框架修补](container-registry-tasks-overview.md)

本教程的内容：

> [!div class="checklist"]
> * 创建任务
> * 测试任务
> * 查看任务状态
> * 使用代码提交触发任务

本教程假设你已完成[前面教程](container-registry-tutorial-quick-task.md)中的任务。 如果尚未完成，请先完成前面教程[先决条件](container-registry-tutorial-quick-task.md#prerequisites)部分中的步骤，再继续操作。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

若要在本地使用 Azure CLI，必须安装 Azure CLI **2.0.46** 或更高版本，并使用 [az login][az-login] 登录。 运行 `az --version` 即可查找版本。 如果需要安装或升级 CLI，请参阅[安装 Azure CLI][azure-cli]。

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]

## <a name="create-the-build-task"></a>创建生成任务

现已完成启用 ACR 任务以读取提交状态和在存储库中创建 Webhook 所需的步骤，接下来可以创建任务，以便在向存储库提交内容时触发容器映像生成。

首先，使用适用于环境的值填充这些 shell 环境变量。 此步骤并非必须执行的步骤，但它能让在此教程中执行多个 Azure CLI 命令更容易。 如果未填充这些环境变量，则每当示例命令中出现每个值，都必须手动替换该值。

[![嵌入式启动](https://shell.azure.com/images/launchcloudshell.png "启动 Azure Cloud Shell")](https://shell.azure.com)

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

现在，请执行以下 [az acr task create][az-acr-task-create] 命令创建该任务：

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name taskhelloworld \
    --image helloworld:{{.Run.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file Dockerfile \
    --git-access-token $GIT_PAT
```

> [!IMPORTANT]
> 如果以前在预览期使用 `az acr build-task` 创建了任务，则需要使用 [az acr task][az-acr-task] 命令重新创建这些任务。

此任务指定向 `--context` 指定的主分支存储库提交代码时，ACR 任务将根据该分支中的代码生成容器映像  。 将使用存储库根目录中由 `--file` 指定的 Dockerfile 来生成映像。 `--image` 参数为映像标记的版本部分指定参数化的 `{{.Run.ID}}` 值，确保生成映像与特定生成关联且被唯一标记。

成功的 [az acr task create][az-acr-task-create] 命令的输出应如下所示：

```console
{
  "agentConfiguration": {
    "cpu": 2
  },
  "creationDate": "2018-09-14T22:42:32.972298+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myregistry/providers/Microsoft.ContainerRegistry/registries/myregistry/tasks/taskhelloworld",
  "location": "westcentralus",
  "name": "taskhelloworld",
  "platform": {
    "architecture": "amd64",
    "os": "Linux",
    "variant": null
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "myregistry",
  "status": "Enabled",
  "step": {
    "arguments": [],
    "baseImageDependencies": null,
    "contextPath": "https://github.com/gituser/acr-build-helloworld-node",
    "dockerFilePath": "Dockerfile",
    "imageNames": [
      "helloworld:{{.Run.ID}}"
    ],
    "isPushEnabled": true,
    "noCache": false,
    "type": "Docker"
  },
  "tags": null,
  "timeout": 3600,
  "trigger": {
    "baseImageTrigger": {
      "baseImageTriggerType": "Runtime",
      "name": "defaultBaseimageTriggerName",
      "status": "Enabled"
    },
    "sourceTriggers": [
      {
        "name": "defaultSourceTriggerName",
        "sourceRepository": {
          "branch": "master",
          "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node",
          "sourceControlAuthProperties": null,
          "sourceControlType": "GitHub"
        },
        "sourceTriggerEvents": [
          "commit"
        ],
        "status": "Enabled"
      }
    ]
  },
  "type": "Microsoft.ContainerRegistry/registries/tasks"
}
```

## <a name="test-the-build-task"></a>测试生成任务

现已创建一个用于定义生成的任务。 若要测试生成管道，请执行 [az acr task run][az-acr-task-run] 命令手动触发生成：

```azurecli-interactive
az acr task run --registry $ACR_NAME --name taskhelloworld
```

默认情况下，执行此命令时，`az acr task run` 命令会将日志流式传输到控制台。

```console
$ az acr task run --registry $ACR_NAME --name taskhelloworld

2018/09/17 22:51:00 Using acb_vol_9ee1f28c-4fd4-43c8-a651-f0ed027bbf0e as the home volume
2018/09/17 22:51:00 Setting up Docker configuration...
2018/09/17 22:51:02 Successfully set up Docker configuration
2018/09/17 22:51:02 Logging in to registry: myregistry.azurecr.io
2018/09/17 22:51:03 Successfully logged in
2018/09/17 22:51:03 Executing step: build
2018/09/17 22:51:03 Obtaining source code and scanning for dependencies...
2018/09/17 22:51:05 Successfully obtained source code and scanned for dependencies
Sending build context to Docker daemon  23.04kB
Step 1/5 : FROM node:9-alpine
9-alpine: Pulling from library/node
Digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
Status: Image is up to date for node:9-alpine
 ---> a56170f59699
Step 2/5 : COPY . /src
 ---> 5f574fcf5816
Step 3/5 : RUN cd /src && npm install
 ---> Running in b1bca3b5f4fc
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN helloworld@1.0.0 No repository field.

up to date in 0.078s
Removing intermediate container b1bca3b5f4fc
 ---> 44457db20dac
Step 4/5 : EXPOSE 80
 ---> Running in 9e6f63ec612f
Removing intermediate container 9e6f63ec612f
 ---> 74c3e8ea0d98
Step 5/5 : CMD ["node", "/src/server.js"]
 ---> Running in 7382eea2a56a
Removing intermediate container 7382eea2a56a
 ---> e33cd684027b
Successfully built e33cd684027b
Successfully tagged myregistry.azurecr.io/helloworld:da2
2018/09/17 22:51:11 Executing step: push
2018/09/17 22:51:11 Pushing image: myregistry.azurecr.io/helloworld:da2, attempt 1
The push refers to repository [myregistry.azurecr.io/helloworld]
4a853682c993: Preparing
[...]
4a853682c993: Pushed
[...]
da2: digest: sha256:c24e62fd848544a5a87f06ea60109dbef9624d03b1124bfe03e1d2c11fd62419 size: 1366
2018/09/17 22:51:21 Successfully pushed image: myregistry.azurecr.io/helloworld:da2
2018/09/17 22:51:21 Step id: build marked as successful (elapsed time in seconds: 7.198937)
2018/09/17 22:51:21 Populating digests for step id: build...
2018/09/17 22:51:22 Successfully populated digests for step id: build
2018/09/17 22:51:22 Step id: push marked as successful (elapsed time in seconds: 10.180456)
The following dependencies were found:
- image:
    registry: myregistry.azurecr.io
    repository: helloworld
    tag: da2
    digest: sha256:c24e62fd848544a5a87f06ea60109dbef9624d03b1124bfe03e1d2c11fd62419
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 68cdf2a37cdae0873b8e2f1c4d80ca60541029bf


Run ID: da2 was successful after 27s
```

## <a name="trigger-a-build-with-a-commit"></a>使用命令触发生成

通过手动运行任务对其进行测试后，可通过更改源代码手动触发该任务。

首先，确保你位于包含[存储库][sample-repo]的本地克隆的目录中：

```console
cd acr-build-helloworld-node
```

接下来执行以下命令，创建新文件，并将其提交和推送给你在 GitHub 上的存储库分支：

```console
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Tasks"
git push origin master
```

执行 `git push` 命令时可能需要提供 GitHub 凭据。 提供 GitHub 用户名并输入之前为密码创建的个人访问令牌 (PAT)。

```console
$ git push origin master
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

将提交推送至存储库后，ACR 任务所创建的 Webhook 便会在 Azure 容器注册表中触发并启动一个生成。 显示当前正在运行的任务的日志，以验证和监视生成进度：

```azurecli-interactive
az acr task logs --registry $ACR_NAME
```

输出结果类似于以下内容，显示当前执行（或最近执行）的任务：

```console
$ az acr task logs --registry $ACR_NAME
Showing logs of the last created run.
Run ID: da4

[...]

Run ID: da4 was successful after 38s
```

## <a name="list-builds"></a>生成列表

若要查看 ACR 任务对注册表完成的任务运行列表，请运行 [az acr task list-runs][az-acr-task-list-runs] 命令：

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

该命令产生的输出应如下所示。 将显示 ACR 任务已执行的运行，并在最近执行的任务的 TRIGGER 列中显示“Git Commit”：

```console
$ az acr task list-runs --registry $ACR_NAME --output table

RUN ID    TASK             PLATFORM    STATUS     TRIGGER     STARTED               DURATION
--------  --------------  ----------  ---------  ----------  --------------------  ----------
da4       taskhelloworld  Linux       Succeeded  Git Commit  2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual      2018-09-17T22:29:59Z  00:00:57
```

## <a name="next-steps"></a>后续步骤

在本教程中，我们已了解如何在向 Git 存储库提交源代码时，使用一个任务在 Azure 中自动触发容器映像生成。 请转到下一教程来了解如何创建任务，用于在更新容器映像的基础映像时触发生成。

> [!div class="nextstepaction"]
> [在更新基础映像时自动生成](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-login]: /cli/azure/reference-index#az-login



