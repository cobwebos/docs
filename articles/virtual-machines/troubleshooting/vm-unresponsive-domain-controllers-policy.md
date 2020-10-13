---
title: 在应用默认域控制器策略时，VM 无响应
titlesuffix: Azure Virtual Machines
description: 本文提供了解决在默认域控制器策略阻止启动 Azure VM 时出现的问题的步骤。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 34e6b765-3496-46a1-b7d7-6def00884394
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/08/2020
ms.author: v-miegge
ms.openlocfilehash: 4f181a6dee7ed182150bd0cad2b51690b6c77d7a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91963119"
---
# <a name="vm-is-unresponsive-while-applying-default-domain-controllers-policy"></a>在应用默认域控制器策略时，VM 无响应

本文提供了解决以下问题的步骤：默认域控制器策略阻止 (VM) 的 Azure 虚拟机启动。

## <a name="symptom"></a>症状

使用 " [启动诊断](./boot-diagnostics.md) " 查看 VM 的屏幕截图时，你会看到屏幕截图显示操作系统在使用消息 " **默认域控制器策略**" 启动时停止响应。

  ![图1显示带有 "默认域控制器策略" 消息的操作系统停滞](./media/vm-unresponsive-domain-controllers-policy/1-default-domain-controllers-policy.png)

## <a name="cause"></a>原因

此问题可能是由于对默认域控制器策略的最近更改所致。 否则，将需要执行内存转储文件分析以确定根本原因。

## <a name="solution"></a>解决方案

如果最近更改了 "默认域控制器策略"，则可能想要撤消这些更改以解决此问题。 如果你不确定导致此问题的原因，请收集内存转储，然后提交支持票证。

### <a name="collect-the-memory-dump-file"></a>收集内存转储文件

若要解决此问题，你应该首先收集崩溃的内存转储文件，然后与内存转储文件联系支持人员。 若要收集转储文件，请执行以下步骤：

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>将 OS 磁盘附加到新的修复 VM

1. 使用 [VM 修复命令](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md)的步骤 1-3 来准备一个修复 VM。

1. 使用远程桌面连接来连接到修复 VM。

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>找到转储文件并提交支持票证

1. 在修复 VM 上，中转到附加 OS 磁盘中的 Windows 文件夹。 如果分配给附加 OS 磁盘的驱动器号为 `F`，则需转到 `F:\Windows`。

1. 找到 memory.dmp 文件，然后使用该内存转储文件[提交支持票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

1. 如果在查找内存 dmp 文件时遇到问题，请改为 [在串行控制台中 (NMI) 调用中使用不可屏蔽的中断](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) 。 按照指南操作， [使用 NMI 调用生成故障转储文件](/windows/client-management/generate-kernel-or-complete-crash-dump)。