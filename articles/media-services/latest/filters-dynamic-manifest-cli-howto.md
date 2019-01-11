---
title: 通过 CLI 使用 Azure 媒体服务创建筛选器 | Microsoft Docs
description: 本主题展示如何通过 CLI 使用媒体服务创建筛选器。
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
ms.date: 11/26/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 471277433f0fc9a54a28baa158f1e20f1efb613f
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2019
ms.locfileid: "54000507"
---
# <a name="creating-filters-with-cli"></a>使用 CLI 创建筛选器 

将内容传送到客户（直播流事件或点播视频）时，客户端所需的灵活性可能比默认资产的清单文件中描述的灵活性更高。 使用 Azure 媒体服务可为内容定义帐户筛选器和资产筛选器。 有关详细信息，请参阅[筛选器和动态清单](filters-dynamic-manifest-overview.md)。

本主题说明如何为点播视频资产配置筛选器，并使用适用于媒体服务 v3 的 CLI 创建[帐户筛选器](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)和[资产筛选器](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)。 

## <a name="prerequisites"></a>先决条件 

- 在本地安装并使用 CLI，本文要求使用 Azure CLI 2.0 或更高版本。 运行 `az --version` 即可确定你拥有的版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 

    目前，并非所有[媒体服务 v3 CLI](https://aka.ms/ams-v3-cli-ref) 命令都可在 Azure Cloud Shell 中运行。 建议在本地使用 CLI。
- [创建媒体服务帐户](create-account-cli-how-to.md)。 请务必记住资源组名称和媒体服务帐户名称。 
- 查看[筛选器和动态清单](filters-dynamic-manifest-overview.md)。

## <a name="define-a-filter"></a>定义筛选器 

以下示例定义添加到最终清单的曲目择条件。 此筛选器包括属于 EC-3 的任何音频曲目和比特率在 0-1000000 范围内的任何视频曲目。

REST 中定义的筛选器包括“属性”包装器 JSON 对象。  

```json
[
    {
        "trackSelections": [
            {
                "property": "Type",
                "value": "Audio",
                "operation": "Equal"
            },
            {
                "property": "FourCC",
                "value": "EC-3",
                "operation": "NotEqual"
            }
        ]
    },
    {
        "trackSelections": [
            {
                "property": "Type",
                "value": "Video",
                "operation": "Equal"
            },
            {
                "property": "Bitrate",
                "value": "0-1000000",
                "operation": "Equal"
            }
        ]
    }
]
```

## <a name="create-account-filters"></a>创建帐户筛选器

以下 [az ams account-filter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) 命令创建帐户筛选器，其中包含[之前定义的](#define-a-filter)筛选器曲目选项。 

该命令允许你传递一个可选 `--tracks` 参数，该参数包含表示曲目选择的 JSON。  使用 @{file} 从文件加载 JSON。 如果在本地使用 Azure CLI，请指定整个文件路径：


```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @c:\tracks.json
```

如果使用的是 Azure Cloud Shell，请将文件上传到 Cloud Shell（在 shell 窗口顶部找到“上传/下载文件”按钮）。 然后，可以按如下所示引用该文件：

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

此外，请参阅[筛选器的 JSON 示例](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter)。

## <a name="create-asset-filters"></a>创建资产筛选器

以下 [az ams asset-filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest) 命令创建资产筛选器，其中包含[之前定义的](#define-a-filter)筛选器曲目选项。 

> [!TIP]
> 请参阅上一部分中有关指定文件名位置的信息。

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

此外，请参阅[筛选器的 JSON 示例](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter)。

## <a name="next-step"></a>后续步骤

[流视频](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>另请参阅

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
