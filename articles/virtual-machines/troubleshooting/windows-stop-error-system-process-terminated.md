---
title: Windows 停止错误状态系统进程已终止
description: 本文提供了解决以下问题的步骤：操作系统遇到停止错误0xC000021A，导致无法启动 Azure 虚拟机。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 037d0858-4611-4707-bd46-cc8085d011ed
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/21/2020
ms.author: v-mibufo
ms.openlocfilehash: b13b61aff819271ed1722572f251f9a6d14b17ab
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91976991"
---
# <a name="windows-stop-error---0xc000021a-status-system-process-terminated"></a>Windows 停止错误-0xC000021A 状态系统进程已终止

本文提供了解决以下问题的步骤：操作系统 (OS) 遇到 stop 错误0xC000021A，使 Azure 虚拟机 (VM) 启动。

## <a name="symptom"></a>症状

使用 " [启动诊断](./boot-diagnostics.md) " 查看 VM 的屏幕截图时，屏幕截图将显示操作系统在启动过程中遇到错误的消息，并显示以下消息：

**你的电脑遇到问题，需要重新启动。我们只收集一些错误信息，然后你可以重新启动。 ( # #% 完成) 如果你想要了解详细信息，可以稍后在以后搜索此错误： 0xC000021a**。

  ![图1显示错误代码 #0xC000021A 显示消息 "你的电脑遇到问题，需要重新启动。 我们只收集一些错误信息，然后你可以重新启动。 "。](./media/windows-stop-error-system-process-terminated/1-pc-problem-restart.png)

## <a name="cause"></a>原因

错误0xC000021A 表明 **STATUS_SYSTEM_PROCESS_TERMINATED**。

当关键进程（例如 WinLogon ( # A0) 或客户端服务器 Run-Time 子系统 ( # A1) 失败时，会出现此错误。 内核检测到任一服务已停止后，将引发 **停止 0xc000021a** 错误。 此错误可能有多种原因，包括：

- 安装了不匹配的系统文件。
- Service Pack 或 KB 更新安装失败。
- 用于还原硬盘的备份程序未正确还原可能已被使用的文件。
- 安装了不兼容的第三方程序。

## <a name="solution"></a>解决方案

### <a name="collect-the-memory-dump-file"></a>收集内存转储文件

若要解决此问题，需要对故障转储进行分析。 收集故障的内存转储文件，并与支持人员联系。 若要收集转储文件，请执行以下步骤：

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>将 OS 磁盘附加到新的修复 VM

1.  使用 [VM 修复命令](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) 的步骤 1-3 准备一个修复 VM。
2.  使用 **远程桌面连接**连接到修复 VM。

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>找到转储文件并提交支持票证

1.  在修复 VM 上，转到附加的 OS 磁盘中的 Windows 文件夹。 如果分配给附加 OS 磁盘的驱动程序号是 F，请参阅 F:\Windows。
2.  找到 memory.dmp 文件，然后使用该内存转储文件[提交支持票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
3.  如果在查找内存 dmp 文件时遇到问题，请改为 [在串行控制台中 (NMI) 调用中使用不可屏蔽的中断](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) 。 可以按照此处的指南，[使用 NMI 调用生成故障转储文件](/windows/client-management/generate-kernel-or-complete-crash-dump)。

## <a name="next-steps"></a>后续步骤

- 有关故障排除的详细信息，请参阅 [排查常见启动错误](./boot-error-troubleshoot.md) 或 [如何通过将 OS 磁盘附加到恢复 VM 对 Windows VM 进行](./troubleshoot-recovery-disks-windows.md)故障排除。 还应熟悉 [如何使用启动诊断对虚拟机进行故障排除](./boot-diagnostics.md)。
- 有关使用 Resource Manager 的详细信息，请参阅 [Azure Resource Manager 概述](../../azure-resource-manager/management/overview.md)。
- 如果无法连接到 VM，请参阅 [对 AZURE VM 的 RDP 连接进行故障排除](./troubleshoot-rdp-connection.md)。