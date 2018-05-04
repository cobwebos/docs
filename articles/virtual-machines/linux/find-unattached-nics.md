---
title: 找到并删除未连接的 Azure NIC | Microsoft Docs
description: 如何通过 Azure CLI 2.0 找到并删除未连接到 VM 的 Azure NIC
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: iainfou
ms.openlocfilehash: c730866fe73305a37b37038699a7f729085a16aa
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-find-and-delete-unattached-network-interface-cards-nics-for-azure-vms"></a>如何找到并删除 Azure VM 的未连接网络接口卡 (NIC)
在 Azure 中删除虚拟机 (VM) 时，网络接口卡 (NIC) 不会默认删除。 如果在创建多个 VM 后又将其删除，则未使用过的 NIC 会继续使用内部 IP 地址租约。 创建其他 VM NIC 时，这些 NIC 可能无法在子网的地址空间中获得 IP 租约。 本文介绍如何找到并删除未连接的 NIC。

## <a name="find-and-delete-unattached-nics"></a>查找并删除未连接的 NIC

NIC 的 *virtualMachine* 属性存储该 NIC 连接到的 VM 的 ID 和资源组。 以下脚本循环访问某个订阅中的所有 NIC，检查 *virtualMachine* 属性是否为 null。 如果该属性为 null，则 NIC 未连接到 VM。

若要查看所有未连接的 NIC，强烈建议先在将 *deleteUnattachedNics* 变量设置为 *0* 的情况下运行脚本。 若要在查看列表输出后删除所有未连接的 NIC，请在将 *deleteUnattachedNics* 设置为 *1* 的情况下运行脚本。

```azurecli
# Set deleteUnattachedNics=1 if you want to delete unattached NICs
# Set deleteUnattachedNics=0 if you want to see the Id(s) of the unattached NICs
deleteUnattachedNics=0

unattachedNicsIds=$(az network nic list --query '[?virtualMachine==`null`].[id]' -o tsv)
for id in ${unattachedNicsIds[@]}
do
   if (( $deleteUnattachedNics == 1 ))
   then

       echo "Deleting unattached NIC with Id: "$id
       az network nic delete --ids $id
       echo "Deleted unattached NIC with Id: "$id
   else
       echo $id
   fi
done
```

## <a name="next-steps"></a>后续步骤

若要详细了解如何在 Azure 中创建和管理虚拟网络，请参阅[创建和管理 VM 网络](tutorial-virtual-network.md)。
