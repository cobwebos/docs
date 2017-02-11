---
title: " 使用 Azure 门户调整媒体处理的规模 | Microsoft Docs"
description: "本教程逐步演示如何使用 Azure 门户调整媒体处理的规模。"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: e500f733-68aa-450c-b212-cf717c0d15da
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c7c894b62253273a0cda61c02e198eda28496650


---
# <a name="change-the-reserved-unit-type"></a>更改保留单位类型
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [门户](media-services-portal-scale-media-processing.md)
> * [REST](https://msdn.microsoft.com/library/azure/dn859236.aspx)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>概述
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




<!--HONumber=Nov16_HO3-->


