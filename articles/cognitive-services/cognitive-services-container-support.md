---
title: Azure 认知服务容器
titleSuffix: Azure Cognitive Services
description: 了解 Docker 容器如何使认知服务深入了解你的数据。
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: bda6fae31e3f5ef63d2c917937d80b2c1ea4fc48
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906979"
---
# <a name="azure-cognitive-services-containers"></a>Azure 认知服务容器

> [!WARNING]
> 2020 年 6 月 11 日，Microsoft 宣布在以人权为基础的强有力的法规颁布之前，将不向美国的警察局出售人脸识别技术。 同样地，如果客户是美国警察局、由美国警察局准许使用或针对美国警察局使用 Azure 服务，则这些客户不得使用这类服务中包含的人脸识别特性或功能（例如人脸或视频索引器）。

Azure 认知服务中的容器支持让开发人员能够使用与 Azure 中可用的 API 一样丰富的 API，并能够灵活地选择部署和托管随附 [Docker 容器](https://www.docker.com/what-container)的服务的位置。 容器支持目前适用于部分 Azure 认知服务，包括以下部分：

> [!div class="checklist"]
> * [异常检测器][ad-containers]
> * [计算机视觉][cv-containers]
> * [人脸][fa-containers]
> * [表单识别器][fr-containers]
> * [语言理解 (LUIS)][lu-containers]
> * [语音服务 API][sp-containers]
> * [文本分析][ta-containers]

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

容器化是一种软件分发方法，其中应用程序或服务（包括其依赖关系和配置）被一起打包为容器映像。 如果几乎不进行修改，可将容器映像部署在容器主机上。 容器彼此隔离并与基础操作系统隔离，内存占用小于虚拟机。 容器可以从容器映像中实例化以用于短期任务，并在不再需要时将其删除。

认知服务资源可在 [Microsoft Azure](https://azure.microsoft.com) 上获得。 登录到 [Azure 门户](https://portal.azure.com/)，创建和浏览适用于这些服务的 Azure 资源。

## <a name="features-and-benefits"></a>功能和优势

- **不可变的基础结构**：使 DevOps 团队能够利用一组一致且可靠的已知系统参数，同时能够适应变化。 通过容器，可灵活地在可预测生态系统内进行透视，并避免配置偏移。
- **对数据的控制**：允许客户选择认知服务在何处处理其数据。 对于不能将数据发送到云，但需要访问认知服务技术的客户，此功能非常重要。 支持混合环境中的一致性 - 跨数据、管理、标识和安全性。
- **对模型更新的控制**：为客户提供其解决方案中部署的模型的版本控制和更新方面的灵活性。
- **可移植的体系结构**：支持创建可在 Azure、本地和边缘部署的可移植应用程序体系结构。 可直接将容器部署到 [Azure Kubernetes 服务](../aks/index.yml)、[Azure 容器实例](../container-instances/index.yml)，或部署到 [Azure Stack](/azure-stack/operator) 的 [Kubernetes](https://kubernetes.io/) 集群。 有关详细信息，请参阅[将 Kubernetes 部署到 Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)。
- **高吞吐量/低延迟**：通过使以物理方式运行的认知服务更深入了解其应用程序逻辑和数据，为客户提供缩放功能，以满足高吞吐量和低延迟扩展要求。 容器不限制每秒综合事务数 (TPS)，如果提供了必要的硬件资源，它还可进行纵向或横向扩展，来应对需求。
- **可伸缩性**：随着容器化和容器业务流程软件（如 Kubernetes）的日益普及，可伸缩性已成为技术进步的前沿领域。 基于可缩放的群集的应用程序开发可满足高可用性的需要。

## <a name="containers-in-azure-cognitive-services"></a>Azure 认知服务中的容器

Azure 认知服务容器提供以下一组 Docker 容器，其中每个容器都包含 Azure 认知服务中的服务的功能子集：

| 服务 | 支持的定价层 | 容器 | 描述 |
|--|--|--|--|
| [异常探测器][ad-containers] | F0、S0 | **异常情况-探测器** ([映像](https://hub.docker.com/_/azure-cognitive-services-decision-anomaly-detector))   | 可以使用异常检测器 API 来监视并检测与机器学习配合使用的时序数据中的异常。<br>[请求访问权限][request-access] |
| [计算机视觉][cv-containers] | F0、S1 | **读取** | 从具有不同表面和背景的各种对象的图像中提取打印文本，例如收据、海报和名片。 读取容器还可检测图像中的手写文本，并提供 PDF/TIFF/多页支持。<br/><br/>**重要提示：** 读取容器目前仅适用于英语。 |
| [人脸][fa-containers] | F0、S0 | **人脸** | 检测图像中的人脸并标识属性，包括人脸特征（例如，鼻子和眼睛）、性别、年龄和其他计算机预测的面部特征。 除检测外，“人脸”还可以使用置信评分检查同一/不同图像中的两张人脸，或根据数据库比较人脸，以查看是否已存在类似或相同的人脸。 还可以使用共享视觉特征将类似人脸整理为许多组。<br>[请求访问权限][request-access] |
| [窗体识别器][fr-containers] | F0、S0 | **表单识别器** | 表单理解应用机器学习技术从表单中识别和提取键值对和表。<br>[请求访问权限][request-access] |
| [LUIS][lu-containers] | F0、S0 | **LUIS**（[映像](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409)） | 可将已训练或已发布的语言理解模型（也称为 LUIS 应用）加载到 docker 容器中并提供对容器的 API 终结点中的查询预测的访问权限。 可以从容器中收集查询日志并将这些日志上传回 [LUIS 门户](https://www.luis.ai)以提高应用的预测准确性。 |
| [语音服务 API][sp-containers-stt] | F0、S0 | **语音到文本** ([图像](https://hub.docker.com/_/azure-cognitive-services-speechservices-speech-to-text))  | 将连续的实时语音转换为文本。 |
| [语音服务 API][sp-containers-cstt] | F0、S0 | **自定义语音到文本** ([图像](https://hub.docker.com/_/azure-cognitive-services-speechservices-custom-speech-to-text))  | 使用自定义模型将连续的实时语音转录为文本。 |
| [语音服务 API][sp-containers-tts] | F0、S0 | **文本到语音** ([图像](https://hub.docker.com/_/azure-cognitive-services-speechservices-text-to-speech))  | 将文本转换为自然发音的语音。 |
| [语音服务 API][sp-containers-ctts] | F0、S0 | **自定义文本到语音** ([图像](https://hub.docker.com/_/azure-cognitive-services-speechservices-custom-text-to-speech))  | 使用自定义模型将文本转换为自然声音。 |
| [语音服务 API][sp-containers-ntts] | F0、S0 | **神经文本到语音** ([图像](https://hub.docker.com/_/azure-cognitive-services-speechservices-neural-text-to-speech))  | 使用 deep 神经网络技术将文本转换为自然声音，允许使用更自然的合成语音。 |
| [文本分析][ta-containers-keyphrase] | F0、S | 关键短语提取（[映像](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)）**** | 提取关键短语，以标识要点。 例如，针对输入文本“The food was delicious and there were wonderful staff”，该 API 会返回谈话要点：“food”和“wonderful staff”。 |
| [文本分析][ta-containers-language] | F0、S | 语言检测（[映像](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)）**** | 针对多达 120 种语言，检测输入文本是使用哪种语言编写的，并报告请求中提交的每个文档的单个语言代码。 语言代码与表示评分强度的评分相搭配。 |
| [文本分析][ta-containers-sentiment] | F0、S | 情绪分析 v3（[映像](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)） | 分析原始文本，获取正面或负面情绪的线索。 此版本的情绪分析为每个文档以及其中的句子返回情绪标签（例如正面或负面） 。 |
| [文本分析][ta-containers-health] | F0、S | **运行状况文本分析** | 从非结构化临床文本中提取医疗信息并进行标记。 |
| [空间分析][spa-containers] | S0 | **空间分析** | 从非结构化临床文本中提取医疗信息并进行标记。 |

<!--
|[Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

此外，认知服务[**一体化产品/服务**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)资源密钥支持某些容器。 可以为以下服务创建单个认知服务一体化资源，并在支持的服务之间使用相同的计费密钥：

* 计算机视觉
* 人脸
* LUIS
* 文本分析

## <a name="container-availability-in-azure-cognitive-services"></a>Azure 认知服务中的容器可用性

Azure 认知服务容器通过 Azure 订阅公开发布，并可以从 Microsoft 容器注册表或 Docker 中心拉取 Docker 容器映像。 可以使用 [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) 命令从相应注册表下载容器映像。

[!INCLUDE [Container repositories and images](containers/includes/cognitive-services-container-images.md)]

## <a name="prerequisites"></a>先决条件

使用 Azure 认知服务容器之前，必须先满足以下先决条件：

**Docker 引擎**：必须在本地安装 Docker 引擎。 Docker 提供用于在 [macOS](https://docs.docker.com/docker-for-mac/)、[Linux](https://docs.docker.com/engine/installation/#supported-platforms) 和 [Windows](https://docs.docker.com/docker-for-windows/) 上配置 Docker 环境的包。 在 Windows 上，必须将 Docker 配置为支持 Linux 容器。 还可直接将 Docker 容器直接部署到 [Azure Kubernetes 服务](../aks/index.yml)或 [Azure 容器实例](../container-instances/index.yml)。

必须将 Docker 配置为允许容器连接 Azure 并向其发送账单数据。

**熟悉 Microsoft 容器注册表和 Docker**：应对 Microsoft 容器注册表和 Docker 概念有基本的了解，例如注册表、存储库、容器和容器映像，以及基本的 `docker` 命令的知识。

有关 Docker 和容器的基础知识，请参阅 [Docker 概述](https://docs.docker.com/engine/docker-overview/)。

各容器还可以有其自己的要求，包括服务器和内存分配要求。

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

[!INCLUDE [Discoverability of more container information](../../includes/cognitive-services-containers-discoverability.md)]

## <a name="next-steps"></a>后续步骤

了解可以用于认知服务的[容器配方](containers/container-reuse-recipe.md)。

安装和浏览 Azure 认知服务中的容器提供的功能：

* [异常探测器容器][ad-containers]
* [计算机视觉容器][cv-containers]
* [人脸容器][fa-containers]
* [表单识别器容器][fr-containers]
* [语言理解 (LUIS) 容器][lu-containers]
* [语音服务 API 容器][sp-containers]
* [文本分析容器][ta-containers]

<!--* [Personalizer containers](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409)
-->

[ad-containers]: anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: face/face-how-to-install-containers.md
[fr-containers]: form-recognizer/form-recognizer-container-howto.md
[lu-containers]: luis/luis-container-howto.md
[sp-containers]: speech-service/speech-container-howto.md
[spa-containers]: https://docs.microsoft.com/azure/cognitive-services/computer-vision/spatial-analysis-container
[sp-containers-stt]: speech-service/speech-container-howto.md?tabs=stt
[sp-containers-cstt]: speech-service/speech-container-howto.md?tabs=cstt
[sp-containers-tts]: speech-service/speech-container-howto.md?tabs=tts
[sp-containers-ctts]: speech-service/speech-container-howto.md?tabs=ctts
[sp-containers-ntts]: speech-service/speech-container-howto.md?tabs=ntts
[ta-containers]: text-analytics/how-tos/text-analytics-how-to-install-containers.md
[ta-containers-keyphrase]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-containers-language]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-containers-sentiment]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
[ta-containers-health]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health
[request-access]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u
