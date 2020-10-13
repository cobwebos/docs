---
title: 使用 Azure 虚拟机修复命令修复 Linux VM | Microsoft Docs
description: 本文详细介绍如何使用 Azure 虚拟机修复命令将磁盘连接到另一个 Linux VM 来修复所有错误，然后重新生成原始 VM。
services: virtual-machines-linux
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 09/10/2019
ms.author: v-miegge
ms.openlocfilehash: bfd3b2351a280f423ba0ef0b15318449554b5e3b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91595929"
---
# <a name="repair-a-linux-vm-by-using-the-azure-virtual-machine-repair-commands"></a>使用 Azure 虚拟机修复命令修复 Linux VM

如果 Linux 虚拟机 (VM) 在 Azure 中遇到启动或磁盘错误，可能需要对磁盘本身执行缓解操作。 一个常见示例是应用程序更新失败，使 VM 无法成功启动。 本文详细介绍如何使用 Azure 虚拟机修复命令将磁盘连接到另一个 Linux VM 来修复所有错误，然后重新生成原始 VM。

> [!IMPORTANT]
> * 本文中的脚本仅适用于使用 [Azure 资源管理器](../../azure-resource-manager/management/overview.md)的 VM。
> * 运行脚本需要 VM 的出站连接（端口 443）。
> * 一次只能运行一个脚本。
> * 无法取消正在运行的脚本。
> * 脚本最多可以运行 90 分钟，之后它将超时。
> * 对于使用 Azure 磁盘加密的 VM，仅支持通过单次传递加密（带有或不带 KEK）加密的托管磁盘。

## <a name="repair-process-overview"></a>修复过程概述

现在可以使用 Azure 虚拟机修复命令更改 VM 的 OS 磁盘，而不再需要删除并重新创建 VM。

请按照下列步骤排查 VM 问题：

1. 启动 Azure Cloud Shell
2. 运行 az extension add/update
3. 运行 az vm repair create
4. 运行 az vm repair run 或执行缓解步骤。
5. 运行 az vm repair restore

有关其他文档和说明，请参阅 [az vm repair](/cli/azure/ext/vm-repair/vm/repair)。

## <a name="repair-process-example"></a>修复过程示例

1. 启动 Azure Cloud Shell

   Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。

   若要打开 Cloud Shell，请从代码块的右上角选择“试一试”。 也可以在单独的浏览器标签页中通过转到 [https://shell.azure.com](https://shell.azure.com) 打开 Cloud Shell。

   选择“复制”以复制代码块，然后将其粘贴到 Cloud Shell 中，选择 Enter 来运行它。 

   如果希望在本地安装并使用 CLI，则本快速入门需要 Azure CLI version 2.0.30 或更高版本。 运行 ``az --version`` 即可查找版本。 如果需要安装或升级 Azure CLI，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。
   
   如果需要使用与当前登录到 Azure 门户的帐户不同的帐户登录 Cloud Shell，可以使用 ``az login`` [az login reference](/cli/azure/reference-index?view=azure-cli-latest#az-login&preserve-view=true)。  若要在与你的帐户关联的订阅之间切换，可使用 ``az account set --subscription`` [az account set reference](/cli/azure/account?view=azure-cli-latest#az-account-set&preserve-view=true)。

2. 如果是首次使用 `az vm repair` 命令，请添加 vm-repair CLI 扩展。

   ```azurecli-interactive
   az extension add -n vm-repair
   ```

   如果以前使用过 `az vm repair` 命令，请将任何更新应用于 vm-repair 扩展。

   ```azurecli-interactive
   az extension update -n vm-repair
   ```

3. 运行 `az vm repair create`。 此命令将为非功能性 VM 创建 OS 磁盘的副本，在新资源组中创建修复 VM，并附加 OS 磁盘副本。  修复 VM 的大小和区域将与指定的非功能性 VM 相同。 所有步骤中使用的资源组和 VM 名称都将用于非功能性 VM。 如果 VM 使用的是 Azure 磁盘加密，该命令将尝试解锁已加密的磁盘，以便在附加到修复 VM 时可对其进行访问。

   ```azurecli-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password password!234 --verbose
   ```

4. 运行 `az vm repair run`。 此命令将通过修复 VM 在附加的磁盘上运行指定的修复脚本。 如果你使用的故障排除指南指定了运行 id，请在此处使用它，否则可以使用 az vm repair list，查看可用的修复脚本。 此处使用的资源组和 VM 名称适用于第 3 步中使用的非功能性 VM。 有关修复脚本的其他信息可以在 [修复脚本库](https://github.com/Azure/repair-script-library)中找到。

   ```azurecli-interactive
   az vm repair run -g MyResourceGroup -n MyVM --run-on-repair --run-id lin-hello-world --verbose
   ```

   或者，你可以使用修复 VM 来执行任何所需的手动缓解步骤，然后继续执行步骤5。

5. 运行 `az vm repair restore`。 此命令会将已修复的 OS 磁盘与 VM 的原始 OS 磁盘交换。 此处使用的资源组和 VM 名称适用于第 3 步中使用的非功能性 VM。

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

* 如果在连接到 VM 时遇到问题，请参阅[对 Azure 虚拟机的 RDP 连接进行故障排除](./troubleshoot-rdp-connection.md)。
* 如果在访问 VM 上运行的应用程序时遇到问题，请参阅[排查 Azure 中虚拟机上的应用程序连接问题](./troubleshoot-app-connection.md)。
* 有关使用 Resource Manager 的详细信息，请参阅 [Azure Resource Manager 概述](../../azure-resource-manager/management/overview.md)。
