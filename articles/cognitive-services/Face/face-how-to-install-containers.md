---
title: 安装、运行容器
titlesuffix: Face - Azure Cognitive Services
description: 通过本演练教程了解如何下载、安装和运行“人脸”容器。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: 556cf755890f49e540afe64de6e485d9ebde2147
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2018
ms.locfileid: "53086430"
---
# <a name="install-and-run-containers"></a>安装和运行容器

容器化是一种软件分发方法，其中应用程序或服务被打包成容器映像。 容器映像中包含应用程序或服务的配置和依赖项。 然后可以几乎不进行修改，就将容器映像部署在容器主机上。 容器彼此隔离并与基础操作系统隔离，内存占用小于虚拟机。 容器可以从容器映像中实例化以用于短期任务，并在不再需要时将其删除。

人脸识别服务提供适用于 Docker 的标准化 Linux 容器。该容器名为“人脸”，可检测图像中的人脸和识别属性，包括人脸特征（例如，鼻子和眼睛）、性别、年龄和其他计算机预测的面部特征。 除检测外，“人脸”还可以使用置信评分检查同一/不同图像中的两张人脸，或根据数据库比较人脸，以查看是否已存在类似或相同的人脸。 还可以使用共享视觉特征将类似人脸整理为许多组。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="preparation"></a>准备工作

使用“人脸”容器之前，必须满足以下先决条件：

**Docker 引擎**：必须在本地安装 Docker 引擎。 Docker 提供用于在 [macOS](https://docs.docker.com/docker-for-mac/)、[Linux](https://docs.docker.com/engine/installation/#supported-platforms) 和 [Windows](https://docs.docker.com/docker-for-windows/) 上配置 Docker 环境的包。 在 Windows 上，必须将 Docker 配置为支持 Linux 容器。 还可直接将 Docker 容器部署到 [Azure Kubernetes 服务](/azure/aks/)、[Azure 容器实例](/azure/container-instances/)，或部署到 [Azure Stack](/azure/azure-stack/) 的 [Kubernetes](https://kubernetes.io/) 集群。 有关将 Kubernetes 部署到 Azure Stack 的详细信息，请参阅[将 Kubernetes 部署到 Azure Stack](/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)。

必须将 Docker 配置为允许容器连接 Azure 并向其发送账单数据。

**熟悉 Microsoft 容器注册表和 Docker**：应对 Microsoft 容器注册表和 Docker 概念有基本的了解，例如注册表、存储库、容器和容器映像，以及基本的 `docker` 命令的知识。  

有关 Docker 和容器的基础知识，请参阅 [Docker 概述](https://docs.docker.com/engine/docker-overview/)。

### <a name="container-requirements-and-recommendations"></a>容器要求和建议

“人脸”容器至少需要 2.6 GHz 的单核 CPU 和 4 GB分配内存，但我们建议至少使用双核 CPU 和 6 GB 分配内存。

## <a name="request-access-to-the-private-container-registry"></a>请求访问专用容器注册表

在请求访问“人脸”容器之前，必须先填写并提交[认知服务视觉容器请求表单](https://aka.ms/VisionContainersPreview)。 通过该表单请求有关你、你的公司以及要使用该容器的用户方案的信息。 提交后，Azure 认知服务团队可以检查表单，以确保满足访问专用容器注册表的条件。

> [!IMPORTANT]
> 必须使用与表单中的 Microsoft 帐户 (MSA) 或 Azure Active Directory (Azure AD) 帐户关联的电子邮件地址。

如果请求获得批准，则你会收到一封电子邮件，其中说明了如何获取凭据和访问专用容器注册表。

## <a name="create-a-face-resource-on-azure"></a>在 Azure 上创建“人脸”资源

若要使用“人脸”容器，必须在 Azure 上创建“人脸”资源。 创建资源后，使用资源中的订阅密钥和终结点 URL 来实例化容器。 有关实例化容器的详细信息，请参阅[从下载的容器映像实体化容器](#instantiate-a-container-from-a-downloaded-container-image)。

执行以下步骤，从“人脸”资源创建和检索信息：

1. 在 Azure 门户中创建“人脸”资源。  
   若要使用“人脸”容器，必须先在 Azure 门户中创建相应的“人脸”资源。 有关详细信息，请参阅[快速入门：在 Azure 门户中创建认知服务帐户](../cognitive-services-apis-create-account.md)。

1. 获取 Azure 资源的终结点 URL 和订阅密钥。  
   创建 Azure 资源后，必须使用该资源中的终结点 URL 和订阅密钥来实例化相应的“人脸”容器。 可以分别从 Azure 门户中“人脸”资源的“快速启动”和“密钥”页复制终结点 URL 和订阅密钥。

## <a name="log-in-to-the-private-container-registry"></a>登录到专用容器注册表

可通过多种方法使用认知服务容器的专用容器注册表进行身份验证，但建议的方法是在 [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/) 中使用命令行。

使用 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 命令（如以下示例所示）登录到 `containerpreview.azurecr.io`，即认知服务容器的专用容器注册表。 将 \<username\> 替换为用户名，将 \<password\> 替换为从 Azure 认知服务团队收到的凭据中提供的密码。

```docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

如果已在文本文件中保护了凭据，则可以使用 `cat` 命令将该文本文件的内容连接到 `docker login` 命令，如以下示例所示。 将 \<passwordFile\> 替换为包含密码的文本文件的路径和名称，将 \<username\> 替换为凭据中提供的用户名。

```docker
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

## <a name="download-container-images-from-the-private-container-registry"></a>从专用容器注册表下载容器映像

可从 Azure 容器注册表中名为 `containerpreview.azurecr.io` 的专用 Docker 容器注册表获取“人脸”容器的容器映像。 必须从存储库下载识别“人脸”容器的容器映像才能在本地运行容器。

使用 [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) 命令从存储库下载容器映像。 例如，若要从存储库下载最新的“人脸”容器映像，请使用以下命令：

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

有关“人脸”容器的可用标记的完整说明，请参阅 Docker 中心上的[识别文本](https://go.microsoft.com/fwlink/?linkid=2018655)。

> [!TIP]
> 可以使用 [docker images](https://docs.docker.com/engine/reference/commandline/images/) 命令列出下载的容器映像。 例如，以下命令以表格列出每个下载的容器映像的 ID、存储库和标记：
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>  ```
>

## <a name="instantiate-a-container-from-a-downloaded-container-image"></a>从下载的容器映像中实例化容器

使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令从下载的容器映像实例化容器。 例如，以下命令：

* 从“人脸”容器映像实例化容器
* 分配两个 CPU 核心和 6 GB 内存
* 公开 TCP 端口 5000，并为容器分配伪 TTY
* 退出后自动删除容器

```Docker
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789
```

> [!IMPORTANT]
> 必须将 `Eula`、`Billing` 和 `ApiKey` 选项指定为实例化容器；否则，该容器不会启动。  有关详细信息，请参阅[计费](#billing)。

实例化后，可以使用容器的主机 URI 从容器调用操作。 例如，以下主机 URI 表示在上一个示例中实例化的“人脸”容器：

```http
http://localhost:5000/
```

> [!TIP]
> 可从该容器的 `/swagger` 相对 URI 访问 [OpenAPI 规范](https://swagger.io/docs/specification/about/)（以前称为 Swagger 规范，描述实例化容器支持的操作）。 例如，以下 URI 提供对上一示例中实例化的“人脸”容器 OpenAPI 规范的访问：
>
>  ```http
>  http://localhost:5000/swagger
>  ```

可以[调用容器提供的 REST API 操作](https://docs.microsoft.com/azure/cognitive-services/face/face-api-how-to-topics/howtodetectfacesinimage)，或使用 [Azure 认知服务人脸客户端库](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/)来调用这些操作。  
> [!IMPORTANT]
> 若要将客户端库与容器一起使用，必须安装 Azure 认知服务人脸客户端库 2.0 或更高版本。

从容器调用给定的操作与从 Azure 上的相应服务调用相同操作的唯一差别在于，前者使用容器的主机 URI 而不是 Azure 区域的主机 URI 来调用该操作。 例如，若要使用美国西部 Azure 区域中运行的“人脸”实例来检测人脸，则需要调用以下 REST API 操作：

```http
POST https://westus.api.cognitive.microsoft.com/face/v1.0/detect
```

若要使用本地计算机上运行的“人脸”容器的默认配置来检测人脸，需调用以下 REST API 操作：

```http
POST http://localhost:5000/face/v1.0/detect
```

### <a name="billing"></a>计费

“人脸”容器使用 Azure 帐户中相应的“人脸”资源向 Azure 发送账单信息。 “人脸”容器使用以下命令行选项进行计费：

| 选项 | Description |
|--------|-------------|
| `ApiKey` | 用于跟踪账单信息的“人脸”资源的 API 密钥。<br/>必须将此选项的值设置为 `Billing` 中指定的已预配“人脸”Azure 资源的 API 密钥。 |
| `Billing` | 用于跟踪账单信息的“人脸”资源的终结点。<br/>必须将此选项的值设置为已预配的“人脸”Azure 资源的终结点 URI。|
| `Eula` | 表示已接受容器的许可证。<br/>此选项的值必须设置为 `accept`。 |

> [!IMPORTANT]
> 必须使用有效值指定所有三个选项，否则容器将无法启动。

有关这些选项的详细信息，请参阅[配置容器](face-resource-container-config.md)。

## <a name="summary"></a>摘要

在本文中，我们已学习相关的概念，以及“人脸”容器的下载、安装和运行工作流。 综上所述：

* 人脸识别服务提供一个名为“人脸”的适用于 Docker 的 Linux 容器，用于通过人员数据库检测或识别人脸。
* 从 Azure 中的专用容器注册表中下载容器映像。
* 容器映像在 Docker 中运行。
* 可以使用 REST API 或 SDK 通过指定容器的主机 URI 来调用“人脸”容器中的操作。
* 必须在实例化容器时指定账单信息。

> [!IMPORTANT]
> 如果未连接到 Azure 进行计量，则无法授权并运行认知服务容器。 客户需要始终让容器向计量服务传送账单信息。 认知服务容器不会将客户数据（例如，正在分析的图像或文本）发送给 Microsoft。

## <a name="next-steps"></a>后续步骤

* 查看[配置容器](face-resource-container-config.md)了解配置设置
* 查看[“人脸”概述](Overview.md)，详细了解如何检测和识别人脸  
* 有关该容器支持的方法的详细信息，请参阅[人脸 API](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)。
* 参阅[常见问题解答 (FAQ)](FAQ.md) 解决与“人脸”功能相关的问题。
