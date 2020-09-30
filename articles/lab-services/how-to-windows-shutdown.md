---
title: 在 Azure 实验室服务中控制 Windows 关闭行为指南 |Microsoft Docs
description: 自动关闭空闲 Windows 虚拟机并删除 Windows shutdown 命令的步骤。
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: c6021131787dde4fe23ec4caad107bda2e20158a
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541554"
---
# <a name="guide-to-controlling-windows-shutdown-behavior"></a>控制 Windows 关闭行为指南

Azure 实验室服务提供多个成本控制，以确保 (Vm) 的 Windows 虚拟机不会意外运行：
 - [设置日程安排](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
 - [为用户设置配额](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users)
 - [启用在断开连接时自动关闭](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

即使使用这些成本控制，在某些情况下，Windows VM 可能会意外地继续运行;因此，从学生的配额中减去：

- **RDP 窗口处于打开状态**
  
    当学生使用 RDP 连接到其 VM 时，可能会无意中使 RDP 窗口处于打开状态。  只要 RDP 窗口保持打开状态，" **断开连接时自动关闭** " 设置将永远不起作用，因为它仅在 RDP 会话断开连接后触发。

- **Windows shutdown 命令用于关闭 VM**
  
    学生可以使用 Windows 关机命令或 Windows 中提供的其他关闭机制来关闭 VM，而不是使用 [Azure 实验室服务的 "停止" 按钮](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-classroom-lab#start-or-stop-the-vm)。  发生这种情况时，从 Azure 实验室服务的角度来看，仍在使用 VM。
    
为了帮助防止出现这种情况，本指南提供了自动关闭空闲 Windows VM 并从 " **开始** " 菜单中删除 Windows shutdown 命令的步骤。  

> [!NOTE]
> 当学生启动 VM 时，虚拟机也可能会意外地从配额中扣减，但绝不会真正使用 RDP 连接到该 VM。  本指南当前 *不* 会处理这种情况。  相反，应提醒学生在启动后立即使用 RDP 连接到其 VM;或者，它们应停止 VM。

## <a name="remove-windows-shutdown-command-from-start-menu"></a>从 "开始" 菜单中删除 Windows 关闭命令

利用 Windows **本地组策略** 设置，还可以从 " **开始** " 菜单中删除 shutdown 命令。

若要删除 shutdown 命令，你可以连接到模板 VM 并执行以下 PowerShell 脚本。

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "HidePowerOptions" -Value 1 -Force
```

或者，你可以选择使用模板 VM 执行以下手动步骤：

1. 按 Windows 键，键入 " **gpedit.msc**"，然后选择 **"编辑组策略" (控制面板 ") **。

1. 请在 " **开始" 菜单和任务栏管理模板 > 中转到 "计算机 > 配置**"。  

    ![本地组策略编辑器](./media/how-to-windows-shutdown/group-policy-shutdown.png)

1. 右键单击 **"删除"，阻止访问 "关机"、"重新启动"、"睡眠" 和 "休眠" 命令**，然后单击 " **编辑**"。

1. 选择 " **已启用** " 设置，然后单击 **"确定"**：
 
   ![关机设置](./media/how-to-windows-shutdown/edit-shutdown.png)

1. 请注意，关闭命令不再显示在 Windows " **开始** " 菜单下;仅显示 " **断开连接** " 命令。

    ![Shutdown 命令](./media/how-to-windows-shutdown/start-menu.png)

## <a name="next-steps"></a>后续步骤
请参阅有关如何准备 Windows 模板 VM 的文章： [在 Azure 实验室服务中设置 Windows 模板计算机指南](how-to-prepare-windows-template.md)
