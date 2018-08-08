---
title: 调整媒体处理的规模概述 | Microsoft Docs
description: 本主题概述了如何使用 Azure 媒体服务调整媒体处理的规模。
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 780ef5c2-3bd6-4261-8540-6dee77041387
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2018
ms.author: juliako
ms.openlocfilehash: 81fab8903c0101d0e4aae8a392f05129651cd762
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2018
ms.locfileid: "39368625"
---
# <a name="scaling-media-processing-overview"></a>调整媒体处理的规模概述
此页概述了如何以及为何调整媒体处理的规模。 

## <a name="overview"></a>概述
媒体服务帐户与预留单位类型关联，后者决定了编码处理任务的处理速度。 可以在以下预留单位类型中进行选择：**S1**、**S2** 或 **S3**。 例如，与 **S1** 预留单位类型相比，使用 **S2** 预留单位类型时，同一编码作业运行速度更快。 有关详细信息，请参阅[预留单位类型](https://azure.microsoft.com/blog/high-speed-encoding-with-azure-media-services/)。

除了指定预留单位类型，还可以指定通过预留单位来设置帐户。 设置的预留单位数决定了给定帐户中可并发处理的媒体任务数。 例如，如果帐户具有 5 个预留单位，则只要有任务要处理，就可以同时运行 5 个媒体任务。 其余任务将排队等待，运行的任务完成后才选择它们以按顺序进行处理。 如果帐户未设置任何预留单位，则按顺序选择任务进行处理。 在这种情况下，完成一个任务和开始下一个任务之间的等待时间将取决于系统中资源的可用性。

## <a name="choosing-between-different-reserved-unit-types"></a>在不同的预留单位类型之间进行选择
下表有助于在不同的编码速度之间进行选择时做出决定。 它还提供了几个基准案例，并提供可用于下载视频的 SAS URL，可以对该视频执行自己的测试：

| 方案 | **S1** | **S2** | **S3** |
| --- | --- | --- | --- |
| 预期的用例 |单比特率编码。 <br/>具有 SD 或更低分辨率的文件，不具有高时效性，成本低。 |单比特率和多比特率编码。<br/>SD 和 HD 编码的正常使用情况。 |单比特率和多比特率编码。<br/>全高清和 4K 分辨率视频。 对时间敏感，更快的编码周转。 |
| 基准 |编码为具有相同分辨率的单比特率 MP4 文件大约需要 11 分钟。 |预设为“H264 单比特率 720p”的编码大约需要 5 分钟。<br/><br/>预设为“H264 多比特率 720p”的编码大约需要 11.5 分钟。 |预设为“H264 单比特率 1080p”的编码大约需要 2.7 分钟。<br/><br/>预设为“H264 多比特率 1080p”的编码大约需要 5.7 分钟。 |


## <a name="considerations"></a>注意事项
> [!IMPORTANT]
> 查看本节中所述的注意事项。  
> 
> 

* 对于由媒体服务 v3 或视频索引器触发的音频分析和视频分析作业，强烈建议采用 S3 单位类型。
* 如果使用共享的池（即没有任何预留单位），则编码任务将具有与 S1 RU 相同的性能。 但是，任务在排队状态下花费的时间可能没有上限，并且在任何给定的时间内，最多只会运行一项任务。

## <a name="billing"></a>计费

将根据媒体预留单位的实际使用分钟数对你进行收费。 有关详细说明，请参阅[媒体服务定价](https://azure.microsoft.com/pricing/details/media-services/)页的“常见问题”部分。   

## <a name="quotas-and-limitations"></a>配额和限制
有关配额和限制以及如何开具支持票证的信息，请参阅[配额和限制](media-services-quotas-and-limitations.md)。

## <a name="next-step"></a>后续步骤
通过以下技术之一完成调整媒体处理的规模任务： 

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [门户](media-services-portal-scale-media-processing.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 

> [!NOTE]
> 要获取最新版本的 Java SDK 并开始使用 Java 进行开发，请参阅[媒体服务的 Java 客户端 SDK 入门](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use)。 <br/>
> 要下载最新的媒体服务 PHP SDK，请在 [Packagist 存储库](https://packagist.org/packages/microsoft/windowsazure#v0.5.7)中查找 0.5.7 版 Microsoft/WindowAzure 包。  

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

