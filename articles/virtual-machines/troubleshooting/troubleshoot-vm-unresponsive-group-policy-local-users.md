---
title: 在应用 "组策略本地用户 & 组" 策略时，虚拟机无响应
description: 本文介绍在 Azure 虚拟机（VM）中的启动过程中，解决加载屏幕停滞应用策略问题的步骤。
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: ff113268-f5bf-4e6a-986e-63b9b0ceff20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 04/02/2020
ms.author: v-mibufo
ms.openlocfilehash: 085880122e9a80e976cfe59686748b58aeba1922
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80620852"
---
# <a name="virtual-machine-is-unresponsive-while-applying-group-policy-local-users--groups-policy"></a>在应用 "组策略本地用户 & 组" 策略时，虚拟机无响应

本文提供了一些步骤，用于解决在启动过程中的 Azure 虚拟机（VM）中加载屏幕遇到的问题。

## <a name="symptom"></a>症状

当你使用[启动诊断](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)来查看 VM 的屏幕截图时，你会看到屏幕上出现以下消息： "正在*应用组策略本地用户和组策略*"。

![替换文本：显示应用组策略本地用户和组策略加载（Windows Server 2012）的屏幕。](media/troubleshoot-vm-unresponsive-group-policy-local-users/1.png)

Windows Server 2012

![替换文本：显示应用组策略本地用户和组策略加载的屏幕（Windows Server 2012 R2）。](media/troubleshoot-vm-unresponsive-group-policy-local-users/2.png)

Windows Server 2012 R2

## <a name="cause"></a>原因

此冻结的症状是由 Windows 配置文件服务动态链接库（*profsvc*）中的代码缺陷导致的。

> [!NOTE]
> 此缺陷仅适用于 Windows Server 2012 和 Windows Server 2012 R2。

### <a name="the-policy-in-question"></a>相关策略

所应用的不完成其进程的策略是：

* *在系统重新启动时，计算机 Configuration\Policies\Administrative Templates \ System/User Profiles\Delete 用户配置文件早于指定的天数*

仅当满足以下六个条件时，此策略才会挂起：

* 已启用 "*删除超过指定天数的用户配置文件" 系统重新启动*策略。
* 你的配置文件满足要求清理所需的期限。
* 你的组件已经注册了用于配置文件的删除通知。
* 组件调用（直接或间接）需要从 Windows 的服务控制管理器（SCM）组件获取数据，例如启动、停止或查询有关服务的信息。
* 你的服务已配置为*自动*启动。
* 此服务设置为在域帐户（而不是使用内置帐户，如本地系统）的上下文中运行。

### <a name="the-code-defect"></a>代码缺陷

代码缺陷是因为服务控制管理器（SCM）和配置文件服务试图同时应用锁定。 锁定存在是为了防止多个服务同时对相同的数据进行更改，这会导致损坏。 通常，多个锁请求不会导致问题。 但是，由于在启动过程中发生这种情况，无论服务在等待的进程停滞，都不能完成其进程。

### <a name="os-bug-5880648---service-control-manager-deadlocks-with-the-delete-user-profiles-on-restart-policy"></a>操作系统 Bug 5880648-服务控制管理器死锁，出现 "重新启动时删除用户配置文件" 策略

有两个重叠的操作，以便：

* 操作1获取配置文件锁定，但尚未获取 SCM 锁。

  **与**

* 操作2获取 SCM 锁，但尚未获得配置文件锁定。

发生此死锁后，尝试获取第二个所需的锁会挂起该操作。

### <a name="action-1---old-profile-deletion-notification-has-profile-lock-needs-scm-lock"></a>操作 1-旧配置文件删除通知（具有**配置文件锁定**，需要**SCM 锁定**）

1. 首先，将策略设置为删除旧配置文件将获取内部配置文件服务锁。

   * 此锁定用于防止两个线程在执行*删除操作*时与配置文件进行交互。

2. 策略将查找足以删除的配置文件。
3. 作为删除配置文件的一部分，已注册配置文件删除通知的组件会尝试**启动服务**。
4. 在启动服务之前，服务控制管理器（SCM）需要获取**操作 2**中的线程所持有的**内部 SCM 锁**。

### <a name="action-2---profile-loadcreation-for-user-specific-data-has-scm-lock-needs-profile-lock"></a>操作 2-配置文件加载/创建用户特定数据（具有**SCM 锁定**，需要**配置文件锁定**）

1. 在启动时，SCM 需要按组对所有*自动启动*服务和这些服务所依赖的任何服务进行排序。

2. Scm 获取用于在对服务进行排序时控制对启动、停止或配置服务的访问的**内部 scm 锁**。

3. 一旦服务按顺序进行，SCM 就会循环遍历每个服务，并启动它。

4. 如果服务在域帐户的上下文中运行，则需要为域帐户加载或创建一个配置文件，以便它可以存储用户特定数据。

5. 此请求将发送到**配置文件服务**。

6. 配置文件服务需要访问在**操作 1**中获取的**内部锁**。

## <a name="solution"></a>解决方案

### <a name="process-overview"></a>流程概述

1. 创建和访问修复 VM
2. 启用串行控制台和内存转储收集
3. 重新生成 VM
4. 收集内存转储文件

   > [!NOTE]
   > 当遇到此启动错误时，来宾操作系统不能正常工作。 你将在脱机模式下进行故障排除，以解决此问题。

### <a name="create-and-access-a-repair-vm"></a>创建和访问修复 VM

1. 使用[VM 修复命令的步骤 1-3](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example)来准备修复 VM。
2. 使用远程桌面连接连接到修复 VM。

### <a name="enable-serial-console-and-memory-dump-collection"></a>启用串行控制台和内存转储收集

若要启用内存转储收集和串行控制台，请运行以下脚本：

1. 打开提升的命令提示符会话（以管理员身份运行）。
2. 运行以下命令：

   * 启用串行控制台：

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

3. 验证 OS 磁盘上的可用空间是否与 VM 上的内存大小（RAM）一样多。

   * 如果 OS 磁盘上没有足够的空间，则应更改内存转储文件的创建位置，并将其引用到任何附加到具有足够可用空间的 VM 的数据磁盘。 若要更改位置，请`%SystemRoot%`在以下命令中将替换为数据磁盘的驱动器号（例如 "F："）。

#### <a name="suggested-configuration-to-enable-os-dump"></a>启用 OS 转储的建议配置

**加载损坏的 OS 磁盘：**

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**在 ControlSet001 上启用：**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**在 ControlSet002 上启用：**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

### <a name="rebuild-the-vm"></a>重新生成 VM

使用[Vm 修复命令的步骤 5](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example)重新组装 vm。

### <a name="collect-the-memory-dump-file"></a>收集内存转储文件

若要解决此问题，需要首先收集崩溃的内存转储文件，并与内存转储文件联系支持人员。 若要收集转储文件，请执行以下步骤：

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>将 OS 磁盘附加到新的修复 VM

1. 使用[VM Repair 命令](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example)的步骤1-3 准备新的修复 VM。

2. 使用远程桌面连接连接到修复 VM。

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>找到转储文件并提交支持票证

1. 在修复 VM 上，在附加的 OS 磁盘中中转到 windows 文件夹。 如果分配给附加 OS 磁盘的驱动器号为 F，则需转到 F:\Windows。

2. 找到内存 dmp 文件，然后使用内存转储文件[提交支持票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

3. 如果在查找内存 dmp 文件时遇到问题，请改为[在串行控制台中使用不可屏蔽中断（NMI）调用](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls)。 可以按照指南使用 NMI 调用[生成内核或完成故障转储](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump)文件。
