---
title: 获取主机名、端口和密钥 - Azure Cache for Redis - Azure CLI
description: 此 Azure CLI 代码示例演示如何获取 Azure Cache for Redis 实例的主机名、端口和密钥。
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9fef834e550f144075ec9cc72cac3b11cc1e99e4
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87504205"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-cache-for-redis"></a>获取 Azure Redis 缓存的主机名、端口和密钥

在此方案中，将学习如何检索用于连接到 Azure Redis 缓存实例的主机名、端口和密钥。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Cache for Redis")]


## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令检索 Azure Redis 缓存实例的主机名、密钥和端口。 表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis) | 检索 Azure Redis 缓存实例的详细信息。 |
| [az redis list-keys](https://docs.microsoft.com/cli/azure/redis) | 检索 Azure Redis 缓存实例的访问密钥。 |


## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure)。

可以在 [Azure Redis 缓存文档](../cli-samples.md)中找到其他 Azure Redis 缓存 CLI 脚本示例。
