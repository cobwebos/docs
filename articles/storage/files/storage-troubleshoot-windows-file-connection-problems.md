---
title: 在 Windows 中排查 Azure 文件问题 | Microsoft Docs
description: 在 Windows 中排查 Azure 文件问题
services: storage
author: jeffpatt24
tags: storage
ms.service: storage
ms.topic: article
ms.date: 01/02/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 1cadf61d7ce6ed48ea2d42b299dede860a505f0d
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2019
ms.locfileid: "58877425"
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>在 Windows 中排查 Azure 文件问题

本文列出了从 Windows 客户端进行连接时，与 Microsoft Azure 文件相关的常见问题。 此外，还提供了这些问题的可能原因和解决方法。 除本文中的疑难解答步骤之外，还可使用 [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) ，以确保 Windows 客户端环境满足正确的先决条件。 AzFileDiagnostics 会自动检测本文中提及的大多数症状，并帮助设置环境，以实现最佳性能。 还可在 [Azure 文件共享疑难解答](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares)中找到这些信息，该疑难解答提供相关步骤来帮助解决连接/映射/装载 Azure 文件共享时遇到的问题。

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>装载 Azure 文件共享时出现错误 5

尝试装载文件共享时，可能会收到以下错误：

- 发生系统错误 5。 访问被拒绝。

### <a name="cause-1-unencrypted-communication-channel"></a>原因 1：信道未加密

出于安全考虑，如果信道未加密，且连接尝试并非来自 Azure 文件共享所在的同一数据中心，将阻止连接到 Azure 文件共享。 如果在存储帐户中启用[需要安全传输](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer)设置，则还可以阻止同一数据中心中未加密的连接。 仅当用户的客户端 OS 支持 SMB 加密时，才提供加密的信道。

Windows 8、Windows Server 2012 及更高版本的每个系统协商包括支持加密的 SMB 3.0 的请求。

### <a name="solution-for-cause-1"></a>原因 1 的解决方案

1. 从支持 SMB 加密的客户端（Windows 8、Windows Server 2012 或更高版本）进行连接，或者从用于 Azure 文件共享的 Azure 存储帐户所在数据中心内的虚拟机进行连接。
2. 如果客户端不支持 SMB 加密，请确保在存储帐户上禁用[需要安全传输](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer)设置。

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>原因 2：在存储帐户上启用了虚拟网络或防火墙规则 

如果在存储帐户上配置了虚拟网络 (VNET) 和防火墙规则，则将拒绝访问网络流量，除非允许客户端 IP 地址或虚拟网络访问。

### <a name="solution-for-cause-2"></a>原因 2 的解决方案

验证是否已在存储帐户上正确配置虚拟网络和防火墙规则。 若要测试虚拟网络或防火墙规则是否导致此问题，请将存储帐户上的设置临时更改为“允许来自所有网络的访问”。 若要了解详细信息，请参阅[配置 Azure 存储防火墙和虚拟网络](https://docs.microsoft.com/azure/storage/common/storage-network-security)。

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>尝试装载或卸载 Azure 文件共享时发生错误 53、错误 67 或错误 87

尝试从本地或其他数据中心装载文件共享时，可能会看到以下错误消息：

- 发生系统错误 53。 找不到网络路径。
- 发生系统错误 67。 找不到网络名称。
- 发生系统错误 87。 参数不正确。

### <a name="cause-1-port-445-is-blocked"></a>原因 1：端口 445 被阻止

如果端口 445 到 Azure 文件数据中心的出站通信受阻，可能会发生系统错误 53 或 67。 若要概览允许或不允许从端口 445 访问的 ISP，请转到 [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx)。

若要检查防火墙或 ISP 是否阻止端口 445，请使用 [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) 工具或 `Test-NetConnection` cmdlet。 

若要使用 `Test-NetConnection` cmdlet，则必须安装 AzureRM PowerShell 模块。有关详细信息，请参阅[安装 Azure PowerShell 模块](/powershell/azure/azurerm/install-azurerm-ps)。 记得将 `<your-storage-account-name>` 和 `<your-resource-group-name>` 替换为存储帐户的相应名称。

   
    $resourceGroupName = "<your-resource-group-name>"
    $storageAccountName = "<your-storage-account-name>"

    # This command requires you to be logged into your Azure account, run Login-AzureRmAccount if you haven't
    # already logged in.
    $storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName

    # The ComputerName, or host, is <storage-account>.file.core.windows.net for Azure Public Regions.
    # $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign clouds
    # or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
    Test-NetConnection -ComputerName ([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) -Port 445
    
如果连接成功，则会看到以下输出：
    
  
    ComputerName     : <your-storage-account-name>
    RemoteAddress    : <storage-account-ip-address>
    RemotePort       : 445
    InterfaceAlias   : <your-network-interface>
    SourceAddress    : <your-ip-address>
    TcpTestSucceeded : True
 

> [!Note]  
> 以上命令返回存储帐户的当前 IP 地址。 此 IP 地址不一定保持不变，可能会随时更改。 请勿将此 IP 地址硬编码到任何脚本中或某个防火墙配置中。

### <a name="solution-for-cause-1"></a>原因 1 的解决方案

#### <a name="solution-1---use-azure-file-sync"></a>解决方案 1-使用 Azure 文件同步
Azure 文件同步可以将你的本地 Windows Server 转换为 Azure 文件共享的快速缓存。 可以使用 Windows Server 上可用的任意协议本地访问数据，包括 SMB、NFS 和 FTPS。 Azure 文件同步可通过端口 443，并因此可作为一种解决方法从端口 445 被阻止的客户端访问 Azure 文件。 [了解如何设置 Azure 文件同步](https://docs.microsoft.com/en-us/azure/storage/files/storage-sync-files-extend-servers)。

#### <a name="solution-2---use-vpn"></a>解决方案 2-使用 VPN
通过设置 VPN 连接到特定存储帐户，流量会通过安全隧道而不是通过 internet。 请按照[有关安装 VPN 的说明](https://github.com/Azure-Samples/azure-files-samples/tree/master/point-to-site-vpn-azure-files
)从 Windows 访问 Azure 文件。

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>解决方案 3-取消阻止端口 445 帮助您的 ISP / IT 管理员
与 IT 部门或 ISP 配合，向 [Azure IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)开放端口 445 出站通信。

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>解决方案 4-使用 REST API 基于存储资源管理器/Powershell 等工具
除了 SMB，Azure 文件存储还支持 REST。 REST 访问可以通过端口 443 进行（标准 tcp）。 有许多工具是用 REST API 编写的，可以给用户带来丰富的 UI 体验。 [存储资源管理器](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)是其中之一。 [下载并安装存储资源管理器](https://azure.microsoft.com/en-us/features/storage-explorer/)，然后将其连接到 Azure 文件存储支持的文件共享。 也可使用 [PowerShell](https://docs.microsoft.com/en-us/azure/storage/files/storage-how-to-use-files-powershell)，此工具也使用 REST API。


### <a name="cause-2-ntlmv1-is-enabled"></a>原因 2：NTLMv1 已启用

如果客户端上已启用 NTLMv1 通信，可能会出现系统错误 53 或 87。 Azure 文件仅支持 NTLMv2 身份验证。 启用 NTLMv1 将创建安全级别较低的客户端。 因此，Azure 文件的通信受阻。 

若要确定错误是否由此造成，请验证以下注册表子项的值是否设置为 3：

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

有关详细信息，请参阅 TechNet 上的 [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) 主题。

### <a name="solution-for-cause-2"></a>原因 2 的解决方案

在以下注册表子项中，将 LmCompatibilityLevel 值还原为默认值 3：

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>复制到 Azure 文件共享时，出现错误 1816“配额不足，无法处理此命令”

### <a name="cause"></a>原因

在要装载文件共享的计算机上，如果达到文件允许的并发打开句柄上限，便会发生错误 1816。

### <a name="solution"></a>解决方案

关闭一些句柄，减少并发打开句柄的数量，再重试。 有关详细信息，请参阅 [Microsoft Azure 存储性能和可伸缩性清单](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

<a id="accessdeniedportal"></a>
## <a name="error-access-denied-when-browsing-to-an-azure-file-share-in-the-portal"></a>浏览到门户中的 Azure 文件共享时出现“访问被拒绝”错误

浏览到门户中的 Azure 文件共享时，可能会收到以下错误：

访问被拒绝  
你无权访问  
你似乎无权访问此内容。 若要获取访问权限，请联系所有者。  

### <a name="cause-1-your-user-account-does-not-have-access-to-the-storage-account"></a>原因 1：你的用户帐户无权访问该存储帐户

### <a name="solution-for-cause-1"></a>原因 1 的解决方案

浏览到Azure文件共享所在的存储帐户，单击“访问控制(IAM)”，确保你的用户帐户有权访问该存储帐户。 若要了解详细信息，请参阅[如何使用基于角色的访问控制 (RBAC) 来保护存储帐户](https://docs.microsoft.com/azure/storage/common/storage-security-guide#how-to-secure-your-storage-account-with-role-based-access-control-rbac)。

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>原因 2：在存储帐户上启用了虚拟网络或防火墙规则

### <a name="solution-for-cause-2"></a>原因 2 的解决方案

验证是否已在存储帐户上正确配置虚拟网络和防火墙规则。 若要测试虚拟网络或防火墙规则是否导致此问题，请将存储帐户上的设置临时更改为“允许来自所有网络的访问”。 若要了解详细信息，请参阅[配置 Azure 存储防火墙和虚拟网络](https://docs.microsoft.com/azure/storage/common/storage-network-security)。

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>在 Windows 中将文件复制到 Azure 文件以及从中复制文件时速度缓慢

尝试将文件传输到 Azure 文件服务时，可能会发现速度缓慢。

- 如果没有特定的最低 I/O 大小要求，建议 I/O 大小为 1 MiB 以实现最佳性能。
-   如果知道通过写入要扩展的最终文件大小，并且软件在文件的未写入结尾包含零时未出现兼容性问题，请提前设置文件大小，而不是让每次写入都成为扩展写入。
-   使用正确的复制方法：
    -   使用 [AZCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) 在两个文件共享之间传输任何内容。
    -   在本地计算机上的文件共享之间使用 [Robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/)。

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Windows 8.1 或 Windows Server 2012 R2 的注意事项

对于运行 Windows 8.1 或 Windows Server 2012 R2 的客户端，请确保已安装修补程序 [KB3114025](https://support.microsoft.com/help/3114025)。 此修补程序可提升创建和关闭句柄的性能。

可运行以下脚本，检查是否已安装此修补程序：

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

如果安装了修补程序，会显示以下输出：

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> 自 2015 年 12 月起，Azure 市场中的 Windows Server 2012 R2 映像将默认安装修补程序 KB3114025。

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

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc"`

  在批处理文件中，可以按如下方式运行命令：

  `Echo new-smbMapping ... | powershell -command –`

- 用双引号将密钥括起来，可以解决此问题（除非正斜杠是首个字符）。 如果是，可以使用交互模式并单独输入密码，也可以生成密钥来获取不以正斜杠开头的密钥。

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>应用程序或服务无法访问装载的 Azure 文件驱动器

### <a name="cause"></a>原因

每个用户都装载了驱动器。 如果运行应用程序或服务的用户帐户与装载驱动器的用户帐户不同，应用程序将检测不到驱动器。

### <a name="solution"></a>解决方案

使用以下解决方案之一：

-   从包含应用程序的同一用户帐户装载驱动器。 可以使用 PsExec 等工具。
- 在 net use 命令的用户名和密码参数中传递存储帐户名称和密钥。
- 使用 cmdkey 命令将凭据添加到凭据管理器中。 从命令行在服务帐户上下文中通过交互式登录或使用运行方式执行此操作。
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- 不使用映射驱动器号直接映射共享。 某些应用程序可能无法正确地重新连接到驱动器号，因此使用完整的 UNC 路径可能会更可靠。 

  `net use * \\storage-account-name.file.core.windows.net\share`

按照这些说明操作后，对系统/网络服务帐户运行 net use 时，可能会收到以下错误消息：“发生系统错误 1312。 如果为系统/网络服务帐户运行 可能已被终止。” 如果发生这种情况，请确保传递到 net use 的用户名包括域信息（例如，“[存储帐户名称].file.core.windows.net”）。

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>出现错误“要将该文件复制到的目标不支持加密”

通过网络复制文件时，文件在源计算机上被解密，以明文形式传输，并在目标位置上被重新加密。 不过，尝试复制加密文件时，可能会看到以下错误：“要将该文件复制到的目标不支持加密。”

### <a name="cause"></a>原因
如果使用的是加密文件系统 (EFS)，可能会出现此问题。 可将 BitLocker 加密的文件复制到 Azure 文件。 不过，Azure 文件不支持 NTFS EFS。

### <a name="workaround"></a>解决方法
必须先将文件解密，才能通过网络进行复制。 使用以下方法之一：

- 运行 copy /d 命令。 这样，可以将加密文件作为解密文件保存到目标位置。
- 设置以下注册表项：
  - Path = HKLM\Software\Policies\Microsoft\Windows\System
  - Value type = DWORD
  - 名称 = CopyFileAllowDecryptedRemoteDestination
  - 值= 1

请注意，设置注册表项会影响对网络共享进行的所有复制操作。

## <a name="slow-enumeration-of-files-and-folders"></a>文件和文件夹的枚举速度变慢

### <a name="cause"></a>原因

如果客户端计算机上用于大型目录的缓存不足，则可能会出现此问题。

### <a name="solution"></a>解决方案

若要解决此问题，请调整 DirectoryCacheEntrySizeMax 注册表值以允许在客户端计算机上缓存较大的目录列表：

- 位置：HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- 值名称：DirectoryCacheEntrySizeMax 
- 值类型：DWORD
 
 
例如，可将其设置为 0x100000，并查看性能是否有所提高。

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>为 Azure 文件存储启用 Azure Active Directory 身份验证时，出现错误 AadDsTenantNotFound：“找不到具有租户 ID aad-tenant-id 的活动租户”

### <a name="cause"></a>原因

如果存储帐户在关联订阅的 AAD 租户上未创建 [AAD Domain Service(AAD DS)](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)则在该帐户上[为 Azure 文件存储启用 Azure Active Directory 身份验证](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-enable)时，会发生错误 AadDsTenantNotFound。  

### <a name="solution"></a>解决方案

在部署存储帐户的订阅的 AAD 租户上启用 AAD DS。 需要 AAD 租户的管理员权限才能创建托管域。 如果你不是 Azure AD 租户的管理员，请与管理员联系并按照分步指南操作，以[使用 Azure 门户启用 Azure Active Directory 域服务](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。
如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，以快速解决问题。
