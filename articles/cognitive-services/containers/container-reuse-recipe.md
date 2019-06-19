---
title: 用于 Docker 容器方案
titleSuffix: Azure Cognitive Services
description: 使用这些容器方案来创建可重复使用的认知服务容器。 以便在不需要启动容器时，可以使用部分或全部配置设置生成容器。 一旦具有这一新的容器层 （设置），并且你已在本地测试，您可以在容器注册表中存储容器。 容器启动时，它将仅需要当前未在容器中存储这些设置。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 06/12/2019
ms.author: dapine
ms.openlocfilehash: a2fd9073bfb472f52d5dbf60538214c8b4f3d360
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67207476"
---
# <a name="create-containers-for-reuse"></a>创建以供重复使用容器

使用这些容器方案来创建可重复使用的认知服务容器。 可以使用部分或全部配置设置生成容器，以便它们_不_启动容器时需要。

一旦具有这一新的容器层 （设置），并且你已在本地测试，您可以在容器注册表中存储容器。 容器启动时，它将仅需要当前未在容器中存储这些设置。 专用注册表容器配置为您提供空间以传递中的这些设置。

## <a name="docker-run-syntax"></a>Docker 运行语法

任何`docker run`本文档中的示例假定使用一个 Windows 控制台`^`行继续符。 请考虑以下供自己使用：

* 除非非常熟悉 docker 容器，否则不要更改参数顺序。
* 如果使用的 Windows，以外的操作系统或非 Windows 控制台的控制台，使用正确的控制台终端，文件夹语法进行装载和用于控制台和系统的行继续符。  认知服务容器是 Linux 操作系统，因为目标装入使用 Linux 样式文件夹语法。
* `docker run` 示例使用目录关闭`c:`驱动器以避免在 Windows 上的任何权限冲突。 如果需要使用特定目录作为输入目录，则需要授予 docker 服务权限。

## <a name="store-no-configuration-settings-in-image"></a>在图像中存储任何配置设置

该示例`docker run`命令为每个服务是否在容器中存储的任何配置设置。 从控制台或注册表的服务启动容器时，需要传入这些配置设置。 专用注册表容器配置为您提供空间以传递中的这些设置。

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>重复使用方案： 将与容器的所有配置设置都存储

若要存储的所有配置设置，创建`Dockerfile`使用这些设置。

使用此方法的问题：

* 新的容器具有单独的名称和原始容器中的标记。
* 若要更改这些设置，你将需要更改 Dockerfile 的值、 重新生成映像，并重新发布到注册表。
* 如果有人获取对容器注册表或本地主机访问，它们可以运行容器，并使用认知服务终结点。
* 如果你的认知服务不需要输入的装载，请勿添加`COPY`到 Dockerfile 的行。

创建 Dockerfile，从现有的认知服务容器，你想要使用，然后使用 Dockerfile 中的 docker 命令来设置或信息中拉取容器需要。

此示例中：

* 设置计费终结点`{BILLING_ENDPOINT}`从主机的环境键使用`ENV`。
* 设置计费的 API 密钥，`{ENDPOINT_KEY}`主机的环境键使用，从 ENV。

### <a name="reuse-recipe-store-billing-settings-with-container"></a>重复使用方案： 存储与容器的计费设置

此示例演示如何从 Dockerfile 生成文本分析情绪容器。

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

生成并运行容器[本地](#how-to-use-container-on-your-local-host)或从你[专用注册表容器](#how-to-add-container-to-private-registry)根据需要。

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>重复使用方案： 存储计费和装载与容器设置

此示例演示如何使用语言理解，dockerfile 保存计费和模型。

* 复制语言理解 (LUIS) 模型文件从主机的文件系统使用`COPY`。
* LUIS 容器支持多个模型。 如果所有模型都存储在同一文件夹中，所有需要一个`COPY`语句。
* 从模型输入目录的相对父运行 docker 文件。 以下示例中，对于运行`docker build`并`docker run`命令中的相对父`/input`。 第一个`/input`上`COPY`命令是主计算机的目录。 第二个`/input`是容器的目录。

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

生成并运行容器[本地](#how-to-use-container-on-your-local-host)或从你[专用注册表容器](#how-to-add-container-to-private-registry)根据需要。

## <a name="how-to-use-container-on-your-local-host"></a>如何在本地主机上使用容器

若要生成 Docker 文件，将为`<your-image-name>`使用新映像的名称，然后使用：

```console
docker build -t <your-image-name> .
```

若要运行映像，并将其删除，在容器停止时 (`--rm`):

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>如何将容器添加到专用注册表

请按照下列步骤使用 Dockerfile 并将新映像放在专用容器注册表。  

1. 创建`Dockerfile`使用重用方案中的文本。 一个`Dockerfile`不具有扩展名。

1. 尖括号中的任何值替换为你自己的值。

1. 在命令行或终端，使用以下命令为图像生成文件。 替换尖括号中的值`<>`、 与你自己的容器名称和标记。  

    标记选项， `-t`，是一种方法，以添加有关已更改容器内容的信息。 例如，容器名称的`modified-LUIS`指示原始容器具有分层。 标记名称的`with-billing-and-model`指示如何语言理解 (LUIS) 容器已被修改。

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. 从控制台中登录到 Azure CLI。 此命令将打开浏览器，并要求进行身份验证。 身份验证后，可以关闭浏览器并继续在控制台中工作。

    ```azure-cli
    az login
    ```

1. 从控制台中登录到你的专用注册表使用 Azure CLI。

    替换尖括号中的值`<my-registry>`，用您自己的注册表名称。  

    ```azure-cli
    az acr login --name <my-registry>
    ```

    如果分配了服务主体，也可以登录使用 docker 登录名登录。

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. 标记的专用注册表位置的容器。 替换尖括号中的值`<my-registry>`，用您自己的注册表名称。 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    如果不使用标记名称，`latest`隐式。

1. 新映像推送到专用容器注册表。 当您查看私有容器注册表时，使用以下 CLI 命令中的容器名称将存储库的名称。

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