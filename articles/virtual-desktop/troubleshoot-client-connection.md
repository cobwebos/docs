---
title: Windows 虚拟桌面-Azure 中的远程桌面客户端连接
description: 如何设置在 Windows 虚拟桌面租户环境中的客户端连接时解决问题。
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: b7a6daa791e44227fd146c9c328a939560ebb3b1
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605291"
---
# <a name="remote-desktop-client-connections"></a>远程桌面客户端连接

使用本文章以解决与 Windows 虚拟桌面客户端连接的问题。

## <a name="provide-feedback"></a>提供反馈

目前我们不会受理 Windows 虚拟桌面预览版的支持案例。 请访问 [Windows 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，与产品团队和活跃的社区成员共同探讨 Windows 虚拟桌面服务。

## <a name="you-cant-open-a-web-client"></a>无法打开 web 客户端

确认没有 internet 连接的打开另一个网站;例如， [www.Bing.com](https://www.bing.com)。

使用**nslookup**来确认 DNS 可解析的 FQDN:

```cmd
nslookup rdweb.wvd.microsoft.com
```

尝试使用另一个客户端，如适用于 Windows 7 或 Windows 10 中，并检查如果可以打开 web 客户端的远程桌面客户端连接。

### <a name="error-opening-another-site-fails"></a>错误：打开另一个站点失败

原因：  网络问题和/或服务中断。

**解决方法：** 请联系网络支持。

### <a name="error-nslookup-cannot-resolve-the-name"></a>错误：Nslookup 无法解析名称

原因：  网络问题和/或服务中断。

**解决方法：** 请联系网络支持

### <a name="error-you-cant-connect-but-other-clients-can-connect"></a>错误：无法连接，但其他客户端可以连接

原因：  在浏览器不行为如预期的和已停止工作。

**解决方法：** 按照以下说明来解决浏览器。

1. 重新启动浏览器。
2. 清除浏览器 cookie。 请参阅[如何删除 Internet Explorer 中的 cookie 文件](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)。
3. 清除浏览器缓存。 请参阅[清除您的浏览器的浏览器缓存](https://binged.it/2RKyfdU)。
4. 在专用模式下打开的浏览器。

## <a name="web-client-stops-responding-or-disconnects"></a>Web 客户端停止响应或断开连接

请尝试使用其他浏览器或客户端进行连接。

### <a name="error-other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>错误：其他浏览器和客户端也正常工作，或打开失败

原因：  网络和/或操作的系统问题或中断

**解决方法：** 联系支持团队。

## <a name="web-client-keeps-prompting-for-credentials"></a>Web 客户端将会提示输入凭据

如果 Web 客户端将会提示输入凭据，请按照这些说明。

1. 确认 web 客户端 URL 正确。
2. 确认凭据所用于绑定到的 URL 的 Windows 虚拟桌面环境。
3. 清除浏览器 cookie。 请参阅[如何删除 Internet Explorer 中的 cookie 文件](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)。
4. 清除浏览器缓存。 请参阅[清除您的浏览器的浏览器缓存](https://binged.it/2RKyfdU)。
5. 在专用模式下打开的浏览器。

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>对于 Windows 7 或 Windows 10 的远程桌面客户端停止响应或无法打开

使用以下 PowerShell cmdlet 以进行带外 (OOB) 客户端的注册表清除。

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

导航到 **%AppData%\RdClientRadc**和删除所有内容。

卸载并重新安装适用于 Windows 7 和 Windows 10 的远程桌面客户端。

## <a name="troubleshooting-end-user-connectivity"></a>最终用户连接性疑难解答

有时用户可以访问其源和本地资源，但仍然可以配置、 可用性或性能问题，使其无法访问远程资源。 在这些情况下，用户将获取消息类似于以下：

![远程桌面连接错误消息。](media/eb76b666808bddb611448dfb621152ce.png)

![无法连接到网关错误消息。](media/a8fbb9910d4672147335550affe58481.png)

请遵循以下常规故障排除说明获取客户端连接的错误代码。

1. 确认用户名称和遇到问题时的时间。
2. 打开**PowerShell**并建立连接到 Windows 虚拟桌面租户报告此问题。
3. 确认连接到具有正确的租户**Get RdsTenant。**
4. 使用**Get RdsHostPool**并**Get RdsSessionHost** cmdlet，确认的正确的主机池上完成故障排除。
5. 执行以下命令以获取在指定的时间范围的类型连接的所有失败的活动的列表：

    ```PowerShell
     Get-RdsDiagnosticActivities -TenantName <TenantName> -username <UPN> -StartTime
     "11/21/2018 1:07:03 PM" -EndTime "11/21/2018 1:27:03 PM" -Outcome Failure -ActivityType Connection
    ```

6. 使用**ActivityId**在先前的 cmdlet 输出中，运行以下命令：

    ```PowerShell
    (Get-RdsDiagnosticActivities -TenantName $tenant -ActivityId <ActivityId> -Detailed).Errors
    ```

7. 该命令生成类似于如下所示的输出的输出。 使用**ErrorCodeSymbolic**并**ErrorMessage**来排查问题的根本原因。

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

### <a name="error-oaddusertogroupfailed--failed-to-add-user--username-to-group--remote-desktop-users-reason-win32errornosuchmember"></a>错误：O_ADD_USER_TO_GROUP_FAILED / 无法将用户添加到组 ≤username≥ = = Remote Desktop Users。 原因：Win32.ERROR_NO_SUCH_MEMBER

原因：  未将 VM 加入到用户对象的域中。

**解决方法：** 将 VM 添加到正确的域。 请参阅[Windows Server 虚拟机加入托管域](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal)。

### <a name="error-nslookup-cannot-resolve-the-name"></a>错误：Nslookup 无法解析名称

原因：  网络问题或中断。

**解决方法：** 请联系网络支持

### <a name="error-connectionfailedclientprotocolerror"></a>错误：ConnectionFailedClientProtocolError

原因：  该用户尝试连接到虚拟机未加入域。

**解决方法：** 加入到域控制器主机池的一部分的所有 Vm。

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>用户连接，但不是显示任何内容 （任何源）

用户可以启动远程桌面客户端，并且能够进行身份验证，但是用户不会看到 web 发现馈送中的任何图标。

确认用户报告的问题已被分配到应用程序组通过使用以下命令行：

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

确认用户正在使用正确的凭据登录。

如果正在使用 web 客户端，确认没有任何缓存的凭据问题。

## <a name="next-steps"></a>后续步骤

- 有关故障排除 Windows 虚拟桌面，升级进行跟踪的概述，请参阅[故障排除概述、 反馈和支持](troubleshoot-set-up-overview.md)。
- 若要在 Windows 虚拟桌面环境中创建的租户和主机池时排查问题，请参阅[租户以及主机池创建](troubleshoot-set-up-issues.md)。
- 若要在 Windows 虚拟机中配置虚拟机 (VM) 时解决问题，请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration.md)。
- 若要排查问题时使用 PowerShell 管理 Windows 虚拟桌面，请参阅[Windows 虚拟桌面 PowerShell](troubleshoot-powershell.md)。
- 若要了解有关预览服务的详细信息，请参阅[Windows Desktop 预览环境](https://docs.microsoft.com/azure/virtual-desktop/environment-setup?)。
- 若要完成故障排除教程，请参阅[教程：对资源管理器模板部署进行故障排除](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)。
- 若要了解审核操作，请参阅[使用 Resource Manager 执行审核操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)。
- 若要了解部署期间为确定错误需要执行哪些操作，请参阅[查看部署操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations)。
