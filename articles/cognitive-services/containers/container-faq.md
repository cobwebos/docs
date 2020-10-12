---
title: 认知服务容器常见问题解答 (FAQ)
titleSuffix: Azure Cognitive Services
description: 常见问题及其解答。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: aahi
ms.openlocfilehash: 3d35a1f6913d0b657956489d0e57836a05f9eb1d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90900051"
---
# <a name="azure-cognitive-services-containers-frequently-asked-questions-faq"></a>Azure 认知服务容器常见问题解答 (FAQ)

## <a name="general-questions"></a>一般问题

**问：认知服务容器提供哪些功能？**

**答:** Azure 认知服务容器可让开发人员使用 Azure 中提供的相同智能 API，但除此之外，还提供容器化的[优势](../cognitive-services-container-support.md#features-and-benefits)。 某些容器以封闭预览版的形式提供，可能需要通过应用程序进行访问。 其他容器公开以非封闭预览版或正式版的形式发布。 可在 [Azure 认知服务中的容器支持](../cognitive-services-container-support.md#container-availability-in-azure-cognitive-services)一文中找到容器及其可用性的完整列表。 你还可以查看 [Docker 中心](https://hub.docker.com/_/microsoft-azure-cognitive-services)中的容器。

**问：认知服务云与容器之间是否有任何差异？**

**答:** 认知服务容器是认知服务云的替代方案。 容器提供的功能与相应的云服务相同。 客户可在本地或 Azure 中部署容器。 容器与相应云服务的核心 AI 技术、定价层、API 密钥和 API 签名是相同的。 容器的[这些功能和优势](../cognitive-services-container-support.md#features-and-benefits)使得它们比其对应的云服务更受欢迎。

**问：如何实现访问并使用封闭预览容器？**

**答：** 以前，封闭预览容器承载于 `containerpreview.azurecr.io` 存储库中。 从9月22日2020开始，这些容器托管在 Microsoft 容器注册表上，下载它们不需要你使用 docker login 命令。 如果你的 Azure 资源是使用已批准的 Azure 订阅 ID 创建的，则可以运行封闭预览容器。 如果你的 Azure 订阅在完成 [请求窗体](https://aka.ms/csgate)后尚未获得批准，你将无法运行该容器。


**问：容器是否适用于所有认知服务，接下来期待会推出哪一套容器？**

**答:** 我们希望以容器产品/服务的形式推出更多的认知服务。 请与当地的 Microsoft 客户经理联系，以获取有关新容器版本和其他认知服务公告的更新。

**问：认知服务容器的服务级别协议 (SLA) 是什么？**

**答:** 认知服务容器不附带 SLA。

认知服务容器的资源配置由客户控制，因此 Microsoft 不会为正式版 (GA) 提供 SLA。 客户可以在本地自由部署容器，因此宿主环境由他们定义。

> [!IMPORTANT]
> 若要详细了解认知服务服务级别协议，请[访问我们的 SLA 页](https://azure.microsoft.com/support/legal/sla/cognitive-services/v1_1/)。

**问：这些容器在主权云中是否可用？**

**答：** 并非每个人都熟悉术语 "主权 cloud"，因此让我们从定义开始：

> "主权 cloud" 由 [Azure 政府](../../azure-government/documentation-government-welcome.md)版、 [Azure 德国](../../germany/germany-welcome.md)和 [azure 中国世纪互联](https://docs.microsoft.com/azure/china/overview-operations) 云组成。

遗憾的是，主权云中并 *不* 本机支持认知服务容器。 容器可以在这些云中运行，但会从公有云中提取，并需要将使用情况数据发送到公共终结点。

### <a name="versioning"></a>版本控制

**问：如何将容器更新到最新版本？**

**答:** 客户可以选择何时更新已部署的容器。 容器将标有标准的 [Docker 标记](https://docs.docker.com/engine/reference/commandline/tag/)（例如 `latest`），表示使用的是最新版本。 我们建议客户下载发布的最新版本的容器。有关在映像更新后如何接收通知的详细信息，请查看 [Azure 容器注册表 Webhook](../../container-registry/container-registry-webhook.md)。
 
**问：支持哪些版本？**

**答:** 支持容器的当前版本和最新主要版本。 但是，我们建议客户保持使用最新技术。
 
**问：如何对更新进行版本控制？**

**答:** 主要版本变更表示 API 签名发生了中断性变更。 我们预计，这种变更往往与相应认知服务云产品/服务的主要版本变更相一致。 次要版本变更表示发生了 bug 修复、模型更新或推出了新的功能，但 API 签名未发生中断性变更。

## <a name="technical-questions"></a>技术问题

**问：如何在 IoT 设备上运行认知服务容器？**

**答:** 你没有可靠的 Internet 连接，或者想要节省带宽成本。 或者，如果具有低延迟要求，或者正在处理需要在现场进行分析的敏感数据，[Azure IoT Edge 与认知服务容器](https://azure.microsoft.com/blog/running-cognitive-services-on-iot-edge/)能提供与云的一致性。

**问：这些容器是否与 OpenShift 兼容？** 

我们不会通过 OpenShift 测试容器，但通常情况下，认知服务容器应可在支持 Docker 映像的任何平台上运行。 如果你使用 OpenShift，建议以 `root-user` 身份运行容器。

**问：如何提供产品反馈和功能建议？**

**答:** 我们鼓励客户公开[表达其关注的问题](https://cognitive.uservoice.com/)，并在可能有问题重叠时投票赞同其他人提出的问题。 用户语音工具可用于产品反馈和功能建议。

**问：认知服务容器返回哪些状态消息和错误？**

**答：** 有关状态消息和错误的列表，请参阅下表。

|状态  | 说明  |
|---------|---------|
| `Valid` | API 密钥有效，无需执行任何操作。 |
| `Invalid` |   你的 API 密钥无效。 必须提供有效的 API 密钥才能运行容器。 请在 Azure 认知服务资源的 " **密钥和终结点** " 部分中找到 API 密钥和服务区域，并在 Azure 门户。 |
| `Mismatch` | 你为不同种类的认知服务资源提供了 API 密钥或终结点。 在 Azure 认知服务资源的 " **密钥和终结点** " 部分中查找 API 密钥和服务区域。 |
| `CouldNotConnect` | 容器无法连接到计费终结点。 检查 `Retry-After` 该值，并在发出附加请求之前等待此时间段结束。 |
| `OutOfQuota` | API 密钥超出了配额。 你可以升级定价层，或者等待可用的额外配额。 在 Azure 认知服务资源的 " **定价层** " 部分的 "Azure 门户中找到你的层。 |
| `BillingEndpointBusy` | 计费终结点当前正忙。 检查 `Retry-After` 该值，并在发出附加请求之前等待此时间段结束。 |
| `ContainerUseUnauthorized` | 提供的 API 密钥无权与此容器一起使用。 你可能使用的是封闭容器，因此请确保你的 Azure 订阅 ID 通过提交 [联机请求](https://aka.ms/csgate)获得批准。 |
| `Unknown` | 服务器当前无法处理帐单请求。 |


**问：如何联系支持人员？**

**答:** 客户支持渠道与认知服务云产品/服务相同。 所有认知服务容器包括日志记录功能，可帮助我们和社区为客户提供支持。 如需其他支持，请查看以下选项。

### <a name="customer-support-plan"></a>客户支持计划

客户应参考其 [Azure 支持计划](https://azure.microsoft.com/support/plans/)来确定要向谁请求支持。

### <a name="azure-knowledge-center"></a>Azure 知识中心

客户可以任意浏览 [Azure 知识中心](https://azure.microsoft.com/resources/knowledge-center/)以解答问题和帮助解决问题。

### <a name="stack-overflow"></a>Stack Overflow

> [Stack Overflow](https://en.wikipedia.org/wiki/Stack_Overflow) 是面向专业和发烧级程序员的问答站点。

如果遇到潜在问题并想要获得满意的解答，请浏览以下标记。

* [Azure 认知服务](https://stackoverflow.com/questions/tagged/azure-cognitive-services)
* [Microsoft 认知](https://stackoverflow.com/questions/tagged/microsoft-cognitive)

**问：如何计费？**

**答:** 类似于认知服务云，客户按消耗量付费。 容器需配置为向 Azure 发送计量数据，因此会相应地收取事务费。 跨托管服务和本地服务使用的资源将按分层定价计入到单个配额，并且会同时计入到这两个服务的用量。 有关更多详细信息，请参阅相应产品/服务的定价页。

* [异常检测器][ad-containers-billing]
* [计算机视觉][cv-containers-billing]
* [人脸][fa-containers-billing]
* [表单识别器][fr-containers-billing]
* [语言理解 (LUIS)][lu-containers-billing]
* [语音服务 API][sp-containers-billing]
* [文本分析][ta-containers-billing]

> [!IMPORTANT]
> 如果未连接到 Azure 进行计量，则无法授权并运行认知服务容器。 客户需要始终让容器向计量服务传送账单信息。 认知服务容器不会将客户数据发送给 Microsoft。
 
**问：容器的当前支持担保是什么？**

**答:** 预览版不提供任何担证。 在正式宣布推出容器的正式版 (GA) 后，将实施 Microsoft 面向企业软件的标准担保。
 
**问：断开 Internet 连接时，认知服务容器会发生什么情况？**

**答:** 如果未连接到 Azure 进行计量，则无法授权并运行认知服务容器。 客户需使容器始终能够与计量服务通信。

**问：在未连接到 Azure 的情况下，容器可以运行多长时间？**

**答:** 如果未连接到 Azure 进行计量，则无法授权并运行认知服务容器。 客户需使容器始终能够与计量服务通信。
 
**问：当前需要哪些硬件才能运行这些容器？**

**答:** 认知服务容器是基于 x64 的容器，可运行任何兼容的 Linux 节点、VM，以及支持 x64 Linux Docker 容器的边缘设备。 这些设备都需要 CPU 处理器。 下面提供了每个容器产品/服务的最低和推荐配置：

* [异常检测器][ad-containers-recommendations]
* [计算机视觉][cv-containers-recommendations]
* [人脸][fa-containers-recommendations]
* [表单识别器][fr-containers-recommendations]
* [语言理解 (LUIS)][lu-containers-recommendations]
* [语音服务 API][sp-containers-recommendations]
* [文本分析][ta-containers-recommendations]
 
**问：Windows 当前是否支持这些容器？**

**答:** 认知服务容器属于 Linux 容器，但 Windows 对 Linux 容器提供一定的支持。 有关 Windows 上的 Linux 容器的详细信息，请参阅 [Docker 文档](https://blog.docker.com/2017/09/preview-linux-containers-on-windows/)。
 
**问：如何发现容器？**

**答:** 认知服务容器在各个位置均可用，例如 Azure 门户、Docker Hub 和 Azure 容器注册表。 有关最新的容器位置，请参阅[容器存储库和映像](../cognitive-services-container-support.md#container-repositories-and-images)。

**问：认知服务容器与 AWS 和 Google 产品/服务有何不同？**

**答:** Microsoft 是第一家将预先训练的 AI 模型移入容器并采用简单的按事务计费的云提供商，在体验中客户如同使用云服务一样。 Microsoft 相信混合云能够为客户提供更多的选择。

**问：容器已获得哪些合规认证？**

**答:** 认知服务容器未获得任何合规认证

**问：可在哪些区域使用认知服务容器？**

**答:** 可在任何区域的任何位置运行容器，但是，容器需要一个密钥，并需要接回 Azure 以进行计量。 云服务支持的所有区域也受访问计量的容器的支持。

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
