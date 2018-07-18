---
title: 将资产加载到 Azure 媒体剪辑器 | Microsoft Docs
description: 将资产加载到 Azure 媒体剪辑器的步骤
services: media-services
keywords: 剪辑;子剪辑;编码;媒体
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 6a479218ff8bd5addf4273b23c06380859e0ea08
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33782896"
---
# <a name="loading-assets-into-azure-media-clipper"></a>将资产加载到 Azure 媒体剪辑器
可以通过两种方法将资产加载到 Azure 媒体剪辑器：
1. 静态传入资产库
2. 通过 API 动态生成资产列表

## <a name="statically-load-videos-into-clipper"></a>将视频静态加载到剪辑器
将视频静态加载到剪辑器，使最终用户可以无需从资产选择面板中选择视频也可以生成剪辑。

在这种情况下，将一组静态资产传递到剪辑器。 每个资产都包括 AMS 资产/筛选器 ID、名称、已发布的流式处理 URL。 如果适用，则可以传入内容保护身份验证令牌或一组缩略图 URL。 如果传入，缩略图将填充到界面中。 在资产库为静态且较小的情况下，可以传入库中每个资产的资产协定。

> [!NOTE]
> 将资产静态加载到剪辑器时，资产会直接添加到时间线，且不呈现资产窗格。 第一个资产添加到时间线，其余资产堆叠在时间线右侧。

若要加载静态资产库，请使用 load 方法，传入每个资产的 JSON 表示形式。 以下代码示例举例说明一个资产的 JSON 表示形式。

```javascript
var assets = [
    {
      /* Required: represents the Azure Media Services asset Id when "type" === "asset"; otherwise, represents the dynamic manifest asset filter Id ("type" === "filter")  */
      "id": "my-asset-or-dynamic-manifest-asset-filter-id",
    
      /* Required: represents the asset name as shown in the Clipper interface */
      "name": "My Asset or Dynamic Manifest Asset Filter Name",
    
      /* Required: must be one of the following values: "asset" or "filter" */
      /* NOTE: "asset" type represents a rendered asset; "filter" type represents a dynamic manifest asset filter */
      "type": "asset",
    
      /* Required */
      "source": {
    
        /* Required: represents the asset streaming locator, the base Smooth Streaming URL */
        "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
    
        /* Optional: default value "application/vnd.ms-sstr+xml" */
        "type": "application/vnd.ms-sstr+xml",
    
        /* Required: If the asset has content protection applied, then you must include an array with the different protection types along with the token to request the license/key; otherwise, provide an empty array */
        "protectionInfo": [{
            "type": "AES",
            "authenticationToken": "Bearer aes-token-placeholder"
          },
          {
            "type": "PlayReady",
            "authenticationToken": "Bearer playready-token-placeholder"
          },
          {
            "type": "Widevine",
            "authenticationToken": "Bearer widevine-token-placeholder"
          },
          {
            "type": "FairPlay",
            "certificateUrl": "//example/path/to/myfairplay.der",
            "authenticationToken": "Bearer fairplay-token-placeholder"
          }
        ]
      },
    
      /* Optional: array containing thumbnail URLs for the video. */
      /* NOTE: For the thumbnail URLs to work as expected in the Clipper timeline they must be evenly distributed across the video (based on the duration) and in chronological order within the array. */
      "thumbnails": [
        "//example/path/thumbnail_001.jpg",
        "//example/path/thumbnail_002.jpg",
        "//example/path/thumbnail_003.jpg",
        "//example/path/thumbnail_004.jpg",
        "//example/path/thumbnail_005.jpg"
        ]
    }
];

var subclipper = new subclipper({
    selector: '#root',
    restVersion: '2.0',
    submitSubclipCallback: onSubmitSubclip,
});
subclipper.ready(function () {
    subclipper.load(assets);
});

```

> [!NOTE]
> 建议如上例所示连接 load() 方法调用和 ready(handler) 方法。 上例确保加载资产前小组件已准备就绪。

> [!NOTE]
> 若要缩略图 URL 在剪辑器时间线中达到预期效果，则必须将其均匀分布在视频中（基于持续时间），并按照时间顺序在缩略图组中排列。 可以使用以下 JSON 预设代码片段作为示例参考，从而使用“Media Encoder Standard”处理器生成图像：

```json
{
  "Start": "0",
  "Step": "00:00:05",
  "Range": "100%",
  "Type": "PngImage",
  "PngLayers": [
    {
      "Type": "PngLayer",
      "Width": 48,
      "Height": 26
    }
  ]
}
```

## <a name="dynamically-load-videos-in-clipper"></a>将视频动态加载到剪辑器
将视频动态加载到剪辑器，使最终用户从资产选择面板中选择要剪辑的视频。

或者，可以通过回调动态加载资产。 在资产为动态生成或库较大的情况下，应通过回调进行加载。 若要动态加载资产，则必须实现可选的 onLoadAssets 回调函数。 此函数在初始化时传递到剪辑器。 已解析资产应遵循与静态加载资产相同的协定。 以下代码示例举例说明方法签名、预期输入和预期输出。

> [!NOTE]
> 将资产动态加载到剪辑器时，资产呈现在“资产选择面板”中。

```javascript
// Video Assets Pane Callback
    //
    // Filter Parameters:
    // - search: string value term that will be used in the back-end to filter assets by name.
    // - skip: int value used for pagination in the back-end that allows skipping a number of assets in the response.
    // - take: int value used for pagination in the back-end that allows defining the number of assets to include in the response.
    // - type: ('filter', 'asset') value that will be used in the back-end to filter assets by type.
    //
    // Returns: a Promise object that, when resolved, retuns an object containing an array of assets (input contract)
    //          that satisfies the filter parameters, plus optionally the total types of files available:
    // {
    //  total: 100,
    //  assets: [{...}],
    // }
    var onLoadAssets = function (search, skip, take, type) {
        var promise = new Promise(function (resolve, reject) {
            // TODO: implement the getAssetsFromBackend method to get the assets from the back-end using the filter parameters (search, skip, take, type).
            getAssetsFromBackend(search, skip, take, type)
                .then(function (assets) {
                    resolve({
                        total: assets.length,
                        assets: assets
                    });
                }).catch(function () {
                    reject(Error("error details"));
                });
        });
    
        return promise;
    };

    // Create widget instance:
    // - using a root element selector
    // - enabling the Video Assets panel by registering a callback in the 'assetsPanelLoaderCallback' option parameter.
    var widget = new subclipper({
        selector: '#root',

        // Enable the Video Assets panel in the widget to automatically load assets (input contract)
        assetsPanelLoaderCallback: onLoadAssets
    });

    // ...
    // The widget will automatically invoke the 'assetsPanelLoaderCallback' callback with the filter parameters specified by the user 
    // and load assets returned by the Promise into the Video Assets panel.
    // ...
```