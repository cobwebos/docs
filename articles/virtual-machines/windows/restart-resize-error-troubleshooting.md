---
title: "Azure 中的 VM 重新启动或大小调整问题 | Microsoft 文档"
description: "排查在 Azure 中重新启动或调整现有 Windows 虚拟机时遇到的 Resource Manager 部署问题"
services: virtual-machines-windows, azure-resource-manager
documentationcenter: 
author: Deland-Han
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: 0756b52d-4f5a-4503-ae45-c00a6a2edcdf
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.workload: required
ms.date: 11/03/2017
ms.author: delhan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 482a194535cba8715b70ecff4679691515b206a9
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2018
---
# <a name="troubleshoot-deployment-issues-with-restarting-or-resizing-an-existing-windows-vm-in-azure"></a>排查在 Azure 中重新启动现有 Windows VM 或调整其大小时遇到的部署问题
尝试启动已停止的 Azure 虚拟机 (VM)，或调整现有 Azure VM 的大小时，经常遇到的错误是分配失败。 当群集或区域没有可用的资源或无法支持所请求的 VM 大小时，将发生此错误。

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="collect-activity-logs"></a>收集活动日志
若要开始故障排除，请收集活动日志，以识别与问题相关的错误。 以下链接包含有关过程的详细信息：

[查看部署操作](../../azure-resource-manager/resource-manager-deployment-operations.md)

[通过查看活动日志管理 Azure 资源](../../resource-group-audit.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>问题：启动已停止的 VM 时发生错误
尝试启动已停止的 VM，但出现分配失败。

### <a name="cause"></a>原因
必须在托管云服务的原始群集上尝试发出启动已停止 VM 的请求。 但是，群集没有足够的空间可完成该请求。

### <a name="resolution"></a>解决方法
* 停止可用性集中的所有 VM 并重新启动每个 VM。
  
  1. 单击“资源组” > *资源组* > “资源” > *可用性集* > “虚拟机” > *虚拟机* > “停止”。
  2. 所有 VM 停止后，选择每个已停止的 VM 并单击“启动”。
* 稍后重试重新启动请求。

## <a name="issue-error-when-resizing-an-existing-vm"></a>问题：调整现有 VM 的大小时发生错误
尝试调整现有 VM 的大小，但出现分配失败。

### <a name="cause"></a>原因
必须在托管云服务的原始群集上尝试发出调整 VM 大小的请求。 但是，群集不支持请求的 VM 大小。

### <a name="resolution"></a>解决方法
* 以更小的 VM 大小重试请求。
* 如果无法更改请求的 VM 大小：
  
  1. 停止可用性集中的所有 VM。
     
     * 单击“资源组” > *资源组* > “资源” > *可用性集* > “虚拟机” > *虚拟机* > “停止”。
  2. 所有 VM 停止后，将所需的 VM 调整到更大的大小。
  3. 选择已调整大小的 VM，单击“启动”，并启动每个已停止的 VM。

## <a name="next-steps"></a>后续步骤
如果在 Azure 中创建新的 Windows VM 时遇到问题，请参阅[排查在 Azure 中新建 Windows 虚拟机时遇到的部署问题](troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

