---
title: Docker compose 容器方案
titleSuffix: Azure Cognitive Services
description: 了解如何部署认知服务的多个容器。 此过程演示如何安排使用 Docker Compose 的多个 Docker 容器映像。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 8afb7e866bc2a5fefe28a71653c4a2a87fdc7a5b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445793"
---
# <a name="use-multiple-containers-in-a-private-network-with-docker-compose"></a>使用 Docker Compose 的专用网络中使用多个容器

了解如何部署认知服务的多个容器。 此过程演示如何安排使用 Docker Compose 的多个 Docker 容器映像。

> [Docker Compose](https://docs.docker.com/compose/)是用于定义和运行多容器 Docker 应用程序的工具。 借助 Compose，使用 YAML 文件来配置应用程序的服务。 然后，使用单个命令，你创建并启动所有服务从你的配置。

如果合适，因为它可以协调单个主机计算机上的多个容器映像可以是极具吸引力。 在本文中，我们将拉取识别文本服务和窗体识别器服务在一起。

## <a name="prerequisites"></a>必备组件

此过程要求必须在本地安装和运行多个工具。

* 使用 Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* [Docker 引擎](https://www.docker.com/products/docker-engine)并验证 Docker CLI 是否可在控制台窗口中工作。
* 具有适当定价层的 Azure 资源。 并非所有定价层都适用于此容器：
  * **计算机视觉**仅资源使用 F0 或标准定价层。
  * **形成识别器**仅资源使用 F0 或标准定价层。
  * 具有 S0 定价层的认知服务资源  。

## <a name="request-access-to-the-container-registry"></a>请求访问容器注册表

填写并提交[认知服务语音容器请求窗体](https://aka.ms/speechcontainerspreview/)容器请求访问。 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="docker-compose-file"></a>Docker compose 文件

YAML 文件定义要部署的所有服务。 这些服务依赖`DockerFile`或现有容器映像，在这种情况下我们将使用两种预览图像。 复制并粘贴以下 YAML 文件，然后将其保存为*docker compose.yaml*。 提供相应_apikey_，_计费_，并_终结点 URI_中的值_的 docker-compose.yml_以下文件。

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
         source: e:\publicpreview\output
         target: /output
       - type: bind
         source: e:\publicpreview\input
         target: /input
    ports:
      - "5010:5000"

  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    environment:
      eula: accept
      apikey: # < Your recognize text API key >
      billing: # < Your recognize text billing URL >
    ports:
      - "5021:5000"
```

> [!IMPORTANT]
> 在下指定的主机计算机上创建目录`volumes`节点。 这是必需的因为目录必须存在然后再尝试使用卷绑定装载映像。

## <a name="start-the-configured-docker-compose-services"></a>开始配置 docker compose 服务

Docker compose 文件启用了管理的所有定义的服务的生命周期;从启动/停止和重新生成服务，查看服务状态，以及日志流式处理。 从项目目录打开一个命令行接口 (其中*docker compose.yaml*文件位于)。

> [!NOTE]
> 若要避免错误，请确保主机已正确共享具有驱动器**Docker 引擎**。 例如，如果*e:\publicpreview*用作目录*docker compose.yaml*共享*E 驱动器*使用 docker。

从命令行界面中执行以下命令以启动 （或重新启动） 定义的所有服务*docker compose.yaml*:

```console
docker-compose up
```

第一次执行`docker-compose up`使用此配置，命令**Docker**将拉取映像下配置`services`节点-下载/安装它们：

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
Pulling ocr (containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:)...
latest: Pulling from microsoft/cognitive-services-recognize-text
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

下载图像，则图像服务已启动。

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

以下是示例输出：

```
IMAGE ID            REPOSITORY                                                                 TAG
2ce533f88e80        containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer   latest
4be104c126c5        containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text    latest
```

### <a name="test-the-recognize-text-container"></a>测试 recognize 的文本容器

打开主机计算机上的浏览器并导航到`localhost`使用从指定的端口*docker compose.yaml*，例如， `http://localhost:5021/swagger/index.html`。 可以使用其功能在尝试要测试 recognize 文本终结点的 api。

![识别文本 Swagger](media/recognize-text-swagger-page.png)

### <a name="test-the-form-recognizer-container"></a>测试窗体识别器容器

打开主机计算机上的浏览器并导航到`localhost`使用从指定的端口*docker compose.yaml*，例如， `http://localhost:5010/swagger/index.html`。 可以使用其功能在尝试要测试窗体识别器终结点的 api。

![窗体识别器 Swagger](media/form-recognizer-swagger-page.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [认知服务容器](../cognitive-services-container-support.md)