---
title: YAML 参考-ACR 任务
description: 有关在 YAML 中为 ACR 任务定义任务的参考，包括任务属性、步骤类型、步骤属性和内置变量。
ms.topic: article
ms.date: 10/23/2019
ms.openlocfilehash: a27f55d08a7ed5d7bf3360030eabefc4b7720b82
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454650"
---
# <a name="acr-tasks-reference-yaml"></a>ACR 任务参考：YAML

ACR 任务中的多步骤任务定义提供注重于生成、测试和修补容器的，以容器为中心的计算基元。 本文介绍用于定义多步骤任务的 YAML 文件的命令、参数、属性和语法。

本文包含有关为 ACR 任务创建多步骤任务 YAML 文件的参考信息。 如需 ACR 任务的简介，请参阅 [ACR 任务概述](container-registry-tasks-overview.md)。

## <a name="acr-taskyaml-file-format"></a>acr-task.yaml 文件格式

ACR 任务支持采用标准 YAML 语法的多步骤任务声明。 在 YAML 文件中定义任务的步骤。 然后，可以通过将该文件传递到 [az acr run][az-acr-run] 命令来手动运行该任务。 或者，运行 [az acr task create][az-acr-task-create] 命令并使用该文件创建一个任务，每次执行 Git 提交或基础映像更新时，会自动触发该任务。 尽管本文将 `acr-task.yaml` 称作包含步骤的文件，但 ACR 任务支持带有[受支持扩展名](#supported-task-filename-extensions)的任何有效文件名。

顶级 `acr-task.yaml` 基元为**任务属性**、**步骤类型**和**步骤属性**：

* [任务属性](#task-properties)应用到整个任务执行中的所有步骤。 有几个全局任务属性，包括：
  * `version`
  * `stepTimeout`
  * `workingDirectory`
* [任务步骤类型](#task-step-types)表示可在任务中执行的操作类型。 有三个步骤类型：
  * `build`
  * `push`
  * `cmd`
* [任务步骤属性](#task-step-properties)是应用到单个步骤的参数。 有多个步骤属性，其中包括：
  * `startDelay`
  * `timeout`
  * `when`
  * ...等等。

`acr-task.yaml` 文件的基本格式（包括一些通用步骤属性）如下。 尽管本文并未提供所有可用步骤属性或步骤类型用法的详尽表述，但提供了基本文件格式的简要概述。

```yml
version: # acr-task.yaml format version.
stepTimeout: # Seconds each step may take.
steps: # A collection of image or container actions.
  - build: # Equivalent to "docker build," but in a multi-tenant environment
  - push: # Push a newly built or retagged image to a registry.
    when: # Step property that defines either parallel or dependent step execution.
  - cmd: # Executes a container, supports specifying an [ENTRYPOINT] and parameters.
    startDelay: # Step property that specifies the number of seconds to wait before starting execution.
```

### <a name="supported-task-filename-extensions"></a>支持的任务文件扩展名

ACR 任务具有多个保留的文件扩展名（包括 `.yaml`），它将这些文件作为任务文件进行处理。 ACR 任务将以下列表中未列出的任何扩展名视为 Dockerfile：.yaml、.yml、.toml、.json、.sh、.bash、.zsh、.ps1、.ps、.cmd、.bat、.ts、.js、.php、.py、.rb、.lua

YAML 是 ACR 任务目前支持的唯一一种文件格式。 其他文件扩展名是保留的，将来可能受到支持。

## <a name="run-the-sample-tasks"></a>运行示例任务

本文的后续部分参考了多个示例任务文件。 这些示例任务在公共 GitHub 存储库 [Azure-Samples/acr-tasks][acr-tasks] 中提供。 可以使用 Azure CLI 命令 [az acr run][az-acr-run] 运行这些任务。 示例命令如下所示：

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

示例命令的格式假设已在 Azure CLI 中配置了默认注册表，因此省略了 `--registry` 参数。 若要配置默认注册表，请结合 [ 参数（接受 ][az-configure] 值）使用 `--defaults`az configure`acr=REGISTRY_NAME` 命令。

例如，若要在 Azure CLI 中配置名为“myregistry”的默认注册表：

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>任务属性

任务属性通常显示在 `acr-task.yaml` 文件的顶部，是在整个任务步骤执行中应用的全局属性。 其中的某些全局属性可在单个步骤中重写。

| 属性 | 类型 | 可选 | 说明 | 支持的重写 | 默认值 |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | 字符串 | 是 | ACR 任务服务分析的 `acr-task.yaml` 文件的版本。 ACR 任务致力于保持向后兼容性，而此值能使 ACR 任务与某个定义的版本保持兼容。 如果未指定，则默认为最新版本。 | 否 | 无 |
| `stepTimeout` | 整数（秒） | 是 | 步骤可以运行的最大秒数。 如果在任务中指定该属性，则会设置所有步骤的默认 `timeout` 属性。 如果在步骤中指定 `timeout` 属性，则会替代任务提供的属性。 | 是 | 600（10 分钟） |
| `workingDirectory` | 字符串 | 是 | 运行时期间容器的工作目录。 如果在任务中指定该属性，则会设置所有步骤的默认 `workingDirectory` 属性。 如果在步骤中指定，则会替代任务提供的属性。 | 是 | `$HOME` |
| `env` | [字符串, 字符串, ...] | 是 |  采用 `key=value` 格式的字符串数组，定义任务的环境变量。 如果在任务中指定该属性，则会设置所有步骤的默认 `env` 属性。 如果在步骤中指定，则会替代从任务继承的所有环境变量。 | 无 |
| `secrets` | [secret, secret, ...] | 是 | [secret](#secret) 对象的数组。 | 无 |
| `networks` | [network, network, ...] | 是 | [network](#network) 对象的数组。 | 无 |

### <a name="secret"></a>secret

secret 对象具有以下属性。

| 属性 | 类型 | 可选 | 说明 | 默认值 |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | 字符串 | 否 | 机密的标识符。 | 无 |
| `keyvault` | 字符串 | 是 | Azure Key Vault 机密 URL。 | 无 |
| `clientID` | 字符串 | 是 | Azure 资源的[用户分配的托管标识](container-registry-tasks-authentication-managed-identity.md)的客户端 ID。 | 无 |

### <a name="network"></a>网络

network 对象具有以下属性。

| 属性 | 类型 | 可选 | 说明 | 默认值 |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | 字符串 | 否 | 网络的名称。 | 无 |
| `driver` | 字符串 | 是 | 用于管理网络的驱动程序。 | 无 |
| `ipv6` | bool | 是 | IPv6 网络是否已启用。 | `false` |
| `skipCreation` | bool | 是 | 是否跳过网络创建过程。 | `false` |
| `isDefault` | bool | 是 | 该网络是否是随 Azure 容器注册表一起提供的默认网络 | `false` |

## <a name="task-step-types"></a>步骤任务类型

ACR 任务支持三种步骤类型。 每种步骤类型支持多个属性，每个步骤类型的相关部分中会予以详述。

| 步骤类型 | 说明 |
| --------- | ----------- |
| [`build`](#build) | 使用熟悉的 `docker build` 语法生成容器映像。 |
| [`push`](#push) | 执行 `docker push`，将新生成或重新标记的映像推送到容器注册表。 支持 Azure 容器注册表、其他专用注册表和公共 Docker 中心。 |
| [`cmd`](#cmd) | 结合传递给容器的 `[ENTRYPOINT]` 的参数，以命令形式运行容器。 `cmd` 步骤类型支持 `env`、`detach` 等参数和其他常见的 `docker run` 命令选项，可对并发容器执行启用单元测试和功能测试。 |

## <a name="build"></a>build

生成容器映像。 `build` 步骤类型表示在云中以多租户的安全方式将 `docker build` 作为第一类基元运行。

### <a name="syntax-build"></a>语法：build

```yml
version: v1.1.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

`build` 步骤类型支持下表中的参数。 `build` 步骤类型还支持 [docker build](https://docs.docker.com/engine/reference/commandline/build/) 命令的所有生成选项，例如 `--build-arg` 以设置生成时变量。

| 参数 | 说明 | 可选 |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | 定义所生成的映像的完全限定 `image:tag`。<br /><br />由于映像可用于内部任务验证（例如功能测试），并非所有映像都需要通过 `push` 推送到注册表。 但是，若要实例化任务执行中的某个映像，该映像确实需要引用某个名称。<br /><br />与 `az acr build` 不同，正在运行的 ACR 任务不提供默认的推送行为。 使用 ACR 任务时，默认方案假设能够生成、验证再推送映像。 请参阅 [push](#push)，了解如何选择性地推送所生成的映像。 | 是 |
| `-f` &#124; `--file` | 指定要传递给 `docker build` 的 Dockerfile。 如果未指定，则假设使用上下文根目录中的默认 Dockerfile。 若要指定 Dockerfile，请传递相对于上下文根目录的文件名。 | 是 |
| `context` | 传递给 `docker build` 的根目录。 每个任务的根目录设置为某个共享的 [workingDirectory](#task-step-properties)，包括关联的 Git 克隆目录所在的根目录。 | 否 |

### <a name="properties-build"></a>属性：build

`build` 步骤类型支持以下属性。 可在本文的[任务步骤属性](#task-step-properties)部分找到这些属性的详细信息。

| | | |
| -------- | ---- | -------- |
| `detach` | bool | 可选 |
| `disableWorkingDirectoryOverride` | bool | 可选 |
| `entryPoint` | 字符串 | 可选 |
| `env` | [字符串, 字符串, ...] | 可选 |
| `expose` | [字符串, 字符串, ...] | 可选 |
| `id` | 字符串 | 可选 |
| `ignoreErrors` | bool | 可选 |
| `isolation` | 字符串 | 可选 |
| `keep` | bool | 可选 |
| `network` | 对象 | 可选 |
| `ports` | [字符串, 字符串, ...] | 可选 |
| `pull` | bool | 可选 |
| `repeat` | int | 可选 |
| `retries` | int | 可选 |
| `retryDelay` | 整数（秒） | 可选 |
| `secret` | 对象 | 可选 |
| `startDelay` | 整数（秒） | 可选 |
| `timeout` | 整数（秒） | 可选 |
| `when` | [字符串, 字符串, ...] | 可选 |
| `workingDirectory` | 字符串 | 可选 |

### <a name="examples-build"></a>示例：build

#### <a name="build-image---context-in-root"></a>生成映像 - 根目录中的上下文

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>生成映像 - 子目录中的上下文

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>push

将生成或重新标记的一个或多个映像推送到容器注册表。 支持推送到 Azure 容器注册表等专用注册表，或推送到公共 Docker 中心。

### <a name="syntax-push"></a>语法：push

`push` 步骤类型支持映像集合。 YAML 集合语法支持内联和嵌套格式。 推送单个映像的操作通常使用内联语法来表示：

```yml
version: v1.1.0
steps:
  # Inline YAML collection syntax
  - push: ["$Registry/hello-world:$ID"]
```

为方便阅读，请在推送多个映像时使用嵌套语法：

```yml
version: v1.1.0
steps:
  # Nested YAML collection syntax
  - push:
    - $Registry/hello-world:$ID
    - $Registry/hello-world:latest
```

### <a name="properties-push"></a>属性：push

`push` 步骤类型支持以下属性。 可在本文的[任务步骤属性](#task-step-properties)部分找到这些属性的详细信息。

| | | |
| -------- | ---- | -------- |
| `env` | [字符串, 字符串, ...] | 可选 |
| `id` | 字符串 | 可选 |
| `ignoreErrors` | bool | 可选 |
| `startDelay` | 整数（秒） | 可选 |
| `timeout` | 整数（秒） | 可选 |
| `when` | [字符串, 字符串, ...] | 可选 |

### <a name="examples-push"></a>示例：push

#### <a name="push-multiple-images"></a>推送多个映像

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>生成、推送和运行

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

`cmd` 步骤类型运行容器。

### <a name="syntax-cmd"></a>语法：cmd

```yml
version: v1.1.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>属性：cmd

`cmd` 步骤类型支持以下属性：

| | | |
| -------- | ---- | -------- |
| `detach` | bool | 可选 |
| `disableWorkingDirectoryOverride` | bool | 可选 |
| `entryPoint` | 字符串 | 可选 |
| `env` | [字符串, 字符串, ...] | 可选 |
| `expose` | [字符串, 字符串, ...] | 可选 |
| `id` | 字符串 | 可选 |
| `ignoreErrors` | bool | 可选 |
| `isolation` | 字符串 | 可选 |
| `keep` | bool | 可选 |
| `network` | 对象 | 可选 |
| `ports` | [字符串, 字符串, ...] | 可选 |
| `pull` | bool | 可选 |
| `repeat` | int | 可选 |
| `retries` | int | 可选 |
| `retryDelay` | 整数（秒） | 可选 |
| `secret` | 对象 | 可选 |
| `startDelay` | 整数（秒） | 可选 |
| `timeout` | 整数（秒） | 可选 |
| `when` | [字符串, 字符串, ...] | 可选 |
| `workingDirectory` | 字符串 | 可选 |

可在本文的[任务步骤属性](#task-step-properties)部分找到这些属性的详细信息。

### <a name="examples-cmd"></a>示例：cmd

#### <a name="run-hello-world-image"></a>运行 hello-world 映像

此命令执行 `hello-world.yaml` 任务文件，此文件引用 Docker 中心内的 [hello-world](https://hub.docker.com/_/hello-world/) 映像。

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>运行 bash 映像并回显“hello world”

此命令执行 `bash-echo.yaml` 任务文件，此文件引用 Docker 中心内的 [bash](https://hub.docker.com/_/bash/) 映像。

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>运行特定的 bash 映像标记

若要运行特定的映像版本，请在 `cmd` 中指定标记。

此命令执行 `bash-echo-3.yaml` 任务文件，此文件引用 Docker 中心内的 [bash:3.0](https://hub.docker.com/_/bash/) 映像。

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>运行自定义映像

`cmd` 步骤类型使用标准的 `docker run` 格式引用映像。 不是以注册表名称开头的映像被认为源自 docker.io。 下面是前一示例的同等表示形式：

```yml
version: v1.1.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

使用标准的 `docker run` 映像引用约定，`cmd` 可以运行任何专用注册表或公共 Docker Hub 中的映像。 如果引用执行 ACR 任务的同一注册表中的映像，则无需指定任何注册表凭据。

* 运行 Azure 容器注册表中的映像。 下面的示例假定你有一个名为 `myregistry`的注册表和一个自定义映像 `myimage:mytag`。

    ```yml
    version: v1.1.0
    steps:
        - cmd: myregistry.azurecr.io/myimage:mytag
    ```

* 使用运行变量或别名通用化注册表引用

    你可以使用[运行变量](#run-variables)或[别名](#aliases)，使其更易于移植，而不是在 `acr-task.yaml` 文件中对你的注册表名称进行硬编码。 `Run.Registry` 变量或 `$Registry` 别名在运行时扩展到正在执行任务的注册表的名称。

    例如，若要通用化前面的任务，使其在任何 Azure 容器注册表中运行，请在映像名称中引用 $Registry 变量：

    ```yml
    version: v1.1.0
    steps:
      - cmd: $Registry/myimage:mytag
    ```

## <a name="task-step-properties"></a>任务步骤属性

每个步骤类型支持适用于其类型的多个属性。 下表定义了所有可用的步骤属性。 并非所有步骤类型都支持所有属性。 若要查看其中的哪些属性可用于每个步骤类型，请参阅 [cmd](#cmd)、[build](#build) 和 [push](#push) 步骤类型参考部分。

| 属性 | 类型 | 可选 | 说明 | 默认值 |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | bool | 是 | 在运行时是否应分离容器。 | `false` |
| `disableWorkingDirectoryOverride` | bool | 是 | 是否禁用 `workingDirectory` 重写功能。 将此属性与 `workingDirectory` 结合使用可以全面控制容器的工作目录。 | `false` |
| `entryPoint` | 字符串 | 是 | 重写步骤容器的 `[ENTRYPOINT]`。 | 无 |
| `env` | [字符串, 字符串, ...] | 是 | 采用 `key=value` 格式的字符串数组，定义步骤的环境变量。 | 无 |
| `expose` | [字符串, 字符串, ...] | 是 | 从容器公开的端口的数组。 |  无 |
| [`id`](#example-id) | 字符串 | 是 | 唯一标识任务中的步骤。 任务中的其他步骤可以引用步骤的 `id`，例如，使用 `when` 执行依赖项检查。<br /><br />`id` 也是正在运行的容器的名称。 例如，在任务的其他容器中运行的进程可以引用 `id` 作为其 DNS 主机名，或者通过 Docker 日志 [id] 来访问该步骤。 | `acb_step_%d`，其中，`%d` 是 YAML 文件中的步骤的从 0 开始的索引（按从上到下的顺序排列）。 |
| `ignoreErrors` | bool | 是 | 是否将步骤标记为成功（无论容器执行过程中是否发生了错误）。 | `false` |
| `isolation` | 字符串 | 是 | 容器的隔离级别。 | `default` |
| `keep` | bool | 是 | 执行后是否应保留该步骤的容器。 | `false` |
| `network` | 对象 | 是 | 标识运行容器的网络。 | 无 |
| `ports` | [字符串, 字符串, ...] | 是 | 从容器发布到主机的端口的数组。 |  无 |
| `pull` | bool | 是 | 在执行容器之前是否强制提取容器以防止任何缓存行为。 | `false` |
| `privileged` | bool | 是 | 是否在特权模式下运行容器。 | `false` |
| `repeat` | int | 是 | 尝试重复执行容器的次数。 | 0 |
| `retries` | int | 是 | 容器执行失败时的重试次数。 仅当容器的退出代码非零时才重试。 | 0 |
| `retryDelay` | 整数（秒） | 是 | 重试容器执行的间隔延迟（以秒为单位）。 | 0 |
| `secret` | 对象 | 是 | 标识 Azure Key Vault 机密或 [Azure 资源的托管标识](container-registry-tasks-authentication-managed-identity.md)。 | 无 |
| `startDelay` | 整数（秒） | 是 | 将容器执行延迟的秒数。 | 0 |
| `timeout` | 整数（秒） | 是 | 步骤在终止之前可以执行的最大秒数。 | 600 |
| [`when`](#example-when) | [字符串, 字符串, ...] | 是 | 配置某个步骤对任务中其他一个或多个步骤的依赖。 | 无 |
| `user` | 字符串 | 是 | 容器的用户名或 UID | 无 |
| `workingDirectory` | 字符串 | 是 | 设置步骤的工作目录。 默认情况下，ACR 任务会创建一个根目录作为工作目录。 但是，如果生成包含多个步骤，则前面的步骤可以通过指定相同的工作目录，来与后面的步骤共享项目。 | `$HOME` |

### <a name="examples-task-step-properties"></a>示例：任务步骤属性

#### <a name="example-id"></a>示例：id

生成两个映像，并实例化功能测试映像。 每个步骤由任务中的其他步骤在其 `id` 属性中引用的唯一 `when` 进行标识。

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>示例：when

`when` 属性指定某个步骤对任务中其他一个或多个步骤的依赖。 它支持两个参数值：

* `when: ["-"]` - 指示不依赖于其他步骤。 指定 `when: ["-"]` 的步骤将立即执行，并启用并发步骤执行。
* `when: ["id1", "id2"]` - 指示该步骤依赖于使用 `id`“id1”和 `id`“id2”的步骤。 在“id1”和“id2”步骤完成之前，此步骤不会执行。

如果未在某个步骤中指定 `when`，则该步骤依赖于 `acr-task.yaml` 文件中上一个步骤的完成。

在不指定 `when` 的情况下按顺序执行步骤：

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

在指定 `when` 的情况下按顺序执行步骤：

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

并行映像生成：

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel.yaml)]

并行映像生成和依赖测试：

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Run 变量

ACR 任务包含一组在执行时可供任务步骤使用的默认变量。 可以使用 `{{.Run.VariableName}}` 格式访问这些变量，其中，`VariableName` 是以下值之一：

* `Run.ID`
* `Run.SharedVolume`
* `Run.Registry`
* `Run.RegistryName`
* `Run.Date`
* `Run.OS`
* `Run.Architecture`
* `Run.Commit`
* `Run.Branch`
* `Run.TaskName`

变量名称通常一目了然。 下面是常用变量的详细信息。 从 YAML 版本 `v1.1.0`开始，你可以使用缩写的预定义[任务别名](#aliases)来代替大多数运行变量。 例如，若要使用 `{{.Run.Registry}}`，请使用 `$Registry` 别名。

### <a name="runid"></a>Run.ID

每次运行时，通过 `az acr task create`创建的任务的 `az acr run`或触发器的执行都具有唯一的 ID。 此 ID 表示当前正在执行的运行。

通常用于唯一标记某个映像：

```yml
version: v1.1.0
steps:
    - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistry"></a>Run.Registry

注册表的完全限定服务器名称。 通常用于泛式引用正在运行任务的注册表。

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistryname"></a>运行 RegistryName

容器注册表的名称。 通常用于不需要完全限定的服务器名称的任务步骤中，例如 `cmd` 在注册表上运行 Azure CLI 命令的步骤。

```yml
version 1.1.0
steps:
# List repositories in registry
- cmd: az login --identity
- cmd: az acr repository list --name $RegistryName
```

### <a name="rundate"></a>Run.Date

运行开始时的当前 UTC 时间。

### <a name="runcommit"></a>Run.Commit

对于提交到 GitHub 存储库后触发的任务，值为提交标识符。

### <a name="runbranch"></a>Run.Branch

对于提交到 GitHub 存储库后触发的任务，值为分支名称。

## <a name="aliases"></a>别名

`v1.1.0`，在执行时，ACR 任务支持任务步骤可用的别名。 别名在概念上类似于 bash 中支持的别名（命令快捷方式）以及一些其他命令 shell。 

使用别名，可以通过输入单个单词来启动任何命令或命令组（包括选项和文件名）。

ACR 任务支持多个预定义的别名以及您创建的自定义别名。

### <a name="predefined-aliases"></a>预定义别名

以下任务别名可用于代替[运行变量](#run-variables)：

| 别名 | 运行变量 |
| ----- | ------------ |
| `ID` | `Run.ID` |
| `SharedVolume` | `Run.SharedVolume` |
| `Registry` | `Run.Registry` |
| `RegistryName` | `Run.RegistryName` |
| `Date` | `Run.Date` |
| `OS` | `Run.OS` |
| `Architecture` | `Run.Architecture` |
| `Commit` | `Run.Commit` |
| `Branch` | `Run.Branch` |

在任务步骤中，在别名前面加上 `$` 指令，如本示例所示：

```yaml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID -f hello-world.dockerfile .
```

### <a name="image-aliases"></a>图像别名

以下每个别名指向 Microsoft 容器注册表（MCR）中的稳定映像。 您可以在任务文件的 `cmd` 部分中引用每个文件，而无需使用指令。

| 别名 | 映像 |
| ----- | ----- |
| `acr` | `mcr.microsoft.com/acr/acr-cli:0.1` |
| `az` | `mcr.microsoft.com/acr/azure-cli:d0725bc` |
| `bash` | `mcr.microsoft.com/acr/bash:d0725bc` |
| `curl` | `mcr.microsoft.com/acr/curl:d0725bc` |

以下示例任务使用几个别名在运行注册表中的存储库中[清除](container-registry-auto-purge.md)超过7天的映像标记 `samples/hello-world`：

```yaml
version: v1.1.0
steps:
  - cmd: acr tag list --registry $RegistryName --repository samples/hello-world
  - cmd: acr purge --registry $RegistryName --filter samples/hello-world:.* --ago 7d
```

### <a name="custom-alias"></a>自定义别名

在 YAML 文件中定义自定义别名，并使用它，如以下示例中所示。 别名只能包含字母数字字符。 用于展开别名的默认指令是 `$` 字符。

```yml
version: v1.1.0
alias:
  values:
    repo: myrepo
steps:
  - build: -t $Registry/$repo/hello-world:$ID -f Dockerfile .
```

可以链接到远程或本地 YAML 文件，以获取自定义别名定义。 以下示例链接到 Azure blob 存储中的 YAML 文件：

```yml
version: v1.1.0
alias:
  src:  # link to local or remote custom alias files
    - 'https://link/to/blob/remoteAliases.yml?readSasToken'
[...]
```

## <a name="next-steps"></a>后续步骤

有关多步骤任务的概述，请参阅[在 ACR 任务中运行多步骤生成、测试和修补任务](container-registry-tasks-multi-step.md)。

有关单步骤生成，请参阅 [ACR 任务概述](container-registry-tasks-overview.md)。



<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-configure]: /cli/azure/reference-index#az-configure
