---
title: Azure 实验室服务中控制 Windows 关闭行为的指南 |微软文档
description: 自动关闭空闲 Windows 虚拟机并删除 Windows 关机命令的步骤。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.topic: article
ms.date: 3/30/2020
ms.author: spelluru
ms.openlocfilehash: 39ff4f42457451dfa4aae90b281d6b163c56b4cd
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522233"
---
# <a name="guide-to-controlling-windows-shutdown-behavior"></a>控制 Windows 关机行为指南

Azure 实验室服务提供了多个成本控制，以确保 Windows 虚拟机 （VM） 不会意外运行：
 - [设置计划](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
 - [为用户设置配额](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users)
 - [允许在断开连接时自动关闭](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

即使使用这些成本控制，在某些情况下，Windows VM 也可能意外继续运行;因此，从学生的配额中扣除：

- **RDP 窗口处于打开状态**
  
    当学生使用 RDP 连接到其 VM 时，他们可能会无意中使 RDP 窗口保持打开状态。  只要 RDP 窗口保持打开状态，**断开连接设置上的自动关机**将永远不会生效，因为它仅在断开 RDP 会话后触发。

- **Windows 关机命令用于关闭 VM**
  
    学生可以使用 Windows 关机命令或 Windows 中提供的其他关闭机制来关闭 VM，而不是使用[Azure 实验室服务的停止按钮](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-classroom-lab#start-or-stop-the-vm)。  发生这种情况时，从 Azure 实验室服务的角度来看，VM 仍在使用中。
    
为帮助您防止发生这些情况，本指南提供了自动关闭空闲 Windows VM 并从 **"开始"** 菜单中删除 Windows 关机命令的步骤。  

> [!NOTE]
> 当学生启动 VM 时，VM 也可能意外地从配额中扣除，但从未使用 RDP 实际连接到该 VM。  本指南当前*未*解决此方案。  相反，应提醒学生在启动 RDP 后立即连接到他们的 VM;或者，他们应该停止 VM。

## <a name="automatic-rdp-disconnect-and-shutdown-for-idle-vm"></a>空闲 VM 的自动 RDP 断开连接和关机

Windows 提供**本地组策略**设置，可用于设置时间限制，以便在 RDP 会话空闲时自动断开连接。  当*没有任何*鼠标+键盘输入时，会话被确定为空闲。  任何不涉及鼠标键盘输入的长运行活动都会导致 VM 处于空闲状态。  这包括执行长查询、流视频、编译等。 根据类的需求，您可以选择设置空闲时间限制，以便其足够长的时间来处理这些类型的活动。  例如，如果需要，可以将空闲时间限制设置为 1 小时或更长时间。

以下是学生在将**空闲会话限制**与[**断开连接设置时自动关机**](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)结合使用时的经验：
 1. 学生使用 RDP 连接到其 Windows VM。
 2. 当学生离开他们的 RDP 窗口打开，并且 VM 处于您指定的**空闲会话限制**的空闲状态（例如 5 分钟），学生将看到以下对话框：

    ![空闲时间限制过期对话框](../media/how-to-windows-shutdown/idle-time-expired.png)

1. 如果学生*不*单击 **"确定**"，则 2 分钟后，他们的 RDP 会话将自动断开连接。
2. RDP 会话断开连接后，一旦达到**断开连接设置时自动关闭**的指定时间范围，Azure 实验室服务将自动关闭 VM。

### <a name="set-rdp-idle-session-time-limit-on-the-template-vm"></a>在模板 VM 上设置 RDP 空闲会话时间限制

要设置 RDP 会话空闲时间限制，可以连接到模板 VM 并执行以下 PowerShell 脚本。

```powershell
# The MaxIdleTime is in milliseconds; by default, this script sets MaxIdleTime to 15 minutes.
$maxIdleTime = 15 * 60 * 90

Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" -Name "MaxIdleTime" -Value $maxIdleTime -Force
```
或者，您可以选择使用模板 VM 执行以下手动步骤：

1. 按 Windows 键，键入**gpedit，** 然后选择 **"编辑组策略"（控制面板）。**

1. 转到**计算机配置>管理模板> Windows 组件>远程桌面服务>远程桌面会话主机>会话时间限制**。  

    ![本地组策略编辑器](../media/how-to-windows-shutdown/group-policy-idle.png)
   
1. 右键单击**设置活动但空闲远程桌面服务会话的时间限制**，然后单击"**编辑**"。

1. 输入以下设置，然后单击**确定**：
   1. 选择“已启用”****。
   1. 在 **"选项**"下，指定**空闲会话限制**。

    ![空闲会话限制](../media/how-to-windows-shutdown/edit-idle-time-limit.png)

1. 最后，要将此行为与**断开连接设置上的自动关机**相结合，应按照操作方法文章中的步骤：[在断开连接时启用 VM 的自动关闭](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)。

## <a name="remove-windows-shutdown-command-from-start-menu"></a>从"开始"菜单中删除 Windows 关机命令

Windows**本地组策略**设置还允许您从 **"开始"** 菜单中删除关机命令。

要删除关机命令，可以连接到模板 VM 并执行以下 PowerShell 脚本。

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "HidePowerOptions" -Value 1 -Force
```

或者，您可以选择使用模板 VM 执行以下手动步骤：

1. 按 Windows 键，键入**gpedit，** 然后选择 **"编辑组策略"（控制面板）。**

1. 转到**计算机配置>"开始菜单"和"任务栏">管理模板**。  

    ![本地组策略编辑器](../media/how-to-windows-shutdown/group-policy-shutdown.png)

1. 右键单击 **"删除并阻止访问关闭"、"重新启动"、"睡眠"和"休眠"命令**，然后单击"**编辑**"。

1. 选择 **"已启用"** 设置，然后单击"**确定**"：
 
   ![关机设置](../media/how-to-windows-shutdown/edit-shutdown.png)

1. 请注意，关闭命令不再显示在 Windows 开始菜单下;因此，关闭命令不再显示在**Windows"开始"** 菜单下。只显示 **"断开连接"** 命令。

    ![关机命令](../media/how-to-windows-shutdown/start-menu.png)

## <a name="next-steps"></a>后续步骤
请参阅有关如何准备 Windows 模板 VM 的文章：[在 Azure 实验室服务中设置 Windows 模板计算机的指南](how-to-prepare-windows-template.md)