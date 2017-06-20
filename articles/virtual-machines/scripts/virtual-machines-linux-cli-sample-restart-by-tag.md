---
title: "Azure CLI 脚本示例 — 重新启动 VM | Microsoft Docs"
description: "Azure CLI 脚本示例 — 按标记和 ID 重新启动 VM"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2017
ms.author: allclark
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: fec7a2738c3b8e74ac335f62189f3d9b1dd346ab
ms.contentlocale: zh-cn
ms.lasthandoff: 05/15/2017

---

# <a name="restart-vms"></a>重新启动 VM

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

此示例展示了用来获取一些 VM 并重新启动它们的几种方法。

第一种方法重新启动资源组中的所有 VM。

```bash
az vm restart --ids $(az vm list --resource-group myResourceGroup --query "[].id" -o tsv)
```

第二种方法使用 `az resouce list` 获取带标记的 VM，并筛选出是 VM 的资源，然后重新启动那些 VM。

```bash
az vm restart --ids $(az resource list --tag "restart-tag" --query "[?type=='Microsoft.Compute/virtualMachines'].id" -o tsv)
```

此示例在 Bash shell 中正常工作。 有关在 Windows 客户端上运行 Azure CLI 脚本的选项，请参阅[在 Windows 中运行 Azure CLI](../windows/cli-options.md)。


## <a name="sample-script"></a>示例脚本

此示例具有三个脚本。
第一个脚本用来预配虚拟机。
它使用了 no-wait 选项，因此，命令不会等待每个 VM 完成部署便会返回。
第二个脚本等到 VM 完全部署后才会返回。
第三个脚本重新启动已部署的所有 VM，然后仅重新启动带标记的 VM。

### <a name="provision-the-vms"></a>预配 VM

此脚本创建一个资源组，然后它创建三个 VM 并重新启动。
其中的两个带有标记。

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/provision.sh "预配 VM")]

### <a name="wait"></a>等待

此脚本每隔 20 秒检查一次预配状态，直到三个 VM 全部完成预配，或者其中一个未能完成预配。

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/wait.sh "等待 VM 完成预配")]

### <a name="restart-the-vms"></a>重新启动 VM

此脚本重新启动资源组中的所有 VM，然后它仅重新启动带标记的 VM。

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/restart.sh "按标记重新启动 VM")]

## <a name="clean-up-deployment"></a>清理部署 

运行脚本示例后，可以使用以下命令删除资源组、VM 以及所有相关的资源。

```azurecli-interactive 
az group delete -n myResourceGroup --no-wait --yes
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、虚拟机、可用性集、负载均衡器和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 创建用于存储所有资源的资源组。 |
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set#create) | 创建虚拟机。  |
| [az vm list](https://docs.microsoft.com/cli/azure/vm#list) | 与 `--query` 一起使用，用来确保在重新启动 VM 之前已对其进行了预配，然后获取这些 VM 的 ID 以将其重新启动。 |
| [az resource list](https://docs.microsoft.com/cli/azure/vm#list) | 与 `--query` 一起使用来获取使用该标记的 VM 的 ID。 |
| [az vm restart](https://docs.microsoft.com/cli/azure/vm#list) | 重新启动 VM。 |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure Linux VM 文档](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中找到其他虚拟机 CLI 脚本示例。

