---
title: 语音服务配额和限制
titleSuffix: Azure Cognitive Services
description: 有关 Azure 认知语音服务配额和限制的快速参考、详细说明及最佳做法
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: alexeyo
ms.openlocfilehash: 7e22b772ec35ff9b63c99acd81ad6bb5abe328a0
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91567156"
---
# <a name="speech-services-quotas-and-limits"></a>语音服务配额和限制

本文包含有关所有[定价层](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)的 Azure 认知语音服务配额和限制的详细说明。 它也包含避免请求限制的一些最佳做法。 

## <a name="quotas-and-limits-quick-reference"></a>配额和限制快速参考
跳转到[文本转语音配额和限制](#text-to-speech-quotas-and-limits-per-speech-resource)
### <a name="speech-to-text-quotas-and-limits-per-speech-resource"></a>每个语音资源的语音转文本配额和限制
在下表中，没有“可调”行的参数对于所有价格层都不能进行调整。

| Quota | 免费 (F0)<sup>1</sup> | 标准 (S0) |
|--|--|--|
| **联机听录并发请求限制（基础模型和自定义模型）** |  |  |
| 默认值 | 1 | 20 |
| 可调 | 否<sup>2</sup> | Yes<sup>2</sup> |
| **REST API 请求限制（[API 管理](../../api-management/api-management-key-concepts.md)终结点）** | 每 10 秒 100 个请求 | 每 10 秒 100 个请求 |
| **数据导入的最大数据集文件大小** | 2 GB | 2 GB |
| **批量听录的最大输入 Blob 大小** | 空值 | 2.5 GB |
| **批量听录的最大 Blob 容器大小** | 空值 | 5 GB |
| **批量听录的每个容器的最大 Blob 数** | 空值 | 10000 |
| **使用多个内容 Url 作为输入时每个脚本请求的最大文件数 () ** | 空值 | 1000  |
| **批量听录同时运行作业的最大数量** | 空值 | 2000  |

<sup>1</sup> 有关免费 (F0) 定价层，请参阅[定价页](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)上的每月限额。<br/>
<sup>2</sup> 请参阅[其他说明](#detailed-description-quota-adjustment-and-best-practices)、[最佳做法](#general-best-practices-to-mitigate-throttling-during-autoscaling)和[调整说明](#speech-to-text-increasing-online-transcription-concurrent-request-limit)。<br/> 

### <a name="text-to-speech-quotas-and-limits-per-speech-resource"></a>每个语音资源的文本转语音配额和限制
在下表中，没有“可调”行的参数对于所有价格层都不能进行调整。

| Quota | 免费 (F0)<sup>3</sup> | 标准 (S0) |
|--|--|--|
| **标准语音和神经网络语音的每秒事务数 (TPS) 的最大数目** | 200<sup>4</sup> | 200<sup>4</sup> |  |
| **自定义语音的并发请求限制** |  |  |
| 默认值 | 10 | 10 |
| 可调 | 否<sup>5</sup> | 是<sup>5</sup> |
| **HTTP 特定配额** |  |
| 每个请求已生成的最大音频长度 | 10 分钟 | 10 分钟 |
| SSML 中不同 `<voice>` 标记的最大数目 | 50 | 50 |
| **Websocket 特定配额** |  |  |
|每轮已生成的最大音频长度 | 10 分钟 | 10 分钟 |
|每轮的最大 SSML 消息大小 |64 KB |64 KB |
| **REST API 请求限制** | 每分钟 20 个请求 | 每 5 秒 25 个请求 |


<sup>3</sup> 有关免费 (F0) 定价层，请参阅[定价页](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)上的每月限额。<br/>
<sup>4</sup> 请参阅[其他说明](#detailed-description-quota-adjustment-and-best-practices)和[最佳做法](#general-best-practices-to-mitigate-throttling-during-autoscaling)。<br/>
<sup>5</sup> 查看 [其他说明](#detailed-description-quota-adjustment-and-best-practices)、 [最佳做法](#general-best-practices-to-mitigate-throttling-during-autoscaling)和 [调整说明](#text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice)。<br/> 

## <a name="detailed-description-quota-adjustment-and-best-practices"></a>详细说明、配额调整和最佳做法
请求增加配额之前（如果适用），请确保其必要性。 语音服务正在使用自动缩放技术将所需的计算资源带入“按需”模式，同时通过不保留过多的硬件容量来降低客户成本。 每当应用程序收到响应代码 429（“请求过多”）时，但工作负载又在定义的限制内（请参阅[配额和限制快速参考](#quotas-and-limits-quick-reference)），最可能的解释是，该服务正在按需进行扩展，并且尚未达到所需的扩展，因此不会立即有足够的资源来提供请求服务。 此状态通常是暂时的，不应持续太久。

### <a name="general-best-practices-to-mitigate-throttling-during-autoscaling"></a>在自动缩放期间缓解限制的常规最佳做法
为了最大程度地减少与限制（响应代码 429）相关的问题，我们建议使用以下方法：
- 在应用程序中实现重试逻辑
- 避免工作负载的急剧变化。 逐步增加工作负载 <br/>
*示例。* 应用程序正在使用文本转语音，当前工作负载是 5 TPS（每秒事务数）。 下一秒，负载增加到 20 TPS（即四倍以上）。 服务会立即开始扩展以实现新的负载，但可能无法在一秒钟内完成此操作，因此某些请求将获得响应代码 429。   
- 测试不同负载增加模式
  - 查看[语音转文本示例](#speech-to-text-example-of-a-workload-pattern-best-practice)
- 在相同或不同的区域中创建其他语音资源，并使用“轮循机制”方法在区域间分配工作负载。 这对于文本转语音 TPS（每秒事务数）参数特别重要，该参数设置为每个语音资源 200 个，并且无法调整  

下一部分介绍调整配额的特定情况。<br/>
跳转到 [文本到语音转换。增大自定义语音的脚本并发请求限制](#text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice)

### <a name="speech-to-text-increasing-online-transcription-concurrent-request-limit"></a>语音转文本：增加联机听录并发请求限制
默认情况下，每个语音资源（基础模型）或每个自定义终结点（自定义模型）的并发请求数限制为 20。 此数量在标准定价层中可能有所增加。 提交请求之前，请确保熟悉[此部分](#detailed-description-quota-adjustment-and-best-practices)中的材料，并了解这些[最佳做法](#general-best-practices-to-mitigate-throttling-during-autoscaling)。

增加并发请求限制不会直接影响成本。 语音服务使用“只需为使用的资源付费”模型。 此限制定义了服务在开始限制请求之前可缩放的程度。

基础模型和自定义模型的并发请求限制需要单独调整  。

并发请求限制参数的现有值不通过 Azure 门户、命令行工具或 API 请求显示。 若要验证现有值，请创建 Azure 支持请求。

>[!NOTE]
>[语音容器](speech-container-howto.md) 不需要增加并发请求限制，因为容器只受托管它们的硬件的 cpu 的约束。

#### <a name="have-the-required-information-ready"></a>准备好必需的信息：
- 对于基础模型：
  - 语音资源 ID
  - 区域
- 对于自定义模型： 
  - 区域
  - 自定义终结点 ID

- **如何获取信息（基础模型）** ：  
  - 转到 [Azure 门户](https://portal.azure.com/)
  - 选择要增加并发请求限制的语音资源
  - 选择“属性”（资源管理组）  
  - 复制并保存以下字段的值：
    - 资源 ID
    - 位置（终结点区域）

- **如何获取信息（自定义模型）** ：
  - 转到 [Speech Studio](https://speech.microsoft.com/) 门户
  - 视需要登录
  - 转到自定义语音识别
  - 选择项目
  - 转到“部署”
  - 选择所需终结点
  - 复制并保存以下字段的值：
    - 服务区域（终结点区域）
    - **终结点 ID**
  
#### <a name="create-and-submit-support-request"></a>创建并提交支持请求
开始增加资源的并发请求限制，或在必要时通过提交支持请求来检查今天的限制：

- 确保具有[所需的信息](#have-the-required-information-ready)
- 转到 [Azure 门户](https://portal.azure.com/)
- 选择要增加（或要检查）并发请求限制的语音资源
- 选择“新建支持请求”（支持 + 疑难解答组）  
- 将出现一个新窗口，其中包含有关 Azure 订阅和 Azure 资源的自动填充信息
- 输入“摘要”（如“增加 STT 并发请求限制”）
- 在“问题类型”中选择“配额或订阅问题”
- 在出现的“问题子类型”中，选择以下内容：
  - “配额或并发请求增加” - 用于增加请求
  - “配额或使用情况验证”，用于检查现有限制
- 单击“下一步:解决方案
- 进一步创建请求
- 在“详细信息”选项卡的“说明”字段中输入以下内容 ：
  - 注释，即与语音转文本配额有关的请求
  - 基础模型或自定义模型 
  - [先前收集的](#have-the-required-information-ready) Azure 资源信息 
  - 输入必填信息之后，单击“审阅 + 创建”选项卡中的“创建”按钮 
  - 注意 Azure 门户通知中的支持请求编号。 我们会尽快联系你以进行进一步处理

### <a name="speech-to-text-example-of-a-workload-pattern-best-practice"></a>语音转文本：工作负载模式最佳做法的示例
本示例介绍了我们建议采用的方法，以减少由于[正在进行自动缩放](#detailed-description-quota-adjustment-and-best-practices)而导致的可能的请求限制。 它不是精确的方案，而只是我们根据需要请求遵循和调整的模板。

假设语音资源的并发请求限制设置为 300。 从 20 个并发连接启动工作负载，并按每 1.5-2 分钟 20 个并发连接的方式增加负载。 如果收到太多响应代码 429，则控制服务响应并实施回退的逻辑（减少负载）。 然后以 1-2-4-4 分钟模式进行重试。 （即重试在 1 分钟内增加负载，如果仍不起作用，则在 2 分钟内重试，以此类推）

通常，强烈建议在投入生产之前测试工作负载和工作负载模式。

### <a name="text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice"></a>文本到语音转换：增加对自定义语音的脚本并发请求限制
默认情况下，自定义语音终结点的并发请求数限制为10。 此数量在标准定价层中可能有所增加。 提交请求之前，请确保熟悉[此部分](#detailed-description-quota-adjustment-and-best-practices)中的材料，并了解这些[最佳做法](#general-best-practices-to-mitigate-throttling-during-autoscaling)。

增加并发请求限制不会直接影响成本。 语音服务使用“只需为使用的资源付费”模型。 此限制定义了服务在开始限制请求之前可缩放的程度。

并发请求限制参数的现有值不通过 Azure 门户、命令行工具或 API 请求显示。 若要验证现有值，请创建 Azure 支持请求。

>[!NOTE]
>[语音容器](speech-container-howto.md) 不需要增加并发请求限制，因为容器只受托管它们的硬件的 cpu 的约束。

#### <a name="prepare-the-required-information"></a>准备所需的信息：
若要创建增加请求，需要提供部署区域和自定义终结点 ID。 若要获取它，请执行以下操作： 

- 转到 [Speech Studio](https://speech.microsoft.com/) 门户
- 视需要登录
- 中转到 *自定义语音*
- 选择项目
- 转到“部署”
- 选择所需终结点
- 复制并保存以下字段的值：
    - 服务区域（终结点区域）
    - **终结点 ID**
  
#### <a name="create-and-submit-support-request"></a>创建并提交支持请求
开始增加资源的并发请求限制，或在必要时通过提交支持请求来检查今天的限制：

- 确保具有[所需的信息](#prepare-the-required-information)
- 转到 [Azure 门户](https://portal.azure.com/)
- 选择要增加（或要检查）并发请求限制的语音资源
- 选择“新建支持请求”（支持 + 疑难解答组）  
- 将出现一个新窗口，其中包含有关 Azure 订阅和 Azure 资源的自动填充信息
- 输入 *摘要* (如 "增大 TTS 自定义终结点并发请求限制" ) 
- 在“问题类型”中选择“配额或订阅问题”
- 在出现的“问题子类型”中，选择以下内容：
  - “配额或并发请求增加” - 用于增加请求
  - “配额或使用情况验证”，用于检查现有限制
- 单击“下一步:解决方案
- 进一步创建请求
- 在“详细信息”选项卡的“说明”字段中输入以下内容 ：
  - 请注意，请求是关于 **文本到语音的** 配额
  - [先前收集的](#prepare-the-required-information) Azure 资源信息 
  - 输入必填信息之后，单击“审阅 + 创建”选项卡中的“创建”按钮 
  - 注意 Azure 门户通知中的支持请求编号。 我们会尽快联系你以进行进一步处理

