---
title: "在 Azure 中重新部署 Linux 虚拟机 | Microsoft Docs"
description: "在 Azure 中如何通过重新部署 Linux 虚拟机来缓解 SSH 连接问题。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/16/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 13ae22245d105b32b9fc50d7dbc8a6d50ad4560a
ms.openlocfilehash: 85fcc919b97d4cc06f89fc1ea5dc701ff61c2b27


---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>将 Linux 虚拟机重新部署到新的 Azure 节点
如果在对 SSH 或应用程序对 Azure 中 Linux 虚拟机 (VM) 的访问进行故障排除时遇到困难，重新部署 VM 可能会有帮助。 重新部署 VM 时，将 VM 移到 Azure 基础结构中的新节点，然后重新打开它，同时保留所有配置选项和关联的资源。 本文介绍如何使用 Azure CLI 或 Azure 门户重新部署 VM。

> [!NOTE]
> 重新部署 VM 后，临时磁盘将丢失，并将更新与虚拟网络接口关联的动态 IP 地址。 

可使用以下选项之一重新部署 VM。 只需选择一个选项来重新部署 VM：

- [Azure CLI 1.0](#azure-cli-10)
- [Azure CLI 2.0（预览版）](#azure-cli-20-preview)
- [Azure 门户](#using-azure-portal)


## <a name="azure-cli-10"></a>Azure CLI 1.0
安装[最新的 Azure CLI 1.0](../xplat-cli-install.md)，登录到 Azure 帐户，并确保处于 Resource Manager 模式 (`azure config mode arm`)。

以下示例重新部署 `myResourceGroup` 资源组中名为 `myVM` 的 VM：

```azurecli
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

## <a name="azure-cli-20-preview"></a>Azure CLI 2.0（预览版）
安装最新的 [Azure CLI 2.0（预览版）](/cli/azure/install-az-cli2)并使用 [az login](/cli/azure/#login) 登录到 Azure 帐户。

使用 [az vm redeploy](/cli/azure/vm#redeploy) 重新部署 VM。 以下示例重新部署 `myResourceGroup` 资源组中名为 `myVM` 的 VM：

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>后续步骤
如果在连接 VM 时遇到问题，可以在 [troubleshooting SSH connections](virtual-machines-linux-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)（SSH 连接故障排除）或 [detailed SSH troubleshooting steps](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)（详细的 SSH 故障排除步骤）中找到特定的帮助。 如果无法访问在 VM 上运行的应用程序，还可以阅读 [application troubleshooting issues](virtual-machines-linux-troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)（应用程序故障排除问题）。




<!--HONumber=Feb17_HO3-->


