---
title: 为域名创建 DNS 区域和记录 - Azure CLI - Azure DNS
description: 此 Azure CLI 脚本示例演示如何为域名创建 DNS 区域和记录
services: dns
author: asudbring
ms.service: dns
ms.topic: sample
ms.date: 09/20/2019
ms.author: allensu
ms.openlocfilehash: 8f5dde0a35f31d2c33ab2857659efc88339c2ead
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74210402"
---
# <a name="azure-cli-script-example-create-a-dns-zone-and-record"></a>Azure CLI 脚本示例：创建 DNS 区域和记录

此 Azure CLI 脚本示例为域名创建 DNS 区域和记录。 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/dns/create-dns-zone-and-record/create-dns-zone-and-record.sh "Create DNS zone and record")]

## <a name="clean-up-deployment"></a>清理部署 

运行以下命令以删除资源组、DNS 区域和所有相关资源。

```azurecli
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、虚拟机、可用性集、负载均衡器和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az network dns zone create](/cli/azure/network/dns/zone#az-network-dns-zone-create) | 创建 Azure DNS 区域。 |
| [az network dns record-set a add-record](/cli/azure/network/dns/record-set) | 将 *A* 记录添加到 DNS 区域。 |
| [az network dns record-set list](/cli/azure/network/dns/record-set) | 列出 DNS 区域中的所有 *A* 记录集。 |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure)。

