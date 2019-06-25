---
title: 子剪辑时使用 Azure 媒体服务 REST API 进行编码的视频
description: 本主题介绍如何使用 Azure 媒体服务 REST 和编码时，子剪辑视频
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/10/2019
ms.author: juliako
ms.openlocfilehash: df8c8a4040b4aae4379b4bfe0e9a16337588dd1b
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305066"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>子剪辑的视频编码使用 Media Services 的 REST 时

您可以修整或使用对其进行编码时，子剪辑视频[作业](https://docs.microsoft.com/rest/api/media/jobs)。 此功能适用于任何[转换](https://docs.microsoft.com/rest/api/media/transforms)生成使用[BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset)预设，或者[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)预设。 

本主题中的 REST 示例创建修剪视频，因为它将提交编码作业的作业。 

## <a name="prerequisites"></a>必备组件

若要完成本主题中所述的步骤，必须：

- [创建 Azure 媒体服务帐户](create-account-cli-how-to.md)。
- [配置 Postman 以便进行 Azure 媒体服务 REST API 调用](media-rest-apis-with-postman.md)。
    
    确保遵循[获取 Azure AD 令牌](media-rest-apis-with-postman.md#get-azure-ad-token)主题中的最后一步。 
- 创建一个转换，转换和输出资产。 您可以了解如何创建一个转换，转换和输出中的资产[对基于 URL 的远程文件进行编码和流式传输视频-REST](stream-files-tutorial-with-rest.md)教程。
- 审阅[编码概念](encoding-concept.md)主题。

## <a name="create-a-subclipping-job"></a>创建子剪辑作业

1. 在下载的 Postman 集合，选择**转换和作业** -> **具有子剪辑创建作业**。
    
    **放置**请求如下所示：
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. 使用你的转换名称更新"transformName"环境变量的值。 
1. 选择**正文**选项卡上，并更新输出"myOutputAsset"资产名称。

    ```
    {
      "properties": {
        "description": "A Job with transform cb9599fb-03b3-40eb-a2ff-7ea909f53735 and single clip.",
       
        "input": {
          "@odata.type": "#Microsoft.Media.JobInputHttp",
          "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
          "files": [
                "Ignite-short.mp4"
            ],
          "start": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT10S"
          },
          "end": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT40S"
          }
        },
      
        "outputs": [
          {
            "@odata.type": "#Microsoft.Media.JobOutputAsset",
            "assetName": "myOutputAsset"
          }
        ],
        "priority": "Normal"
      }
    }
    ```
1. 按“发送”。 

    您看到**响应**与已创建并提交的作业有关的信息和作业的状态。 

## <a name="next-steps"></a>后续步骤

[如何进行编码自定义转换](custom-preset-rest-howto.md) 
