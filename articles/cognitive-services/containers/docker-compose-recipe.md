---
title: 使用 Docker Compose 部署多个容器
titleSuffix: Azure Cognitive Services
description: 了解如何部署多个认知服务容器。 本文介绍如何使用 Docker Compose 来协调多个 Docker 容器映像。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: dapine
ms.openlocfilehash: bfbaa03469ee04ff900a215aadd8c814efcba761
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79037517"
---
# <a name="use-docker-compose-to-deploy-multiple-containers"></a>使用 Docker Compose 部署多个容器

本文介绍如何部署多个 Azure 认知服务容器。 具体而言，其中将会介绍如何使用 Docker Compose 来协调多个 Docker 容器映像。

> [Docker Compose](https://docs.docker.com/compose/) 是用于定义和运行多容器 Docker 应用程序的工具。 在"撰写"中，您可以使用 YAML 文件来配置应用程序的服务。 然后，运行一条命令，即可从配置中创建并启动所有服务。

使用 Compose 可在一台主计算机上方便地协调多个容器映像。 在本文中，我们将组合读取和表单识别器容器。

## <a name="prerequisites"></a>先决条件

此过程要求必须在本地安装和运行多个工具：

* Azure 订阅。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* [多克引擎](https://www.docker.com/products/docker-engine)。 确认 Docker CLI 是否可在控制台窗口中工作。
* 具有适当定价层的 Azure 资源。 只有以下定价层适用于此容器：
  * 仅限使用 F0 或标准定价层的“计算机视觉”资源。****
  * 仅具有 F0 或标准定价层**的窗体识别器**资源。
  * 具有 S0 定价层的认知服务资源****。

## <a name="request-access-to-the-container-registry"></a>请求访问容器注册表

完成并提交[认知服务语音容器请求表单](https://aka.ms/speechcontainerspreview/)。 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="docker-compose-file"></a>Docker Compose 文件

YAML 文件定义要部署的所有服务。 这些服务依赖于 `DockerFile` 或现有的容器映像。 在本例中，我们将使用两个预览映像。 复制并粘贴以下 YAML 文件，并将其保存为 *docker-compose.yaml*。 在文件中提供适当的 **apikey**、**billing** 和 **EndpointUri** 值。

```yaml
version: '3.7'
services:
  forms:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    environment:
       eula: accept
       billing: # < Your form recognizer billing URL >
       apikey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionApiKey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionEndpointUri: # < Your form recognizer URI >
    volumes:
       - type: bind
         source: E:\publicpreview\output
         target: /output
       - type: bind
         source: E:\publicpreview\input
         target: /input
    ports:
      - "5010:5000"

  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-read"
    environment:
      eula: accept
      apikey: # < Your computer vision API key >
      billing: # < Your computer vision billing URL >
    ports:
      - "5021:5000"
```

> [!IMPORTANT]
> 在 **volumes** 节点下指定的主计算机上创建目录。 之所以需要这样做，是因为在尝试使用卷绑定装载映像之前，目录必须存在。

## <a name="start-the-configured-docker-compose-services"></a>启动已配置的 Docker Compose 服务

使用 Docker Compose 文件可以管理所定义服务的生命周期中的所有阶段：启动、停止和重新生成服务；查看服务状态；记录流。 从项目目录（docker-compose.yaml 文件所在的位置）打开命令行接口。

> [!NOTE]
> 为了避免出错，请确保主计算机与 Docker 引擎正确共享驱动器。 例如，如果*E：\公共预览*在*docker-compose.yaml*文件中用作目录，则与 Docker 共享驱动器**E。**

从命令行接口中，执行以下命令以启动（或重新启动 *）docker-compose.yaml*文件中定义的所有服务：

```console
docker-compose up
```

当 Docker 首次使用此配置执行 **docker-compose up** 命令时，它将提取 **services** 节点下配置的映像，然后下载并装载这些映像：

```console
Pulling forms (containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:)...
latest: Pulling from microsoft/cognitive-services-form-recognizer
743f2d6c1f65: Pull complete
72befba99561: Pull complete
2a40b9192d02: Pull complete
c7715c9d5c33: Pull complete
f0b33959f1c4: Pull complete
b8ab86c6ab26: Pull complete
41940c21ed3c: Pull complete
e3d37dd258d4: Pull complete
cdb5eb761109: Pull complete
fd93b5f95865: Pull complete
ef41dcbc5857: Pull complete
4d05c86a4178: Pull complete
34e811d37201: Pull complete
Pulling ocr (containerpreview.azurecr.io/microsoft/cognitive-services-read:)...
latest: Pulling from microsoft/cognitive-services-read
f476d66f5408: Already exists
8882c27f669e: Already exists
d9af21273955: Already exists
f5029279ec12: Already exists
1a578849dcd1: Pull complete
45064b1ab0bf: Download complete
4bb846705268: Downloading [=========================================>         ]  187.1MB/222.8MB
c56511552241: Waiting
e91d2aa0f1ad: Downloading [==============================================>    ]  162.2MB/176.1MB
```

下载映像后，映像服务将会启动：

```console
Starting docker_ocr_1   ... done
Starting docker_forms_1 ... doneAttaching to docker_ocr_1, docker_forms_1forms_1  | forms_1  | forms_1  | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
forms_1  | 
forms_1  | 
forms_1  | Using '/input' for reading models and other read-only data.
forms_1  | Using '/output/forms/812d811d1bcc' for writing logs and other output data.
forms_1  | Logging to console.
forms_1  | Submitting metering to 'https://westus2.api.cognitive.microsoft.com/'.
forms_1  | WARNING: No access control enabled!
forms_1  | warn: Microsoft.AspNetCore.Server.Kestrel[0]
forms_1  |       Overriding address(es) 'http://+:80'. Binding to endpoints defined in UseKestrel() instead.
forms_1  | Hosting environment: Production
forms_1  | Content root path: /app/forms
forms_1  | Now listening on: http://0.0.0.0:5000
forms_1  | Application started. Press Ctrl+C to shut down.
ocr_1    | 
ocr_1    | 
ocr_1    | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
ocr_1    |
ocr_1    | 
ocr_1    | Logging to console.
ocr_1    | Submitting metering to 'https://westcentralus.api.cognitive.microsoft.com/'.
ocr_1    | WARNING: No access control enabled!
ocr_1    | Hosting environment: Production
ocr_1    | Content root path: /
ocr_1    | Now listening on: http://0.0.0.0:5000
ocr_1    | Application started. Press Ctrl+C to shut down.
```

## <a name="verify-the-service-availability"></a>验证服务可用性

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

下面是一些示例输出：

```
IMAGE ID            REPOSITORY                                                                 TAG
2ce533f88e80        containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer   latest
4be104c126c5        containerpreview.azurecr.io/microsoft/cognitive-services-read              latest
```

### <a name="test-containers"></a>测试容器

在主机上打开浏览器，并使用*docker-compose.yaml*文件中的指定端口转到http://localhost:5021/swagger/index.html**本地主机**，例如 。 例如，可以使用 API 中的 **"尝试它"** 功能来测试表单识别器终结点。 两个容器的摆动页面都应该可用和可测试。

![“表单识别器”容器](media/form-recognizer-swagger-page.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [认知服务容器](../cognitive-services-container-support.md)
