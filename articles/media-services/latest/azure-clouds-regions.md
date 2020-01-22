---
title: Azure 媒体服务 v3 可用的云和区域
description: 本文讨论 Azure 媒体服务 v3 可用的 Azure 云和区域。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/21/2020
ms.author: juliako
ms.openlocfilehash: 58b5b749e81aab4d8563d09cbfd139629520531c
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310555"
---
# <a name="clouds-and-regions-in-which-azure-media-services-v3-exists"></a>存在 Azure 媒体服务 v3 的云和区域

Azure 媒体服务 v3 可通过 Azure资源管理器清单在全球 Azure、Azure 政府、Azure德国、Azure 中国世纪互联中使用。 但是，并非所有媒体服务功能都可在所有 Azure 云中使用。 本文档概述了主要媒体服务 v3 组件的可用性。

## <a name="feature-availability-in-azure-clouds"></a>Azure 云中的功能可用性

| 功能|全球 Azure 区域 | Azure Government|Azure Germany|Azure 中国世纪互联|
| --- | --- | --- | --- | --- |
| [Azure EventGrid](reacting-to-media-services-events.md) | 可用 | 不可用 | 不可用 | 不可用 |
| [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) |  可用 | 不可用 | 不可用 | 不可用 |
| [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md) |  可用 | 不可用 | 不可用 | 不可用 |
| [StandardEncoderPreset](encoding-concept.md) | 可用 | 可用 | 可用 | 可用 |
| [LiveEvents](live-streaming-overview.md) | 可用 | 可用 | 可用 | 可用 |
| [StreamingEndpoints](streaming-endpoint-concept.md) | 可用 | 可用 | 可用 | 可用 |

## <a name="regionsgeographieslocations"></a>区域/地域/位置

[部署 Azure 媒体服务服务的区域](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)

### <a name="region-code-name"></a>区域代码名 

如果需要提供**位置**参数，则需要提供区域代码名称作为**位置**值。 若要获取你的帐户所在的并且应当将你的调用路由到的区域的代码名称，可以在 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 中运行以下命令行

```bash
az account list-locations
```

运行上面所示的命令行后，会得到所有 Azure 区域的列表。 导航到具有你要查找的 *displayName* 的 Azure 区域，并将其 *name* 值用于**位置**参数。

例如，对于 Azure 区域“美国西部 2”（如下所示），你将“westus2”用于**位置**参数。

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="endpoints"></a>终结点  

从不同的区域 Azure 云连接到媒体服务帐户时，应记住以下终结点。

### <a name="global-azure"></a>全球 Azure

|终结点 ||
| --- | --- | 
| Azure Resource Manager |  `https://management.azure.com/` |
| 身份验证 | `https://login.microsoftonline.com/` | 
| 令牌受众 | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

|终结点||
| --- | --- | 
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| 身份验证 | `https://login.microsoftonline.us/` | 
| 令牌受众 | `https://management.core.usgovcloudapi.net/` |

### <a name="azure-germany"></a>Azure Germany

| 终结点 ||
| --- | --- |  
| Azure Resource Manager | `https://management.cloudapi.de/` |
| 身份验证 | `https://login.microsoftonline.de/` |
| 令牌受众 | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure 中国世纪互联

|终结点||
| --- | --- | 
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| 身份验证 | `https://login.chinacloudapi.cn/` |
| 令牌受众 |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>另请参阅

* [Azure 区域](https://azure.microsoft.com/global-infrastructure/regions/)
* [Azure 地域](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Azure 位置](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>后续步骤

[媒体服务 v3 概述](media-services-overview.md)
