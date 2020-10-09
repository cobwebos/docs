---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: 9f079be76d4d75f05b4a8e132ac425255eed8558
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88605977"
---
<!--Create a media services asset CLI-->

以下 Azure CLI 命令创建新的媒体服务资产。 将 `assetName`、`amsAccountName` 和 `resourceGroup` 值替换为当前正在使用的值。

```azurecli
az ams asset create -n assetName -a amsAccountName -g resourceGroup
```
