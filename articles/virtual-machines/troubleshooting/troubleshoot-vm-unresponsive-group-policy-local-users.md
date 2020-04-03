---
title: 虚拟机在应用"组策略本地用户&组"策略时无响应
description: 本文提供了解决在 Azure 虚拟机 （VM） 中启动期间应用策略时负载屏幕卡住的问题的步骤。
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
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80620852"
---
# <a name="virtual-machine-is-unresponsive-while-applying-group-policy-local-users--groups-policy"></a>虚拟机在应用"组策略本地用户&组"策略时无响应

本文提供了解决在启动期间在 Azure 虚拟机 （VM） 中应用策略时负载屏幕卡住的问题的步骤。

## <a name="symptom"></a>症状

当您使用[引导诊断](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)来查看 VM 的屏幕截图时，您将看到屏幕已卡住加载消息：*应用组策略本地用户和组策略*。

![替换文本：显示应用组策略本地用户和组策略加载的屏幕（Windows Server 2012）。](media/troubleshoot-vm-unresponsive-group-policy-local-users/1.png)

Windows Server 2012

![替换文本：显示应用组策略本地用户和组策略加载（Windows Server 2012 R2）的屏幕。](media/troubleshoot-vm-unresponsive-group-policy-local-users/2.png)

Windows Server 2012 R2

## <a name="cause"></a>原因

此冻结的症状是由 Windows 配置文件服务动态链接库中的代码缺陷 *（profsvc.dll*） 引起的。

> [!NOTE]
> 此缺陷仅适用于 Windows 服务器 2012 和 Windows 服务器 2012 R2。

### <a name="the-policy-in-question"></a>有关政策

正在应用的策略无法完成其流程，其策略是：

* *计算机配置_策略_管理模板_系统/用户配置文件\删除在系统重新启动时超过指定天数的用户配置文件*

仅当以下六个条件为 true 时，此策略才会挂起：

* 启用 *"删除用户配置文件"，这些配置文件早于系统重新启动策略上指定天数*。
* 您有满足年龄要求的配置文件，需要清理。
* 您有已注册用于删除配置文件通知的组件。
* 组件发出需要从 Windows 的服务控制管理器 （SCM） 组件获取数据的调用（直接或间接），例如有关服务的"开始"、停止或查询信息。
* 您已将服务配置为*自动启动。*
* 此服务设置为在域帐户的上下文中运行（而不是使用内置帐户（如本地系统）。

### <a name="the-code-defect"></a>代码缺陷

代码缺陷是由于服务控制管理器 （SCM） 和配置文件服务尝试同时对彼此应用锁造成的。 存在锁以防止多个服务同时对同一数据进行更改，这将导致损坏。 通常，多个锁请求不会导致问题。 但是，由于这种情况在引导过程中发生，因此两个服务都不能完成其进程，因为它们彼此处于等待阶段。

### <a name="os-bug-5880648---service-control-manager-deadlocks-with-the-delete-user-profiles-on-restart-policy"></a>OS Bug 5880648 - 服务控制管理器死锁，具有"在重新启动时删除用户配置文件"策略

有两个操作重叠，以便：

* 操作 1 获取配置文件锁，但尚未获取 SCM 锁。

  **AND**

* 操作 2 获取 SCM 锁，但尚未获取配置文件锁。

发生此死锁后，获取第二个所需锁的尝试将挂起操作。

### <a name="action-1---old-profile-deletion-notification-has-profile-lock-needs-scm-lock"></a>操作 1 - 旧配置文件删除通知（具有**配置文件锁**，需要**SCM 锁定**）

1. 首先，删除旧配置文件的策略集获取内部配置文件服务锁。

   * 此锁用于防止两个线程在*删除操作*进行时与配置文件交互。

2. 策略查找已足够旧到可以删除的配置文件。
3. 作为配置文件删除的一部分，已注册配置文件删除通知的组件尝试**启动服务**。
4. 在开始服务之前，服务控制管理器 （SCM） 需要获取**操作 2**中的线程持有的**内部 SCM 锁**。

### <a name="action-2---profile-loadcreation-for-user-specific-data-has-scm-lock-needs-profile-lock"></a>操作 2 - 用户特定数据的配置文件加载/创建（具有**SCM 锁定**，需要**配置文件锁定**）

1. 在启动时，SCM 需要按其组订购所有*自动启动*服务，以及这些服务所依赖的任何服务。

2. **SCM 获取内部 SCM 锁**，用于在服务订购时控制对服务启动、停止或配置的访问。

3. 服务按顺序进行，SCM 会循环访问每个服务并启动它。

4. 如果服务在域帐户的上下文中运行，则需要为域帐户加载或创建配置文件，以便它可以存储用户特定数据。

5. 此请求将发送到**配置文件服务**。

6. 配置文件服务需要访问**操作 1**中获取**的内部锁**。

## <a name="solution"></a>解决方案

### <a name="process-overview"></a>流程概述

1. 创建和访问修复 VM
2. 启用串行控制台和内存转储集合
3. 重建 VM
4. 收集内存转储文件

   > [!NOTE]
   > 遇到此启动错误时，来宾操作系统无法运行。 您将在脱机模式下进行故障排除以解决此问题。

### <a name="create-and-access-a-repair-vm"></a>创建和访问修复 VM

1. 使用[VM 修复命令的步骤 1-3](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example)准备修复 VM。
2. 使用远程桌面连接连接到修复 VM。

### <a name="enable-serial-console-and-memory-dump-collection"></a>启用串行控制台和内存转储集合

要启用内存转储集合和串行控制台，请运行以下脚本：

1. 打开提升的命令提示会话（以管理员身份运行）。
2. 运行以下命令：

   * 启用串行控制台：

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

3. 验证 OS 磁盘上的可用空间是否与 VM 上的内存大小 （RAM） 相同。

   * 如果 OS 磁盘上没有足够的空间，则应更改将创建内存转储文件的位置，并将其引用到连接到具有足够可用空间的 VM 的任何数据磁盘。 要更改位置，请用`%SystemRoot%`以下命令中数据磁盘的驱动器号（如"F："）替换。

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

### <a name="rebuild-the-vm"></a>重建 VM

使用[VM 修复命令的步骤 5](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example)重新组装 VM。

### <a name="collect-the-memory-dump-file"></a>收集内存转储文件

要解决此问题，您需要首先收集崩溃的内存转储文件，并与内存转储文件联系支持。 要收集转储文件，请按照以下步骤操作：

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>将操作系统磁盘连接到新的修复 VM

1. 使用[VM 修复命令的步骤 1-3](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example)准备新的修复 VM。

2. 使用远程桌面连接连接到修复 VM。

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>找到转储文件并提交支持票证

1. 在修复 VM 上，转到连接的 OS 磁盘中的窗口文件夹。 如果分配给附加 OS 磁盘的驱动器号为 F，则需转到 F:\Windows。

2. 找到内存.dmp 文件，然后提交包含内存转储文件[的支持票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

3. 如果在查找内存.dmp 文件时遇到问题，您可能希望在[串行控制台中使用不可屏蔽的中断 （NMI） 调用](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls)。 您可以按照本指南使用 NMI 调用[生成内核或完整的崩溃转储](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump)文件。
