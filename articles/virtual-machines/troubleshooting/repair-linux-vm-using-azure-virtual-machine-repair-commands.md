---
title: 使用 Azure 虚拟机 repair 命令修复 Linux VM |Microsoft Docs
description: 本文详细介绍如何使用 Azure 虚拟机修复命令将磁盘连接到另一个 Linux VM，以修复任何错误，然后重新生成原始 VM。
services: virtual-machines-linux
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 09/10/2019
ms.author: v-miegge
ms.openlocfilehash: 56e420f9641638bfa79ff077be73132b00b934ab
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71132086"
---
# <a name="repair-a-linux-vm-by-using-the-azure-virtual-machine-repair-commands"></a>使用 Azure 虚拟机 repair 命令修复 Linux VM

如果 Azure 中的 Linux 虚拟机（VM）遇到启动或磁盘错误，则可能需要在磁盘本身上进行缓解。 一个常见示例是应用程序更新失败，使 VM 无法成功启动。 本文详细介绍如何使用 Azure 虚拟机修复命令将磁盘连接到另一个 Linux VM，以修复任何错误，然后重新生成原始 VM。

> [!IMPORTANT]
> 本文中的脚本仅适用于使用[Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)的 vm。

## <a name="repair-process-overview"></a>修复过程概述

你现在可以使用 Azure 虚拟机修复命令来更改 VM 的 OS 磁盘，并且不再需要删除和重新创建 VM。

请按照以下步骤解决 VM 问题：

1. 启动 Azure Cloud Shell
2. 运行 az extension add/update
3. 运行 az vm repair create
4. 执行缓解步骤
5. 运行 az vm repair restore

有关其他文档和说明，请参阅[az vm repair](https://docs.microsoft.com/cli/azure/ext/vm-repair/vm/repair)。

## <a name="repair-process-example"></a>修复过程示例

1. 启动 Azure Cloud Shell

   Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。 它包括预安装并配置为与帐户一起使用的常用 Azure 工具。

   若要打开 Cloud Shell，请选择代码块右上角的 "**试用**"。 也可以在单独的浏览器标签页中通过转到 [https://shell.azure.com](https://shell.azure.com) 打开 Cloud Shell。

   选择 "**复制**" 以复制代码块，然后将代码粘贴到 Cloud Shell 中，然后选择 " **Enter** " 以运行该代码。

   如果希望在本地安装并使用 CLI，则本快速入门需要 Azure CLI version 2.0.30 或更高版本。 运行 ``az --version`` 即可查找版本。 如果需要安装或升级 Azure CLI，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

2. 如果这是你首次使用这些`az vm repair`命令，请添加 vm 修复 CLI 扩展。

   ```azurecli-interactive
   az extension add -n vm-repair
   ```

   如果你以前使用过这些`az vm repair`命令，请将任何更新应用于 vm 修复扩展。

   ```azurecli-interactive
   az extension update -n vm-repair
   ```

3. 运行 `az vm repair create`。 此命令将为非功能性 VM 创建 OS 磁盘的副本，创建修复 VM，并附加该磁盘。

   ```azurecli-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password password!234 --verbose
   ```

4. 在创建的修复 VM 上执行任何所需的缓解步骤，然后继续执行步骤5。

5. 运行 `az vm repair restore`。 此命令会将修复的 OS 磁盘与 VM 的原始 OS 磁盘交换。

   ```azurecli-interactive
   az vm repair restore -g MyResourceGroup -n MyVM --verbose
   ```

## <a name="verify-and-enable-boot-diagnostics"></a>验证和启用启动诊断

以下示例在名为 ``myResourceGroup`` 的资源组中名为 ``myVMDeployed`` 的 VM 上启用诊断扩展：

Azure CLI

```azurecli-interactive
az vm boot-diagnostics enable --name myVMDeployed --resource-group myResourceGroup --storage https://mystor.blob.core.windows.net/
```

## <a name="next-steps"></a>后续步骤

* 如果在连接到 VM 时遇到问题，请参阅[对 Azure 虚拟机的 RDP 连接进行故障排除](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-rdp-connection)。
* 有关访问 VM 上运行的应用程序的问题，请参阅[排查 Azure 中虚拟机上的应用程序连接问题](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-app-connection)。
* 有关资源组的详细信息，请参阅 [Azure 资源管理器概述](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。
