---
title: "Azure CLI 脚本示例 - 获取 Azure Redis 缓存的主机名、端口和密钥 | Microsoft Docs"
description: "Azure CLI 脚本示例 - 获取 Azure Redis 缓存实例的主机名、端口和密钥"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
tags: azure-service-management
ms.assetid: 761eb24e-2ba7-418d-8fc3-431153e69a90
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: sdanie
ms.openlocfilehash: aee24e5c478c4453655952cc626d7d6c857e7962
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="get-the-hostname-ports-and-keys-for-azure-redis-cache"></a>获取 Azure Redis 缓存的主机名、端口和密钥

在此方案中，将学习如何检索用于连接到 Azure Redis 缓存实例的主机名、端口和密钥。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Redis Cache")]


## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令检索 Azure Redis 缓存实例的主机名、密钥和端口。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis#az_redis_show) | 检索 Azure Redis 缓存实例的详细信息。 |
| [az redis list-keys](https://docs.microsoft.com/cli/azure/redis#az_redis_list_keys) | 检索 Azure Redis 缓存实例的访问密钥。 |


## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure Redis 缓存文档](../cli-samples.md)中找到其他 Azure Redis 缓存 CLI 脚本示例。