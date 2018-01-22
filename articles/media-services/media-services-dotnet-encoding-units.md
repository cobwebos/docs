---
title: "通过添加编码单元缩放媒体处理 - Azure | Microsoft 文档"
description: "了解如何使用 .NET 添加编码单元"
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 33f7625a-966a-4f06-bc09-bccd6e2a42b5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2017
ms.author: juliako;milangada;
ms.openlocfilehash: 9274e2807beeb0fd517ba1e848cfc26db1c57655
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/13/2018
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

> [!NOTE]
> 若要获取最新版本的 Java SDK 并开始使用 Java 进行开发，请参阅[媒体服务的 Java 客户端 SDK 入门](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use)。 <br/>
> 若要下载最新的媒体服务 PHP SDK，请在 [Packagist 存储库](https://packagist.org/packages/microsoft/windowsazure#v0.5.7)中查找 0.5.7 版 Microsoft/WindowAzure 包。  

## <a name="overview"></a>概述
> [!IMPORTANT]
> 请确保查看[概述](media-services-scale-media-processing-overview.md)主题，获取有关调整媒体处理规模的详细信息。
> 
> 

若要使用 .NET SDK 更改保留单位类型和编码保留单位数目，请执行以下操作：

    IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
    encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
    encodingS1ReservedUnit.Update();
    Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);

    encodingS1ReservedUnit.CurrentReservedUnits = 2;
    encodingS1ReservedUnit.Update();

    Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

## <a name="opening-a-support-ticket"></a>创建支持票证
默认情况下，每个媒体服务帐户最多可缩放到 25 个编码保留单位和 5 个点播流保留单位。 可以通过创建支持票证申请更高的限制值。

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

