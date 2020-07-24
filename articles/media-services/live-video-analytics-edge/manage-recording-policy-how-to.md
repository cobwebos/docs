---
title: 管理录制策略-Azure
description: 本主题介绍如何管理记录策略。
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: d3a1be915dc1cc8714e49cc7b2fe68bbe9cad161
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87011475"
---
# <a name="manage-recording-policy"></a>管理录制策略

你可以使用 IoT Edge 上的实时视频分析来[录制连续视频](continuous-video-recording-concept.md)，从而将视频录制到云中几周或几个月。 可以使用 Azure 存储中内置的[生命周期管理工具](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal)来管理该云存档的长度（以天为单位）。  

你的媒体服务帐户已链接到 Azure 存储帐户，当你将视频录制到云时，内容将写入到媒体服务[资产](../latest/assets-concept.md)中。 每个资产都映射到存储帐户中的一个容器。 生命周期管理使你可以为存储帐户定义[策略](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#policy)，你可以在其中指定如下[规则](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#rules)。

```
{
  "rules": [
    {
      "name": "NinetyDayRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "delete": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

上述规则：

* 适用于存储帐户中的所有块 blob。
* 指定当 blob 生存期超过30天时，它们将从[热访问层移到 "冷](../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal)"。
* 如果 blob 生存期超过90天，则将其删除。

由于实时视频分析会按指定的时间单位存档视频，因此，资产将包含一系列的 blob，每段一个 blob。 当生命周期管理策略启动并删除旧的 blob 时，你将继续能够通过媒体服务 Api 访问和播放剩余的 blob。 有关详细信息，请参阅[播放录制](playback-recordings-how-to.md)。 

## <a name="limitations"></a>限制

下面是生命周期管理的一些已知限制：

* 策略中最多可以有100个规则，并且每个规则最多可以指定10个容器。 因此，如果您需要具有不同的录制策略（例如，面向停车场的相机的3天存档，则为装货部的相机输入30天，在 "结帐" 计数器后的相机为180天），然后使用一个媒体服务帐户，您可以自定义最多1000个照相机的规则。
* 不会立即更新生命周期管理策略。 有关更多详细信息，请参阅[此 FAQ 部分](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#faq)。
* 如果选择应用一个策略，其中 blob 已移到 "冷" 层，则播放该部分存档可能会受到影响。 你可能会看到其他延迟或偶尔发生的错误。 媒体服务不支持在存档层中播放内容。

## <a name="next-steps"></a>后续步骤

[播放录制](playback-recordings-how-to.md)
