---
title: 排除 Windows 虚拟桌面会话主机故障 - Azure
description: 在配置 Windows 虚拟桌面会话主机虚拟机时如何解决问题。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: c7d9a5d576ceec301eba7436c1e0af34412ae854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127582"
---
# <a name="session-host-virtual-machine-configuration"></a>会话主机虚拟机配置

使用本文可解决配置 Windows 虚拟桌面会话主机虚拟机 （VM） 时遇到的问题。

## <a name="provide-feedback"></a>提供反馈

请访问 [Windows 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，与产品团队和活跃的社区成员共同探讨 Windows 虚拟桌面服务。

## <a name="vms-are-not-joined-to-the-domain"></a>VM 未加入域

如果您在将 VM 加入域时遇到问题，请按照这些说明操作。

- 使用[将 Windows 服务器虚拟机加入托管域](../active-directory-domain-services/join-windows-vm.md)或使用[域联接模板](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)中的过程手动加入 VM。
- 尝试从 VM 上的命令行 ping 域名。
- 查看[故障排除域联接错误消息](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx)中的域联接错误消息的列表。

### <a name="error-incorrect-credentials"></a>错误：凭据不正确

**原因：** 在 Azure 资源管理器模板界面修复中输入凭据时，会进行拼写错误。

**修复：** 采取以下操作之一来解决。

- 手动将 VM 添加到域。
- 确认凭据后重新部署模板。 请参阅[使用 PowerShell 创建主机池](create-host-pools-powershell.md)。
- 使用将现有 Windows VM 加入[AD 域的](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)模板将 VM 加入域。

### <a name="error-timeout-waiting-for-user-input"></a>错误：超时等待用户输入

**原因：** 用于完成域联接的帐户可能具有多重身份验证 （MFA）。

**修复：** 采取以下操作之一来解决。

- 暂时删除帐户的 MFA。
- 使用服务帐户。

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>错误：预配期间使用的帐户没有完成操作的权限

**原因：** 由于合规性和法规，正在使用的帐户没有将 VM 加入域的权限。

**修复：** 采取以下操作之一来解决。

- 使用作为管理员组成员的帐户。
- 向正在使用的帐户授予必要的权限。

### <a name="error-domain-name-doesnt-resolve"></a>错误：域名无法解析

**原因 1：** VM 位于与域所在的虚拟网络 （VNET） 不关联的虚拟网络上。

**修复 1：** 在预配 VM 的 VNET 和域控制器 （DC） 运行的 VNET 之间创建 VNET 对等互连。 请参阅[创建虚拟网络对等互连 - 资源管理器，不同的订阅](../virtual-network/create-peering-different-subscriptions.md)。

**原因 2：** 使用 Azure 活动目录域服务 （Azure AD DS） 时，虚拟网络没有更新其 DNS 服务器设置以指向托管域控制器。

**修复 2：** 要更新包含 Azure AD DS 的虚拟网络的 DNS 设置，请参阅[更新 Azure 虚拟网络的 DNS 设置](../active-directory-domain-services/tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)。

**原因 3：** 网络接口的 DNS 服务器设置不指向虚拟网络上的相应 DNS 服务器。

**修复 3：** 按照 [更改 DNS 服务器] 中的步骤执行以下操作之一进行解析。
- 使用["更改 DNS"服务器](../virtual-network/virtual-network-network-interface.md#change-dns-servers)的步骤将网络接口的 DNS 服务器设置更改为 **"自定义"，** 并指定虚拟网络上的 DNS 服务器的专用 IP 地址。
- 使用["更改 DNS"服务器](../virtual-network/virtual-network-network-interface.md#change-dns-servers)的步骤将网络接口的 DNS 服务器设置更改为**从虚拟网络继承**，然后使用[更改 DNS 服务器](../virtual-network/manage-virtual-network.md#change-dns-servers)的步骤更改虚拟网络的 DNS 服务器设置。

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>未安装 Windows 虚拟桌面代理和 Windows 虚拟桌面引导加载程序

预配 VM 的建议方法是使用 Azure 资源管理器**创建和预配 Windows 虚拟桌面主机池**模板。 该模板会自动安装 Windows 虚拟桌面代理和 Windows 虚拟桌面代理引导加载程序。

按照这些说明确认已安装组件并检查错误消息。

1. 通过签入**控制面板** > **程序和** > **功能**，确认安装这两个组件。 如果**Windows 虚拟桌面代理**和**Windows 虚拟桌面代理引导加载程序**不可见，则它们未安装在 VM 上。
2. 打开**文件资源管理器**并导航到**C：\Windows\Temp_ScriptLog.log**。 如果文件丢失，则表示安装这两个组件的 PowerShell DSC 无法在提供的安全上下文中运行。
3. 如果文件**C：_Windows_Temp_ScriptLog.log**存在，请打开该文件并检查错误消息。

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptloglog-is-also-missing"></a>错误：缺少 Windows 虚拟桌面代理和 Windows 虚拟桌面代理引导加载程序。 C：\Windows\Temp_ScriptLog.log 也缺少

**原因 1：** 在输入 Azure 资源管理器模板期间提供的凭据不正确或权限不足。

**修复 1：** 使用[使用 PowerShell 创建主机池](create-host-pools-powershell.md)，手动将缺少的组件添加到 VM 中。

**原因 2：** PowerShell DSC 能够启动和执行，但未能完成，因为它无法登录到 Windows 虚拟桌面并获取所需信息。

**修复 2：** 确认以下列表中的项目。

- 确保帐户没有 MFA。
- 确认租户名称准确，并且租户存在于 Windows 虚拟桌面中。
- 确认该帐户至少具有 RDS 参与者权限。

### <a name="error-authentication-failed-error-in-cwindowstempscriptloglog"></a>错误：身份验证失败，C 错误：\Windows\Temp_ScriptLog.log.log

**原因：** PowerShell DSC 能够执行，但无法连接到 Windows 虚拟桌面。

**修复：** 确认以下列表中的项目。

- 使用 Windows 虚拟桌面服务手动注册 VM。
- 用于连接到 Windows 虚拟桌面的确认帐户对租户具有创建主机池的权限。
- 确认帐户没有 MFA。

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>Windows 虚拟桌面代理未向 Windows 虚拟桌面服务注册

首次在会话主机 VM 上安装 Windows 虚拟桌面代理（手动或通过 Azure 资源管理器模板和 PowerShell DSC）时，它提供注册令牌。 以下部分介绍适用于 Windows 虚拟桌面代理和令牌的疑难解答问题。

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>错误：在 Get-RdsSessionHost cmdlet 中提交的状态显示状态为"不可用"

![获取 RdsSessionHost cmdlet 将显示状态为"不可用"。](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**原因：** 代理无法将自己更新到新版本。

**修复：** 按照这些说明手动更新代理。

1. 在会话主机 VM 上下载代理的新版本。
2. 启动任务管理器，并在"服务"选项卡中停止 RDAgentBootLoader 服务。
3. 运行新版本 Windows 虚拟桌面代理的安装程序。
4. 当提示注册令牌时，删除INVALID_TOKEN条目，然后按下一个条目（不需要新令牌）。
5. 完成安装向导。
6. 打开任务管理器并启动 RDAgentBootLoader 服务。

## <a name="error--windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>错误：Windows 虚拟桌面代理注册表项"注册"显示值 0

**原因：** 注册令牌已过期或已生成过期值为 999999。

**修复：** 按照这些说明修复代理注册表错误。

1. 如果已有注册令牌，请使用删除 RDS 注册信息将其删除。
2. 使用 Rds-New注册信息生成新令牌。
3. 确认 -ExpriationHours 参数设置为 72（最大值为 99999）。

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>错误：运行 Get-RdsSessionHost 时，Windows 虚拟桌面代理未报告检测信号

**原因 1：** RDAgentBootLoader 服务已停止。

**修复 1：** 启动任务管理器，如果服务选项卡报告 RDAgentBootLoader 服务的已停止状态，则启动该服务。

**原因 2：** 端口 443 可能关闭。

**修复 2：** 按照这些说明打开端口 443。

1. 通过从[Sys内部工具](/sysinternals/downloads/psping/)下载 PSPing 工具，确认端口 443 已打开。
2. 在代理正在运行的会话主机 VM 上安装 PSPing。
3. 以管理员身份打开命令提示并发出以下命令：

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. 确认 PSPing 从 RD Broker 收到的信息：

    ```
    PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
    Copyright (C) 2012-2016 Mark Russinovich
    Sysinternals - www.sysinternals.com
    TCP connect to 13.77.160.237:443:
    5 iterations (warmup 1) ping test:
    Connecting to 13.77.160.237:443 (warmup): from 172.20.17.140:60649: 2.00ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60650: 3.83ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60652: 2.21ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60653: 2.14ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60654: 2.12ms
    TCP connect statistics for 13.77.160.237:443:
    Sent = 4, Received = 4, Lost = 0 (0% loss),
    Minimum = 2.12ms, Maximum = 3.83ms, Average = 2.58ms
    ```

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>解决 Windows 虚拟桌面并排堆栈的问题

Windows 虚拟桌面并行堆栈会自动与 Windows 服务器 2019 一起安装。 使用 Microsoft 安装程序 （MSI） 在 Microsoft Windows 服务器 2016 或 Windows 服务器 2012 R2 上安装并行堆栈。 对于微软 Windows 10，Windows 虚拟桌面并行堆栈启用了**enablesxstacks.ps1**。

在会话主机池 VM 上安装或启用并行堆栈有三种主要方式：

- 使用 Azure 资源管理器**创建和预配新的 Windows 虚拟桌面主机池**模板
- 通过在主映像中包含并启用
- 在每个 VM 上手动安装或启用（或使用扩展/电源外壳）

如果 Windows 虚拟桌面并排堆栈出现问题，请从命令提示符键入**qwinsta**命令，以确认已安装或启用并行堆栈。

如果安装并启用了并排堆栈，**则 qwinsta**的输出将在输出中列出**rdp-sxs。**

![并行堆栈安装或启用，输出中 qwinsta 列为 rdp-sxs。](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

检查下面列出的注册表项，并确认其值是否匹配。 如果注册表项丢失或值不匹配，请按照创建[具有 PowerShell 的主机池](create-host-pools-powershell.md)中的说明来了解如何重新安装并排堆栈。

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-o_reverse_connect_stack_failure"></a>错误： O_REVERSE_CONNECT_STACK_FAILURE

![O_REVERSE_CONNECT_STACK_FAILURE错误代码。](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**原因：** 会话主机 VM 上未安装并行堆栈。

**修复：** 按照这些说明在会话主机 VM 上安装并行堆栈。

1. 使用远程桌面协议 （RDP） 以本地管理员身份直接进入会话主机 VM。
2. 下载并导入[Windows 虚拟桌面 PowerShell 模块](/powershell/windows-virtual-desktop/overview/)，以便如果您尚未在 PowerShell 会话中使用，然后运行此 cmdlet 以登录您的帐户：

    ```powershell
    Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
    ```

3. 使用[PowerShell 创建主机池](create-host-pools-powershell.md)安装并排堆栈。

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>如何修复出现故障的 Windows 虚拟桌面并排堆栈

已知情况可能导致并排堆栈出现故障：

- 未按照步骤的正确顺序启用并行堆栈
- 自动更新至 Windows 10 增强型多功能光盘 （EVD）
- 缺少远程桌面会话主机 （RDSH） 角色
- 多次运行启用 xsstackrc.ps1
- 在没有本地管理员权限的帐户中运行 enablesxsstackrc.ps1

本节中的说明可以帮助您卸载并行安装 Windows 虚拟桌面堆栈。 卸载并排堆栈后，转到[使用 PowerShell 创建主机池](create-host-pools-powershell.md)的"使用 Windows 虚拟桌面主机池注册 VM"以重新安装并排堆栈。

用于运行修正的 VM 必须与并排堆栈发生故障的 VM 位于同一子网和域上。

按照以下说明从同一子网和域运行修正：

1. 使用标准远程桌面协议 （RDP） 连接到将应用修复程序的 VM。
2. 从 下载https://docs.microsoft.com/sysinternals/downloads/psexecPsExec。
3. 解压缩下载的文件。
4. 以本地管理员身份启动命令提示。
5. 导航到 PsExec 已解压缩的文件夹。
6. 从命令提示符，使用以下命令：

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!Note]
    >VMname 是 VM 的机名，并排堆栈出现故障。

7. 单击"同意"接受 PsExec 许可协议。

    ![软件许可协议屏幕截图。](media/SoftwareLicenseTerms.png)

    >[!Note]
    >此对话框将仅在首次运行 PsExec 时显示。

8. 在 VM 上打开具有故障的并排堆栈后，运行 qwinsta 并确认名为 rdp-sxs 的条目可用。 如果没有，VM 上不存在并排堆栈，因此问题不会与并行堆栈相关联。

    ![管理员命令提示](media/AdministratorCommandPrompt.png)

9. 运行以下命令，该命令将列出安装在 VM 上的 Microsoft 组件，并附带故障的并排堆栈。

    ```cmd
        wmic product get name
    ```

10. 运行下面的命令，从上面的步骤中使用产品名称。

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. 卸载以"远程桌面"开头的所有产品。

12. 卸载所有 Windows 虚拟桌面组件后，请按照操作系统的说明操作：

13. 如果操作系统是 Windows Server，请重新启动并排堆栈出现故障的 VM（使用 Azure 门户或从 PsExec 工具）。

如果您的操作系统是 Microsoft Windows 10，请继续执行以下说明：

14. 从运行 PsExec 的 VM、打开的文件资源管理器和复制禁用sxsstackrc.ps1到 VM 的系统驱动器，并附带故障的堆栈。

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname 是 VM 的机名，并排堆栈出现故障。

15. 建议的过程：从 PsExec 工具启动 PowerShell 并从上一步导航到文件夹，并运行禁用 ssstackrc.ps1。 或者，您可以运行以下 cmdlet：

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. 运行完 cmdlet 后，使用故障并排堆栈重新启动 VM。

## <a name="remote-desktop-licensing-mode-isnt-configured"></a>未配置远程桌面许可模式

如果您使用管理帐户登录到 Windows 10 企业多会话，您可能会收到一条通知，指出"远程桌面许可模式未配置，远程桌面服务将在 X 天内停止工作。 在连接代理服务器上，使用服务器管理器指定远程桌面许可模式。

如果时间限制到期，将显示一条错误消息，指出"远程会话已断开连接，因为此计算机没有可用的远程桌面客户端访问许可证。

如果您看到这些消息中的任何一条，这意味着映像没有安装最新的 Windows 更新，或者您正在通过组策略设置远程桌面许可模式。 按照以下各节中的步骤检查组策略设置、标识 Windows 10 企业多会话的版本以及安装相应的更新。  

>[!NOTE]
>当主机池包含 Windows 服务器会话主机时，Windows 虚拟桌面仅需要 RDS 客户端访问许可证 （CAL）。 要了解如何配置 RDS CAL，请参阅[使用客户端访问许可证许可 RDS 部署](/windows-server/remote/remote-desktop-services/rds-client-access-license/)。

### <a name="disable-the-remote-desktop-licensing-mode-group-policy-setting"></a>禁用远程桌面许可模式组策略设置

通过在 VM 中打开组策略编辑器并导航到**管理模板** > **Windows 组件** > **远程桌面服务远程** > **桌面会话主机** > **许可** > **设置远程桌面许可模式**，检查组策略设置。 如果组策略设置为 **"已启用"，** 则将其更改为 **"已禁用**"。 如果已禁用，则保留其正当时状态。

>[!NOTE]
>如果通过域设置组策略，请禁用针对这些 Windows 10 企业多会话 VM 的策略上的此设置。

### <a name="identify-which-version-of-windows-10-enterprise-multi-session-youre-using"></a>标识您使用的 Windows 10 企业多会话的版本

要检查哪个版本的 Windows 10 企业多会话，请进行：

1. 使用管理员帐户登录。
2. 在"开始"菜单旁边的搜索栏中输入"关于"。
3. 选择 **"关于你的电脑**"。
4. 检查"版本"旁边的数字。 数字应为"1809"或"1903"，如下图所示。

    ![Windows 规范窗口的屏幕截图。 版本号以蓝色突出显示。](media/windows-specifications.png)

现在，您已经了解了版本号，请跳到相关部分。

### <a name="version-1809"></a>版本 1809

如果您的版本号显示"1809"，请安装[KB4516077 更新](https://support.microsoft.com/help/4516077)。

### <a name="version-1903"></a>版本 1903

使用最新版本的 Windows 10 版本 1903 映像从 Azure 库重新部署主机操作系统。

## <a name="next-steps"></a>后续步骤

- 有关 Windows 虚拟桌面和升级跟踪的故障排除概述，请参阅[故障排除概述、反馈和支持](troubleshoot-set-up-overview.md)。
- 要在 Windows 虚拟桌面环境中创建租户和主机池时解决问题，请参阅[租户和主机池创建](troubleshoot-set-up-issues.md)。
- 要在 Windows 虚拟桌面中配置虚拟机 （VM） 时解决问题，请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration.md)。
- 要解决 Windows 虚拟桌面客户端连接的问题，请参阅[Windows 虚拟桌面服务连接](troubleshoot-service-connection.md)。
- 要解决远程桌面客户端的问题，请参阅[排除远程桌面客户端的故障](troubleshoot-client.md)
- 要解决在 Windows 虚拟桌面中使用 PowerShell 时的问题，请参阅[Windows 虚拟桌面电源外壳](troubleshoot-powershell.md)。
- 要了解有关该服务的更多信息，请参阅[Windows 虚拟桌面环境](environment-setup.md)。
- 要完成疑难解答教程，请参阅[教程：解决资源管理器模板部署的疑难解答](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
- 若要了解审核操作，请参阅[使用 Resource Manager 执行审核操作](../azure-resource-manager/management/view-activity-logs.md)。
- 若要了解部署期间为确定错误需要执行哪些操作，请参阅[查看部署操作](../azure-resource-manager/templates/deployment-history.md)。
