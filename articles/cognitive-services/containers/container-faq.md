---
title: 认知服务容器常见问题解答 (FAQ)
titleSuffix: Azure Cognitive Services
description: 常见问题及其解答。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: dapine
ms.openlocfilehash: 33b99d50db4384c7de818a7dd0bb8492c86bef97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "73961896"
---
# <a name="azure-cognitive-services-containers-frequently-asked-questions-faq"></a>Azure 认知服务容器常见问题解答 (FAQ)

## <a name="general-questions"></a>一般问题

**问：有哪些可用内容？**

**答：Azure** [认知服务中的容器支持](../cognitive-services-container-support.md)允许开发人员使用 Azure 中可用的智能 API，但具有容器化[的优点](../cognitive-services-container-support.md#features-and-benefits)。 目前已在 Azure 认知服务的子集中发布容器支持预览版，这些服务包括：

> [!div class="checklist"]
> * [异常检测器][ad-containers]
> * [计算机视觉][cv-containers]
> * [脸][fa-containers]
> * [表单识别器][fr-containers]
> * [语言理解（LUIS）][lu-containers]
> * [语音服务 API][sp-containers]
> * [文本分析][ta-containers]

**问：认知服务云和容器之间有什么区别吗？**

**答：** 认知服务容器是认知服务云的替代方法。 容器提供的功能与相应的云服务相同。 客户可在本地或 Azure 中部署容器。 容器与相应云服务的核心 AI 技术、定价层、API 密钥和 API 签名是相同的。 容器的[这些功能和优势](../cognitive-services-container-support.md#features-and-benefits)使得它们比其对应的云服务更受欢迎。

**问：容器能否适用于所有认知服务，以及下一组容器应该期待什么？**

**答：** 我们希望提供更多的认知服务作为容器产品提供。 联系您当地的 Microsoft 客户经理，获取有关新容器版本和其他认知服务公告的更新。

**问：认知服务容器的服务级别协议 （SLA） 是什么？**

**答：** 认知服务容器没有 SLA。

认知服务容器的资源配置由客户控制，因此 Microsoft 不会为正式版 (GA) 提供 SLA。 客户可以在本地自由部署容器，因此宿主环境由他们定义。

> [!IMPORTANT]
> 若要详细了解认知服务服务级别协议，请[访问我们的 SLA 页](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/)。

**问：这些容器在主权云中可用吗？**

**答：** 不是每个人都熟悉"主权云"一词，因此让我们从定义开始：

> "主权云"由 Azure[政府](../../azure-government/documentation-government-welcome.md)[、Azure 德国](../../germany/germany-welcome.md)和[Azure 中国 21Vianet](https://docs.microsoft.com/azure/china/overview-operations)云组成。

遗憾的是，在主权云中*不支持*认知服务容器。 容器可以在这些云中运行，但它们将从公共云中拉出，并且需要将使用情况数据发送到公共终结点。

### <a name="versioning"></a>版本管理

**问：如何将容器更新到最新版本？**

**答：** 客户可以选择何时更新已部署的容器。 容器将标有标准的 [Docker 标记](https://docs.docker.com/engine/reference/commandline/tag/)（例如 `latest`），表示使用的是最新版本。 我们建议客户下载发布的最新版本的容器。有关在映像更新后如何接收通知的详细信息，请查看 [Azure 容器注册表 Webhook](../../container-registry/container-registry-webhook.md)。
 
**问：将支持哪些版本？**

**答：** 容器的当前和最后一个主要版本将受支持。 但是，我们建议客户保持使用最新技术。
 
**问：如何对更新进行版本控制？**

**答：** 主要版本更改表示 API 签名发生了重大更改。 我们预计，这种变更往往与相应认知服务云产品/服务的主要版本变更相一致。 次要版本变更表示发生了 bug 修复、模型更新或推出了新的功能，但 API 签名未发生中断性变更。

## <a name="technical-questions"></a>技术问题

**问：如何在 IoT 设备上运行认知服务容器？**

无论您没有可靠的互联网连接，还是想要节省带宽成本。 或者，如果具有低延迟要求，或者正在处理需要现场分析的敏感数据，[则使用认知服务容器的 Azure IoT Edge](https://azure.microsoft.com/blog/running-cognitive-services-on-iot-edge/)可使您与云保持一致。

**问：如何提供产品反馈和功能建议？**

**答：** 鼓励客户公开[表达他们的忧虑](https://cognitive.uservoice.com/)，并在潜在问题重叠时对这样做的其他人进行投票。 用户语音工具可用于产品反馈和功能建议。

**问：我应联系谁寻求支持？**

**答：** 客户支持渠道与认知服务云产品相同。 所有认知服务容器包括日志记录功能，可帮助我们和社区为客户提供支持。 如需其他支持，请查看以下选项。

### <a name="customer-support-plan"></a>客户支持计划

客户应参考其 [Azure 支持计划](https://azure.microsoft.com/support/plans/)来确定要向谁请求支持。

### <a name="azure-knowledge-center"></a>Azure 知识中心

客户可以自由浏览 Azure[知识中心](https://azure.microsoft.com/resources/knowledge-center/)来回答问题和支持问题。

### <a name="stack-overflow"></a>堆栈溢出

> [堆栈溢出](https://en.wikipedia.org/wiki/Stack_Overflow)是专业和爱好者程序员的问答网站。

如果遇到潜在问题并想要获得满意的解答，请浏览以下标记。

* [Azure 认知服务](https://stackoverflow.com/questions/tagged/azure-cognitive-services)
* [Microsoft 认知](https://stackoverflow.com/questions/tagged/microsoft-cognitive)

**问：计费如何工作？**

**答：** 客户根据消费付费，类似于认知服务云。 容器需配置为向 Azure 发送计量数据，因此会相应地收取事务费。 跨托管服务和本地服务使用的资源将按分层定价计入到单个配额，并且会同时计入到这两个服务的用量。 有关更多详细信息，请参阅相应产品/服务的定价页。

* [异常检测器][ad-containers-billing]
* [计算机视觉][cv-containers-billing]
* [脸][fa-containers-billing]
* [表单识别器][fr-containers-billing]
* [语言理解（LUIS）][lu-containers-billing]
* [语音服务 API][sp-containers-billing]
* [文本分析][ta-containers-billing]

> [!IMPORTANT]
> 如果未连接到 Azure 进行计量，则无法授权并运行认知服务容器。 客户需要始终让容器向计量服务传送账单信息。 认知服务容器不会将客户数据发送给 Microsoft。
 
**问：当前集装箱支持保修是什么？**

**答：** 预览没有保修。 在正式宣布推出容器的正式版 (GA) 后，将实施 Microsoft 面向企业软件的标准担保。
 
**问：当互联网连接丢失时，认知服务容器会发生什么情况？**

**答：** 认知服务容器未连接到 Azure 进行计量时 *，未获得运行许可*。 客户需使容器始终能够与计量服务通信。

**问：容器在不连接到 Azure 的情况下可以运行多长时间？**

**答：** 认知服务容器未连接到 Azure 进行计量时 *，未获得运行许可*。 客户需使容器始终能够与计量服务通信。
 
**问：运行这些容器需要哪些当前硬件？**

**答：** 认知服务容器是基于 x64 的容器，可以运行任何支持 x64 Linux Docker 容器的兼容 Linux 节点、VM 和边缘设备。 这些设备都需要 CPU 处理器。 下面提供了每个容器产品/服务的最低和推荐配置：

* [异常检测器][ad-containers-recommendations]
* [计算机视觉][cv-containers-recommendations]
* [脸][fa-containers-recommendations]
* [表单识别器][fr-containers-recommendations]
* [语言理解（LUIS）][lu-containers-recommendations]
* [语音服务 API][sp-containers-recommendations]
* [文本分析][ta-containers-recommendations]
 
**问：这些容器当前在 Windows 上是否受支持？**

**答：** 认知服务容器是 Linux 容器，但是 Windows 上有一些对 Linux 容器的支持。 有关 Windows 上的 Linux 容器的详细信息，请参阅 [Docker 文档](https://blog.docker.com/2017/09/preview-linux-containers-on-windows/)。
 
**问：如何发现容器？**

**答：** 认知服务容器可在不同位置使用，例如 Azure 门户、Docker 集线器和 Azure 容器注册表。 有关最新的容器位置，请参阅[容器存储库和映像](../cognitive-services-container-support.md#container-repositories-and-images)。

**问：认知服务容器与 AWS 和 Google 产品相比如何？**

**答：** Microsoft 是第一个将预先训练的 AI 模型移动到容器中的云提供商，每次交易只需简单计费，就像客户使用云服务一样。 Microsoft 相信混合云能够为客户提供更多的选择。

**问：容器有哪些合规性认证？**

**答：** 认知服务容器没有任何合规性认证

**问：认知服务容器在哪些区域可用？**

**答：** 容器可以在任何区域的任意位置运行，但它们需要密钥，并回调到 Azure 进行计量。 云服务支持的所有区域也受访问计量的容器的支持。

[!INCLUDE [Containers next steps](includes/containers-next-steps.md)]

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-containers]: ../speech-service/speech-container-howto.md
[ta-containers]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md

[ad-containers-billing]: ../anomaly-Detector/anomaly-detector-container-howto.md#billing
[cv-containers-billing]: ../computer-vision/computer-vision-how-to-install-containers.md#billing
[fa-containers-billing]: ../face/face-how-to-install-containers.md#billing
[fr-containers-billing]: ../form-recognizer/form-recognizer-container-howto.md#billing
[lu-containers-billing]: ../luis/luis-container-howto.md#billing
[sp-containers-billing]: ../speech-service/speech-container-howto.md#billing
[ta-containers-billing]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#billing

[ad-containers-recommendations]: ../anomaly-Detector/anomaly-detector-container-howto.md#container-requirements-and-recommendations
[cv-containers-recommendations]: ../computer-vision/computer-vision-how-to-install-containers.md#container-requirements-and-recommendations
[fa-containers-recommendations]: ../face/face-how-to-install-containers.md#container-requirements-and-recommendations
[fr-containers-recommendations]: ../form-recognizer/form-recognizer-container-howto.md#container-requirements-and-recommendations
[lu-containers-recommendations]: ../luis/luis-container-howto.md#container-requirements-and-recommendations
[sp-containers-recommendations]: ../speech-service/speech-container-howto.md#container-requirements-and-recommendations
[ta-containers-recommendations]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md#container-requirements-and-recommendations
