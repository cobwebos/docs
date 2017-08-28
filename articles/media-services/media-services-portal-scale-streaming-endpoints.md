---
title: "使用 Azure 门户缩放流式处理终结点 | Microsoft Docs"
description: "本教程逐步演示如何使用 Azure 门户缩放流式处理终结点。"
services: media-services
documentationcenter: 
author: Juliako
manager: SyntaxC4
editor: 
ms.assetid: 1008b3a3-2fa1-4146-85bd-2cf43cd1e00e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/04/2017
ms.author: juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: 76de1d40e8c24abda33e99a137f4a6c263f60a8b
ms.contentlocale: zh-cn
ms.lasthandoff: 07/06/2017

---
# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>使用 Azure 门户缩放流式处理终结点
## <a name="overview"></a>概述

> [!NOTE]
> 若要完成本教程，你需要一个 Azure 帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/)。 
> 
> 

高级流式处理终结点适合用于高级工作负载，同时提供可缩放的专用带宽容量。 默认情况下，使用高级流式处理终结点的客户会获得一个流式处理单位 (SU)。 可通过添加 SU 来缩放流式处理终结点。 每个 SU 为应用程序提供额外的带宽容量。 有关流式处理终结点类型和 CDN 配置的详细信息，请参阅[流式处理终结点概述](media-services-portal-manage-streaming-endpoints.md)主题。
 
本主题介绍了如何缩放流式处理终结点。

有关定价详细信息，请参阅[媒体服务定价详细信息](http://go.microsoft.com/fwlink/?LinkId=275107)。

## <a name="scale-streaming-endpoints"></a>缩放流式处理终结点

若要更改流式处理单位数，请按照以下步骤操作：

1. 在 [Azure 门户](https://portal.azure.com/)中，选择 Azure 媒体服务帐户。
2. 在“设置”窗口中，选择“流式处理终结点”。
3. 单击要缩放的流式处理终结点。 

    [!NOTE] 只能缩放高级流式处理终结点。

4. 滑动滑块，以指定流式处理单位数。

    ![流式处理终结点](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

## <a name="next-steps"></a>后续步骤
查看媒体服务学习路径。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


