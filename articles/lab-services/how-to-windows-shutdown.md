---
title: 在 Azure 实验室服务中控制 Windows 关闭行为指南 |Microsoft Docs
description: 自动关闭空闲 Windows 虚拟机并删除 Windows shutdown 命令的步骤。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: e17f6e79c3d18d82dd206954dcfb0e06b02b4d53
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445162"
---
# <a name="guide-to-controlling-windows-shutdown-behavior"></a>控制 Windows 关闭行为指南

Azure 实验室服务提供多个成本控制来确保 Windows 虚拟机（Vm）不会意外运行：
 - [设置日程安排](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
 - [为用户设置配额](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users)
 - [启用在断开连接时自动关闭](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

即使使用这些成本控制，在某些情况下，Windows VM 可能会意外地继续运行;因此，从学生的配额中减去：

- **RDP 窗口处于打开状态**
  
    当学生使用 RDP 连接到其 VM 时，可能会无意中使 RDP 窗口处于打开状态。  只要 RDP 窗口保持打开状态，"**断开连接时自动关闭**" 设置将永远不起作用，因为它仅在 RDP 会话断开连接后触发。

- **Windows shutdown 命令用于关闭 VM**
  
    学生可以使用 Windows 关机命令或 Windows 中提供的其他关闭机制来关闭 VM，而不是使用[Azure 实验室服务的 "停止" 按钮](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-classroom-lab#start-or-stop-the-vm)。  发生这种情况时，从 Azure 实验室服务的角度来看，仍在使用 VM。
    
为了帮助防止出现这种情况，本指南提供了自动关闭空闲 Windows VM 并从 "**开始**" 菜单中删除 Windows shutdown 命令的步骤。  

> [!NOTE]
> 当学生启动 VM 时，虚拟机也可能会意外地从配额中扣减，但绝不会真正使用 RDP 连接到该 VM。  本指南当前*不*会处理这种情况。  相反，应提醒学生在启动后立即使用 RDP 连接到其 VM;或者，它们应停止 VM。

## <a name="automatic-rdp-disconnect-and-shutdown-for-idle-vm"></a>空闲 VM 的自动 RDP 断开连接和关闭

Windows 提供**本地组策略**设置，你可以使用这些设置来设置时间限制，以便在 RDP 会话处于空闲状态时将其自动断开连接。  如果*没有任何 mouse\keyboard 输入，则*将会话确定为空闲状态。  任何不涉及 mouse\keyboard 输入的长时间运行的活动都将导致 VM 处于空闲状态。  这包括执行较长的查询、流式处理视频、编译等。 根据您的类的需要，您可以选择设置空闲时间限制，使其足够长，以便处理这些类型的活动。  例如，如果需要，可以将空闲时间限制设置为1或更长。

当你在 "[**断开连接时自动关闭**](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)" 设置中配置 "**空闲会话限制**" 时，学生体验如下：
 1. 学生使用 RDP 连接到其 Windows VM。
 2. 当学生将其 RDP 窗口保持为打开状态，并且 VM 闲置指定的**空闲会话限制**（如5分钟）时，学生会看到以下对话框：

    !["空闲时间限制已过期" 对话框](./media/how-to-windows-shutdown/idle-time-expired.png)

1. 如果学生*未*单击 **"确定"**，则其 RDP 会话将在2分钟后自动断开。
2. RDP 会话断开连接后，一旦达到了**自动关机**设置的指定时间范围，Azure 实验室服务将自动关闭该 VM。

### <a name="set-rdp-idle-session-time-limit-on-the-template-vm"></a>在模板 VM 上设置 RDP 空闲会话时间限制

若要设置 RDP 会话空闲时间限制，可以连接到模板 VM，并执行以下 PowerShell 脚本。

```powershell
# The MaxIdleTime is in milliseconds; by default, this script sets MaxIdleTime to 15 minutes.
$maxIdleTime = 15 * 60 * 1000

Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" -Name "MaxIdleTime" -Value $maxIdleTime -Force
```
或者，你可以选择使用模板 VM 执行以下手动步骤：

1. 按 Windows 键，键入 " **gpedit.msc**"，然后选择 **"编辑组策略（控制面板）**"。

1. 请参阅**计算机配置 > 管理模板 > Windows 组件 > 远程桌面服务 > 远程桌面会话主机会话时间限制**。  

    ![本地组策略编辑器](./media/how-to-windows-shutdown/group-policy-idle.png)
   
1. 右键单击 "**为活动但空闲远程桌面服务会话设置时间限制**"，然后单击 "**编辑**"。

1. 输入以下设置，然后单击**确定**：
   1. 选择“**已启用**”。
   1. 在 "**选项**" 下，指定**空闲会话限制**。

    ![空闲会话限制](./media/how-to-windows-shutdown/edit-idle-time-limit.png)

1. 最后，若要将此行为与 "**断开连接时自动关闭**" 设置组合在一起，你应按照操作指南一文：[在断开连接时启用 vm 自动关闭](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)中的步骤操作。

> [!WARNING]
> 使用 PowerShell 配置此设置以直接或使用组策略编辑器手动修改注册表设置之后，必须先重新启动 VM，设置才能生效。  此外，如果使用注册表配置设置，则组策略编辑器不会始终刷新以反映对注册表设置的更改;但是，注册表设置仍然会按预期方式生效，当你指定的时间长度处于空闲状态时，你会看到 RDP 会话已断开连接。

## <a name="remove-windows-shutdown-command-from-start-menu"></a>从 "开始" 菜单中删除 Windows 关闭命令

利用 Windows**本地组策略**设置，还可以从 "**开始**" 菜单中删除 shutdown 命令。

若要删除 shutdown 命令，你可以连接到模板 VM 并执行以下 PowerShell 脚本。

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "HidePowerOptions" -Value 1 -Force
```

或者，你可以选择使用模板 VM 执行以下手动步骤：

1. 按 Windows 键，键入 " **gpedit.msc**"，然后选择 **"编辑组策略（控制面板）**"。

1. 请在 "**开始" 菜单和任务栏管理模板 > 中转到 "计算机 > 配置**"。  

    ![本地组策略编辑器](./media/how-to-windows-shutdown/group-policy-shutdown.png)

1. 右键单击 **"删除"，阻止访问 "关机"、"重新启动"、"睡眠" 和 "休眠" 命令**，然后单击 "**编辑**"。

1. 选择 "**已启用**" 设置，然后单击 **"确定"**：
 
   ![关机设置](./media/how-to-windows-shutdown/edit-shutdown.png)

1. 请注意，关闭命令不再显示在 Windows "**开始**" 菜单下;仅显示 "**断开连接**" 命令。

    ![Shutdown 命令](./media/how-to-windows-shutdown/start-menu.png)

## <a name="next-steps"></a>后续步骤
请参阅有关如何准备 Windows 模板 VM 的文章：[在 Azure 实验室服务中设置 Windows 模板计算机指南](how-to-prepare-windows-template.md)