---
title: 使用 Azure 容器注册表多步骤任务自动执行映像生成、测试和修补
description: 介绍多步骤任务，这是 Azure 容器注册表中 ACR 任务的一项功能，可以提供用于在云中生成、测试和修补容器映像的基于任务的工作流。
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 09/24/2018
ms.author: danlep
ms.openlocfilehash: cdabafc4f70b08076820e7e0d39300b3eb0bc1e7
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2018
ms.locfileid: "48856711"
---
# <a name="run-multi-step-build-test-and-patch-tasks-in-acr-tasks"></a>在 ACR 任务中运行多步骤生成、测试和修补任务

多步骤任务凭借基于多步骤、多容器的工作流扩展了 ACR 任务的单一映像生成和推送功能。 使用多步骤任务可通过串行或并行方式生成和推送多个映像，并在单个任务运行中以命令形式运行这些映像。 每个步骤定义一个容器映像生成或推送操作，此外还可以定义容器的执行。 多步骤任务中的每个步骤使用一个容器作为其执行环境。

> [!IMPORTANT]
> 如果以前在预览期使用 `az acr build-task` 创建了任务，则需要使用 [az acr task][az-acr-task] 命令重新创建这些任务。

例如，可以运行一个任务，其中的步骤可以自动完成以下操作：

1. 生成 Web 应用程序映像
1. 运行 Web 应用程序容器
1. 生成 Web 应用程序测试映像
1. 运行针对正在运行的应用程序容器执行测试的 Web 应用程序测试容器
1. 如果测试通过，则生成 Helm 图表存档包
1. 使用新的 Helm 图表存档包执行 `helm upgrade`

所有步骤在 Azure 中执行，可将工作卸载到 Azure 的计算资源，并消除基础结构的管理工作。 除了 Azure 容器注册表费用以外，只需为所用的资源付费。 有关定价信息，请参阅 [Azure 容器注册表定价][pricing]中的“容器生成”部分。

> [!IMPORTANT]
> 此功能目前处于预览状态。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。

## <a name="common-task-scenarios"></a>常见任务方案

多步骤任务可实现如下所述的方案：

* 以串行或并行方式生成、标记和推送一个或多个容器映像。
* 运行单元测试和代码覆盖并捕获其结果。
* 运行功能测试并捕获其结果。 ACR 任务支持运行多个容器，可在这些容器之间执行一系列请求。
* 执行基于任务的执行，包括容器映像生成的前期/后期步骤。
* 将包含偏好部署引擎的一个或多个容器部署到目标环境。

## <a name="multi-step-task-definition"></a>多步骤任务的定义

ACR 任务中的多步骤任务定义为 YAML 文件中的一系列步骤。 每个步骤可以指定对成功完成前面一个或多个步骤的依赖性。 可使用以下任务步骤类型：

* [`build`](container-registry-tasks-reference-yaml.md#build)：使用熟悉的 `docker build` 语法以串行或并行方式生成一个或多个容器映像。
* [`push`](container-registry-tasks-reference-yaml.md#push)：将生成的映像推送到容器注册表。 支持 Azure 容器注册表等专用注册表，并支持公共 Docker 中心。
* [`cmd`](container-registry-tasks-reference-yaml.md#cmd)：运行容器，使其可以在所运行任务的上下文中作为函数运行。 可将参数传递到容器的 `[ENTRYPOINT]`，并指定 env、detach 等属性，以及其他熟悉的 `docker run` 参数。 `cmd` 步骤类型可以实现单元测试和功能测试，并支持并发容器执行。

多步骤任务有时就像生成并推送单个映像一样简单：

```yaml
version: 1.0-preview-1
steps:
  - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

有时又比较复杂，例如，以下任务包含生成、测试、helm 打包和 helm 部署的步骤：

```yaml
version: 1.0-preview-1
steps:
  - id: build-web
    build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
    when: ["-"]
  - id: build-tests
    build -t {{.Run.Registry}}/hello-world-tests ./funcTests
    when: ["-"]
  - id: push
    push: ["{{.Run.Registry}}/helloworld:{{.Run.ID}}"]
    when: ["build-web", "build-tests"]
  - id: hello-world-web
    cmd: {{.Run.Registry}}/helloworld:{{.Run.ID}}
  - id: funcTests
    cmd: {{.Run.Registry}}/helloworld:{{.Run.ID}}
    env: ["host=helloworld:80"]
  - cmd: {{.Run.Registry}}/functions/helm package --app-version {{.Run.ID}} -d ./helm ./helm/helloworld/
  - cmd: {{.Run.Registry}}/functions/helm upgrade helloworld ./helm/helloworld/ --reuse-values --set helloworld.image={{.Run.Registry}}/helloworld:{{.Run.ID}}
```

## <a name="run-a-sample-task"></a>运行示例任务

任务既支持手动执行（称作“快速运行”），也支持在提交 Git 或更新基础映像时的自动执行。

若要运行某个任务，请先在 YAML 文件中定义该任务的步骤，然后执行 Azure CLI 命令 [az acr run][az-acr-run]。

以下示例 Azure CLI 命令使用示例任务 YAML 文件运行一个任务。 其任务的步骤生成映像，然后推送该映像。 在运行该命令之前，请使用自己的 Azure 容器注册表名称更新 `\<acrName\>`。

```azurecli
az acr run --registry <acrName> -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

运行该任务时，输出应显示 YAML 文件中定义的每个步骤的进度。 在以下输出中，步骤显示为 `acb_step_0` 和 `acb_step_1`。

```console
$ az acr run --registry myregistry -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
Sending context to registry: myregistry...
Queued a run with ID: yd14
Waiting for an agent...
2018/09/12 20:08:44 Using acb_vol_0467fe58-f6ab-4dbd-a022-1bb487366941 as the home volume
2018/09/12 20:08:44 Creating Docker network: acb_default_network
2018/09/12 20:08:44 Successfully set up Docker network: acb_default_network
2018/09/12 20:08:44 Setting up Docker configuration...
2018/09/12 20:08:45 Successfully set up Docker configuration
2018/09/12 20:08:45 Logging in to registry: myregistry.azurecr-test.io
2018/09/12 20:08:46 Successfully logged in
2018/09/12 20:08:46 Executing step: acb_step_0
2018/09/12 20:08:46 Obtaining source code and scanning for dependencies...
2018/09/12 20:08:47 Successfully obtained source code and scanned for dependencies
Sending build context to Docker daemon  109.6kB
Step 1/1 : FROM hello-world
 ---> 4ab4c602aa5e
Successfully built 4ab4c602aa5e
Successfully tagged myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:48 Executing step: acb_step_1
2018/09/12 20:08:48 Pushing image: myregistry.azurecr-test.io/hello-world:yd14, attempt 1
The push refers to repository [myregistry.azurecr-test.io/hello-world]
428c97da766c: Preparing
428c97da766c: Layer already exists
yd14: digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812 size: 524
2018/09/12 20:08:55 Successfully pushed image: myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:55 Step id: acb_step_0 marked as successful (elapsed time in seconds: 2.035049)
2018/09/12 20:08:55 Populating digests for step id: acb_step_0...
2018/09/12 20:08:57 Successfully populated digests for step id: acb_step_0
2018/09/12 20:08:57 Step id: acb_step_1 marked as successful (elapsed time in seconds: 6.832391)
The following dependencies were found:
- image:
    registry: myregistry.azurecr-test.io
    repository: hello-world
    tag: yd14
    digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0add3ace90ecb4adbf7777e9aacf18357296e799f81cabc9fde470971e499788
  git: {}


Run ID: yd14 was successful after 19s
```

有关提交 Git 或更新基础映像时的自动生成的详细信息，请参阅[自动映像生成](container-registry-tutorial-build-task.md)和[基础映像更新生成](container-registry-tutorial-base-image-update.md)教程文章。

## <a name="preview-feedback"></a>预览功能反馈

在 ACR 任务的多步骤任务功能预览期，我们邀请各位提供反馈。 可以使用多个反馈渠道：

* [问题](https://aka.ms/acr/issues) - 查看现有 bug 和问题，以及记录新问题
* [UserVoice](https://aka.ms/acr/uservoice) - 对现有功能请求投票，或创建新请求
* [讨论](https://aka.ms/acr/feedback) - 在 Stack Overflow 社区参与 Azure 容器注册表的讨论

## <a name="next-steps"></a>后续步骤

可在以下资源中找到多步骤任务的参考信息和示例：

* [任务参考](container-registry-tasks-reference-yaml.md) - 任务步骤的类型、属性和用法。
* [任务示例][task-examples] - 从简单到复杂的多种方案的示例 `task.yaml` 文件。

<!-- IMAGES -->

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[task-examples]: https://github.com/Azure-Samples/acr-tasks
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-run]: /cli/azure/acr/run#az-acr-run
[az-acr-task]: /cli/azure/acr#az-acr-task