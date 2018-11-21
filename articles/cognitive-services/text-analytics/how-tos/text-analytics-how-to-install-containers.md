---
title: 安装和运行容器
titleSuffix: Text Analytics - Cognitive Services - Azure
description: 通过本演练教程了解如何下载、安装和运行文本分析容器。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: 99bdb42d9a0d86d0d2acc4a6272e0c802042e6b5
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634885"
---
# <a name="install-and-run-containers"></a>安装和运行容器

容器化是一种软件分发方法，其中应用程序或服务被打包成容器映像。 容器映像中包含应用程序或服务的配置和依赖项。 然后可以几乎不进行修改，就将容器映像部署在容器主机上。 容器彼此隔离并与底层操作系统隔离，内存占用小于虚拟机。 容器可以从容器映像中实例化以用于短期任务，并在不再需要时将其删除。

文本分析提供以下 Docker 容器集，其中每个容器包含功能的子集：

| 容器| Description |
|----------|-------------|
|关键短语提取 | 提取关键短语，以标识主要点。 例如，针对输入文本“The food was delicious and there were wonderful staff”，该 API 会返回谈话要点：“food”和“wonderful staff”。 |
|语言检测 | 针对多达 120 种语言，检测并报告输入文本是使用哪种语言编写的。 容器将报告请求中包含的每个文档的单个语言代码。 语言代码与表示评分强度的评分相搭配。 |
|情绪分析 | 分析原本文本，获取正面或负面情绪的线索。 此 API 针对每个文档返回介于 0 和 1 之间的情绪评分，1 是最积极的评分。 分析模型已使用 Microsoft 提供的大量文本正文和自然语言技术进行预先训练。 对于[选定的语言](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages.md)，该 API 可以分析和评分提供的任何原始文本，并直接将结果返回给调用方应用程序。 |

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="preparation"></a>准备工作

使用文本分析容器之前，必须满足以下先决条件：

**Docker 引擎**：必须本地安装 Docker 引擎。 Docker 提供用于在 [macOS](https://docs.docker.com/docker-for-mac/)、[Linux](https://docs.docker.com/engine/installation/#supported-platforms) 和 [Windows](https://docs.docker.com/docker-for-windows/) 上配置 Docker 环境的包。 在 Windows 上，必须将 Docker 配置为支持 Linux 容器。 还可直接将 Docker 容器部署到 [Azure Kubernetes 服务](/azure/aks/)、[Azure 容器实例](/azure/container-instances/)，或部署到 [Azure Stack](/azure/azure-stack/) 中部署的 [Kubernetes](https://kubernetes.io/) 群集。 有关将 Kubernetes 部署到 Azure Stack 的详细信息，请参阅[将 Kubernetes 部署到 Azure Stack](/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)。

必须配置 Docker 才能让容器连接到 Azure 并向其发送账单数据。

**熟悉 Microsoft 容器注册表和 Docker**：应该大致了解 Microsoft 容器注册表和 Docker 概念（例如注册表、存储库、容器和容器映像）以及基本的 `docker` 命令。  

有关 Docker 和容器的基础知识，请参阅 [Docker 概述](https://docs.docker.com/engine/docker-overview/)。

### <a name="server-requirements-and-recommendations"></a>服务器要求和建议

下表描述了每个文本分析容器的 CPU 和内存配置，其中包括要分配的最少和建议 CPU 核心数（至少 2.6 GHz）和内存量 (GB)。

| 容器 | 最小值 | 建议 |
|-----------|---------|-------------|
|关键短语提取 | 单核，2 GB 内存 | 单核，4 GB 内存 |
|语言检测 | 单核，2 GB 内存 | 单核，4 GB 内存 |
|情绪分析 | 单核，8 GB 内存 | 单核，8 GB 内存 |

## <a name="download-container-images-from-microsoft-container-registry"></a>从 Microsoft 容器注册表下载容器映像

Microsoft 容器注册表中提供了文本分析的容器映像。 下表列出了 Microsoft 容器注册表中为文本分析容器提供的存储库。 每个存储库包含一个容器映像，必须下载此映像才能在本地运行容器。

| 容器 | 存储库 |
|-----------|------------|
|关键短语提取 | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
|语言检测 | `mcr.microsoft.com/azure-cognitive-services/language` |
|情绪分析 | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

使用 [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) 命令从存储库下载容器映像。 例如，若要从存储库下载最新的关键短语提取容器映像，请使用以下命令：

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```

有关文本分析容器可用标记的完整说明，请查看 Docker 中心内的以下容器：

* [关键短语提取](https://go.microsoft.com/fwlink/?linkid=2018757)
* [语言检测](https://go.microsoft.com/fwlink/?linkid=2018759)
* [情绪分析](https://go.microsoft.com/fwlink/?linkid=2018654)

> [!TIP]
> 可以使用 [docker images](https://docs.docker.com/engine/reference/commandline/images/) 命令列出下载的容器映像。 例如，以下命令以表格表格列出每个下载的容器映像的 ID、存储库和标记：
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>  ```
>

## <a name="instantiate-a-container-from-a-downloaded-container-image"></a>从下载的容器映像中实例化容器

使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令从下载的容器映像实例化容器。 例如，以下命令：

* 从情绪分析容器映像实例化容器
* 分配一个 CPU 核心和 8 GB 内存
* 公开 TCP 端口 5000，并为容器分配伪 TTY
* 退出后自动删除容器

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing=https://westus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789

```

> [!IMPORTANT]
> 必须将 `Eula`、`Billing` 和 `ApiKey` 命令行选项指定为实例化容器；否则，该容器不会启动。  有关详细信息，请参阅[计费](#billing)。

实例化后，可以使用容器的主机 URI 从容器调用操作。 例如，以下主机 URI 表示在上一个示例中实例化的情绪分析容器：

```http
http://localhost:5000/
```

> [!TIP]
> 可从该容器的 `/swagger` 相对 URI 访问 [OpenAPI 规范](https://swagger.io/docs/specification/about/)（以前称为 Swagger 规范，描述实例化容器支持的操作）。 例如，以下 URI 提供对上一示例中实例化的情绪分析容器 OpenAPI 规范的访问：
>
>  ```http
>  http://localhost:5000/swagger
>  ```

可以[调用容器提供的 REST API 操作](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-call-api)，或使用 [Azure 认知服务文本分析 SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics) 客户端库来调用这些操作。  
> [!IMPORTANT]
> 若要将客户端库与容器配合使用，必须安装 Azure 认知服务文本分析 SDK 2.1.0 或更高版本。

从容器调用给定的操作与从 Azure 上的相应服务调用相同操作的唯一差别在于，前者使用容器的主机 URI 而不是 Azure 区域的主机 URI 来调用该操作。 例如，若要使用美国西部 Azure 区域中运行的文本分析实例，则需要调用以下 REST API 操作：

```http
POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases
```

若要使用本地计算机上运行的关键短语提取容器的默认配置，需调用以下 REST API 操作：

```http
POST http://localhost:5000/text/analytics/v2.0/keyPhrases
```

### <a name="billing"></a>计费

文本分析容器使用 Azure 帐户中相应的文本分析资源向 Azure 发送账单信息。 文本分析容器使用以下命令行选项进行计费：

| 选项 | Description |
|--------|-------------|
| `ApiKey` | 用于跟踪账单信息的文本分析资源的 API 密钥。<br/>必须将此选项的值设置为 `Billing` 中指定的已预配文本分析 Azure 资源的 API 密钥。 |
| `Billing` | 用于跟踪账单信息的文本分析资源的终结点。<br/>必须将此选项的值设置为已预配的文本分析 Azure 资源的终结点 URI。|
| `Eula` | 表示已接受容器的许可证。<br/>此选项的值必须设置为 `accept`。 |

> [!IMPORTANT]
> 必须使用有效值指定所有三个选项，否则容器将无法启动。

有关这些选项的详细信息，请参阅[配置容器](../text-analytics-resource-container-config.md)。

## <a name="summary"></a>摘要

在本文中，我们已学习相关的概念，以及文本分析容器的下载、安装和运行工作流。 综上所述：

* 文本分析提供三个适用于 Docker 的 Linux 容器，用于封装关键短语提取、语言检测和情绪分析。
* 从 Azure 中的专用容器注册表中下载容器映像。
* 容器映像在 Docker 中运行。
* 可以使用 REST API 或 SDK 通过指定容器的主机 URI 来调用文本分析容器中的操作。
* 必须在实例化容器时指定账单信息。
* **如果未连接到 Azure 进行计量，则无法授权并运行认知服务容器。 客户需要始终让容器向计量服务传送账单信息。 认知服务容器不会将客户数据（例如，正在分析的图像或文本）发送给 Microsoft。  

## <a name="next-steps"></a>后续步骤

* 查看[配置容器](../text-analytics-resource-container-config.md)了解配置设置
* 查看[文本分析概述](../overview.md)来详细了解关键短语检测、语言检测和情绪分析  
* 有关该容器支持的方法的详细信息，请参阅[文本分析 API](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)。
* 参阅[常见问题解答 (FAQ)](../text-analytics-resource-faq.md)，以解决与计算机视觉功能相关的问题。