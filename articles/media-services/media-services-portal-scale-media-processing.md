---
title: "使用 Azure 门户缩放媒体处理 | Microsoft Docs"
description: "本教程逐步介绍了如何使用 Azure 门户缩放媒体处理。"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: e500f733-68aa-450c-b212-cf717c0d15da
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/04/2017
ms.author: juliako
ms.openlocfilehash: 46ca29d3e66701f2abcb185791089e94761984e8
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="change-the-reserved-unit-type"></a>更改保留单位类型
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [门户](media-services-portal-scale-media-processing.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>概述

媒体服务帐户与保留单位类型关联，后者决定了编码处理任务的处理速度。 可以在以下保留单位类型中进行选择：**S1**、**S2** 或 **S3**。 例如，与 **S1** 保留单位类型相比，使用 **S2** 保留单位类型时，同一编码作业运行速度更快。

除了指定保留单位类型，还可以指定为帐户预配保留单位 (RU)。 预配的 RU 数决定了给定帐户中可并发处理的媒体任务数。

>[!NOTE]
>RU 可用于并行化所有媒体处理，包括使用 Azure Media Indexer 为作业编制索引。 但是，与编码不同，索引作业使用更快的保留单位并不能更快地完成处理。

> [!IMPORTANT]
> 请确保查看[概述](media-services-scale-media-processing-overview.md)主题，获取有关调整媒体处理的规模主题的详细信息。
> 
> 

## <a name="scale-media-processing"></a>调整媒体处理的规模
若要更改保留单位类型和保留单位数目，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com/)中，选择 Azure 媒体服务帐户。
2. 在“设置”窗口中，选择“媒体保留单位”。
   
    若要更改所选保留单位类型的保留单位数，请使用“媒体保留单位”滑块。
   
    若要更改“保留单位类型”，请按“S1”、“S2”或“S3”。
   
    ![“处理器”页](./media/media-services-portal-scale-media-processing/media-services-scale-media-processing.png)
3. 按“保存”按钮保存更改。
   
    按“保存”后，会立即分配新的保留单位。

## <a name="next-steps"></a>后续步骤
查看媒体服务学习路径。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

