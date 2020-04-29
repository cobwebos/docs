---
title: 使用 Azure 虚拟机 repair 命令修复 Windows VM |Microsoft Docs
description: 本文详细介绍如何使用 Azure VM 修复命令将磁盘连接到另一个 Windows VM 来修复所有错误，然后重新生成原始 VM。
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 09/10/2019
ms.author: v-miegge
ms.openlocfilehash: 2055558ef80a641084a7cf9d299281497d282936
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80060679"
---
# <a name="repair-a-windows-vm-by-using-the-azure-virtual-machine-repair-commands"></a>使用 Azure 虚拟机修复命令修复 Windows VM

如果 Windows 虚拟机 (VM) 在 Azure 中遇到启动或磁盘错误，可能需要对磁盘本身执行缓解操作。 一个常见示例是应用程序更新失败，使 VM 无法成功启动。 本文详细介绍如何使用 Azure VM 修复命令将磁盘连接到另一个 Windows VM 来修复所有错误，然后重新生成原始 VM。

> [!IMPORTANT]
> 本文中的脚本仅适用于使用 [Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)的 VM。

## <a name="repair-process-overview"></a>修复过程概述

现在可以使用 Azure VM 修复命令更改 VM 的 OS 磁盘，而不再需要删除并重新创建 VM。

请按照下列步骤排查 VM 问题：

1. 启动 Azure Cloud Shell
2. 运行 az extension add/update。
3. 运行 az vm repair create。
4. 运行 az vm repair run。
5. 运行 az vm repair restore。

有关其他文档和说明，请参阅 [az vm repair](https://docs.microsoft.com/cli/azure/ext/vm-repair/vm/repair)。

## <a name="repair-process-example"></a>修复过程示例

> [!NOTE]
> * 运行脚本需要 VM 的出站连接（端口 443）。
> * 一次只能运行一个脚本。
> * 无法取消正在运行的脚本。
> * 脚本最多可以运行 90 分钟，之后它将超时。

1. 启动 Azure Cloud Shell

   Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。 它包括预安装并配置为与帐户一起使用的常用 Azure 工具。

   若要打开 Cloud Shell，请选择代码块右上角的 "**试用**"。 你还可以通过访问[https://shell.azure.com](https://shell.azure.com)在单独的浏览器选项卡中打开 Cloud Shell。

   选择 "**复制**" 以复制代码块，然后将代码粘贴到 Cloud Shell 中，然后选择 " **Enter** " 以运行该代码。

   如果希望在本地安装并使用 CLI，则本快速入门需要 Azure CLI version 2.0.30 或更高版本。 运行 ``az --version`` 即可查找版本。 如果需要安装或升级 Azure CLI，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

2. 如果是首次使用 `az vm repair` 命令，请添加 vm-repair CLI 扩展。

   ```azurecli-interactive
   az extension add -n vm-repair
   ```

   如果以前使用过 `az vm repair` 命令，请将任何更新应用于 vm-repair 扩展。

   ```azurecli-interactive
   az extension update -n vm-repair
   ```

3. 运行 `az vm repair create`。 此命令将为无法运行的 VM 创建 OS 磁盘的副本，创建修复 VM 并附加磁盘。

   ```azurecli-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password password!234 --verbose
   ```

4. 运行 `az vm repair run`。 此命令将通过修复 VM 在附加的磁盘上运行指定的修复脚本。

   ```azurecli-interactive
   az vm repair run  –g MyResourceGroup –n MyVM -–run-on-repair --run-id 2 --verbose
   ```

5. 运行 `az vm repair restore`。 此命令会将已修复的 OS 磁盘与 VM 的原始 OS 磁盘交换。

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

* 如果在连接到 VM 时遇到问题，请参阅[对 Azure VM 的 RDP 连接进行故障排除](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-rdp-connection)。
* 如果在访问 VM 上运行的应用程序时遇到问题，请参阅[排查 Azure 中虚拟机上的应用程序连接问题](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-app-connection)。
* 有关资源组的详细信息，请参阅 [Azure 资源管理器概述](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。
