---
title: Azure 串行控制台电源选项 |微软文档
description: Azure 串行控制台中提供的 VM 电源选项
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
ms.openlocfilehash: 11c2549d7282bae5654ede1ac34d63a43cd0f059
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451202"
---
# <a name="power-options-available-from-the-azure-serial-console"></a>可从 Azure 串行控制台提供的电源选项

Azure 串行控制台提供了多个功能强大的工具，用于在 VM 或虚拟机规模集中进行电源管理。 这些电源管理选项可能会混淆某些选项，因此这是每个工具及其预期用例的概述。

串行控制台功能 | 描述 | 用例
:----------------------|:------------|:---------
重新启动 VM | 正常重新启动 VM 或虚拟机缩放集实例。 此操作与调用"概述"页中可用的重新启动功能相同。 | 在大多数情况下，此选项应该是尝试重新启动 VM 的第一个工具。 串行控制台连接将经历短暂的中断，并在 VM 重新启动后立即自动恢复。
重置 VM | 由 Azure 平台设置的 VM 或虚拟机扩展的强大电源周期。 | 此选项用于立即重新启动操作系统，而不管其当前状态如何。 由于此操作不正常，因此存在数据丢失或损坏的风险。 串行控制台连接没有中断，这对于在启动时间早期发送命令（例如，在 Linux VM 上访问 GRUB 或在 Windows VM 中访问安全模式）可能很有用。
SysRq - 重新启动 （b） | 强制来宾重新启动的系统请求。 | 此功能仅适用于 Linux 操作系统，并要求在操作系统中[启用 SysRq。](./serial-console-nmi-sysrq.md#system-request-sysrq) 如果操作系统已正确配置为 SysRq，则此命令将导致操作系统重新启动。
NMI（不可屏蔽中断） | 中断命令，该命令将传递到操作系统 | 此操作适用于[Windows](./serial-console-windows.md#use-the-serial-console-for-nmi-calls)和[Linux](./serial-console-nmi-sysrq.md#non-maskable-interrupt-nmi) VM，并且需要启用 NMI。 发送 NMI 通常会导致操作系统崩溃。 您可以将操作系统配置为创建转储文件，然后在收到 NMI 后重新启动，这在低级调试中可能很有用。

## <a name="next-steps"></a>后续步骤
* 了解有关 Linux VM 的[Azure 串行控制台](./serial-console-linux.md)的信息
* 了解有关 Windows VM 的[Azure 串行控制台](./serial-console-windows.md)的信息