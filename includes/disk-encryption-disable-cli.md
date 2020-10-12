---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/19/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: ed2b13077ff5809899097254dde0fa8406e1db3b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85601372"
---
可以使用 Azure PowerShell、Azure CLI 或资源管理器模板禁用加密。 

>[!IMPORTANT]
>在 Linux VM 上，仅支持对数据卷禁用 Azure 磁盘加密。 如果 OS 卷已加密，则不支持对数据卷或 OS 卷禁用加密。  

- **使用 Azure PowerShell 禁用磁盘加密：** 若要禁用加密，请使用 [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet。 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [-VolumeType DATA]
     ```

- **使用 Azure CLI 禁用加密：** 若要禁用加密，请使用 [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) 命令。 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```
- **使用资源管理器模板禁用加密：** 使用[在正在运行的 Linux VM 上禁用加密](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad)模板禁用加密。
     1. 单击 **“部署到 Azure”** 。
     2. 选择订阅、资源组、位置、VM、法律条款和协议。
