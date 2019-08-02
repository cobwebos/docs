---
title: 使用 Azure 媒体服务 REST API 编码时子剪辑视频
description: 本主题说明如何在使用 REST 通过 Azure 媒体服务进行编码时子剪辑视频
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
ms.openlocfilehash: 3557aef6213955ef77542bffafe0a2b0c374ed68
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704448"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>使用媒体服务进行编码时子剪辑视频-REST

使用[作业](https://docs.microsoft.com/rest/api/media/jobs)对视频进行编码时, 可以使用它来剪裁或子剪辑视频。 此功能适用于使用[BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset)预设或[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)预设生成的任何[转换](https://docs.microsoft.com/rest/api/media/transforms)。 

本主题中的 REST 示例创建了一个作业, 该作业会在提交编码作业时剪裁视频。 

## <a name="prerequisites"></a>先决条件

若要完成本主题中所述的步骤，必须：

- [创建 Azure 媒体服务帐户](create-account-cli-how-to.md)。
- [配置 Postman 以便进行 Azure 媒体服务 REST API 调用](media-rest-apis-with-postman.md)。
    
    确保遵循[获取 Azure AD 令牌](media-rest-apis-with-postman.md#get-azure-ad-token)主题中的最后一步。 
- 创建转换和输出资产。 可以在 "[根据 URL 编码远程文件并传输视频 REST](stream-files-tutorial-with-rest.md)教程" 中查看如何创建转换和输出资产。
- 查看[编码概念](encoding-concept.md)主题。

## <a name="create-a-subclipping-job"></a>创建子剪辑作业

1. 在下载的 Postman 集合中, 选择 "**转换" 和 "作业** -> " "**创建具有子剪辑的作业**"。
    
    **PUT**请求如下所示:
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. 请将 "transformName" 环境变量的值更新为你的转换名称。 
1. 选择 "**正文**" 选项卡, 并将 "myOutputAsset" 更新为输出资产名称。

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

    你会看到**响应**, 其中包含有关已创建和提交作业的信息, 以及作业的状态。 

## <a name="next-steps"></a>后续步骤

[如何使用自定义转换进行编码](custom-preset-rest-howto.md) 
