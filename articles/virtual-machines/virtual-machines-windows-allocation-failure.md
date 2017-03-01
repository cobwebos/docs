---
title: "排查 Windows VM 分配失败 | Microsoft Docs"
description: "排查在 Azure 中创建、重新启动 Windows VM 或调整其大小时发生的分配失败"
services: virtual-machines-windows, azure-resource-manager
documentationcenter: 
author: JiangChen79
manager: felixwu
editor: 
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: bb939e23-77fc-4948-96f7-5037761c30e8
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/02/2016
ms.author: cjiang
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 7c83071775cd4c237dfcdb77dc89a6d5c4df4aac


---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-windows-vms-in-azure"></a>排查在 Azure 中创建、重新启动 Windows VM 或调整其大小时发生的分配失败
创建 VM、重新启动已停止（解除分配）的 VM 和重设 VM 大小时，Microsoft Azure 会为你的订阅分配计算资源。 执行这些操作时，即使尚未达到 Azure 订阅限制，也可能偶尔发生错误。 本文说明一些常见分配故障的原因，并建议可能的补救方法。 规划服务的部署时，本信息可能也有用。 还可以[排查在 Azure 中创建、重新启动 Linux VM 或调整其大小时发生的分配失败](virtual-machines-linux-allocation-failure.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

[!INCLUDE [virtual-machines-common-allocation-failure](../../includes/virtual-machines-common-allocation-failure.md)]




<!--HONumber=Nov16_HO3-->


