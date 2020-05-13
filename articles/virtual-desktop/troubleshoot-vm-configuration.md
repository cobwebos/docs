---
title: Windows 虚拟桌面会话主机故障排除-Azure
description: 如何解决在配置 Windows 虚拟桌面会话主机虚拟机时遇到的问题。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1e4428fecdbb5d664111adc591812a5122bf2eda
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125108"
---
# <a name="session-host-virtual-machine-configuration"></a>会话主机虚拟机配置

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Windows 虚拟桌面对象的 2020 春季更新版。 如果你使用的是不包含 Azure 资源管理器对象的 Windows 虚拟桌面 2019 秋季版，请参阅[此文](./virtual-desktop-fall-2019/troubleshoot-vm-configuration-2019.md)。
>
> Windows 虚拟桌面 2020 春季更新版目前为公共预览版。 此预览版未提供服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文介绍配置 Windows 虚拟桌面会话主机虚拟机（Vm）时遇到的问题。

## <a name="provide-feedback"></a>提供反馈

请访问 [Windows 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，与产品团队和活跃的社区成员共同探讨 Windows 虚拟桌面服务。

## <a name="vms-are-not-joined-to-the-domain"></a>Vm 未加入域

如果在将虚拟机（Vm）加入到域时遇到问题，请按照这些说明进行操作。

- 使用将[Windows Server 虚拟机加入到托管域](../active-directory-domain-services/join-windows-vm.md)或使用[域加入模板](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)中的过程，手动联接 VM。
- 尝试从 VM 上的命令行 ping 域名。
- 在[排查域加入错误消息](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx)中查看域加入错误消息列表。

### <a name="error-incorrect-credentials"></a>错误：凭据不正确

**原因：** 在 Azure 资源管理器模板接口修复中输入凭据时，出现了一个错误。

**修复：** 执行下列操作之一来解决。

- 手动将 Vm 添加到域。
- 确认凭据后，重新部署模板。 请参阅[使用 PowerShell 创建主机池](create-host-pools-powershell.md)。
- 使用将[现有 WINDOWS VM 加入 AD 域](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)的模板将 vm 加入域。

### <a name="error-timeout-waiting-for-user-input"></a>错误：等待用户输入超时

**原因：** 用于完成域加入的帐户可能具有多重身份验证（MFA）。

**修复：** 执行下列操作之一来解决。

- 暂时删除帐户的 MFA。
- 使用服务帐户。

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>错误：在预配过程中使用的帐户不具有完成此操作的权限

**原因：** 由于合规性和法规要求，所使用的帐户没有将 Vm 加入到域中的权限。

**修复：** 执行下列操作之一来解决。

- 使用作为管理员组成员的帐户。
- 向所使用的帐户授予必要的权限。

### <a name="error-domain-name-doesnt-resolve"></a>错误：域名不能解析

**原因1：** Vm 位于与域所在的虚拟网络（VNET）不关联的虚拟网络上。

**修复1：** 在配置了 Vm 的 VNET 和运行域控制器（DC）的 VNET 之间创建 VNET 对等互连。 请参阅[创建虚拟网络对等互连-资源管理器、不同订阅](../virtual-network/create-peering-different-subscriptions.md)。

**原因2：** 使用 Azure Active Directory 域服务（Azure AD DS）时，不会将虚拟网络的 DNS 服务器设置更新为指向托管域控制器。

**修复2：** 若要更新包含 Azure AD DS 的虚拟网络的 DNS 设置，请参阅[更新 Azure 虚拟网络的 dns 设置](../active-directory-domain-services/tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)。

**原因3：** 网络接口的 DNS 服务器设置未指向虚拟网络上适当的 DNS 服务器。

**修复3：** 按照 [更改 DNS 服务器] 中的步骤操作，执行以下操作之一以解决此问题。
- 将网络接口的 DNS 服务器设置更改为 "**自定义**"，并执行 "[更改 dns 服务器](../virtual-network/virtual-network-network-interface.md#change-dns-servers)" 和 "指定虚拟网络上 DNS 服务器的专用 IP 地址" 中的步骤。
- 更改网络接口的 DNS 服务器设置，使其从 "[更改 dns 服务器](../virtual-network/virtual-network-network-interface.md#change-dns-servers)" 中的步骤**继承**，然后更改虚拟网络的 dns 服务器设置和 "[更改 dns 服务器](../virtual-network/manage-virtual-network.md#change-dns-servers)" 中的步骤。

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>未安装 windows 虚拟桌面代理和 Windows 虚拟桌面启动加载程序

用于预配 Vm 的建议方法是使用 Azure 门户创建模板。 该模板会自动安装 Windows 虚拟桌面代理和 Windows 虚拟桌面代理启动加载程序。

按照以下说明确认是否已安装组件并检查是否有错误消息。

1. 检查 "**控制面板" "程序" "** 程序  >  **Programs**  >  **和功能**"，确认是否已安装这两个组件。 如果**Windows 虚拟桌面代理**和**Windows 虚拟桌面代理启动加载程序**不可见，则不会在 VM 上安装它们。
2. 打开**文件资源管理器**并导航到**C:\Windows\Temp\ScriptLog.log**。 如果缺少该文件，则表示安装了这两个组件的 PowerShell DSC 无法在提供的安全上下文中运行。
3. 如果文件**C:\Windows\Temp\ScriptLog.log**存在，请将其打开，并检查错误消息。

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptloglog-is-also-missing"></a>错误：缺少 Windows 虚拟桌面代理和 Windows 虚拟桌面代理启动加载程序。 也缺少 C:\Windows\Temp\ScriptLog.log

**原因1：** 在 Azure 资源管理器模板的输入过程中提供的凭据不正确或权限不足。

**修复1：** 使用 "[使用 PowerShell 创建主机池](create-host-pools-powershell.md)"，手动将缺少的组件添加到 vm。

**原因2：** PowerShell DSC 能够启动并执行，但无法完成，因为它无法登录到 Windows 虚拟桌面并获得所需的信息。

**修复2：** 确认以下列表中的项。

- 请确保该帐户没有 MFA。
- 确认主机池的名称正确，并且主机池在 Windows 虚拟桌面中存在。
- 确认该帐户至少对 Azure 订阅或资源组具有 "参与者" 权限。

### <a name="error-authentication-failed-error-in-cwindowstempscriptloglog"></a>错误：身份验证失败，C:\Windows\Temp\ScriptLog.log 中出错

**原因：** PowerShell DSC 可以执行，但无法连接到 Windows 虚拟桌面。

**修复：** 确认以下列表中的项。

- 手动向 Windows 虚拟桌面服务注册 Vm。
- 用于连接到 Windows 虚拟桌面的确认帐户对 Azure 订阅或资源组具有创建主机池的权限。
- 确认帐户没有 MFA。

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>Windows 虚拟桌面代理未向 Windows 虚拟桌面服务注册

当首次在会话主机 Vm 上安装 Windows 虚拟桌面代理（手动或通过 Azure 资源管理器模板和 PowerShell DSC）时，它将提供注册令牌。 以下部分介绍适用于 Windows 虚拟桌面代理和令牌的疑难解答问题。

### <a name="error-the-status-filed-in-get-azwvdsessionhost-cmdlet-shows-status-as-unavailable"></a>错误： AzWvdSessionHost cmdlet 中存档的状态显示为 "不可用"

![AzWvdSessionHost cmdlet 将状态显示为 "不可用"。](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**原因：** 代理无法将自身更新为新版本。

**修复：** 按照这些说明手动更新代理。

1. 在会话主机 VM 上下载新版本的代理。
2. 启动任务管理器，在 "服务" 选项卡上，停止 RDAgentBootLoader 服务。
3. 运行 Windows 虚拟桌面代理新版本的安装程序。
4. 当系统提示输入注册令牌时，删除条目 INVALID_TOKEN，然后按 "下一步" （不需要新的令牌）。
5. 完成安装向导。
6. 打开任务管理器并启动 RDAgentBootLoader 服务。

## <a name="error-windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>错误： Windows 虚拟桌面代理注册表项 IsRegistered 显示值0

**原因：** 注册令牌已过期，或已生成，其到期值为999999。

**修复：** 按照以下说明修复代理注册表错误。

1. 如果已有注册令牌，请使用 AzWvdRegistrationInfo 将其删除。 
2. 运行**AzWvdRegistrationInfo** cmdlet 以生成新令牌。 
3. 确认 *-ExpriationTime*参数设置为3天。

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-azwvdsessionhost"></a>错误：运行 AzWvdSessionHost 时 Windows 虚拟桌面代理未报告检测信号

**原因1：** RDAgentBootLoader 服务已停止。

**修复1：** 启动任务管理器，如果 "服务" 选项卡报告 RDAgentBootLoader 服务的已停止状态，则启动该服务。

**原因2：** 可能会关闭端口443。

**修复2：** 按照以下说明打开端口443。

1. 从[Sysinternal 工具](/sysinternals/downloads/psping/)下载 PSPing 工具，确认端口443已打开。
2. 在运行代理的会话主机 VM 上安装 PSPing。
3. 以管理员身份打开命令提示符，并发出以下命令：

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. 确认 PSPing 收到来自 RDBroker 的信息：

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

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>排查 Windows 虚拟桌面并行堆栈问题

Windows 虚拟桌面并行堆栈随 Windows Server 2019 自动安装。 使用 Microsoft Installer （MSI）在 Microsoft Windows Server 2016 或 Windows Server 2012 R2 上安装并行堆栈。 对于 Microsoft Windows 10，将使用**enablesxstackrs**启用 Windows 虚拟桌面并行堆栈。

在会话主机池 Vm 上安装或启用并行堆栈的主要方式有三种：

- 与 Azure 门户创建模板
- 通过在主映像上包含和启用
- 已在每个 VM 上手动安装或启用（或通过扩展/PowerShell）

如果你遇到 Windows 虚拟桌面并行堆栈问题，请在命令提示符下键入**qwinsta**命令，以确认已安装或启用并行堆栈。

如果安装并启用了并列堆栈，则**qwinsta**的输出将在输出中列出**rdp-sxs** 。

![已安装或启用并行堆栈，其中 qwinsta 在输出中列为 rdp-sxs。](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

检查下面列出的注册表项，并确认它们的值是否匹配。 如果缺少注册表项或值不匹配，请按照如何重新安装并行堆栈中的[使用 PowerShell 创建主机池](create-host-pools-powershell.md)中的说明进行操作。

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-o_reverse_connect_stack_failure"></a>错误： O_REVERSE_CONNECT_STACK_FAILURE

![O_REVERSE_CONNECT_STACK_FAILURE 错误代码。](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**原因：** 并行堆栈未安装在会话主机 VM 上。

**修复：** 按照这些说明在会话主机 VM 上安装并行堆栈。

1. 使用远程桌面协议（RDP）作为本地管理员直接加入会话主机 VM。
2. 使用 "使用[PowerShell 创建主机池](create-host-pools-powershell.md)" 安装并行堆栈。

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>如何修复发生故障的 Windows 虚拟桌面并行堆栈

在某些情况下，可能会导致并列堆栈不正常：

- 不遵循正确的步骤顺序来启用并排堆栈
- 自动更新到 Windows 10 增强的多功能光盘（EVD）
- 缺少远程桌面会话主机（RDSH）角色
- 多次运行 enablesxsstackrc
- 在没有本地管理员权限的帐户中运行 enablesxsstackrc

本节中的说明可帮助你卸载 Windows 虚拟桌面并行堆栈。 卸载并列堆栈后，请在[使用 PowerShell 创建主机池](create-host-pools-powershell.md)中的 "使用 Windows 虚拟机主机池注册 VM" 中，使用安装并行堆栈。

用于运行修正的 VM 必须与具有故障并行堆栈的 VM 位于同一子网和域中。

按照以下说明从同一子网和域运行修正：

1. 将与标准远程桌面协议（RDP）连接到将应用修复的 VM。
2. 从下载 PsExec https://docs.microsoft.com/sysinternals/downloads/psexec 。
3. 解压缩下载的文件。
4. 以本地管理员身份启动命令提示符。
5. 导航到在其中解压缩了 PsExec 的文件夹。
6. 在命令提示符下，使用以下命令：

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!Note]
    >VMname 是并行堆栈不正常的 VM 的计算机名称。

7. 单击 "同意" 以接受 PsExec 许可协议。

    ![软件许可协议屏幕截图。](media/SoftwareLicenseTerms.png)

    >[!Note]
    >此对话框仅在第一次运行 PsExec 时才会显示。

8. 在具有故障的并行堆栈的虚拟机上打开命令提示会话后，请运行 qwinsta 并确认名为 rdp-sxs 的条目可用。 如果没有，则并行堆栈不存在于 VM 上，因此该问题不会与并行堆栈相关联。

    ![管理员命令提示符](media/AdministratorCommandPrompt.png)

9. 运行以下命令，该命令将列出并行堆栈出现故障的 VM 上安装的 Microsoft 组件。

    ```cmd
        wmic product get name
    ```

10. 运行以下命令，并在上面的步骤中运行产品名称。

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. 卸载所有以 "远程桌面" 开头的产品。

12. 卸载所有 Windows 虚拟桌面组件后，请按照适用于你的操作系统的说明进行操作：

13. 如果操作系统是 Windows Server，请重新启动具有故障的并行堆栈的 VM （使用 Azure 门户或来自 PsExec 工具）。

如果操作系统是 Microsoft Windows 10，请继续执行以下说明：

14. 从运行 PsExec 的 VM，打开文件资源管理器，将 disablesxsstackrc 复制到 VM 的系统驱动器中，并行堆栈不正常。

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname 是并行堆栈不正常的 VM 的计算机名称。

15. 建议的过程：从 PsExec 工具启动 PowerShell 并导航到上一步骤中的文件夹并运行 disablesxsstackrc。 或者，你可以运行以下 cmdlet：

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. Cmdlet 运行完毕后，请使用不正常的并行堆栈重新启动 VM。

## <a name="remote-desktop-licensing-mode-isnt-configured"></a>未配置远程桌面授权模式

如果使用管理帐户登录到 Windows 10 企业多会话，你可能会收到一条通知，显示 "远程桌面授权模式未配置，远程桌面服务将在 X 天内停止工作。 在连接代理服务器上，使用服务器管理器指定远程桌面授权模式。 "

如果时间限制到期，将出现一条错误消息，显示 "远程会话已断开连接，因为没有可用于此计算机的远程桌面客户端访问许可证"。

如果看到这些消息中的任何一个，则表示该映像未安装最新的 Windows 更新，或者你正在通过组策略设置远程桌面授权模式。 按照以下部分中的步骤检查组策略设置，确定 Windows 10 企业多会话版本，并安装相应的更新。  

>[!NOTE]
>当主机池包含 Windows Server 会话主机时，Windows 虚拟桌面只需要 RDS 客户端访问许可证（CAL）。 若要了解如何配置 RDS CAL，请参阅[使用客户端访问许可证为你的 RDS 部署提供许可](/windows-server/remote/remote-desktop-services/rds-client-access-license/)。

### <a name="disable-the-remote-desktop-licensing-mode-group-policy-setting"></a>禁用 "远程桌面授权模式" 组策略设置

通过在 VM 中打开组策略编辑器并导航到**管理模板**Windows 组件 "，然后导航到" 组策略 "设置  >  **Windows Components**  >  **远程桌面服务**  >  **远程桌面会话主机**  >  **Licensing**  >  **" 授权 "设置" 远程桌面授权 "模式**。 如果 "组策略" 设置已**启用**，请将其更改为 "**已禁用**"。 如果已禁用，则将其保持原样。

>[!NOTE]
>如果通过域设置组策略，则在面向这些 Windows 10 企业多会话 Vm 的策略上禁用此设置。

### <a name="identify-which-version-of-windows-10-enterprise-multi-session-youre-using"></a>确定要使用的 Windows 10 企业多会话版本

若要检查 Windows 10 企业版多会话版本，请执行以下操作：

1. 用管理员帐户登录。
2. 在 "开始" 菜单旁边的搜索栏中输入 "关于"。
3. 选择 "**关于电脑**"。
4. 检查 "版本" 旁边的编号。 该数字应为 "1809" 或 "1903"，如下图所示。

    ![Windows 规范窗口的屏幕截图。 版本号以蓝色突出显示。](media/windows-specifications.png)

了解版本号后，请跳到相关部分。

### <a name="version-1809"></a>版本1809

如果版本号显示 "1809"，则安装[KB4516077 更新](https://support.microsoft.com/help/4516077)。

### <a name="version-1903"></a>版本1903

通过 Azure 库中的最新版本的 Windows 10 版本1903映像重新部署主机操作系统。

## <a name="we-couldnt-connect-to-the-remote-pc-because-of-a-security-error"></a>由于安全错误，无法连接到远程电脑

如果用户看到一条错误消息，"我们无法连接到远程 PC，因为出现安全错误。 如果此问题仍然存在，请向管理员或技术支持人员求助于 "验证更改默认 RDP 权限的任何现有策略"。 可能导致此错误的一种策略是 "允许通过远程桌面服务安全策略登录"。

若要了解有关此策略的详细信息，请参阅[允许通过远程桌面服务登录](/windows/security/threat-protection/security-policy-settings/allow-log-on-through-remote-desktop-services)。

## <a name="next-steps"></a>后续步骤

- 有关 Windows 虚拟桌面故障排除和升级跟踪的概述，请参阅[故障排除概述、反馈和支持](troubleshoot-set-up-overview.md)。
- 若要在 Windows 虚拟桌面环境中创建主机池时排查问题，请参阅[环境和主机池创建](troubleshoot-set-up-issues.md)。
- 若要解决在 Windows 虚拟桌面中配置虚拟机（VM）时遇到的问题，请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration.md)。
- 若要解决 Windows 虚拟桌面客户端连接问题，请参阅[Windows 虚拟桌面服务连接](troubleshoot-service-connection.md)。
- 若要解决远程桌面客户端的问题，请参阅[排查远程桌面客户端](troubleshoot-client.md)问题
- 若要解决将 PowerShell 与 Windows 虚拟桌面结合使用时遇到的问题，请参阅[Windows 虚拟桌面 PowerShell](troubleshoot-powershell.md)。
- 若要了解有关该服务的详细信息，请参阅[Windows 虚拟桌面环境](environment-setup.md)。
- 若要浏览疑难解答教程，请参阅[教程：排查资源管理器模板部署问题](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
- 若要了解审核操作，请参阅[使用 Resource Manager 执行审核操作](../azure-resource-manager/management/view-activity-logs.md)。
- 若要了解部署期间为确定错误需要执行哪些操作，请参阅[查看部署操作](../azure-resource-manager/templates/deployment-history.md)。
