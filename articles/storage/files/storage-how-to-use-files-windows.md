---
title: 将 Azure 文件共享与 Windows 配合使用 | Microsoft Docs
description: 了解如何将 Azure 文件共享与 Windows 和 Windows Server 配合使用。
services: storage
documentationcenter: na
author: RenaShahMSFT
manager: aungoo
editor: tamram
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/07/2018
ms.author: renash
ms.openlocfilehash: 54e084e6480c872ff6dd4625b8c87d5a60a181ba
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2018
ms.locfileid: "39392261"
---
# <a name="use-an-azure-file-share-with-windows"></a>将 Azure 文件共享与 Windows 配合使用
[Azure 文件](storage-files-introduction.md)是 Microsoft 推出的易用云文件系统。 Azure 文件共享可以在 Windows 和 Windows Server 中无缝使用。 本文讨论将 Azure 文件共享与 Windows 和 Windows Server 配合使用时的注意事项。

使用 Azure 文件共享时，如果是在其被托管时所在的 Azure 区域之外（例如本地或其他 Azure 区域），则 OS 必须支持 SMB 3.0。 

对于在 Azure VM 中或本地运行的 Windows 安装，可以在其上使用 Azure 文件共享。 下表说明了哪些 OS 版本支持在哪个环境中访问文件共享：

| Windows 版本        | SMB 版本 | 可以在 Azure VM 中装载 | 可以在本地装载 |
|------------------------|-------------|-----------------------|----------------------|
| Windows Server 2019（预览版）<sup>1</sup> | SMB 3.0 | 是 | 是 |
| Windows 10<sup>2</sup> | SMB 3.0 | 是 | 是 |
| Windows Server 半年通道<sup>3</sup> | SMB 3.0 | 是 | 是 |
| Windows Server 2016    | SMB 3.0     | 是                   | 是                  |
| Windows 8.1            | SMB 3.0     | 是                   | 是                  |
| Windows Server 2012 R2 | SMB 3.0     | 是                   | 是                  |
| Windows Server 2012    | SMB 3.0     | 是                   | 是                  |
| Windows 7              | SMB 2.1     | 是                   | 否                   |
| Windows Server 2008 R2 | SMB 2.1     | 是                   | 否                   |

<sup>1</sup>Windows Server 2019 通过 [Windows Server 预览体验计划](https://insider.windows.com/for-business-getting-started-server/)以预览版形式提供。 虽然 Windows Server 2019 尚不支持生产性使用，但如果你在连接到 Azure 文件共享时存在 [Windows 故障排除指南](storage-troubleshoot-windows-file-connection-problems.md)中没有涵盖的问题，请告知我们。  
<sup>2</sup>Windows 10 版本 1507、1607、1703、1709、1803。  
<sup>3</sup>Windows Server 版本 1709 和 1803。

> [!Note]  
> 我们始终建议你使用相对于 Windows 版本来说最新的 KB。

## <a name="prerequisites"></a>先决条件 
* **存储帐户名称**：需提供存储帐户的名称才能装载 Azure 文件共享。

* **存储帐户密钥**：需提供主要（或辅助）存储帐户密钥才能装载 Azure 文件共享。 目前不支持使用 SAS 密钥进行装载。

* **确保端口 445 已打开**：SMB 协议要求 TCP 端口 445 处于打开状态；如果阻止了端口 445，则连接会失败。 可以通过 `Test-NetConnection` cmdlet 来查看防火墙是否在阻止端口 445。 以下 PowerShell 代码假定你已安装 AzureRM PowerShell 模块。有关详细信息，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 记得将 `<your-storage-account-name>` 和 `<your-resoure-group-name>` 替换为存储帐户的相应名称。

    ```PowerShell
    $resourceGroupName = "<your-resource-group-name>"
    $storageAccountName = "<your-storage-account-name>"

    # This command requires you to be logged into your Azure account, run Login-AzureRmAccount if you haven't
    # already logged in.
    $storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName

    # The ComputerName, or host, is <storage-account>.file.core.windows.net for Azure Public Regions.
    # $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as soverign clouds
    # or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
    Test-NetConnection -ComputerName [System.Uri]::new($storageAccount.Context.FileEndPoint).Host -Port 445
    ```

    如果连接成功，则会看到以下输出：

    ```
    ComputerName     : <storage-account-host-name>
    RemoteAddress    : <storage-account-ip-address>
    RemotePort       : 445
    InterfaceAlias   : <your-network-interface>
    SourceAddress    : <your-ip-address>
    TcpTestSucceeded : True
    ```

    > [!Note]  
    > 以上命令返回存储帐户的当前 IP 地址。 此 IP 地址不一定保持不变，可能会随时更改。 请勿将此 IP 地址硬编码到任何脚本中或某个防火墙配置中。 

## <a name="using-an-azure-file-share-with-windows"></a>将 Azure 文件共享与 Windows 配合使用
若要将 Azure 文件共享与 Windows 配合使用，必须将其装载好（这意味着为其分配一个驱动器号或装入点路径），或者通过其 [UNC 路径](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx)对其进行访问。 

不同于其他你可能与之有交互的 SMB 共享（例如那些托管在 Windows Server、Linux Samba 服务器或 NAS 设备上的共享），Azure 文件共享目前不支持使用 Active Directory (AD) 或 Azure Active Directory (AAD) 标识进行 Kerberos 身份验证，虽然这是一项我们正[努力开发](https://feedback.azure.com/forums/217298-storage/suggestions/6078420-acl-s-for-azurefiles)的功能。 与之相反，必须使用包含 Azure 文件共享的存储帐户的密钥访问 Azure 文件共享。 存储帐户密钥是用于存储帐户的管理员密钥，包括对你要访问的文件共享中所有文件和文件夹的管理员权限，以及对所有文件共享和其他包含在存储帐户中的存储资源（Blob、队列、表等）的权限。 如果这对你的工作负荷来说还不够，则可使用 [Azure 文件同步](storage-files-planning.md#data-access-method)来解决过渡期间缺少 Kerberos 身份验证和 ACL 支持的问题，直至基于 AAD 的 Kerberos 身份验证和 ACL 支持功能公开发布。

若要将预期使用 SMB 文件共享的业务线 (LOB) 应用程序直接迁移到 Azure，通常的模式是使用 Azure 文件共享，而不是在 Azure VM 中运行专用的 Windows 文件服务器。 若要成功地迁移业务线应用程序以使用 Azure 文件共享，一项重要的考量是，许多业务线应用程序在运行时使用系统权限有限的专用服务帐户（而非 VM 的管理帐户）的上下文。 因此，必须确保通过服务帐户（而非管理帐户）的上下文来装载/保存 Azure 文件共享的凭据。

### <a name="persisting-azure-file-share-credentials-in-windows"></a>在 Windows 中保留 Azure 文件共享凭据  
可以使用 [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) 实用程序将存储帐户凭据存储在 Windows 中。 这意味着，在尝试通过 UNC 路径访问 Azure 文件共享或装载 Azure 文件共享时，不需指定凭据。 若要保存存储帐户的凭据，请运行以下 PowerShell 命令，根据需要替换 `<your-storage-account-name>` 和 `<your-resoure-group-name>`。

```PowerShell
$resourceGroupName = "<your-resource-group-name>"
$storageAccountName = "<your-storage-account-name>"

# These commands require you to be logged into your Azure account, run Login-AzureRmAccount if you haven't
# already logged in.
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
$storageAccountKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName

# The cmdkey utility is a command-line (rather than PowerShell) tool. We use Invoke-Expression to allow us to 
# consume the appropriate values from the storage account variables. The value given to the add parameter of the
# cmdkey utility is the host address for the storage account, <storage-account>.file.core.windows.net for Azure 
# Public Regions. $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as soverign 
# clouds or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
Invoke-Expression -Command "cmdkey /add:$([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) " + `
    "/user:AZURE\$($storageAccount.StorageAccountName) /pass:$($storageAccountKeys[0].Value)"
```

可以使用 list 参数验证 cmdkey 实用程序是否已存储该存储帐户的凭据：

```PowerShell
cmdkey /list
```

如果 Azure 文件共享的凭据存储成功，则预期的输出如下所示（可能有其他密钥存储在列表中）：

```
Currently stored credentials:

Target: Domain:target=<storage-account-host-name>
Type: Domain Password
User: AZURE\<your-storage-account-name>
```

现在应该可以在不需提供其他凭据的情况下装载或访问共享。

#### <a name="advanced-cmdkey-scenarios"></a>高级 cmdkey 方案
对于 cmdkey，还有两个其他的可以考虑的方案：在计算机上为另一用户（例如某个服务帐户）存储凭据，以及使用 PowerShell 远程处理功能在远程计算机上存储凭据。

在计算机上为另一用户存储凭据很容易：登录到帐户以后，直接执行以下 PowerShell 命令：

```PowerShell
$password = ConvertTo-SecureString -String "<service-account-password>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "<service-account-username>", $password
Start-Process -FilePath PowerShell.exe -Credential $credential -LoadUserProfile
```

此时会在你的服务帐户（或用户帐户）的用户上下文中打开新的 PowerShell 窗口。 然后即可使用 cmdkey 实用程序，如[上](#persisting-azure-file-share-credentials-in-windows)所述。

但是，不能使用 PowerShell 远程处理功能将凭据存储在远程计算机上，因为当用户通过 PowerShell 远程处理登录时，cmdkey 不允许访问其凭据存储（即使只是进行添加操作）。 建议通过[远程桌面](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/windows)登录到计算机。

### <a name="mount-the-azure-file-share-with-powershell"></a>使用 PowerShell 装载 Azure 文件共享
通过常规的（即权限未提升的）PowerShell 会话运行以下命令，以便装载 Azure 文件共享。 记住将 `<your-resource-group-name>`、`<your-storage-account-name>`、`<your-file-share-name>` 和 `<desired-drive-letter>` 替换为适当的内容。

```PowerShell
$resourceGroupName = "<your-resource-group-name>"
$storageAccountName = "<your-storage-account-name>"
$fileShareName = "<your-file-share-name>"

# These commands require you to be logged into your Azure account, run Login-AzureRmAccount if you haven't
# already logged in.
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
$storageAccountKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName
$fileShare = Get-AzureStorageShare -Context $storageAccount.Context | Where-Object { 
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    throw [System.Exception]::new("Azure file share not found")
}

# The value given to the root parameter of the New-PSDrive cmdlet is the host address for the storage account, 
# <storage-account>.file.core.windows.net for Azure Public Regions. $fileShare.StorageUri.PrimaryUri.Host is 
# used because non-Public Azure regions, such as soverign clouds or Azure Stack deployments, will have different 
# hosts for Azure file shares (and other storage resources).
$password = ConvertTo-SecureString -String $storageAccountKeys[0].Value -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "AZURE\$($storageAccount.StorageAccountName)", $password
New-PSDrive -Name <desired-drive-letter> -PSProvider FileSystem -Root "\\$($fileShare.StorageUri.PrimaryUri.Host)\$($fileShare.Name)" -Credential $credential -Persist
```

> [!Note]  
> 对 `New-PSDrive` cmdlet 使用 `-Persist` 选项只会在凭据已保存的情况下，允许文件共享在启动时重新装载。 可以使用 cmdkey 来保存凭据，如[前所述](#persisting-azure-file-share-credentials-in-windows)。 

可以根据需要使用以下 PowerShell cmdlet 来卸载 Azure 文件共享。

```PowerShell
Remove-PSDrive -Name <desired-drive-letter>
```

### <a name="mount-the-azure-file-share-with-file-explorer"></a>使用文件资源管理器装载 Azure 文件共享
> [!Note]  
> 请注意，以下说明是在 Windows 10 上显示的，在较旧的版本上可能稍有不同。 

1. 打开文件资源管理器。 若要执行此操作，可从“开始”菜单打开，或者按 Win+E 快捷方式。

2. 导航到窗口左侧的“此电脑”项。 这样会更改功能区中的可用菜单。 在“计算机”菜单中，选择“映射网络驱动器”。
    
    ![“映射网络驱动器”下拉菜单的屏幕截图](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

3. 从 Azure 门户的“连接”窗格中复制 UNC 路径。 

    ![Azure 文件“连接”窗格中的 UNC 路径](./media/storage-how-to-use-files-windows/portal_netuse_connect.png)

4. 选择驱动器号并输入 UNC 路径。 
    
    ![“映射网络驱动器”对话框的屏幕截图](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

5. 使用带 `AZURE\` 前缀的存储帐户名称作为用户名，使用存储帐户密钥作为密码。
    
    ![网络凭据对话框的屏幕快照](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

6. 根据需要使用 Azure 文件共享。
    
    ![Azure 文件共享现已装载](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

7. 做好卸载 Azure 文件共享的准备以后，即可在文件资源管理器中右键单击“网络位置”下对应于共享的条目，然后选择“断开连接”。

## <a name="securing-windowswindows-server"></a>保护 Windows/Windows Server
若要在 Windows 上装载 Azure 文件共享，必须确保端口 445 可以访问。 许多组织因 SMB 1 中固有的安全风险而阻止端口 445。 SMB 1 也称 CIFS（通用 Internet 文件系统），是 Windows 和 Windows Server 随附的旧版文件系统协议。 SMB 1 是过时且效率不高的协议，而且最重要的是，它是不安全的协议。 好消息是，Azure 文件不支持 SMB 1，并且所有受支持的 Windows 和 Windows Server 版本均可删除或禁用 SMB 1。 在将 Azure 文件共享用于生产环境之前，[强烈建议](https://aka.ms/stopusingsmb1)删除或禁用 Windows 中的 SMB 1 客户端和服务器。

下表详细说明了每个 Windows 版本中的 SMB 1 的状态：

| Windows 版本                           | SMB 1 默认状态 | 禁用/删除方法       | 
|-------------------------------------------|----------------------|-----------------------------|
| Windows Server 2019（预览版）             | 已禁用             | 通过 Windows 功能进行删除 |
| Windows Server，版本 1709+            | 已禁用             | 通过 Windows 功能进行删除 |
| Windows 10，版本 1709+                | 已禁用             | 通过 Windows 功能进行删除 |
| Windows Server 2016                       | 已启用              | 通过 Windows 功能进行删除 |
| Windows 10，版本 1507、1607 和 1703 | 已启用              | 通过 Windows 功能进行删除 |
| Windows Server 2012 R2                    | 已启用              | 通过 Windows 功能进行删除 | 
| Windows 8.1                               | 已启用              | 通过 Windows 功能进行删除 | 
| Windows Server 2012                       | 已启用              | 通过注册表进行禁用       | 
| Windows Server 2008 R2                    | 已启用              | 通过注册表进行禁用       |
| Windows 7                                 | 已启用              | 通过注册表进行禁用       | 

### <a name="auditing-smb-1-usage"></a>审核 SMB 1 使用情况
> 适用于 Windows Server 2019（预览版）、Windows Server 半年通道（版本 1709 和 1803）、Windows Server 2016、Windows 10（版本 1507、1607、1703、1709 和 1803）、Windows Server 2012 R2 和 Windows 8.1

在删除环境中的 SMB 1 之前，可能需要审核 SMB 1 使用情况，看是否有客户端会因更改而受损。 如果有客户端通过 SMB 1 对 SMB 共享提出了请求，系统会在 `Applications and Services Logs > Microsoft > Windows > SMBServer > Audit` 下的事件日志中记录审核事件。 

> [!Note]  
> 若要在 Windows Server 2012 R2 和 Windows 8.1 上启用审核支持，请至少安装 [KB4022720](https://support.microsoft.com/help/4022720/windows-8-1-windows-server-2012-r2-update-kb4022720)。

若要启用审核，请通过权限提升的 PowerShell 会话执行以下 cmdlet：

```PowerShell
Set-SmbServerConfiguration –AuditSmb1Access $true
```

### <a name="removing-smb-1-from-windows-server"></a>从 Windows Server 中删除 SMB 1
> 适用于 Windows Server 2019（预览版）、Windows Server 半年通道（版本 1709 和 1803）、Windows Server 2016、Windows Server 2012 R2

若要从 Windows Server 实例中删除 SMB 1，请通过权限提升的 PowerShell 会话执行以下 cmdlet：

```PowerShell
Remove-WindowsFeature -Name FS-SMB1
```

若要完成删除过程，请重启服务器。 

> [!Note]  
> 从 Windows 10 和 Windows Server 版本 1709 开始，SMB 1 不再默认安装，且 SMB 1 客户端和 SMB 1 服务器有不同的 Windows 功能。 我们始终建议将 SMB 1 服务器 (`FS-SMB1-SERVER`) 和 SMB 1 客户端 (`FS-SMB1-CLIENT`) 都卸载掉。

### <a name="removing-smb-1-from-windows-client"></a>从 Windows 客户端中删除 SMB 1
> 适用于 Windows 10（版本 1507、1607、1703、1709 和 1803）和 Windows 8.1

若要从 Windows 客户端中删除 SMB 1，请通过权限提升的 PowerShell 会话执行以下 cmdlet：

```PowerShell
Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol
```

若要完成删除过程，请重启电脑。

### <a name="disabling-smb-1-on-legacy-versions-of-windowswindows-server"></a>在旧版 Windows/Windows Server 上禁用 SMB 1
> 适用于 Windows Server 2012、Windows Server 2008 R2 和 Windows 7

不能在旧版 Windows/Windows Server 上彻底删除 SMB 1，但可以通过注册表来禁用它。 若要禁用 SMB 1，请在 `HKEY_LOCAL_MACHINE > SYSTEM > CurrentControlSet > Services > LanmanServer > Parameters` 下新建类型为 `DWORD` 且值为 `0` 的注册表项 `SMB1`。

也可使用以下 PowerShell cmdlet 轻松完成该操作：

```PowerShell
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters" SMB1 -Type DWORD -Value 0 –Force
```

创建此注册表项以后，必须重启服务器才能禁用 SMB 1。

### <a name="smb-resources"></a>SMB 资源
- [Stop using SMB 1](https://blogs.technet.microsoft.com/filecab/2016/09/16/stop-using-smb1/)（停止使用 SMB 1）
- [SMB 1 Product Clearinghouse](https://blogs.technet.microsoft.com/filecab/2017/06/01/smb1-product-clearinghouse/)（SMB 1 产品交换所）
- [Discover SMB 1 in your environment with DSCEA](https://blogs.technet.microsoft.com/ralphkyttle/2017/04/07/discover-smb1-in-your-environment-with-dscea/)（使用 DSCEA 发现环境中的 SMB 1）
- [Disabling SMB 1 through Group Policy](https://blogs.technet.microsoft.com/secguide/2017/06/15/disabling-smbv1-through-group-policy/)（通过组策略禁用 SMB 1）

## <a name="next-steps"></a>后续步骤
请参阅以下链接，获取有关 Azure 文件的更多信息：
- [规划 Azure 文件部署](storage-files-planning.md)
* [常见问题](../storage-files-faq.md)
* [在 Windows 上进行故障排除](storage-troubleshoot-windows-file-connection-problems.md)      