---
title: 跨 Azure 中的租户共享库映像 |Microsoft Docs
description: 了解如何使用共享映像库跨 Azure 租户共享 VM 映像。
services: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: b63bc1089b113edaac637df0a7e55c39f3a11f1a
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904978"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>跨 Azure 租户共享库 VM 映像

共享映像库可让你使用 RBAC 共享映像。 可以使用 RBAC 在租户中共享映像，甚至可以与租户外部的个人共享映像。 有关此简单共享选项的详细信息，请参阅[共享库](/azure/virtual-machines/linux/shared-images-portal#share-the-gallery)。

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> 不能使用门户从另一个 Azure 租户中的映像部署 VM。 若要从租户之间共享的映像创建 VM，必须使用 Azure CLI 或 [Powershell](../windows/share-images-across-tenants.md)。

## <a name="create-a-vm-using-azure-cli"></a>使用 Azure CLI 创建 VM

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

创建 VM。 请将示例中的信息替换为你自己的。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>后续步骤

如果遇到任何问题，可以[对共享映像库进行故障排除](troubleshooting-shared-images.md)。