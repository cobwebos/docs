---
title: 通过群集创建高级 Azure Cache for Redis - Azure CLI
description: 此 Azure CLI 代码示例演示如何创建启用了群集的 6 GB 高级层 Azure Cache for Redis 和两个分片。
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: 68203fe2e054f32ce5764fe4f1b07013b0806104
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "75411149"
---
# <a name="create-a-premium-azure-cache-for-redis-with-clustering"></a>通过群集创建高级 Azure Redis 缓存

在此方案中，了解如何通过启用的群集与两个分片创建 6 GB 高级层 Azure Redis 缓存。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组并通过启用群集创建高级层 Azure Redis 缓存。 表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | 创建用于存储所有资源的资源组。 |
| [az redis create](https://docs.microsoft.com/cli/azure/redis) | 创建 Azure Redis 缓存实例。 |


## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure)。

可以在 [Azure Redis 缓存文档](../cli-samples.md)中找到其他 Azure Redis 缓存 CLI 脚本示例。
