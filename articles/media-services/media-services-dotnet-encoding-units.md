---
title: "通过添加编码单元缩放媒体处理 - Azure | Microsoft 文档"
description: "了解如何使用 .NET 添加编码单元"
services: media-services
documentationcenter: 
author: juliako
manager: SyntaxC4
editor: 
ms.assetid: 33f7625a-966a-4f06-bc09-bccd6e2a42b5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako;milangada;
ms.translationtype: Human Translation
ms.sourcegitcommit: bdf41edfa6260749a91bc52ec0a2b62fcae99fb0
ms.openlocfilehash: ee40b92c48d91a391fe72582c7ea5e244907747f
ms.contentlocale: zh-cn
ms.lasthandoff: 01/27/2017

---
# <a name="how-to-scale-encoding-with-net-sdk"></a>如何使用 .NET SDK 缩放编码
> [!div class="op_single_selector"]
> * [门户](media-services-portal-scale-media-processing.md)
> * [.NET](media-services-dotnet-encoding-units.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>概述
> [!IMPORTANT]
> 请确保查看[概述](media-services-scale-media-processing-overview.md)主题，获取有关调整媒体处理的规模主题的详细信息。
> 
> 

若要使用 .NET SDK 更改保留单元类型和编码保留单元数目，请执行以下操作：

    IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
    encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
    encodingS1ReservedUnit.Update();
    Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);

    encodingS1ReservedUnit.CurrentReservedUnits = 2;
    encodingS1ReservedUnit.Update();

    Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

## <a name="opening-a-support-ticket"></a>创建支持票证
默认情况下，每个媒体服务帐户最多可缩放到 25 个编码保留单元和 5 个点播流保留单元。 你可以通过创建支持票证申请更高的限制值。

### <a name="open-a-support-ticket"></a>开具支持票证
若要开具支持票证，请执行以下操作：

1. 单击[获取支持](https://manage.windowsazure.com/?getsupport=true)。 如果你尚未登录，系统将提示你输入凭据。
2. 选择你的订阅。
3. 在支持类型下，选择“技术”。
4. 单击“创建票证”。
5. 在下一页显示的产品列表中选择“Azure 媒体服务”。
6. 选择适合你问题的“问题类型”。
7. 单击“继续”(Continue)。
8. 根据下一页上的说明进行操作，然后输入问题的详细信息。
9. 单击“提交”以创建该票证。

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


