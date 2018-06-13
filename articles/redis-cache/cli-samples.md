---
title: Azure CLI Redis 缓存示例 | Microsoft Docs
description: Azure Redis 缓存的 Azure CLI 示例。
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 8d2de145-50c0-4f76-bf8f-fdf679f03698
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.date: 04/14/2017
ms.author: wesmc
ms.openlocfilehash: 4450a8f7f16e3503626c9ee0ca125fc2b1ed9052
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
ms.locfileid: "27909833"
---
# <a name="azure-cli-samples-for-azure-redis-cache"></a>Azure Redis 缓存的 Azure CLI 示例

下表包含指向使用 Azure CLI 生成的 bash 脚本的链接。

| | |
|---|---|
|**创建缓存**||
| [创建缓存](./scripts/create-cache.md) | 创建资源组和基本层 Azure Redis 缓存。 |
| [使用群集创建高级缓存](./scripts/create-premium-cache-cluster.md) | 通过启用群集来创建资源组和高级层缓存。|
| [获取缓存的详细信息](./scripts/show-cache.md) | 获取 Azure Redis 缓存实例的详细信息，包括预配状态。 |
| [获取主机名、端口和密钥](./scripts/cache-keys-ports.md) | 获取 Azure Redis 缓存实例的主机名、端口和密钥。 |
|**Web 应用和缓存**||
| [将 Web 应用连接到 Redis 缓存](./../app-service/scripts/app-service-cli-app-service-redis.md) | 创建 Azure Web 应用和 Redis 缓存，然后将 Redis 连接详细信息添加到应用设置。 |
|**删除缓存**||
| [删除缓存](./scripts/delete-cache.md) | 删除 Azure Redis 缓存实例  |
| | |

有关 Azure CLI 2.0 的详细信息，请参阅[安装 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) 和 [Azure CLI 2.0 入门](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)。