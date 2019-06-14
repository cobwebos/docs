---
title: include 文件
description: include 文件
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 03/28/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: e3337d0a0159ef5e57ea3bbaba1c6cc2ac8489ff
ms.sourcegitcommit: f9448a4d87226362a02b14d88290ad6b1aea9d82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2019
ms.locfileid: "66808126"
---
### <a name="retrieve-host-name-ports-and-access-keys-using-azure-cli"></a>使用 Azure CLI 检索主机名、端口和访问密钥

若要检索主机名和端口使用 Azure CLI 可以调用[az redis show](https://docs.microsoft.com/cli/azure/redis#az_redis_show)，并检索的密钥，可调用[az redis 列出密钥](https://docs.microsoft.com/cli/azure/redis#az_redis_list_keys)。 以下脚本调用这两个命令，并将主机名、 端口和密钥向控制台回显。

```azurecli
# Retrieve the hostname, ports, and keys for contosoCache located in contosoGroup

# Retrieve the hostname and ports for an Azure Cache for Redis instance
redis=($(az redis show --name contosoCache --resource-group contosoGroup --query [hostName,enableNonSslPort,port,sslPort] --output tsv))

# Retrieve the keys for an Azure Cache for Redis instance
keys=($(az redis list-keys --name contosoCache --resource-group contosoGroup --query [primaryKey,secondaryKey] --output tsv))

# Display the retrieved hostname, keys, and ports
echo "Hostname:" ${redis[0]}
echo "Non SSL Port:" ${redis[2]}
echo "Non SSL Port Enabled:" ${redis[1]}
echo "SSL Port:" ${redis[3]}
echo "Primary Key:" ${keys[0]}
echo "Secondary Key:" ${keys[1]}
```

有关此脚本的详细信息，请参阅[适用于 Redis Azure 缓存获取主机名、 端口和密钥](../articles/azure-cache-for-redis/scripts/cache-keys-ports.md)。 Azure CLI 的详细信息请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)并[开始使用 Azure CLI](/cli/azure/get-started-with-azure-cli)。
