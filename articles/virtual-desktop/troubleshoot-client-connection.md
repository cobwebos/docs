---
title: Windows 虚拟桌面中的远程桌面客户端连接-Azure
description: 如何解决在 Windows 虚拟桌面租户环境中设置客户端连接时遇到的问题。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
ms.openlocfilehash: 4f325d9fc512fd9f6776fcd799b720aaf60ce472
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876762"
---
# <a name="remote-desktop-client-connections"></a>远程桌面客户端连接

使用本文解决 Windows 虚拟桌面客户端连接问题。

## <a name="provide-feedback"></a>提供反馈

目前我们不会受理 Windows 虚拟桌面预览版的支持案例。 请访问 [Windows 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，与产品团队和活跃的社区成员共同探讨 Windows 虚拟桌面服务。

## <a name="you-cant-open-a-web-client"></a>无法打开 web 客户端

打开另一个网站以确认是否有 internet 连接;例如, [www.Bing.com](https://www.bing.com)。

使用**nslookup**确认 DNS 可以解析 FQDN:

```cmd
nslookup rdweb.wvd.microsoft.com
```

尝试与其他客户端 (如 Windows 7 或 Windows 10 的远程桌面客户端) 进行连接, 并检查是否可以打开 web 客户端。

### <a name="error-opening-another-site-fails"></a>错误：打开另一个站点失败

原因：网络问题和/或中断。

**能够**联系网络支持。

### <a name="error-nslookup-cannot-resolve-the-name"></a>错误：Nslookup 无法解析名称

原因：网络问题和/或中断。

**能够**联系网络支持

### <a name="error-you-cant-connect-but-other-clients-can-connect"></a>错误：你无法连接, 但其他客户端可以连接

原因：浏览器不按预期方式运行, 因此停止工作。

**能够**按照这些说明进行操作, 以对浏览器进行故障排除。

1. 重新启动浏览器。
2. 清除浏览器 cookie。 请参阅[如何在 Internet Explorer 中删除 cookie 文件](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)。
3. 清除浏览器缓存。 请参阅[清除浏览器缓存](https://binged.it/2RKyfdU)。
4. 以专用模式打开浏览器。

## <a name="web-client-stops-responding-or-disconnects"></a>Web 客户端停止响应或断开连接

尝试使用其他浏览器或客户端进行连接。

### <a name="error-other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>错误：其他浏览器和客户端也不能正常工作或未能打开

原因：网络和/或操作系统问题或中断

**能够**联系支持团队。

## <a name="web-client-keeps-prompting-for-credentials"></a>Web 客户端将保留凭据提示

如果 Web 客户端继续提示输入凭据, 请遵循这些说明。

1. 确认 web 客户端 URL 是正确的。
2. 确认凭据适用于绑定到该 URL 的 Windows 虚拟桌面环境。
3. 清除浏览器 cookie。 请参阅[如何在 Internet Explorer 中删除 cookie 文件](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)。
4. 清除浏览器缓存。 请参阅[清除浏览器缓存](https://binged.it/2RKyfdU)。
5. 以专用模式打开浏览器。

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>适用于 Windows 7 的远程桌面客户端或 Windows 10 停止响应或无法打开

使用以下 PowerShell cmdlet 清理带外 (OOB) 客户端注册表。

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

导航到 **%AppData%\RdClientRadc**并删除所有内容。

卸载并重新安装适用于 Windows 7 和 Windows 10 的远程桌面客户端。

## <a name="troubleshooting-end-user-connectivity"></a>最终用户连接故障排除

有时, 用户可以访问其源和本地资源, 但仍有一些阻止他们访问远程资源的配置、可用性或性能问题。 在这些情况下, 用户会收到类似于以下内容的消息:

![远程桌面连接错误消息。](media/eb76b666808bddb611448dfb621152ce.png)

![无法连接到网关错误消息。](media/a8fbb9910d4672147335550affe58481.png)

对于客户端连接错误代码, 请遵循以下常规疑难解答说明。

1. 如果遇到问题, 请确认用户名和时间。
2. 打开**PowerShell**并建立与报告问题的 Windows 虚拟桌面租户的连接。
3. 确认通过 RdsTenant 连接到正确的租户 **。**
4. 使用**RdsHostPool**和**RdsSessionHost** cmdlet 确认正在正确的主机池上进行故障排除。
5. 执行以下命令获取指定时间范围的类型为 connection 的所有失败活动的列表:

    ```PowerShell
     Get-RdsDiagnosticActivities -TenantName <TenantName> -username <UPN> -StartTime
     "11/21/2018 1:07:03 PM" -EndTime "11/21/2018 1:27:03 PM" -Outcome Failure -ActivityType Connection
    ```

6. 使用前面的 cmdlet 输出中的**ActivityId** , 运行以下命令:

    ```PowerShell
    (Get-RdsDiagnosticActivities -TenantName $tenant -ActivityId <ActivityId> -Detailed).Errors
    ```

7. 该命令生成的输出类似于下面所示的输出。 使用**ErrorCodeSymbolic**和**ErrorMessage**对根本原因进行故障排除。

    ```PowerShell
    ErrorSource       : <Source>
    ErrorOperation    : <Operation>
    ErrorCode         : <Error code>
    ErrorCodeSymbolic : <Error code string>
    ErrorMessage      : <Error code message>
    ErrorInternal     : <Internal for the OS>
    ReportedBy        : <Reported by component>
    Time              : <Timestampt>
    ```

### <a name="error-o_add_user_to_group_failed--failed-to-add-user--username-to-group--remote-desktop-users-reason-win32error_no_such_member"></a>错误：O_ADD_USER_TO_GROUP_FAILED/无法将 USER = ≤ username ≥添加到 GROUP = 远程桌面用户。 原因：Win32.ERROR_NO_SUCH_MEMBER

原因：VM 尚未加入到用户对象所在的域。

**能够**将 VM 添加到正确的域。 请参阅[将 Windows Server 虚拟机加入到托管域](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal)。

### <a name="error-nslookup-cannot-resolve-the-name"></a>错误：Nslookup 无法解析名称

原因：网络问题或中断。

**能够**联系网络支持

### <a name="error-connectionfailedclientprotocolerror"></a>错误：ConnectionFailedClientProtocolError

原因：用户尝试连接到的虚拟机未加入域。

**能够**将作为主机池一部分的所有 Vm 加入到域控制器。

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>用户连接, 但不显示任何内容 (无源)

用户可以启动远程桌面客户端, 并可以进行身份验证, 但用户看不到 web 发现源中的任何图标。

使用以下命令行确认已将报告问题的用户分配给应用程序组:

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

确认用户正在用正确的凭据登录。

如果正在使用 web 客户端, 请确认没有缓存的凭据问题。

## <a name="next-steps"></a>后续步骤

- 有关 Windows 虚拟桌面故障排除和升级跟踪的概述, 请参阅[故障排除概述、反馈和支持](troubleshoot-set-up-overview.md)。
- 若要在 Windows 虚拟桌面环境中创建租户和主机池时排查问题, 请参阅[租户和主机池创建](troubleshoot-set-up-issues.md)。
- 若要解决在 Windows 虚拟桌面中配置虚拟机 (VM) 时遇到的问题, 请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration.md)。
- 若要解决将 PowerShell 与 Windows 虚拟桌面结合使用时遇到的问题, 请参阅[Windows 虚拟桌面 PowerShell](troubleshoot-powershell.md)。
- 若要了解有关预览服务的详细信息, 请参阅[Windows 虚拟桌面预览环境](https://docs.microsoft.com/azure/virtual-desktop/environment-setup?)。
- 若要完成故障排除教程，请参阅[教程：排查资源管理器模板部署](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)问题。
- 若要了解审核操作，请参阅[使用 Resource Manager 执行审核操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)。
- 若要了解部署期间为确定错误需要执行哪些操作，请参阅[查看部署操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations)。
