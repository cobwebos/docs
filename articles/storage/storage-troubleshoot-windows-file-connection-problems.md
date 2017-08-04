---
title: "排查 Windows 中的 Azure 文件存储问题 | Microsoft Docs"
description: "排查 Windows 中的 Azure 文件存储问题"
services: storage
documentationcenter: 
author: genlin
manager: willchen
editor: na
tags: storage
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 49bc337dac9d3372da188afc3fa7dff8e907c905
ms.openlocfilehash: 71a8f4edf7776556b383f446e5aad007748ef090
ms.contentlocale: zh-cn
ms.lasthandoff: 07/14/2017

---
# <a name="troubleshoot-azure-file-storage-problems-in-windows"></a>排查 Windows 中的 Azure 文件存储问题

本文列出了从 Windows 客户端进行连接时，与 Microsoft Azure 文件存储相关的常见问题。 此外，还介绍了这些问题的可能原因和解决方案。 除了本文中的疑难解答步骤之外，还可以使用 [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5)，以确保 Windows 客户端环境满足正确的先决条件。 AzFileDiagnostics 会自动检测本文中提及的大多数症状，并帮助设置环境，以实现最佳性能。 还可以在 [Azure 文件共享疑难解答](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares)中找到此信息，该疑难解答提供相关步骤来帮助解决在连接/映射/装载 Azure 文件共享时遇到的问题。


<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>尝试装载或卸载 Azure 文件共享时发生错误 53、错误 67 或错误 87

尝试从本地或其他数据中心装载文件共享时，可能会看到以下错误消息：

- 发生系统错误 53。 找不到网络路径。
- 发生系统错误 67。 找不到网络名称。
- 发生系统错误 87。 参数不正确。

### <a name="cause-1-unencrypted-communication-channel"></a>原因 1：信道未加密

出于安全考虑，如果信道未加密，且连接尝试并非来自 Azure 文件共享所在的同一数据中心，将阻止连接到 Azure 文件共享。 仅当用户的客户端 OS 支持 SMB 加密时，才提供信道加密。

Windows 8、Windows Server 2012 及更高版本的每个系统协商包括支持加密的 SMB 3.0 的请求。

### <a name="solution-for-cause-1"></a>原因 1 的解决方案

从符合以下条件之一的客户端连接：

- 符合 Windows 8 和 Windows Server 2012 或更高版本的要求
- 从与用于 Azure 文件共享的 Azure 存储帐户位于同一数据中心的虚拟机连接

### <a name="cause-2-port-445-is-blocked"></a>原因 2：端口 445 被阻止

如果端口 445 到 Azure 文件存储数据中心的出站通信受阻，可能会发生系统错误 53 或 67。 若要概览允许或不允许从端口 445 访问的 ISP，请转到 [TechNet](http://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx)。

若要确定“系统错误 53”消息是否由此造成，可使用 Portqry 来查询 TCP:445 终结点。 如果 TCP:445 终结点显示为“已筛选”，则 TCP 端口被阻止。 下面是一个示例查询：

  `g:\DataDump\Tools\Portqry>PortQry.exe -n [storage account name].file.core.windows.net -p TCP -e 445`

如果 TCP 端口 445 遭到网络路径上的某条规则阻止，则输出如下所示：

  `TCP port 445 (microsoft-ds service): FILTERED`

若要详细了解如何使用 Portqry，请参阅 [Portqry.exe 命令行实用工具说明](https://support.microsoft.com/help/310099)。

### <a name="solution-for-cause-2"></a>原因 2 的解决方案

配合 IT 部门打开端口 445 到 [Azure IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)的出站通信。

### <a name="cause-3-ntlmv1-is-enabled"></a>原因 3：NTLMv1 已启用

如果客户端上已启用 NTLMv1 通信，可能会出现系统错误 53 或 87。 Azure 文件存储仅支持 NTLMv2 身份验证。 启用 NTLMv1 将创建安全级别较低的客户端。 因此，Azure 文件存储的通信受阻。 

若要确定错误是否由此造成，请验证以下注册表子项的值是否设置为 3：

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

有关详细信息，请参阅 TechNet 上的 [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) 主题。

### <a name="solution-for-cause-3"></a>原因 3 的解决方案

在以下注册表子项中，将 LmCompatibilityLevel 值还原为默认值 3：

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>复制到 Azure 文件共享时，出现错误 1816“配额不足，无法处理此命令”

### <a name="cause"></a>原因

在要装载文件共享的计算机上，如果达到文件允许的并发打开句柄上限，便会发生错误 1816。

### <a name="solution"></a>解决方案

关闭一些句柄，减少并发打开句柄的数量，再重试。 有关详细信息，请参阅 [Microsoft Azure 存储性能和可伸缩性清单](storage-performance-checklist.md)。

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-file-storage-in-windows"></a>在 Windows 中将文件复制到 Azure 文件存储以及从中复制文件时速度缓慢

尝试将文件传输到 Azure 文件服务时，可能会发现速度缓慢。

- 如果没有特定的最低 I/O 大小要求，建议 I/O 大小为 1 MB 以实现最佳性能。
-   如果知道通过写入要扩展的最终文件大小，并且软件在文件的未写入结尾包含零时不出现兼容性问题，请提前设置文件大小，而不是让每次写入都成为扩展写入。
-   使用正确的复制方法：
    -   使用 [AZCopy](storage-use-azcopy.md#file-copy) 在两个文件共享之间传输任何内容。
    -   在本地计算机上的文件共享之间使用 [Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/)。

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Windows 8.1 或 Windows Server 2012 R2 的注意事项

对于运行 Windows 8.1 或 Windows Server 2012 R2 的客户端，请确保已安装修补程序 [KB3114025](https://support.microsoft.com/help/3114025)。 此修补程序可提升创建和关闭句柄的性能。

可运行以下脚本，检查是否已安装此修补程序：

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

如果安装了修补程序，将显示以下输出：

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> 自 2015 年 12 月起，Azure Marketplace 中的 Windows Server 2012 R2 映像将默认安装修补程序 KB3114025。

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer"></a>“我的电脑”中没有带驱动器号的文件夹

如果使用 net use 以管理员身份映射 Azure 文件共享，共享似乎会丢失。

### <a name="cause"></a>原因

默认情况下，Windows 文件资源管理器不以管理员身份运行。 如果通过管理命令提示符运行 net use，可以管理员身份映射网络驱动器。 由于映射的驱动器以用户为中心，如果不同用户帐户下已装载这些驱动器，则已登录的用户帐户将不显示它们。

### <a name="solution"></a>解决方案
从非管理员命令行中装载共享。 此外，可按照 [TechNet 主题](https://technet.microsoft.com/library/ee844140.aspx)配置 **EnableLinkedConnections** 注册表值。

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>如果存储帐户包含正斜杠，则 net use 命令失败

### <a name="cause"></a>原因

net use 命令会将正斜杠 (/) 解释为命令行选项。 如果用户帐户名称以正斜杠开头，则驱动器映射失败。

### <a name="solution"></a>解决方案

若要解决此问题，可完成以下任意步骤：

- 运行以下 PowerShell 命令：

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc" `

  在批处理文件中，可以按如下方式运行命令：

  `Echo new-smbMapping ... | powershell -command –`

- 用双引号将密钥括起来，可以解决此问题（除非正斜杠是首个字符）。 如果是，可以使用交互模式并单独输入密码，也可以生成密钥来获取不以正斜杠开头的密钥。

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-file-storage-drive"></a>应用程序或服务无法访问装载的 Azure 文件存储驱动器

### <a name="cause"></a>原因

每个用户都装载了驱动器。 如果运行应用程序或服务的用户帐户与装载驱动器的用户帐户不同，应用程序将检测不到驱动器。

### <a name="solution"></a>解决方案

使用以下解决方案之一：

-   从包含应用程序的同一用户帐户装载驱动器。 可以使用 PsExec 等工具。
- 在 net use 命令的用户名和密码参数中传递存储帐户名称和密钥。

按照这些说明操作后，对系统/网络服务帐户运行 net use 时，可能会看到以下错误消息：“发生系统错误 1312。 如果为系统/网络服务帐户运行 可能已被终止。” 如果发生这种情况，请确保传递到 net use 的用户名包括域信息（例如，“[存储帐户名称].file.core.windows.net”）。

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>出现错误“要将该文件复制到的目标不支持加密”

通过网络复制文件时，文件在源计算机上被解密，以明文形式传输，并在目标位置上被重新加密。 不过，尝试复制加密文件时，可能会看到以下错误消息：“要将该文件复制到的目标不支持加密。”

### <a name="cause"></a>原因
如果使用的是加密文件系统 (EFS)，可能会出现此问题。 可将 BitLocker 加密的文件复制到 Azure 文件存储。 不过，Azure 文件存储不支持 NTFS EFS。

### <a name="workaround"></a>解决方法
必须先将文件解密，才能通过网络进行复制。 使用以下方法之一：

- 运行 copy /d 命令。 这样，可以将加密文件作为解密文件保存到目标位置。
- 设置以下注册表项：
  - Path = HKLM\Software\Policies\Microsoft\Windows\System
  - Value type = DWORD
  - 名称 = CopyFileAllowDecryptedRemoteDestination
  - 值 = 1

请注意，设置注册表项会影响对网络共享进行的所有复制操作。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。
如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，以快速解决问题。

