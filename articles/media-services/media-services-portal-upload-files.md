---
title: " 使用 Azure 门户将文件上载到媒体服务帐户 | Microsoft 文档s"
description: "本教程介绍了使用 Azure 门户将文件上载到媒体服务帐户的步骤"
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 3ad3dcea-95be-4711-9aae-a455a32434f6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/13/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: ed8ea30b87c8086d41cab879acce82062f08b31c
ms.openlocfilehash: f27ab42ab3c7c704804b9a5493c8b3acd954decb


---
# <a name="upload-files-into-a-media-services-account-using-the-azure-portal"></a>使用 Azure 门户将文件上载到媒体服务帐户
> [!div class="op_single_selector"]
> * [门户](media-services-portal-upload-files.md)
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> 
> [!NOTE]
> 若要完成本教程，你需要一个 Azure 帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/)。 
> 


在媒体服务中，可以将数字文件上载到资产中。 资产可包含视频、音频、图像、缩略图集合、文本轨迹和隐藏式字幕文件（以及这些文件的相关元数据。）上载文件完成后，相关内容即安全地存储在云中供后续处理和流式处理。


## <a name="upload-files"></a>上载文件

>[!NOTE]
>支持在媒体服务中处理的最大文件大小存在限制。 有关文件大小限制的详细信息，请参阅[此主题](media-services-quotas-and-limitations.md)。
>

1. 在 [Azure 门户](https://portal.azure.com/)中，选择 Azure 媒体服务帐户。
2. 在“设置”边栏选项卡中，单击“资产”。
   
    ![上载文件](./media/media-services-portal-vod-get-started/media-services-upload.png)
3. 单击“上载”按钮  。
   
    此时会显示“上载视频资产”窗口。
   
   > [!NOTE]
   > 没有文件大小限制。
   > 
   > 
4. 浏览到计算机中所需视频的位置，选中该视频，然后点击“确定”。  
   
    上载开始，你可以在文件名下看到进度。  

上传完成后，会看到“资产”窗口中列出新的资产。 

## <a name="next-steps"></a>后续步骤
现在可以对上载的资产进行编码。 有关详细信息，请参阅 [对资产进行编码](media-services-portal-encode.md)。

还可以使用 Azure Functions，基于传入到所配置容器中的文件触发编码作业。 有关详细信息，请参阅[此示例](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ )。

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Feb17_HO2-->


