---
title: "include 文件"
description: "include 文件"
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 8d0367b4e087da5954e9b16828655625978634fb
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2018
---
[!INCLUDE [resource group intro text](resource-group.md)]

在 Cloud Shell 中，使用 [`az group create`](/cli/azure/group?view=azure-cli-latest#az_group_create) 命令创建资源组。 以下示例在“西欧”位置创建名为“myResourceGroup”的资源组。 若要查看应用服务支持的所有位置，请运行 [`az appservice list-locations`](/cli/azure/appservice?view=azure-cli-latest#az_appservice_list_locations) 命令。

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

通常在附近的区域中创建资源组和资源。 

此命令完成后，JSON 输出会显示资源组属性。