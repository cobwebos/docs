---
title: "Azure CLI 脚本示例 - 使用 DSC 创建具有 IIS 的 Windows Server 2016 VM | Microsoft 文档"
description: "Azure CLI 脚本示例 - 使用 DSC 创建具有 IIS 的 Windows Server 2016 VM"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: rickstercdn
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-machines-Windows
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: ffcca64e078840050f38b68cd23036dbbbfaee0a
ms.lasthandoff: 04/06/2017

---

# <a name="create-a-vm-with-iis-using-dsc"></a>使用 DSC 创建具有 IIS 的 VM

此脚本创建一个虚拟机，然后使用 Azure 虚拟机 DSC 自定义脚本扩展安装和配置 IIS。 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurecli[主要](../../../cli_scripts/virtual-machine/create-windows-iis-using-dsc/create-windows-iis-using-dsc.sh "快速创建 VM")]

## <a name="clean-up-deployment"></a>清理部署 

运行以下命令来删除资源组、VM 和所有相关资源。

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、虚拟机和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 创建用于存储所有资源的资源组。 |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | 创建虚拟机并将其连接到网卡、虚拟网络、子网和 NSG。 此命令还指定要使用的虚拟机映像和管理凭据。  |
| [az vm extension set](https://docs.microsoft.com/cli/azure/vm#create) | 将自定义脚本扩展添加到虚拟机，此扩展将调用脚本来安装 IIS。 |
| [az vm open-port](https://docs.microsoft.com/cli/azure/vm#open-port) | 创建网络安全组规则，以允许入站流量。 在此示例中，将为 HTTP 流量打开端口 80。 |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | 删除资源组，包括所有嵌套的资源。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure Windows VM 文档](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他虚拟机 CLI 脚本示例。
