---
title: 提交 Azure 媒体剪辑器剪辑作业 | Microsoft 文档
description: 从 Azure 媒体剪辑器提交剪辑作业的步骤
services: media-services
keywords: 剪辑;子剪辑;编码;媒体
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 8372c405087c0dc7a000a65265bb99c395c3a8d6
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33783176"
---
# <a name="submit-clipping-jobs-from-azure-media-clipper"></a>从 Azure 媒体剪辑器提交剪辑作业
Azure 媒体剪辑器需要实现 submitSubclipCallback 方法才可以处理剪辑作业提交。 该功能用于实现指向 Web 服务的 Clipper 输出的 HTTP POST。 此 Web 服务是可以在其中提交编码作业的位置。 Clipper 的输出是对呈现作业的 Media Encoder Standard 编码预设，或者是动态清单筛选器调用的 REST API 有效负载。 此传递模型是必需的，因为客户端浏览器中的媒体服务帐户凭据并不安全。

以下序列图阐释浏览器客户端、Web 服务和 Azure 媒体服务之间的工作流程：![Azure 媒体剪辑器序列图](media/media-services-azure-media-clipper-submit-job/media-services-azure-media-clipper-sequence-diagram.PNG)

在上图中，这四个实体是：最终用户的浏览器、Web 服务、承载剪辑器资源的 CDN 终结点和 Azure 媒体服务。 当最终用户导航到 Web 页时，该页从承载的 CDN 终结点获取剪辑器 JavaScript 和 CSS 资源。 最终用户从浏览器配置剪裁作业或动态清单筛选器创建调用。 当最终用户提交作业或筛选器创建调用时，浏览器会将作业有效负载放入必须部署的 Web 服务中。 此 Web 服务最终使用媒体服务帐户凭据将剪裁作业或筛选器创建调用提交到 Azure 媒体服务。

下面的代码示例阐释了 submitSubclipCallback 示例方法。 此方法实现 Media Encoder Standard 编码预设的 HTTP POST。 如果 POST 成功（结果），则承诺得到解决，否则，它会被拒绝并附带错误详细信息。

```javascript
// Submit Subclip Callback
//
// Parameter:
// - subclip: object that represents the subclip (output contract).
//
// Returns: a Promise object that, when resolved, retuns true if the operation was accept in the back-end; otherwise, returns false.
var onSubmitSubclip = function (subclip) {
    var promise = new Promise(function (resolve, reject) {
        // TODO: perform the back-end AJAX request to submit the subclip job.
        var result = true;

        if (/* everything turned out fine */ result) {
            resolve(result);
        }
        else {
            reject(Error("error details"));
        }
    });

    return promise;
};

var subclipper = new subclipper({
    selector: '#root',
    restVersion: '2.0',
    submitSubclipCallback: onSubmitSubclip,
});
```
作业提交的输出是对呈现作业的 Media Encoder Standard 编码预设，或者是动态清单筛选器的 REST API 有效负载。

## <a name="submitting-encoding-job-to-create-video"></a>提交编码作业以创建视频
可提交 Media Encoder Standard 编码作业来创建帧精确视频剪辑。 编码作业生成呈现视频，即新分段的 MP4 文件。

呈现剪辑的作业输出协定是一个具有以下属性的 JSON 对象：

```json
{
  /* Required */
  "name": "My New Subclip Output Name",

  /* Required: string specifying the format of the Json file.
   */
  /* NOTE: The REST API version, currently 2.0 is supported
   */
  "restVersion": "2.0"

  /* Required: array containing all the input Ids (both "asset" or "filter" type) used in the subclipper timeline;
  it must contain at least one item. */
  /* NOTE: when "output"."type" === "job", all items must match the "inputsIds"[i]."type" === "asset" condition;
  this array can be used in the back-end to get the input assets for the subclipping job with the Media Encoder Standard (MES) processor. */
  /* NOTE: when "output"."type" === "filter", there must be a single item in the array ("inputsIds".length === 1)
  that can be used in the back-end to get the source for the dynamic manifest filter (create or update). */
  "inputsIds": [{
    /* Required */
    "id": "my-asset-id-1",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }, {
    /* Required */
    "id": "my-asset-id-2",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }],

  /* Required */
  "output": {

    /* Required: must be one of the following values: "job" or "filter" */
    "type": "job",

    /* Required if "type" === "job" */
    /* NOTE: This is the preset for the Media Encoder Standard (MES) processor that can be used in the back-end to sumit the subclip job.
    The encoding profile ("Codecs" property) depends on the "singleBitrateMp4Profile" and "multiBitrateMp4Profile" option parameters
    specified when creating the widget instance. */
    /* REFERENCE: https://docs.microsoft.com/azure/media-services/media-services-advanced-encoding-with-mes */
    "job": {
      "Version": 1.0,
      "Codecs": [{
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": true,
          "H264Layers": [{
            "Profile": "Auto",
            "Level": "auto",
            "Bitrate": 6750,
            "MaxBitrate": 6750,
            "BufferWindow": "00:00:05",
            "Width": 1920,
            "Height": 1080,
            "BFrames": 3,
            "ReferenceFrames": 3,
            "AdaptiveBFrame": true,
            "Type": "H264Layer",
            "FrameRate": "0/1"
          }],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [{
        "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
        "Format": {
          "Type": "MP4Format"
        }
      }],
      "Sources": [{
        "AssetId": "my-asset-id-1",
        "StartTime": "0.00:01:15.000",
        "Duration": "0.00:00:25.000"
      }, {
        "AssetId": "my-asset-id-2",
        "StartTime": "0.00:20:04.000",
        "Duration": "0.00:33:07.500"
      }]
    }
  }
}
```

若要执行编码作业，请将 Media Encoder Standard 编码作业和关联的预设一同提交。 有关使用 [.NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-encode-with-media-encoder-standard) 或 [REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-encode-asset) 提交编码作业的详细信息，请参阅本文。

## <a name="quickly-creating-video-clips-without-encoding"></a>无需编码即可快速创建视频剪辑
除了创建编码作业，还可使用 Azure 媒体剪辑器创建动态清单筛选器。 筛选器不需要编码即可快速创建，因为不会创建新资产。 筛选器剪辑的输出协定是一个具有以下属性的 JSON 对象：

```json
{
  /* Required: Filter name (Alphanumeric characters and hyphens only, no whitespace) */
  "name": "FilterName",

  /* Required: string specifying the format of the Json file.
   */
  /* NOTE: This subclipper version always returns '2.0' in this field.
   */
  "restVersion": "2.0"

  /* Required: array containing all the input Ids (both "asset" or "filter" type) used in the subclipper timeline;
  it must contain at least one item. */
  /* NOTE: when "output"."type" === "job", all items must match the "inputsIds"[i]."type" === "asset" condition;
  this array can be used in the back-end to get the input assets for the subclipping job with the Media Encoder Standard (MES) processor. */
  /* NOTE: when "output"."type" === "filter", there must be a single item in the array ("inputsIds".length === 1)
  that can be used in the back-end to get the source for the dynamic manifest filter (create or update). */
  "inputsIds": [{
    /* Required */
    "id": "my-asset-id",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }],

  /* Required */
  "output": {

    /* Required: must be one of the following values: "job" or "filter" */
    "type": "filter",

    /* Required if "type" === "filter" */
    /* REFERENCE: https://docs.microsoft.com/rest/api/media/operations/assetfilter */
    "filter": {
      "PresentationTimeRange": {
        "StartTimestamp": "340000000",
        "EndTimestamp": "580000000",
        "Timescale": "10000000"
      },
      "Tracks": [{
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "video",
          "Operator": "Equal"
        }]
      }, {
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "audio",
          "Operator": "Equal"
        }, {
          "Property": "Name",
          "Value": "audio-track-1",
          "Operator": "Equal"
        }]
      }, {
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "text",
          "Operator": "Equal"
        }, {
          "Property": "Language",
          "Value": "en",
          "Operator": "Equal"
        }]
      }]
    }
  }
}
```

若要提交 REST 调用以创建动态清单筛选器，请使用 [REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest) 提交关联的筛选器有效负载。