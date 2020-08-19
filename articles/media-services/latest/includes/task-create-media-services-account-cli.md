---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: c902ea001d18504dabffc8431acafe33822792e1
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88539095"
---
<!--Create a media services account -->

以下 Azure CLI 命令创建新的媒体服务帐户。 可以替换以下值：`amsaccount``storageaccountforams`（必须与提供给存储帐户的值相符）和 `amsResourceGroup`（必须与提供给资源组的值相符）。

```azurecli
az ams account create --name amsaccount \
   -g amsResourceGroup --storage-account storageaccountforams \
   -l westus2 
```