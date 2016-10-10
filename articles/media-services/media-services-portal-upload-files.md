<properties
	pageTitle=" 使用 Azure 门户将文件上传到媒体服务帐户 | Azure"
	description="本教程将指导你完成利用 Azure 门户将文件上传到媒体服务帐户中的步骤"
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
	ms.topic="get-started-article"
	ms.date="08/29/2016"
	wacn.date=""
	ms.author="juliako"/>



# 使用 Azure 门户将文件上传到媒体服务帐户 

> [AZURE.SELECTOR]
- [门户](/documentation/articles/media-services-portal-upload-files/)
- [.NET](/documentation/articles/media-services-dotnet-upload-files/)
- [REST](/documentation/articles/media-services-rest-upload-files/)

> [AZURE.NOTE] 若要完成本教程，你需要一个 Azure 帐户。有关详细信息，请参阅 [Azure 试用](/pricing/1rmb-trial/)。

在媒体服务中，可以将数字文件上载到资产中。资产可包含视频、音频、图像、缩略图集合、文本轨迹和隐藏式字幕文件（以及这些文件的相关元数据。） 上载文件完成后，相关内容即安全地存储在云中供后续处理和流式处理。
 
1. 在“设置”窗口中，单击“资产”。

	![上载文件](./media/media-services-portal-vod-get-started/media-services-upload.png)

3. 单击“上载”按钮。

	此时会显示“上载视频资产”窗口。

	>[AZURE.NOTE] 没有文件大小限制。
	
4. 浏览到计算机中所需视频的位置，选中该视频，然后点击“确定”。

	上载开始，你可以在文件名下看到进度。

上载完成后，你会看到“资产”窗口中列出新的资产。


## 后续步骤

现即可编码已上传的资产。有关详细信息，请参阅[对资产进行编码](/documentation/articles/media-services-portal-encode/)。

<!---HONumber=Mooncake_0926_2016-->