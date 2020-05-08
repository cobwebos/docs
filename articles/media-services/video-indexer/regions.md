---
title: 提供了视频索引器的区域 - Azure
titleSuffix: Azure Media Services
description: 本文介绍 Azure 媒体服务视频索引器可用的 Azure 区域。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: b71890323f6aca92fb9f847252feed57d8db234f
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594944"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>存在视频索引器的 Azure 区域

视频索引器 API 包含一个**位置**参数，应当将其设置为要将调用路由到的 Azure 区域。 这必须是一个[提供了视频索引器的 Azure 区域](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all)。

## <a name="locations"></a>位置

必须`location`为参数提供 Azure 区域代码名称作为其值。 如果在预览模式下使用视频索引器，应将`"trial"`作为值。 `trial``location`参数的默认值。 否则，若要获取你的帐户所在的并且应当将你的调用路由到的 Azure 区域的代码名称，可以在 [Azure CLI](/cli/azure) 中运行以下命令行：

```azurecli-interactive
az account list-locations
```

运行上面所示的命令行后，会得到所有 Azure 区域的列表。 导航到具有你要查找的 *displayName* 的 Azure 区域，并将其 *name* 值用于**位置**参数。

例如，对于 Azure 区域“美国西部 2”（如下所示），你将“westus2”用于**位置**参数。

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="next-steps"></a>后续步骤

- [使用 API 自定义语言模型](customize-language-model-with-api.md)
- [使用 API 自定义品牌模型](customize-brands-model-with-api.md)
- [使用 API 自定义人员模型](customize-person-model-with-api.md)
