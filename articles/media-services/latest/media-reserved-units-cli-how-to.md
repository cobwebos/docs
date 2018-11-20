---
title: 缩放媒体保留单位 - Azure |Microsoft Docs
description: 本主题概述了如何使用 Azure 媒体服务调整媒体处理的规模。
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
ms.date: 11/11/2018
ms.author: juliako
ms.openlocfilehash: db1915f23c33b5cc0d504f8fcc21b9533228247f
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634390"
---
# <a name="scaling-media-processing"></a>缩放媒体处理能力

使用 Azure 媒体服务，可以通过管理媒体保留单位 (MRU) 来缩放媒体处理能力。 有关详细概述，请参阅[缩放媒体处理能力](../previous/media-services-scale-media-processing-overview.md)。 本文展示了如何使用[媒体服务 v3 CLI](https://aka.ms/ams-v3-cli-ref) 来缩放 MRU。

> [!IMPORTANT]
> 查看[此部分](#considerations)中所述的注意事项。  
> 
>

## <a name="prerequisites"></a>先决条件 

- 在本地安装并使用 CLI，本文要求使用 Azure CLI 2.0 或更高版本。 运行 `az --version` 即可确定你拥有的版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 

    目前，并非所有[媒体服务 v3 CLI](https://aka.ms/ams-v3-cli-ref) 命令都可在 Azure Cloud Shell 中运行。 建议在本地使用 CLI。

- [创建媒体服务帐户](create-account-cli-how-to.md)。

## <a name="scale-media-reserved-units-with-cli"></a>使用 CLI 缩放媒体保留单位

以下 [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) 命令使用 **count** 和 **type** 参数在“amsaccount”帐户上设置媒体保留单位。

```azurecli
az account set mru -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="considerations"></a>注意事项

- 对于由媒体服务 v3 或视频索引器触发的音频分析和视频分析作业，强烈建议为你的帐户预配 10 S3 MRU。
- 如果需要超过 10 S3 MRU 的数量，请使用 [Azure 门户](https://portal.azure.com/)打开一个支持票证。

## <a name="billing"></a>计费

将根据在你的帐户中预配的 MRU 的数量、类型和时间量向你收费。 无论是否运行任何作业，都会收费。 有关详细说明，请参阅[媒体服务定价](https://azure.microsoft.com/pricing/details/media-services/)页的“常见问题”部分。   

## <a name="next-step"></a>后续步骤

[分析视频](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>另请参阅

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
