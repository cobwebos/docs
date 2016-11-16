---
title: " 使用 Azure 门户将文件上传到媒体服务帐户 | Microsoft Docs"
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
ms.date: 10/14/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8daf63e164dc548023878af091491b5d942276d9


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
> 

在媒体服务中，可以将数字文件上载到资产中。 资产可包含视频、音频、图像、缩略图集合、文本轨迹和隐藏式字幕文件（以及这些文件的相关元数据。）上载文件完成后，相关内容即安全地存储在云中供后续处理和流式处理。

1. 在 [Azure 门户](https://portal.azure.com/)中，选择 Azure 媒体服务帐户。
2. 在“设置”边栏选项卡中，单击“资产”。
   
    ![上载文件](./media/media-services-portal-vod-get-started/media-services-upload.png)
3. 单击“上载”按钮  。
   
    此时会显示“上传视频资产”窗口。
   
   > [!NOTE]
   > 没有文件大小限制。
   > 
   > 
4. 浏览到计算机中所需视频的位置，选中该视频，然后点击“确定”。  
   
    上载开始，你可以在文件名下看到进度。  

上传完成后，会看到“资产”窗口中列出新的资产。 

## <a name="next-steps"></a>后续步骤
现在可以对上载的资产进行编码。 有关详细信息，请参阅 [对资产进行编码](media-services-portal-encode.md)。

## <a name="media-services-learning-paths"></a>媒体服务学习路径
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Nov16_HO2-->


