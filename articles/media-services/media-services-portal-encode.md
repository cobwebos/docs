<properties
	pageTitle="通过 Azure 门户使用媒体编码器标准版对资产进行编码 | Azure"
	description="本教程逐步演示如何通过 Azure 门户使用媒体编码器标准版对资产进行编码。"
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
	ms.date="08/29/2016"
	wacn.date=""
	ms.author="juliako"/>


# 通过 Azure 门户使用媒体编码器标准版对资产进行编码

> [AZURE.NOTE] 若要完成本教程，你需要一个 Azure 帐户。有关详细信息，请参阅 [Azure 试用](/pricing/1rmb-trial/)。

使用 Azure 媒体服务时最常见的方案之一是将自适应比特率流传送至你的客户端。媒体服务支持以下自适应比特率流式处理技术：HTTP 实时流式处理 (HLS)、平滑流式处理、MPEG DASH 和 HDS（仅适用于 Adobe PrimeTime/Access 许可证持有人）。若要对视频进行准备，以便进行自适应比特率流式处理，你需要将源视频编码成多比特率文件。应使用“媒体编码器标准版”编码器来编码视频。

此外，媒体服务提供的动态打包可让你以下述流格式传送多比特率 MP4，而无须重新打包成这些流格式：MPEG DASH、HLS、Smooth Streaming 或 HDS。通过动态打包，你只需要存储及支付一种存储格式的文件，媒体服务将会根据客户端的要求创建并提供适当的响应。

若要使用动态打包，必须执行下列操作：

- 将源文件编码成一组多比特率 MP4 文件（本部分稍后将演示编码步骤）。
- 针对你要传送内容的“流式处理终结点”，获取至少一个流式处理单位。有关详细信息，请参阅[配置流式处理终结点](/documentation/articles/media-services-portal-vod-get-started/#configure-streaming-endpoints)。

若要调整媒体处理的规模，请参阅[此](/documentation/articles/media-services-portal-scale-media-processing/)主题。

## 使用 Azure 门户编码

本部分介绍使用媒体编码器标准版为内容编码时可以执行的步骤。

1.  在“设置”窗口中，选择“资产”。
2.  在“资产”窗口中，选择你想要编码的资产。
3.  按“编码”按钮。
4.  在“对资产进行编码”窗口中，选择“媒体编码器标准版”处理器和预设。例如，如果你知道输入视频的分辨率为 1920x1080 像素，则可使用“H264 多比特率 1080p”预设。有关预设的详细信息，请参阅[此](https://msdn.microsoft.com/zh-cn/library/azure/mt269960.aspx)文章 – 选择最适合输入视频的预设很重要。如果视频的分辨率低 (640x360)，则不应使用默认的“H264 多比特率 1080p”预设。
	
	为了方便管理，系统允许你编辑输出资产的名称，以及作业的名称。
		
	![对资产进行编码](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. 按“创建”。


##后续步骤

可通过 Azure 门户监视编码作业的进度，如[此](/documentation/articles/media-services-portal-check-job-progress/)文章中所述。

<!---HONumber=Mooncake_0926_2016-->