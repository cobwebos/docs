---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: d567a4f7d9b9429887d6396cb2b03dfc34c6ac93
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88602389"
---
<!-- Create a resource group -->

使用以下命令创建资源组。 选择用于存储媒体服务帐户的媒体和元数据记录的地理区域。 此区域用于处理和流式传输媒体。

```azurecli
az group create --name amsResourceGroup --location westus2
```
