---
title: 语音服务配额和限制
titleSuffix: Azure Cognitive Services
description: 有关 Azure 认知语音服务配额和限制的快速参考、详细说明和最佳实践
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: alexeyo
ms.openlocfilehash: 554dd0967979bc2457c3a9c8371152e09535381f
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690130"
---
# <a name="speech-services-quotas-and-limits"></a>语音服务配额和限制

本文包含有关 Azure 认知语音服务配额和所有[定价层](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)限制的**详细说明**。 它还包含一些最佳实践，以避免请求限制。 

## <a name="quotas-and-limits-quick-reference"></a>配额和限制快速参考
跳转到 [文本到语音的配额和限制](#text-to-speech-quotas-and-limits-per-speech-resource)
### <a name="speech-to-text-quotas-and-limits-per-speech-resource"></a>语音到文本配额和每个语音资源的限制
在下表中，没有 "可调" 行的参数对于所有价格层都是 **不可** 调整的。

| Quota | 免费 (F0) <sup>1</sup> | 标准 (S0) |
|--|--|--|
| **联机脚本 (基本模型和自定义模型的并发请求限制) ** |  |  |
| 默认值 | 1 | 20 |
| 可调 | 否<sup>2</sup> | Yes<sup>2</sup> |
| **REST API 请求限制 ([API 管理](../../api-management/api-management-key-concepts.md) 终结点) ** | 每10秒100个请求 | 每10秒100个请求 |
| **数据导入的最大数据集文件大小** | 2 GB | 2 GB |
| **批脚本的最大输入 blob 大小** | 不适用 | 2.5 GB |
| **批脚本的最大 blob 容器大小** | 不适用 | 5 GB |
| **批脚本的每个容器的最大 blob 数** | 不适用 | 10000 |
| **批脚本同时运行的作业的最大数量** | 不适用 | 2000  |

<sup>1</sup> 对于 **免费 (F0) ** 定价层，请参阅 [定价页](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)上的每月折中。<br/>
<sup>2</sup> 查看 [其他说明](#detailed-description-quota-adjustment-and-best-practices)、 [最佳做法](#general-best-practices-to-mitigate-throttling-during-autoscaling)和 [调整说明](#speech-to-text-increasing-online-transcription-concurrent-request-limit)。<br/> 

### <a name="text-to-speech-quotas-and-limits-per-speech-resource"></a>每个语音资源的文本到语音配额和限制
在下表中，没有 "可调" 行的参数对于所有价格层都是 **不可** 调整的。

| Quota | 免费 (F0) <sup>3</sup> | 标准 (S0) |
|--|--|--|
| **标准和神经语音的每秒最大事务数 (TPS) ** | 200<sup>4</sup> | 200<sup>4</sup> |  |
| **自定义语音的并发请求限制** |  |  |
| 默认值 | 10 | 10 |
| 可调 | 否<sup>5</sup> | 是<sup>5</sup> |
| **HTTP 特定的配额** |  |
| 每个请求产生的最大音频长度 | 10 分钟 | 10 分钟 |
| SSML 中不同标记的最大数目 `<voice>` | 50 | 50 |
| **Websocket 特定配额** |  |  |
|每轮产生的最大音频长度 | 10 分钟 | 10 分钟 |
|每个转换的最大 SSML 消息大小 |64 KB |64 KB |
| **REST API 请求限制** | 每分钟20个请求 | 每5秒25个请求 |


<sup>3</sup> 对于 **免费 (F0) ** 定价层，请参阅 [定价页](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)上的每月补助。<br/>
<sup>4</sup> 查看 [其他说明](#detailed-description-quota-adjustment-and-best-practices) 和 [最佳实践](#general-best-practices-to-mitigate-throttling-during-autoscaling)。<br/>
<sup>5</sup> 查看 [其他说明](#detailed-description-quota-adjustment-and-best-practices)、 [最佳做法](#general-best-practices-to-mitigate-throttling-during-autoscaling)和 [调整说明](#text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice)。<br/> 

## <a name="detailed-description-quota-adjustment-and-best-practices"></a>详细说明、配额调整和最佳实践
请求配额增加之前 (（如果适用）) 确保必须这样做。 语音服务使用自动缩放技术，使所需的计算资源进入 "按需" 模式，同时通过不保留过多的硬件容量使客户的成本降低。 每次你的应用程序收到响应代码 429 ( "请求过多" ) ，而你的工作负载在定义的限制范围内 (查看 [配额并限制快速参考](#quotas-and-limits-quick-reference)) 最有可能的解释是，该服务正在按你的需求进行扩展，并且尚未达到所需的扩展，因此不会立即提供足够的资源来处理请求。 此状态通常是暂时性的，不应持续很长时间。

### <a name="general-best-practices-to-mitigate-throttling-during-autoscaling"></a>在自动缩放期间缓解限制的一般最佳实践
为了最大限度地减少与限制 (响应代码 429) 相关的问题，我们建议使用以下方法：
- 在应用程序中实现重试逻辑
- 避免对工作负荷进行急剧更改。 逐步增加工作负荷 <br/>
*示例。* 应用程序使用的是文本到语音转换，当前的工作负荷为每秒5个 (事务) 。 接下来，你将负载增加到20个 TPS (，这四次) 。 服务会立即开始扩展以实现新负载，但可能无法在一秒钟内完成此操作，因此某些请求将获得响应代码429。   
- 测试不同负载增加模式
  - 请参阅 [语音到文本示例](#speech-to-text-example-of-a-workload-pattern-best-practice)
- 在相同或不同的区域中创建其他语音资源，并使用 "轮循机制" 方法在它们之间分配工作负荷。 这对于 **文本到语音 TPS (每秒的事务数) ** 参数尤为重要，每个语音资源设置为200，并且无法调整  

下一部分介绍调整配额的特定情况。<br/>
跳转到 [文本到语音转换。增大自定义语音的脚本并发请求限制](#text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice)

### <a name="speech-to-text-increasing-online-transcription-concurrent-request-limit"></a>语音到文本：增加联机脚本并发请求限制
默认情况下，每个语音资源的并发请求数限制为20个 (基础模型) 或 (自定义模型) 的自定义终结点。 对于标准定价层，可以增加此数量。 提交请求之前，请确保熟悉 [此部分](#detailed-description-quota-adjustment-and-best-practices) 中的材料并了解这些 [最佳做法](#general-best-practices-to-mitigate-throttling-during-autoscaling)。

增加并发请求限制 **不** 会直接影响你的成本。 语音服务使用 "仅为你使用的内容付费" 模型。 限制定义了服务在开始限制请求之前可以缩放的程度。

**基本**模型和**自定义**模型的并发请求限制需要**单独**进行调整。

并发请求限制参数的现有值 **无法** 通过 Azure 门户、命令行工具或 API 请求显示。 若要验证现有值，请创建 Azure 支持请求。

>[!NOTE]
>[语音容器](speech-container-howto.md) 不需要增加并发请求限制，因为容器只受托管它们的硬件的 cpu 的约束。

#### <a name="have-the-required-information-ready"></a>准备好所需的信息：
- 对于 **基本模型**：
  - 语音资源 ID
  - 区域
- 对于 **自定义模型**： 
  - 区域
  - 自定义终结点 ID

- **如何获取 (基本模型) 的信息 **：  
  - 中转到 [Azure 门户](https://portal.azure.com/)
  - 选择要增加并发请求限制的语音资源
  - 选择 (*资源管理*组) 的*属性* 
  - 复制并保存以下字段的值：
    - 资源 ID
    - 终结点区域 (**位置**) 

- **如何 (自定义模型) 中获取信息 **：
  - 转到 [Speech Studio](https://speech.microsoft.com/) 门户
  - 如有必要，请登录
  - 中转到自定义语音
  - 选择项目
  - 中转到 *部署*
  - 选择所需的终结点
  - 复制并保存以下字段的值：
    - 终结点区域 (的**服务区域**) 
    - **终结点 ID**
  
#### <a name="create-and-submit-support-request"></a>创建并提交支持请求
启动资源的并发请求限制增加，如果需要，请通过提交支持请求来检查今天的限制：

- 确保你具有 [所需的信息](#have-the-required-information-ready)
- 中转到 [Azure 门户](https://portal.azure.com/)
- 选择要增加 (的语音资源，或检查并发请求限制) 
- 选择 " *新支持请求* (*支持 + 故障排除* " 组)  
- 将显示一个新窗口，其中包含有关 Azure 订阅和 Azure 资源的自动填充信息
- 输入 *摘要* (如 "增加 STT 并发请求限制" ) 
- 在 " *问题类型* " 中，选择 "配额或订阅问题"
- 出现 *问题子类型* 选择：
  - "增加配额或并发请求数"-表示增加请求
  - 用于检查现有限制的 "配额或使用情况验证"
- 单击 "*下一步：解决方案*"
- 继续执行请求创建
- 当在 " *详细信息* " 选项卡的 " *说明* " 字段中输入时：
  - 请注意，请求是关于 **语音到文本** 的配额
  - **基本** 或 **自定义** 模型
  - [之前收集](#have-the-required-information-ready)的 Azure 资源信息 
  - 完成输入所需信息，然后单击 "*查看 + 创建*" 选项卡中的 "*创建*" 按钮
  - 请注意 Azure 门户通知中的支持请求编号。 稍后会联系你进行进一步处理

### <a name="speech-to-text-example-of-a-workload-pattern-best-practice"></a>语音到文本：工作负荷模式最佳做法的示例
此示例演示了下面建议的方法，以减少正在进行的自动 [缩放](#detailed-description-quota-adjustment-and-best-practices)导致的可能的请求阻止。 它不是 "确切食谱"，只是我们邀请的模板，并根据需要进行调整。

假设语音资源的并发请求限制设置为300。 从20个并发连接启动工作负荷，并每 1.5-2 分钟增加20个并发连接的负载。 控制服务响应并实现回退 (降低负载的逻辑) 如果收到太多响应代码429。 然后重试1-2-4-4 分钟模式。 如果仍不起作用， (会重试负载增加1分钟，然后在2分钟内) 

通常，强烈建议在转到生产环境之前测试工作负荷和工作负荷模式。

### <a name="text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice"></a>文本到语音转换：增加对自定义语音的脚本并发请求限制
默认情况下，自定义语音终结点的并发请求数限制为10。 对于标准定价层，可以增加此数量。 提交请求之前，请确保熟悉 [此部分](#detailed-description-quota-adjustment-and-best-practices) 中的材料并了解这些 [最佳做法](#general-best-practices-to-mitigate-throttling-during-autoscaling)。

增加并发请求限制 **不** 会直接影响你的成本。 语音服务使用 "仅为你使用的内容付费" 模型。 限制定义了服务在开始限制请求之前可以缩放的程度。

并发请求限制参数的现有值 **无法** 通过 Azure 门户、命令行工具或 API 请求显示。 若要验证现有值，请创建 Azure 支持请求。

>[!NOTE]
>[语音容器](speech-container-howto.md) 不需要增加并发请求限制，因为容器只受托管它们的硬件的 cpu 的约束。

#### <a name="prepare-the-required-information"></a>准备所需的信息：
若要创建增加请求，需要提供部署区域和自定义终结点 ID。 若要获取它，请执行以下操作： 

- 转到 [Speech Studio](https://speech.microsoft.com/) 门户
- 如有必要，请登录
- 中转到 *自定义语音*
- 选择项目
- 中转到 *部署*
- 选择所需的终结点
- 复制并保存以下字段的值：
    - 终结点区域 (的**服务区域**) 
    - **终结点 ID**
  
#### <a name="create-and-submit-support-request"></a>创建并提交支持请求
启动资源的并发请求限制增加，如果需要，请通过提交支持请求来检查今天的限制：

- 确保你具有 [所需的信息](#prepare-the-required-information)
- 中转到 [Azure 门户](https://portal.azure.com/)
- 选择要增加 (的语音资源，或检查并发请求限制) 
- 选择 " *新支持请求* (*支持 + 故障排除* " 组)  
- 将显示一个新窗口，其中包含有关 Azure 订阅和 Azure 资源的自动填充信息
- 输入 *摘要* (如 "增大 TTS 自定义终结点并发请求限制" ) 
- 在 " *问题类型* " 中，选择 "配额或订阅问题"
- 出现 *问题子类型* 选择：
  - "增加配额或并发请求数"-表示增加请求
  - 用于检查现有限制的 "配额或使用情况验证"
- 单击 "*下一步：解决方案*"
- 继续执行请求创建
- 当在 " *详细信息* " 选项卡的 " *说明* " 字段中输入时：
  - 请注意，请求是关于 **文本到语音的** 配额
  - [之前收集](#prepare-the-required-information)的 Azure 资源信息 
  - 完成输入所需信息，然后单击 "*查看 + 创建*" 选项卡中的 "*创建*" 按钮
  - 请注意 Azure 门户通知中的支持请求编号。 稍后会联系你进行进一步处理

