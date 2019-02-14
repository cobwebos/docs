---
title: Azure CLI 的 Azure Redis 缓存示例 | Microsoft Docs
description: Azure CLI 的 Azure Redis 缓存示例。
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 8d2de145-50c0-4f76-bf8f-fdf679f03698
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: azurecli
ms.topic: article
ms.date: 04/14/2017
ms.author: yegu
ms.openlocfilehash: eea74e8f1291901fea3c77b70ba3c636f40bd132
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56231783"
---
# <a name="azure-cli-samples-for-azure-cache-for-redis"></a>Azure CLI 的 Azure Redis 缓存示例

下表包含指向使用 Azure CLI 生成的 bash 脚本的链接。

| | |
|---|---|
|**创建缓存**||
| [创建缓存](./scripts/create-cache.md) | 创建资源组和基本层 Azure Redis 缓存。 |
| [使用群集创建高级缓存](./scripts/create-premium-cache-cluster.md) | 通过启用群集来创建资源组和高级层缓存。|
| [获取缓存的详细信息](./scripts/show-cache.md) | 获取 Azure Redis 缓存实例的详细信息，包括预配状态。 |
| [获取主机名、端口和密钥](./scripts/cache-keys-ports.md) | 获取 Azure Redis 缓存实例的主机名、端口和密钥。 |
|**Web 应用和缓存**||
| [将 Web 应用连接到 Azure Redis 缓存](./../app-service/scripts/cli-connect-to-redis.md) | 创建 Azure Web 应用和 Azure Redis 缓存，然后将 Redis 连接详细信息添加到应用设置。 |
|**删除缓存**||
| [删除缓存](./scripts/delete-cache.md) | 删除 Azure Redis 缓存实例  |
| | |

有关 Azure CLI 的详细信息，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 和 [Azure CLI 入门](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)。
