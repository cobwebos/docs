---
title: 提供了视频索引器的区域 - Azure
titleSuffix: Azure Media Services
description: 本文介绍 Azure 媒体服务视频索引器可用的 Azure 区域。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/14/2020
ms.author: juliako
ms.openlocfilehash: 6ebdb22f50efbefc695f9752c6e6fc333571828c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530925"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>存在视频索引器的 Azure 区域

视频索引器 API 包含一个**位置**参数，应当将其设置为要将调用路由到的 Azure 区域。 这必须是一个[提供了视频索引器的 Azure 区域](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all)。

## <a name="locations"></a>位置

`location`必须为参数提供 Azure 区域代码名称作为其值。 如果在预览模式下使用视频索引器，应将 `"trial"` 作为值。 `trial` 参数的默认值 `location` 。 否则，若要获取你的帐户所在的 Azure 区域的代码名称，并且应将你的调用路由到，你可以使用 Azure 门户或运行 [Azure CLI](/cli/azure) 命令。

### <a name="azure-portal"></a>Azure 门户

1. 登录到[视频索引器](https://www.videoindexer.ai/)网站。
1. 选择页面右上角的 " **用户帐户** "。
1. 在右上角查找帐户的位置。  

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/location/location1.png" alt-text="位置":::
    
###  <a name="cli-command"></a>CLI 命令

```azurecli-interactive
az account list-locations
```

运行上面所示的命令行后，会得到所有 Azure 区域的列表。 导航到具有你要查找的 *displayName* 的 Azure 区域，并将其 *name* 值用于**位置**参数。

例如，对于 Azure 区域“美国西部 2”（如下所示），你将“westus2”用于**位置**参数。

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/locations/westus2",
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
