---
title: "Azure CLI 脚本示例 - 删除 Azure Redis 缓存 | Microsoft Docs"
description: "Azure CLI 脚本示例 - 删除 Azure Redis 缓存"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
tags: azure-service-management
ms.assetid: 7beded7a-d2c9-43a6-b3b4-b8079c11de4a
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: sdanie
ms.translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: f959823b3a7c5b0262f693ecad1e6efc4eec4f35
ms.contentlocale: zh-cn
ms.lasthandoff: 04/15/2017

---

# <a name="delete-an-azure-redis-cache"></a>删除 Azure Redis 缓存

在本方案中，了解如何删除 Azure Redis 缓存。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurecli[主要](../../../cli_scripts/redis-cache/delete-cache/delete-cache.sh "Azure Redis 缓存")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令删除 Azure Redis 缓存实例。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az redis 删除](https://docs.microsoft.com/cli/azure/redis#delete) | 删除 Redis 缓存实例。 |


## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure Redis 缓存文档](../cli-samples.md)中找到其他 Azure Redis 缓存 CLI 脚本示例。
