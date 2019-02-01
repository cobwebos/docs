---
title: 使用 CLI 缩放媒体保留单位 - Azure | Microsoft Docs
description: 本主题演示如何使用 CLI 通过 Azure 媒体服务来缩放媒体处理能力。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: b40ab6bcc2f718eda85ff64d69a6689e12d60ab8
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2019
ms.locfileid: "55094829"
---
# <a name="scaling-media-processing"></a>缩放媒体处理能力

使用 Azure 媒体服务，可以通过管理媒体保留单位 (MRU) 来缩放媒体处理能力。 有关详细概述，请参阅[缩放媒体处理能力](../previous/media-services-scale-media-processing-overview.md)。 

本文展示了如何使用[媒体服务 v3 CLI](https://aka.ms/ams-v3-cli-ref) 来缩放 MRU。

> [!NOTE]
> 对于由媒体服务 v3 或视频索引器触发的音频分析和视频分析作业，强烈建议为你的帐户预配 10 S3 MRU。 <br/>如果需要超过 10 S3 MRU 的数量，请使用 [Azure 门户](https://portal.azure.com/)打开一个支持票证。

## <a name="prerequisites"></a>先决条件 

[创建媒体服务帐户](create-account-cli-how-to.md)。

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="scale-media-reserved-units-with-cli"></a>使用 CLI 缩放媒体保留单位

运行 `mru` 命令。

以下 [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) 命令使用 **count** 和 **type** 参数在“amsaccount”帐户上设置媒体保留单位。

```azurecli
az account set mru -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>计费

将根据在你的帐户中预配的 MRU 的数量、类型和时间量向你收费。 无论是否运行任何作业，都会收费。 有关详细说明，请参阅[媒体服务定价](https://azure.microsoft.com/pricing/details/media-services/)页的“常见问题”部分。   

## <a name="next-step"></a>后续步骤

[分析视频](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>另请参阅

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
