---
title: Azure 串行控制台电源选项 |Microsoft Docs
description: Azure 串行控制台内可用的 VM 电源选项
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/6/2019
ms.author: alsin
ms.openlocfilehash: f17b96ad880742cf1232b074e4398f3b1d15e5ba
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129570"
---
# <a name="power-options-available-from-the-azure-serial-console"></a>可从 Azure 串行控制台获得的电源选项

Azure 串行控制台为 VM 或虚拟机规模集上的电源管理提供了几个强大的工具。 这些电源管理选项可能令人困惑, 因此本文档概述了每个工具及其预期用例。

串行控制台功能 | 描述 | 用例
:----------------------|:------------|:---------
重启 VM | 虚拟机或虚拟机规模集实例的正常重新启动。 此操作与调用 "概述" 页中提供的重新启动功能相同。 | 在大多数情况下, 此选项应是尝试重新启动 VM 的第一个工具。 串行控制台连接将会经历短暂的中断, 并且会在 VM 重新启动后立即自动恢复。
重置 VM | Azure 平台的 VM 或虚拟机规模集的强制性动力周期。 | 此选项用于立即重新启动操作系统, 而不考虑其当前状态。 由于此操作不正常, 导致数据丢失或损坏的风险。 串行控制台连接不会中断, 这可能会在启动时 (例如, 在 Linux VM 或 Windows VM 的安全模式下进入 GRUB) 发送命令时非常有用。
SysRq (b) | 强制重新启动来宾的系统请求。 | 此功能仅适用于 Linux 操作系统, 并要求在操作系统中[启用 SysRq](./serial-console-nmi-sysrq.md#system-request-sysrq) 。 如果为 SysRq 正确配置了操作系统, 则此命令将导致操作系统重启。
NMI (不可屏蔽中断) | 将传递给操作系统的中断命令 | 此操作适用于[Windows](./serial-console-windows.md#use-the-serial-console-for-nmi-calls)和[Linux](./serial-console-nmi-sysrq.md#non-maskable-interrupt-nmi) vm, 并且需要启用 NMI。 发送 NMI 通常会导致操作系统崩溃。 你可以将操作系统配置为创建转储文件, 然后在收到 NMI 后重启, 这在低级别调试中可能非常有用。

## <a name="next-steps"></a>后续步骤
* 详细了解适用于[Linux vm 的 Azure 串行控制台](./serial-console-linux.md)
* 详细了解适用于[Windows vm 的 Azure 串行控制台](./serial-console-windows.md)