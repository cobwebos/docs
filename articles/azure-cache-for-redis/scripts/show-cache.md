---
title: 获取 Azure Cache for Redis 的详细信息 - Azure CLI
description: 此 Azure CLI 代码示例演示如何检索 Azure Cache for Redis 实例的详细信息，包括其预配状态。
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8874a17b40b60a194811630b2f93d2be9d7faf7f
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87494700"
---
# <a name="get-details-of-an-azure-cache-for-redis"></a>获取 Azure Redis 缓存的详细信息

在此方案中，你将了解如何检索 Azure Redis 缓存实例的详细信息，包括其预配状态。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Cache for Redis")]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令检索 Azure Redis 缓存实例的详细信息。 表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis) | 检索 Azure Redis 缓存实例的详细信息。 |


## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure)。

可以在 [Azure Redis 缓存文档](../cli-samples.md)中找到其他 Azure Redis 缓存 CLI 脚本示例。
