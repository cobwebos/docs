---
title: Windows 虚拟桌面-Azure 中的租户和主机池创建
description: 如何在 Windows 虚拟桌面环境中配置租户和会话主机虚拟机 (VM) 时解决问题。
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: v-chjenk
ms.openlocfilehash: 96a9d8fc7495ea473b0a3250b34251afc5f30c13
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786706"
---
# <a name="tenant-and-host-pool-creation"></a>创建租户和主机池

使用本文配置 Windows 虚拟桌面会话主机虚拟机 (Vm) 时遇到的问题进行疑难解答。

## <a name="provide-feedback"></a>提供反馈

目前我们不会受理 Windows 虚拟桌面预览版的支持案例。 请访问 [Windows 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，与产品团队和活跃的社区成员共同探讨 Windows 虚拟桌面服务。

## <a name="vms-are-not-joined-to-the-domain"></a>Vm 未加入到域

如果遇到问题，将 Vm 加入到域，请按照这些说明。

- 使用中的过程手动将 VM 加入[Windows Server 虚拟机加入托管域](https://docs.microsoft.com/azure/active-directory-domain-services/Active-directory-ds-admin-guide-join-windows-vm-portal)或使用[域联接模板](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)。
- 请尝试 ping 在 VM 上的命令行中的域名。
- 查看域联接中的错误消息列表[故障排除域联接错误消息](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx)。

### <a name="error-incorrect-credentials"></a>错误：不正确的凭据

原因：  出现拼写错误时输入凭据在 Azure 资源管理器模板界面修补程序。

**解决方法：** 按照这些说明来更正凭据。

1. 手动将 Vm 添加到域。
2. 重新部署后已确认凭据。 请参阅[使用 PowerShell 创建主机池](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell)。
3. 将 Vm 加入到域使用的模板[联接到 AD 域的现有 Windows VM](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)。

### <a name="error-timeout-waiting-for-user-input"></a>错误：等待用户输入时超时

原因：  用于完成域加入帐户可能具有多重身份验证 (MFA)。

**解决方法：** 按照这些说明完成域加入。

1. 暂时删除 MFA 的帐户。
2. 使用服务帐户。

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>错误：在预配过程中使用的帐户没有权限完成操作

原因：  所使用的帐户没有权限将 Vm 加入到域由于符合性和法规的约束。

**解决方法：** 按照这些说明进行操作。

1. 使用该帐户是管理员组的成员。
2. 授予对所使用的帐户所需的权限。

### <a name="error-domain-name-doesnt-resolve"></a>错误：未解析的域名

**原因 1：** Vm 是不与域所在的位置的虚拟网络 (VNET) 关联的资源组中。

**修复 1:** 创建 VNET 对等互连的 VNET 已预配 Vm 和 VNET 运行域控制器 (DC) 之间。 请参阅[创建虚拟网络对等互连-资源管理器，不同的订阅](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions)。

**原因 2：** 当使用 AadService (AADS) 时，尚未设置 DNS 条目。

**修复 2:** 若要设置域服务，请参阅[启用 Azure Active Directory 域服务](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns)。

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>未安装 Windows 的虚拟桌面代理和 Windows 虚拟桌面启动加载器

预配 Vm 的建议的方法使用 Azure 资源管理器**创建和预配 Windows 虚拟桌面托管池**模板。 该模板会自动安装的 Windows 虚拟桌面代理和 Windows 虚拟桌面代理启动加载程序。

按照这些说明进行操作以确认安装了组件并检查错误消息。

1. 确认通过检查是否安装了两个组件**Control Panel** > **程序** > **程序和功能**。 如果**Windows 的虚拟桌面代理**并**Windows 虚拟桌面代理启动加载器**不可见，它们不安装在 VM 上。
2. 打开**文件资源管理器**并导航到**C:\Windows\Temp\scriptlogs.log**。 如果缺少该文件，则表示安装了两个组件的 PowerShell DSC 程序无法提供的安全上下文中运行。
3. 如果该文件**C:\Windows\Temp\scriptlogs.log**是否存在，将其打开并检查错误消息。

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptlogslog-is-also-missing"></a>错误：缺少 Windows 虚拟桌面代理和 Windows 虚拟桌面代理启动加载程序。 C:\Windows\Temp\scriptlogs.log is also missing

**原因 1：** Azure 资源管理器模板在输入过程中提供的凭据不正确或权限不足。

**修复 1:** 手动将缺少的组件添加到使用在虚拟机[使用 PowerShell 创建主机池](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell)。

**原因 2：** PowerShell DSC 能够启动并执行，但无法完成，因为它不能登录到 Windows 虚拟桌面，并获取所需的信息。

**修复 2:** 确认以下列表中的项。

- 请确保该帐户不具有 MFA。
- 确认租户名称准确，并且租户存在于 Windows 的虚拟桌面。
- 确认该帐户至少具有 RDS 参与者权限。

### <a name="error-authentication-failed-error-in-cwindowstempscriptlogslog"></a>错误：身份验证失败，错误 C:\Windows\Temp\scriptlogs.log 中

原因：  PowerShell DSC 能够执行，但无法连接到 Windows 虚拟桌面。

**解决方法：** 确认以下列表中的项。

- 手动将 Vm 注册到 Windows 虚拟机服务。
- 确认为连接到 Windows 虚拟机使用的帐户在租户创建主机池上具有的权限。
- 确认帐户不具备 MFA。

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>Windows 虚拟桌面代理不 Windows 虚拟机服务中注册

在会话上首次安装 Windows 的虚拟桌面代理托管虚拟机 （或者手动或通过 Azure 资源管理器模板和 PowerShell DSC），它提供的注册令牌。 以下部分包含适用于 Windows 的虚拟桌面代理和令牌问题故障排除。

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>错误：Get RdsSessionHost cmdlet 中字段的状态显示为不可用状态

![Get RdsSessionHost cmdlet 将状态显示为不可用。](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

原因：  代理不能自行更新到新版本。

**解决方法：** 按照以下说明手动更新代理。

1. 下载会话主机 VM 上的代理的新版本。
2. 启动任务管理器，并在服务选项卡上，停止 RDAgentBootLoader 服务。
3. 运行 Windows 的虚拟桌面代理的新版本的安装程序。
4. 当系统提示输入注册令牌，删除该条目 INVALID_TOKEN 并按下一步 （新的令牌不是必需）。
5. 完成安装向导。
6. 打开任务管理器并启动 RDAgentBootLoader 服务。

## <a name="error--windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>错误：Windows 虚拟桌面代理注册表项 IsRegistered 显示值为 0

原因：  注册令牌已过期或已生成的 999999 的过期值。

**解决方法：** 请按照这些说明，修改注册表错误的代理。

1. 如果已存在的注册令牌，其与删除 RDSRegistrationInfo 将其删除。
2. 生成新令牌 Rds NewRegistrationInfo。
3. 确认-ExpriationHours 参数已设置为 72 （最大值为 99999）。

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>错误：Windows 虚拟桌面代理不运行 Get RdsSessionHost 时进行报告检测信号

**原因 1：** RDAgentBootLoader 服务已停止。

**修复 1:** 启动任务管理器和服务选项卡报告 RDAgentBootLoader 服务已停止的状态，如果启动该服务。

**原因 2：** 可能会关闭端口 443。

**修复 2:** 按照以下说明打开端口 443。

1. 确认端口 443 处于打开状态的下载中的 PSPing 工具[Sysinternal 工具](https://docs.microsoft.com/sysinternals/downloads/psping)。
2. 会话主机代理正在运行的 VM 上安装 PSPing。
3. 打开命令提示符下以管理员身份，并发出以下命令：

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. 确认从 RDBroker 返回该 PSPing 收到信息：

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

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>Windows 虚拟桌面的并行堆栈的故障排除问题

随 Windows Server 2019 自动安装的 Windows 虚拟桌面的并行堆栈。 使用 Microsoft Installer (MSI) 安装 Microsoft Windows Server 2016 或 Windows Server 2012 R2 上的并行堆栈。 适用于 Microsoft Windows 10 的 Windows 虚拟桌面的并行堆栈启用了**enablesxstackrs.ps1**。

有三种主要方式通过并行堆栈获取安装或启用会话主机池 Vm 上：

- 使用 Azure 资源管理器**创建和预配新的 Windows 虚拟机主机池**模板
- 通过所包含并在主映像上启用
- 安装或手动启用每个 VM 上 （或扩展/PowerShell）

如果遇到问题的 Windows 虚拟桌面的并行堆栈，请键入**qwinsta**命令从命令提示符下，若要确认安装或启用通过并行堆栈。

输出**qwinsta**将列出**rdp sxs**时安装并启用了通过并行堆栈输出中。

![通过并行堆栈安装或启用了 qwinsta 作为 rdp sxs 输出中列出。](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

检查下面列出的注册表项，并确认它们的值匹配。 如果注册表项缺失或值不匹配，请按照中的说明[使用 PowerShell 创建主机池](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell)如何重新安装通过并行堆栈上。

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-oreverseconnectstackfailure"></a>错误：O_REVERSE_CONNECT_STACK_FAILURE

![O_REVERSE_CONNECT_STACK_FAILURE 错误代码。](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

原因：  通过并行堆栈会话主机 VM 上未安装。

**解决方法：** 按照这些说明在会话主机 VM 上安装的并行堆栈。

1. 使用远程桌面协议 (RDP) 直接进入会话主机 VM 作为本地管理员。
2. 下载并导入[的 Windows 虚拟桌面 PowerShell 模块](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)如果尚未在 PowerShell 会话中使用。
3. 通过并行堆栈使用安装[使用 PowerShell 创建主机池](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell)。

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>如何修复工作不正常的 Windows 虚拟桌面的并行堆栈

存在一些已知情况下，可能会导致通过并行堆栈无法正常工作：

- 没有遵循正确的顺序的步骤来启用通过并行堆栈
- 自动更新到 Windows 10 增强通用光盘 (EVD)
- 缺少的远程桌面会话主机 (RDSH) 角色
- 多次运行 enablesxsstackrc.ps1
- 在帐户中运行 enablesxsstackrc.ps1 不具有本地管理员权限

在本部分中的说明可帮助你卸载 Windows 虚拟桌面的并行堆栈。 一旦卸载通过并行堆栈，请转到"VM 注册到 Windows 虚拟桌面主机池"[使用 PowerShell 创建主机池](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell)重新安装通过并行堆栈。

用于运行修正的 VM 必须与运行不正常的并行堆栈与 VM 相同的子网和域。

请按照这些说明从同一子网和域运行修正：

1. 从应用修补程序的位置，与标准远程桌面协议 (RDP) 连接到 VM。
2. 下载 PsExec 从 https://docs.microsoft.com/sysinternals/downloads/psexec 。
3. 解压缩下载的文件。
4. 以本地管理员身份启动命令提示符。
5. 导航到 PsExec 已解压缩的文件夹。
6. 在命令提示符下，使用以下命令：

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!Note]
    >VMname 是运行不正常的并行堆栈的 VM 的计算机名称。

7. 通过单击同意接受 PsExec 许可协议。

    ![软件许可协议的屏幕截图。](media/SoftwareLicenseTerms.png)

    >[!Note]
    >只在首次运行 PsExec 会显示此对话框。

8. 与运行不正常的并行堆栈在 VM 上打开命令提示符会话后，运行 qwinsta 并确认一个名为 rdp sxs 词条中可用。 如果没有，通过并行堆栈不存在的 VM 上，因此该问题不能局限于通过并行堆栈。

    ![管理员命令提示符](media/AdministratorCommandPrompt.png)

9. 运行以下命令，将列出安装在运行不正常的并行堆栈的 VM 上的 Microsoft 组件。

    ```cmd
        wmic product get name
    ```

10. 从前面的步骤，请与产品名称中运行以下命令。

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. 卸载开始使用"远程桌面。"的所有产品

12. 已卸载所有的 Windows 虚拟桌面组件后，请按照适用于操作系统的说明进行操作：

13. 如果你的操作系统为 Windows Server，重启 VM，必须运行不正常的并行堆栈 （不管是使用 Azure 门户或从 PsExec 工具）。

如果你的操作系统为 Microsoft Windows 10，继续学习下面的说明：

14. 从运行 PsExec 的 VM，打开文件资源管理器并将 disablesxsstackrc.ps1 复制到 malfunctioned 通过并行堆栈的 VM 的系统驱动器。

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname 是运行不正常的并行堆栈的 VM 的计算机名称。

15. 建议采用的过程： 从 PsExec 工具，启动 PowerShell 并从上一步导航到的文件夹并运行 disablesxsstackrc.ps1。 或者，可以运行以下 cmdlet:

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. 完成 cmdlet 运行，请重新启动工作不正常的并行堆栈的 VM。

## <a name="remote-licensing-model-is-not-configured"></a>未配置远程许可模型

如果登录到 Windows 10 企业版多会话使用的管理帐户时，可能会收到通知，指出:"未配置远程桌面授权模式，远程桌面服务将停止工作，在 X 天。 在连接代理服务器，使用服务器管理器指定的远程桌面授权模式。" 如果看到此消息，这意味着您需要手动配置到的授权模式**每个用户**。

若要手动配置的授权模式：  

1. 转到您**开始菜单**搜索框中，然后查找并打开**gpedit.msc**若要访问本地组策略编辑器。 
2. 转到 **计算机配置** > **管理模板** > **Windows 组件** >  **远程桌面服务** > **远程桌面会话主机** > **许可**。 
3. 选择**设置远程桌面授权模式**并将其更改为**每个用户**。

我们目前正在研究通知及宽限期内超时问题，并计划在将来的更新中解决它们。 

## <a name="next-steps"></a>后续步骤

- 有关故障排除 Windows 虚拟桌面，升级进行跟踪的概述，请参阅[故障排除概述、 反馈和支持](troubleshoot-set-up-overview.md)。
- 若要在 Windows 虚拟桌面环境中创建的租户和主机池时排查问题，请参阅[租户以及主机池创建](troubleshoot-set-up-issues.md)。
- 若要在 Windows 虚拟机中配置虚拟机 (VM) 时解决问题，请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration.md)。
- 若要对 Windows 虚拟桌面客户端连接的问题进行故障排除，请参阅[远程桌面客户端连接](troubleshoot-client-connection.md)。
- 若要排查问题时使用 PowerShell 管理 Windows 虚拟桌面，请参阅[Windows 虚拟桌面 PowerShell](troubleshoot-powershell.md)。
- 若要了解有关预览服务的详细信息，请参阅[Windows Desktop 预览环境](https://docs.microsoft.com/azure/virtual-desktop/environment-setup)。
- 若要完成故障排除教程，请参阅[教程：对资源管理器模板部署进行故障排除](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)。
- 若要了解审核操作，请参阅[使用 Resource Manager 执行审核操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)。
- 若要了解部署期间为确定错误需要执行哪些操作，请参阅[查看部署操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations)。