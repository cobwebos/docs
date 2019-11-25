---
title: YAML reference - ACR Tasks
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

ACR 任务中的多步骤任务定义提供注重于生成、测试和修补容器的，以容器为中心的计算基元。 This article covers the commands, parameters, properties, and syntax for the YAML files that define your multi-step tasks.

本文包含有关为 ACR 任务创建多步骤任务 YAML 文件的参考信息。 如需 ACR 任务的简介，请参阅 [ACR 任务概述](container-registry-tasks-overview.md)。

## <a name="acr-taskyaml-file-format"></a>acr-task.yaml 文件格式

ACR 任务支持采用标准 YAML 语法的多步骤任务声明。 You define a task's steps in a YAML file. You can then run the task manually by passing the file to the [az acr run][az-acr-run] command. Or, use the file to create a task with [az acr task create][az-acr-task-create] that's triggered automatically on a Git commit or base image update. 尽管本文将 `acr-task.yaml` 称作包含步骤的文件，但 ACR 任务支持带有[受支持扩展名](#supported-task-filename-extensions)的任何有效文件名。

顶级 `acr-task.yaml` 基元为**任务属性**、**步骤类型**和**步骤属性**：

* [任务属性](#task-properties)应用到整个任务执行中的所有步骤。 There are several global task properties, including:
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

本文的后续部分参考了多个示例任务文件。 The sample tasks are in a public GitHub repository, [Azure-Samples/acr-tasks][acr-tasks]. You can run them with the Azure CLI command [az acr run][az-acr-run]. 示例命令如下所示：

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

示例命令的格式假设已在 Azure CLI 中配置了默认注册表，因此省略了 `--registry` 参数。 To configure a default registry, use the [az configure][az-configure] command with the `--defaults` parameter, which accepts an `acr=REGISTRY_NAME` value.

例如，若要在 Azure CLI 中配置名为“myregistry”的默认注册表：

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>任务属性

Task properties typically appear at the top of an `acr-task.yaml` file, and are global properties that apply throughout the full execution of the task steps. 其中的某些全局属性可在单个步骤中重写。

| properties | Type | 可选 | 描述 | 支持的重写 | 默认值 |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | 字符串 | 是 | ACR 任务服务分析的 `acr-task.yaml` 文件的版本。 ACR 任务致力于保持向后兼容性，而此值能使 ACR 任务与某个定义的版本保持兼容。 If unspecified, defaults to the latest version. | No | None |
| `stepTimeout` | 整数（秒） | 是 | 步骤可以运行的最大秒数。 If the property is specified on a task, it sets the default `timeout` property of all the steps. If the `timeout` property is specified on a step, it overrides the property provided by the task. | 是 | 600（10 分钟） |
| `workingDirectory` | 字符串 | 是 | The working directory of the container during runtime. If the property is specified on a task, it sets the default `workingDirectory` property of all the steps. If specified on a step, it overrides the property provided by the task. | 是 | `$HOME` |
| `env` | [字符串, 字符串, ...] | 是 |  Array of strings in `key=value` format that define the environment variables for the task. If the property is specified on a task, it sets the default `env` property of all the steps. If specified on a step, it overrides any environment variables inherited from the task. | None |
| `secrets` | [secret, secret, ...] | 是 | Array of [secret](#secret) objects. | None |
| `networks` | [network, network, ...] | 是 | Array of [network](#network) objects. | None |

### <a name="secret"></a>secret

The secret object has the following properties.

| properties | Type | 可选 | 描述 | 默认值 |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | 字符串 | No | The identifier of the secret. | None |
| `keyvault` | 字符串 | 是 | The Azure Key Vault Secret URL. | None |
| `clientID` | 字符串 | 是 | The client ID of the [user-assigned managed identity](container-registry-tasks-authentication-managed-identity.md) for Azure resources. | None |

### <a name="network"></a>网络

The network object has the following properties.

| properties | Type | 可选 | 描述 | 默认值 |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | 字符串 | No | 网络的名称。 | None |
| `driver` | 字符串 | 是 | The driver to manage the network. | None |
| `ipv6` | bool | 是 | Whether IPv6 networking is enabled. | `false` |
| `skipCreation` | bool | 是 | Whether to skip network creation. | `false` |
| `isDefault` | bool | 是 | Whether the network is a default network provided with Azure Container Registry | `false` |

## <a name="task-step-types"></a>步骤任务类型

ACR 任务支持三种步骤类型。 每种步骤类型支持多个属性，每个步骤类型的相关部分中会予以详述。

| 步骤类型 | 描述 |
| --------- | ----------- |
| [`build`](#build) | 使用熟悉的 `docker build` 语法生成容器映像。 |
| [`push`](#push) | 执行 `docker push`，将新生成或重新标记的映像推送到容器注册表。 支持 Azure 容器注册表、其他专用注册表和公共 Docker 中心。 |
| [`cmd`](#cmd) | 结合传递给容器的 `[ENTRYPOINT]` 的参数，以命令形式运行容器。 The `cmd` step type supports parameters like `env`, `detach`, and other familiar `docker run` command options, enabling unit and functional testing with concurrent container execution. |

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

| 参数 | 描述 | 可选 |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | 定义所生成的映像的完全限定 `image:tag`。<br /><br />由于映像可用于内部任务验证（例如功能测试），并非所有映像都需要通过 `push` 推送到注册表。 但是，若要实例化任务执行中的某个映像，该映像确实需要引用某个名称。<br /><br />Unlike `az acr build`, running ACR Tasks doesn't provide default push behavior. 使用 ACR 任务时，默认方案假设能够生成、验证再推送映像。 请参阅 [push](#push)，了解如何选择性地推送所生成的映像。 | 是 |
| `-f` &#124; `--file` | 指定要传递给 `docker build` 的 Dockerfile。 如果未指定，则假设使用上下文根目录中的默认 Dockerfile。 To specify a Dockerfile, pass the filename relative to the root of the context. | 是 |
| `context` | 传递给 `docker build` 的根目录。 每个任务的根目录设置为某个共享的 [workingDirectory](#task-step-properties)，包括关联的 Git 克隆目录所在的根目录。 | No |

### <a name="properties-build"></a>属性：build

`build` 步骤类型支持以下属性。 Find details of these properties in the [Task step properties](#task-step-properties) section of this article.

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

`push` 步骤类型支持以下属性。 Find details of these properties in the [Task step properties](#task-step-properties) section of this article.

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

By using the standard `docker run` image reference convention, `cmd` can run images from any private registry or the public Docker Hub. 如果引用执行 ACR 任务的同一注册表中的映像，则无需指定任何注册表凭据。

* Run an image that's from an Azure container registry. The following example assumes you have a registry named `myregistry`, and a custom image `myimage:mytag`.

    ```yml
    version: v1.1.0
    steps:
        - cmd: myregistry.azurecr.io/myimage:mytag
    ```

* Generalize the registry reference with a Run variable or alias

    Instead of hard-coding your registry name in an `acr-task.yaml` file, you can make it more portable by using a [Run variable](#run-variables) or [alias](#aliases). The `Run.Registry` variable or `$Registry` alias expands at runtime to the name of the registry in which the task is executing.

    For example, to generalize the preceding task so that it works in any Azure container registry, reference the $Registry variable in the image name:

    ```yml
    version: v1.1.0
    steps:
      - cmd: $Registry/myimage:mytag
    ```

## <a name="task-step-properties"></a>任务步骤属性

每个步骤类型支持适用于其类型的多个属性。 下表定义了所有可用的步骤属性。 并非所有步骤类型都支持所有属性。 若要查看其中的哪些属性可用于每个步骤类型，请参阅 [cmd](#cmd)、[build](#build) 和 [push](#push) 步骤类型参考部分。

| properties | Type | 可选 | 描述 | 默认值 |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | bool | 是 | 在运行时是否应分离容器。 | `false` |
| `disableWorkingDirectoryOverride` | bool | 是 | Whether to disable `workingDirectory` override functionality. Use this in combination with `workingDirectory` to have complete control over the container's working directory. | `false` |
| `entryPoint` | 字符串 | 是 | 重写步骤容器的 `[ENTRYPOINT]`。 | None |
| `env` | [字符串, 字符串, ...] | 是 | 采用 `key=value` 格式的字符串数组，定义步骤的环境变量。 | None |
| `expose` | [字符串, 字符串, ...] | 是 | Array of ports that are exposed from the container. |  None |
| [`id`](#example-id) | 字符串 | 是 | 唯一标识任务中的步骤。 任务中的其他步骤可以引用步骤的 `id`，例如，使用 `when` 执行依赖项检查。<br /><br />`id` 也是正在运行的容器的名称。 例如，在任务的其他容器中运行的进程可以引用 `id` 作为其 DNS 主机名，或者通过 Docker 日志 [id] 来访问该步骤。 | `acb_step_%d`, where `%d` is the 0-based index of the step top-down in the YAML file |
| `ignoreErrors` | bool | 是 | Whether to mark the step as successful regardless of whether an error occurred during container execution. | `false` |
| `isolation` | 字符串 | 是 | The isolation level of the container. | `default` |
| `keep` | bool | 是 | 执行后是否应保留该步骤的容器。 | `false` |
| `network` | 对象 | 是 | Identifies a network in which the container runs. | None |
| `ports` | [字符串, 字符串, ...] | 是 | Array of ports that are published from the container to the host. |  None |
| `pull` | bool | 是 | Whether to force a pull of the container before executing it to prevent any caching behavior. | `false` |
| `privileged` | bool | 是 | Whether to run the container in privileged mode. | `false` |
| `repeat` | int | 是 | The number of retries to repeat the execution of a container. | 0 |
| `retries` | int | 是 | The number of retries to attempt if a container fails its execution. A retry is only attempted if a container's exit code is non-zero. | 0 |
| `retryDelay` | 整数（秒） | 是 | The delay in seconds between retries of a container's execution. | 0 |
| `secret` | 对象 | 是 | Identifies an Azure Key Vault secret or [managed identity for Azure resources](container-registry-tasks-authentication-managed-identity.md). | None |
| `startDelay` | 整数（秒） | 是 | Number of seconds to delay a container's execution. | 0 |
| `timeout` | 整数（秒） | 是 | 步骤在终止之前可以执行的最大秒数。 | 600 |
| [`when`](#example-when) | [字符串, 字符串, ...] | 是 | 配置某个步骤对任务中其他一个或多个步骤的依赖。 | None |
| `user` | 字符串 | 是 | The user name or UID of a container | None |
| `workingDirectory` | 字符串 | 是 | 设置步骤的工作目录。 默认情况下，ACR 任务会创建一个根目录作为工作目录。 但是，如果生成包含多个步骤，则前面的步骤可以通过指定相同的工作目录，来与后面的步骤共享项目。 | `$HOME` |

### <a name="examples-task-step-properties"></a>示例：任务步骤属性

#### <a name="example-id"></a>示例：id

生成两个映像，并实例化功能测试映像。 每个步骤由任务中的其他步骤在其 `when` 属性中引用的唯一 `id` 进行标识。

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

Parallel images build:

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

The variable names are generally self-explanatory. Details follows for commonly used variables. As of YAML version `v1.1.0`, you can use an abbreviated, predefined [task alias](#aliases) in place of most run variables. For example, in place of `{{.Run.Registry}}`, use the `$Registry` alias.

### <a name="runid"></a>Run.ID

Each Run, through `az acr run`, or trigger based execution of tasks created through `az acr task create`, has a unique ID. 此 ID 表示当前正在执行的运行。

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

### <a name="runregistryname"></a>Run.RegistryName

The name of the container registry. Typically used in task steps that don't require a fully qualified server name, for example, `cmd` steps that run Azure CLI commands on registries.

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

For a task triggered by a commit to a GitHub repository, the commit identifier.

### <a name="runbranch"></a>Run.Branch

For a task triggered by a commit to a GitHub repository, the branch name.

## <a name="aliases"></a>别名

As of `v1.1.0`, ACR Tasks supports aliases that are available to task steps when they execute. Aliases are similar in concept to aliases (command shortcuts) supported in bash and some other command shells. 

With an alias, you can launch any command or group of commands (including options and filenames) by entering a single word.

ACR Tasks supports several predefined aliases and also custom aliases you create.

### <a name="predefined-aliases"></a>Predefined aliases

The following task aliases are available to use in place of [run variables](#run-variables):

| 别名 | Run variable |
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

In task steps, precede an alias with the `$` directive, as in this example:

```yaml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID -f hello-world.dockerfile .
```

### <a name="image-aliases"></a>Image aliases

Each of the following aliases points to a stable image in Microsoft Container Registry (MCR). You can refer to each of them in the `cmd` section of a Task file without using a directive.

| 别名 | 图像 |
| ----- | ----- |
| `acr` | `mcr.microsoft.com/acr/acr-cli:0.1` |
| `az` | `mcr.microsoft.com/acr/azure-cli:d0725bc` |
| `bash` | `mcr.microsoft.com/acr/bash:d0725bc` |
| `curl` | `mcr.microsoft.com/acr/curl:d0725bc` |

The following example task uses several aliases to [purge](container-registry-auto-purge.md) image tags older than 7 days in the repo `samples/hello-world` in the run registry:

```yaml
version: v1.1.0
steps:
  - cmd: acr tag list --registry $RegistryName --repository samples/hello-world
  - cmd: acr purge --registry $RegistryName --filter samples/hello-world:.* --ago 7d
```

### <a name="custom-alias"></a>Custom alias

Define a custom alias in your YAML file and use it as shown in the following example. An alias can contain only alphanumeric characters. The default directive to expand an alias is the `$` character.

```yml
version: v1.1.0
alias:
  values:
    repo: myrepo
steps:
  - build: -t $Registry/$repo/hello-world:$ID -f Dockerfile .
```

You can link to a remote or local YAML file for custom alias definitions. The following example links to a YAML file in Azure blob storage:

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
