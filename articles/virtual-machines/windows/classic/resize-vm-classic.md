---
title: 调整经典部署模型中 Windows VM 的大小 - Azure | Microsoft 文档
description: 使用 Azure Powershell 调整在经典部署模型中创建的 Windows 虚拟机大小。
services: virtual-machines-windows
documentationcenter: ''
author: Drewm3
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: e3038215-001c-406e-904d-e0f4e326a4c7
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/19/2016
ms.author: drewm
ms.openlocfilehash: 6afc8125462a659d0892e7dbb53f1f71789815cc
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="resize-a-windows-vm-created-in-the-classic-deployment-model"></a>调整经典部署模型中创建的 Windows VM 大小
本文介绍使用 Azure Powershell 调整在经典部署模型中创建的 Windows VM 大小。

考虑调整 VM 大小的功能时，有两个概念控制可用于调整虚拟机大小的大小范围。 第一个概念是部署 VM 的区域。 在区域中可用的 VM 大小列表在 Azure 区域网页的“服务”选项卡下。 第二个概念是当前托管 VM 的物理硬件。 托管 VM 的物理服务器一起组合在公用物理硬件群集中。 更改 VM 大小的方法因当前托管 VM 的硬件群集是否支持所需的新 VM 大小而异。

> [!IMPORTANT] 
> Azure 提供两个不同的部署模型用于创建和处理资源：[资源管理器和经典模型](../../../resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用资源管理器模型。 还可以[调整在 Resource Manager 部署模型中创建的 VM 大小](../resize-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

## <a name="add-your-account"></a>添加帐户
必须配置 Azure PowerShell 才能处理经典 Azure 资源。 按照下面的步骤配置 Azure PowerShell 以管理经典资源。

1. 在 PowerShell 命令提示符处，键入 `Add-AzureAccount` 并单击“Enter”。 
2. 键入与 Azure 订阅相关联的电子邮件地址并单击“继续”。 
3. 键入帐户的密码。 
4. 单击“登录”。 

## <a name="resize-in-the-same-hardware-cluster"></a>在相同的硬件群集中调整大小
要将 VM 大小调整为在托管该 VM 的硬件群集中可用的大小，请执行以下步骤。

1. 运行以下 PowerShell 命令以列出在托管包含该 VM 的云服务的硬件群集中可用的 VM 大小。
   
    ```powershell
    Get-AzureService | where {$_.ServiceName -eq "<cloudServiceName>"}
    ```
2. 运行以下命令以调整 VM 大小。
   
    ```powershell
    Get-AzureVM -ServiceName <cloudServiceName> -Name <vmName> | Set-AzureVMSize -InstanceSize <newVMSize> | Update-AzureVM
    ```

## <a name="resize-on-a-new-hardware-cluster"></a>在新硬件群集中调整大小
要将 VM 大小调整为在托管该 VM 的硬件群集中不可用的大小，必须重新创建云服务和云服务中的所有 VM。 每个云服务托管在单个硬件群集中，因此云服务中的所有 VM 都必须是在硬件群集中受支持的大小。 以下步骤介绍如何通过删除并重新创建云服务调整 VM 大小。

1. 运行以下 PowerShell 命令以列出在区域中可用的 VM 大小。 
   
    ```powershell
    Get-AzureLocation | where {$_.Name -eq "<locationName>"}
    ```
2. 记下包含要调整大小的 VM 的云服务中每个 VM 的所有配置设置。 
3. 在选择保留每个 VM 的磁盘的情况下，删除云服务中的所有 VM。
4. 使用所需的 VM 大小重新创建要调整大小的 VM。
5. 使用在现在托管云服务的硬件群集中可用的 VM 大小重新创建曾在该云服务中的所有其他 VM。

可以在[此处](https://github.com/Azure/azure-vm-scripts)找到删除并使用新 VM 大小重新创建云服务的示例脚本。 

## <a name="next-steps"></a>后续步骤
* [调整在 Resource Manager 部署模型中创建的 VM 大小](../resize-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

