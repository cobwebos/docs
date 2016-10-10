<properties
	pageTitle=" 如何使用 Azure 门户调整媒体处理的规模 | Azure"
	description="本教程逐步演示如何使用 Azure 门户调整媒体处理的规模。"
	services="media-services"
	documentationCenter=""
	authors="Juliako"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/01/2016"
	wacn.date=""
	ms.author="juliako"/>

# 更改保留单位类型

> [AZURE.SELECTOR]
- [.NET](/documentation/articles/media-services-dotnet-encoding-units/)
- [门户](/documentation/articles/media-services-portal-scale-media-processing/)
- [REST](https://msdn.microsoft.com/zh-cn/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

## 概述

>[AZURE.IMPORTANT] 请确保查看[概述](/documentation/articles/media-services-scale-media-processing-overview/)主题，获取有关调整媒体处理的规模主题的详细信息。

## 调整媒体处理的规模

若要更改保留单位类型和保留单位数目，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.cn/)登录。

2. 在“设置”窗口中，选择“媒体保留单位”。

	若要更改所选保留单位类型的保留单位数，请使用“媒体保留单位”滑块。

	若要更改“保留单位类型”，请按“S1”、“S2”或“S3”。

	![“处理器”页](./media/media-services-portal-scale-media-processing/media-services-scale-media-processing.png)

3. 按“保存”按钮保存更改。

	按“保存”后，会立即分配新的保留单位。

<!---HONumber=Mooncake_0926_2016-->