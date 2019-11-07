---
title: Docker 容器的配方
titleSuffix: Azure Cognitive Services
description: 了解如何使用部分或全部配置设置生成、测试和存储容器，以便进行部署和重复使用。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: dbe2e288309b6682041bf3db9fe3d39455359806
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647283"
---
# <a name="create-containers-for-reuse"></a>创建要重复使用的容器

使用这些容器配方来创建可重用的认知服务容器。 可以生成包含一部分或所有配置设置的容器，使容器在启动时不需要获取这些设置。

创建此新容器层（包含设置）并在本地对其进行测试后，可将容器存储在容器注册表中。 容器启动时，它只需获取当前未存储在容器中的设置。 专用注册表容器提供用于传入这些设置的配置空间。

## <a name="docker-run-syntax"></a>Docker 运行语法

本文档中的任何 `docker run` 示例假设使用 Windows 控制台，其中包含 `^` 行继续符。 在自行使用时，请注意以下几点：

* 除非非常熟悉 docker 容器，否则不要更改参数顺序。
* 如果使用的操作系统不是 Windows，或者使用的控制台不是 Windows 控制台，请使用正确的控制台/终端、装入点的文件夹语法，以及适用于该控制台和系统的行继续符。  由于认知服务容器是一个 Linux 操作系统，因此目标载入点将使用 Linux 样式的文件夹语法。
* `docker run` 示例使用 `c:` 驱动器外的目录来避免 Windows 上的任何权限冲突。 如果需要使用特定目录作为输入目录，则需要授予 docker 服务权限。

## <a name="store-no-configuration-settings-in-image"></a>不会在映像中存储任何配置设置

每个服务的示例 `docker run` 命令不会将任何配置设置存储在容器中。 从控制台或注册表服务启动容器时，需要传入这些配置设置。 专用注册表容器提供用于传入这些设置的配置空间。

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>重用配方：将所有配置设置存储到容器中

若要存储所有配置设置，请使用这些设置创建 `Dockerfile`。

此方法的问题：

* 新容器的名称和标记不同于原始容器。
* 若要更改这些设置，必须更改 Dockerfile 的值，重新生成映像，然后将其重新发布到注册表。
* 如果有人获取了容器注册表或本地主机的访问权限，则他们可以运行该容器并使用认知服务终结点。
* 如果认知服务不需要输入装入点，请不要将 `COPY` 行添加到 Dockerfile。

创建 Dockerfile，从要使用的现有认知服务容器提取数据，然后在 Dockerfile 中使用 docker 命令设置或提取容器所需的信息。

本示例：

* 使用 `{BILLING_ENDPOINT}` 通过主机的环境密钥设置计费终结点 `ENV`。
* 使用 `ENV 通过主机的环境密钥设置计费 API 密钥 `{ENDPOINT_KEY}`。

### <a name="reuse-recipe-store-billing-settings-with-container"></a>重用配方：将计费设置存储到容器中

本示例演示如何基于 Dockerfile 生成文本分析的情绪容器。

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

根据需要，[在本地](#how-to-use-container-on-your-local-host)或者从[专用注册表容器](#how-to-add-container-to-private-registry)生成并运行容器。

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>重用配方：将计费和装载设置存储到容器中

本示例演示如何使用语言理解，以及如何保存 Dockerfile 中的计费和模型。

* 使用 `COPY` 从主机的文件系统复制语言理解 (LUIS) 模型文件。
* LUIS 容器支持多个模型。 如果所有模型存储在同一文件夹中，则只需一个 `COPY` 语句。
* 从模型输入目录的相对父级运行 Docker 文件。 以下示例从 `docker build` 的相对父级运行 `docker run` 和 `/input` 命令。 `/input` 命令中的第一个 `COPY` 为主计算机的目录。 第二个 `/input` 为容器的目录。

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

根据需要，[在本地](#how-to-use-container-on-your-local-host)或者从[专用注册表容器](#how-to-add-container-to-private-registry)生成并运行容器。

## <a name="how-to-use-container-on-your-local-host"></a>如何在本地主机上使用容器

若要生成 Docker 文件，请将 `<your-image-name>` 替换为映像的新名称，然后使用：

```console
docker build -t <your-image-name> .
```

若要运行映像，并在容器停止时将其删除 (`--rm`)：

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>如何将容器添加到专用注册表

遵循以下步骤使用 Dockerfile，并将新映像放在专用容器注册表中。  

1. 使用重用配方中的文本创建 `Dockerfile`。 `Dockerfile` 没有扩展。

1. 请将尖括号中的所有值替换为自己的值。

1. 在命令行或终端中，使用以下命令将文件生成为映像。 请将尖括号 `<>` 中的值替换为自己的容器名称和标记。  

    可以使用标记选项 `-t` 添加有关对容器做了哪些更改的信息。 例如，容器名称 `modified-LUIS` 表示已将原始容器分层。 标记名称 `with-billing-and-model` 表示语言理解 (LUIS) 容器的修改方式。

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. 从控制台登录到 Azure CLI。 此命令打开浏览器并要求身份验证。 完成身份验证后，可以关闭浏览器并在控制台中继续操作。

    ```azurecli
    az login
    ```

1. 在控制台中使用 Azure CLI 登录到专用注册表。

    请将尖括号 `<my-registry>` 中的值替换为自己的注册表名称。  

    ```azurecli
    az acr login --name <my-registry>
    ```

    如果分配了服务主体，则还可以使用 docker login 登录。

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. 使用专用注册表位置标记容器。 请将尖括号 `<my-registry>` 中的值替换为自己的注册表名称。 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    如果不使用标记名称，则表示要使用 `latest`。

1. 将新映像推送到专用容器注册表。 查看专用容器注册表时，以下 CLI 命令中使用的容器名称是存储库的名称。

    ```Bash
    docker push <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建和使用 Azure 容器实例](azure-container-instance-recipe.md)

<!--
## Store input and output configuration settings

Bake in input params only

FROM containerpreview.azurecr.io/microsoft/cognitive-services-luis:<tag>
COPY luisModel1 /input/
COPY luisModel2 /input/

## Store all configuration settings

If you are a single manager of the container, you may want to store all settings in the container. The new, resulting container will not need any variables passed in to run. 

Issues with this approach:

* In order to change these settings, you will have to change the values of the Dockerfile and rebuild the file. 
* If someone gets access to your container registry or your local host, they can run the container and use the Cognitive Services endpoints. 

The following _partial_ Dockerfile shows how to statically set the values for billing and model. This example uses the 

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing=<billing value>
ENV apikey=<apikey value>
COPY luisModel1 /input/
COPY luisModel2 /input/
```

->