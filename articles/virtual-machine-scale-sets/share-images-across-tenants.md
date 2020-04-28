---
title: 跨 Azure 中的租户共享库映像
description: 了解如何使用共享映像库跨 Azure 租户共享 VM 映像。
author: cynthn
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: a29999102ad8a10d8965145b31a7d804675e0e57
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "76276341"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>跨 Azure 租户共享库 VM 映像

[!INCLUDE [virtual-machines-share-images-across-tenants](../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-scale-set-using-azure-cli"></a>使用 Azure CLI 创建规模集

使用租户 1 的 appID、应用密钥以及 ID 登录到租户 1 的服务主体。 可以根据需要使用 `az account show --query "tenantId"` 获取租户 ID。

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
使用租户 2 的 appID、应用密钥以及 ID 登录到租户 2 的服务主体：

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

创建规模集。 请将示例中的信息替换为你自己的。

```azurecli-interactive
az vmss create \
  -g myResourceGroup \
  -n myScaleSet \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>后续步骤

如果遇到任何问题，可以[对共享映像库进行故障排除](troubleshooting-shared-images.md)。
