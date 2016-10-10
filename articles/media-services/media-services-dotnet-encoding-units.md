<properties 
	pageTitle="如何添加编码单元" 
	description="了解如何使用 .NET 添加编码单元"  
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
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
	ms.author="juliako;milangada;gtrifonov"/>


#如何使用 .NET SDK 缩放编码

> [AZURE.SELECTOR]
- [门户](/documentation/articles/media-services-portal-scale-media-processing/)
- [.NET](/documentation/articles/media-services-dotnet-encoding-units/)
- [REST](https://msdn.microsoft.com/zh-cn/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)

##概述

>[AZURE.IMPORTANT] 请确保查看[概述](/documentation/articles/media-services-scale-media-processing-overview/)主题，获取有关调整媒体处理的规模主题的详细信息。
 
若要使用 .NET SDK 更改保留单元类型和编码保留单元数目，请执行以下操作：

	IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
	encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
	encodingS1ReservedUnit.Update();
	Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);
	
	encodingS1ReservedUnit.CurrentReservedUnits = 2;
	encodingS1ReservedUnit.Update();
	
	Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

##创建支持票证

默认情况下，每个媒体服务帐户最多可缩放到 25 个编码保留单元和 5 个点播流保留单元。你可以通过创建支持票证申请更高的限制值。

###开具支持票证

若要开具支持票证，请执行以下操作：

1. 单击[获取支持](https://manage.windowsazure.cn/?getsupport=true)。如果你尚未登录，系统将提示你输入凭据。

1. 选择你的订阅。

1. 在支持类型下，选择“技术”。

1. 单击“创建票证”。

1. 在下一页显示的产品列表中选择“Azure 媒体服务”。

1. 选择适合你问题的“问题类型”。

1. 单击“继续”(Continue)。

1. 根据下一页上的说明进行操作，然后输入问题的详细信息。

1. 单击“提交”以创建该票证。

<!---HONumber=Mooncake_0926_2016-->