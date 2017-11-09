---
title: "VM 重新启动或大小调整问题 | Microsoft Docs"
description: "排查在 Azure 中重新启动或调整现有 Linux 虚拟机时遇到的经典部署问题"
services: virtual-machines-linux
documentationcenter: 
author: Deland-Han
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: 73f2672c-602e-4766-8948-2b180115d299
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: required
ms.date: 01/10/2017
ms.devlang: na
ms.author: delhan
ms.openlocfilehash: 7f5718a7e1ab2b14902fa61ffb3053910e584ac6
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2017
---
# <a name="troubleshoot-classic-deployment-issues-with-restarting-or-resizing-an-existing-linux-virtual-machine-in-azure"></a>排查在 Azure 中重新启动或调整现有 Linux 虚拟机时遇到的经典部署问题
> [!div class="op_single_selector"]
> * [经典](restart-resize-error-troubleshooting.md)
> * [Resource Manager](../restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> 
> 

尝试启动已停止的 Azure 虚拟机 (VM)，或调整现有 Azure VM 的大小时，经常遇到的错误是分配失败。 当群集或区域没有可用的资源或无法支持所请求的 VM 大小时，将发生此错误。

> [!IMPORTANT] 
> Azure 提供两个不同的部署模型用于创建和处理资源：[Resource Manager 和经典模型](../../../resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用 Resource Manager 模型。 有关 Resource Manager 版本，请参阅[此处](../restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>收集审核日志
若要开始故障排除，请收集审核日志，以识别与问题相关的错误。

在 Azure 门户中，单击“浏览” > “虚拟机” >  *Linux 虚拟机* > “设置” > “审核日志”。

## <a name="issue-error-when-starting-a-stopped-vm"></a>问题：启动已停止的 VM 时发生错误
尝试启动已停止的 VM，但出现分配失败。

### <a name="cause"></a>原因
必须在托管云服务的原始群集上尝试发出启动已停止 VM 的请求。 但是，群集没有足够的空间可完成该请求。

### <a name="resolution"></a>解决方法
* 创建新的云服务，并将它与区域或基于区域的虚拟网络（而不是地缘组）关联。
* 删除已停止的 VM。
* 使用磁盘在新的云服务中重新创建 VM。
* 启动重新创建的 VM。

如果在尝试创建新的云服务时收到错误，请稍后再试一次，或更改云服务的区域。

> [!IMPORTANT]
> 新的云服务将有新的名称和 VIP，因此需要为所有将信息用于现有云服务的依赖性更改该信息。
> 
> 

## <a name="issue-error-when-resizing-an-existing-vm"></a>问题：调整现有 VM 的大小时发生错误
尝试调整现有 VM 的大小，但出现分配失败。

### <a name="cause"></a>原因
必须在托管云服务的原始群集上尝试发出调整 VM 大小的请求。 但是，群集不支持请求的 VM 大小。

### <a name="resolution"></a>解决方法
减少请求的 VM 大小，并重试调整大小请求。

* 单击“浏览全部” > “虚拟机(经典)” > *虚拟机* > “设置” > “大小”。 有关详细步骤，请参阅[调整虚拟机的大小](https://msdn.microsoft.com/library/dn168976.aspx)。

如果无法减少 VM 大小，请遵循以下步骤：

* 创建新的云服务，确保它不链接到地缘组，并且未与链接到地缘组的虚拟网络相关联。
* 在其中创建更大的新 VM。

可以在同一个云服务中合并所有 VM。 如果现有的云服务和基于区域的虚拟网络相关联，可以将新云服务连接到现有虚拟网络。

如果现有的云服务未与基于区域的虚拟网络相关联，则必须删除现有云服务中的 VM，并在新云服务中从其磁盘重新创建 VM。 然而，请务必记得新的云服务将有新的名称和 VIP，因此需要为所有目前将此信息用于现有云服务的依赖性更新该信息。

## <a name="next-steps"></a>后续步骤
如果在 Azure 中创建新的 Linux VM 时遇到问题，请参阅[排查在 Azure 中新建 Linux 虚拟机时遇到的部署问题](../troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

