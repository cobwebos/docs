---
title: 将 Azure 文件共享与 Windows 配合使用 | Microsoft Docs
description: 了解如何将 Azure 文件共享与 Windows 和 Windows Server 配合使用。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: bb9e7582317851d1968e104cd351a2b5e02b1e19
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85509772"
---
# <a name="use-an-azure-file-share-with-windows"></a>将 Azure 文件共享与 Windows 配合使用
[Azure 文件](storage-files-introduction.md)是 Microsoft 推出的易用云文件系统。 Azure 文件共享可以在 Windows 和 Windows Server 中无缝使用。 本文讨论将 Azure 文件共享与 Windows 和 Windows Server 配合使用时的注意事项。

使用 Azure 文件共享时，如果是在其被托管时所在的 Azure 区域之外（例如本地或其他 Azure 区域），则 OS 必须支持 SMB 3.0。 

对于在 Azure VM 中或本地运行的 Windows 安装，可以在其上使用 Azure 文件共享。 下表说明了哪些 OS 版本支持在哪个环境中访问文件共享：

| Windows 版本        | SMB 版本 | 可在 Azure VM 中装载 | 可在本地装载 |
|------------------------|-------------|-----------------------|-----------------------|
| Windows Server 2019 | SMB 3.0 | 是 | 是 |
| Windows 10<sup>1</sup> | SMB 3.0 | 是 | 是 |
| Windows Server 半年通道<sup>2</sup> | SMB 3.0 | 是 | 是 |
| Windows Server 2016 | SMB 3.0 | 是 | 是 |
| Windows 8.1 | SMB 3.0 | 是 | 是 |
| Windows Server 2012 R2 | SMB 3.0 | 是 | 是 |
| Windows Server 2012 | SMB 3.0 | 是 | 是 |
| Windows 7<sup>3</sup> | SMB 2.1 | 是 | 否 |
| Windows Server 2008 R2<sup>3</sup> | SMB 2.1 | 是 | 否 |

<sup>1</sup>Windows 10 版本 1507、1607、1709、1803、1809、1903 和 1909。  
<sup>2</sup>Windows Server 版本 1809、1903 和 1909。  
<sup>3</sup>对 Windows 7 和 Windows Server 2008 R2 的常规 Microsoft 支持已结束。 仅可通过[扩展安全更新 (ESU) 计划](https://support.microsoft.com/help/4497181/lifecycle-faq-extended-security-updates)购买对安全更新的额外支持。 强烈建议从这些操作系统中签出。

> [!Note]  
> 我们始终建议你使用相对于 Windows 版本来说最新的 KB。

## <a name="prerequisites"></a>先决条件 

确保端口 445 处于打开状态：SMB 协议要求 TCP 端口 445 处于打开状态；如果端口 445 被阻止，则连接会失败。 可以通过 cmdlet 检查防火墙是否正在阻止端口 445 `Test-NetConnection` 。 若要了解有关阻止的445端口的变通方法，请参阅 Windows 故障排除指南中的[原因1：端口445被阻止](storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked)部分。

## <a name="using-an-azure-file-share-with-windows"></a>将 Azure 文件共享与 Windows 配合使用
若要将 Azure 文件共享与 Windows 配合使用，必须将其装载好（这意味着为其分配一个驱动器号或装入点路径），或者通过其 [UNC 路径](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx)对其进行访问。 

本文使用存储帐户密钥来访问文件共享。 存储帐户密钥是用于存储帐户的管理员密钥，包括对你要访问的文件共享中所有文件和文件夹的管理员权限，以及对所有文件共享和其他包含在存储帐户中的存储资源（Blob、队列、表等）的权限。 如果这对你的工作负载来说还不够，可使用 [Azure 文件同步](storage-sync-files-planning.md)，或者可使用[通过 SMB 的基于标识的身份验证](storage-files-active-directory-overview.md)。

若要将预期使用 SMB 文件共享的业务线 (LOB) 应用程序直接迁移到 Azure，通常的模式是使用 Azure 文件共享，而不是在 Azure VM 中运行专用的 Windows 文件服务器。 若要成功地迁移业务线应用程序以使用 Azure 文件共享，一项重要的考量是，许多业务线应用程序在运行时使用系统权限有限的专用服务帐户（而非 VM 的管理帐户）的上下文。 因此，必须确保通过服务帐户（而非管理帐户）的上下文来装载/保存 Azure 文件共享的凭据。

### <a name="mount-the-azure-file-share"></a>装载 Azure 文件共享

Azure 门户提供了一个脚本，可用于将文件共享直接装载到主机。 建议使用提供的脚本。

获取此脚本：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 导航到包含要装载的文件共享的存储帐户。
1. 选择 "**文件共享**"。
1. 选择要装载的文件共享。

    :::image type="content" source="media/storage-how-to-use-files-windows/select-file-shares.png" alt-text="实例":::

1. 选择“连接”。

    :::image type="content" source="media/storage-how-to-use-files-windows/file-share-connect-icon.png" alt-text="文件共享的连接图标屏幕截图。":::

1. 选择要用于装载共享的驱动器号。
1. 复制提供的脚本。

    :::image type="content" source="media/storage-how-to-use-files-windows/files-portal-mounting-cmdlet-resize.png" alt-text="示例文本":::

1. 将脚本粘贴到要在其上装载文件共享的主机上的 shell 中，并运行该脚本。

现已装载 Azure 文件共享。

### <a name="mount-the-azure-file-share-with-file-explorer"></a>使用文件资源管理器装载 Azure 文件共享
> [!Note]  
> 请注意，以下说明是在 Windows 10 上显示的，在较旧的版本上可能稍有不同。 

1. 打开文件资源管理器。 若要执行此操作，可从“开始”菜单打开，或者按 Win+E 快捷方式。

1. 导航到窗口左侧的 "**此电脑**"。 这样会更改功能区中的可用菜单。 在“计算机”菜单中，选择“映射网络驱动器”。
    
    ![“映射网络驱动器”下拉菜单的屏幕截图](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

1. 选择驱动器号并输入 UNC 路径，路径格式为 `\\<storageAccountName>.file.core.windows.net\<fileShareName>`。 例如：`\\anexampleaccountname.file.core.windows.net\example-share-name`。
    
    ![“映射网络驱动器”对话框的屏幕截图](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

1. 使用带 `AZURE\` 前缀的存储帐户名称作为用户名，使用存储帐户密钥作为密码。
    
    ![网络凭据对话框的屏幕快照](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

1. 根据需要使用 Azure 文件共享。
    
    ![Azure 文件共享现已装载](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

1. 做好卸载 Azure 文件共享的准备以后，即可在文件资源管理器中右键单击“网络位置”下对应于共享的条目，然后选择“断开连接”。 

### <a name="accessing-share-snapshots-from-windows"></a>从 Windows 访问共享快照
如果已手动或通过脚本或 Azure 备份等服务自动获取共享快照，则可以从 Windows 上的文件共享查看以前版本的共享、目录或特定文件。 可以使用[Azure PowerShell](storage-how-to-use-files-powershell.md)、 [Azure CLI](storage-how-to-use-files-cli.md)或[Azure 门户](storage-how-to-use-files-portal.md)拍摄共享快照。

#### <a name="list-previous-versions"></a>列出以前版本
浏览到需要还原的项或父项。 通过双击转到所需的目录。 右键单击，然后从菜单中选择“属性”。

![所选目录的右键单击菜单](./media/storage-how-to-use-files-windows/snapshot-windows-previous-versions.png)

选择"以前版本”，以查看此目录的共享快照列表。 列表可能需要几秒钟才能加载，具体要取决于网速和目录中共享快照的数量。

![“以前版本”选项卡](./media/storage-how-to-use-files-windows/snapshot-windows-list.png)

可以选择“打开”以打开特定快照。 

![打开的快照](./media/storage-how-to-use-files-windows/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>从以前版本还原
选择“还原”，以递归方式将整个目录在共享快照创建时包含的内容复制到原始位置。

 ![警告消息中的“还原”按钮](./media/storage-how-to-use-files-windows/snapshot-windows-restore.png) 

## <a name="securing-windowswindows-server"></a>保护 Windows/Windows Server
若要在 Windows 上装载 Azure 文件共享，必须确保端口 445 可以访问。 许多组织因 SMB 1 中固有的安全风险而阻止端口 445。 SMB 1 也称 CIFS（通用 Internet 文件系统），是 Windows 和 Windows Server 随附的旧版文件系统协议。 SMB 1 是过时且效率不高的协议，而且最重要的是，它是不安全的协议。 好消息是，Azure 文件不支持 SMB 1，并且所有受支持的 Windows 和 Windows Server 版本均可删除或禁用 SMB 1。 在将 Azure 文件共享用于生产环境之前，[强烈建议](https://aka.ms/stopusingsmb1)删除或禁用 Windows 中的 SMB 1 客户端和服务器。

下表详细说明了每个 Windows 版本中的 SMB 1 的状态：

| Windows 版本                           | SMB 1 默认状态 | 禁用/删除方法       | 
|-------------------------------------------|----------------------|-----------------------------|
| Windows Server 2019                       | 已禁用             | 通过 Windows 功能进行删除 |
| Windows Server，版本 1709+            | 已禁用             | 通过 Windows 功能进行删除 |
| Windows 10，版本 1709+                | 已禁用             | 通过 Windows 功能进行删除 |
| Windows Server 2016                       | Enabled              | 通过 Windows 功能进行删除 |
| Windows 10，版本 1507、1607 和 1703 | Enabled              | 通过 Windows 功能进行删除 |
| Windows Server 2012 R2                    | Enabled              | 通过 Windows 功能进行删除 | 
| Windows 8.1                               | Enabled              | 通过 Windows 功能进行删除 | 
| Windows Server 2012                       | Enabled              | 通过注册表进行禁用       | 
| Windows Server 2008 R2                    | Enabled              | 通过注册表进行禁用       |
| Windows 7                                 | Enabled              | 通过注册表进行禁用       | 

### <a name="auditing-smb-1-usage"></a>审核 SMB 1 使用情况
> 适用于 Windows Server 2019、Windows Server 半年频道（版本 1709 和 1803）、Windows Server 2016、Windows 10（版本 1507、1607、1703、1709 和 1803）、Windows Server 2012 R2 和 Windows 8.1

在删除环境中的 SMB 1 之前，可能需要审核 SMB 1 使用情况，看是否有客户端会因更改而受损。 如果有客户端通过 SMB 1 对 SMB 共享提出了请求，系统会在 `Applications and Services Logs > Microsoft > Windows > SMBServer > Audit` 下的事件日志中记录审核事件。 

> [!Note]  
> 若要在 Windows Server 2012 R2 和 Windows 8.1 上启用审核支持，请至少安装 [KB4022720](https://support.microsoft.com/help/4022720/windows-8-1-windows-server-2012-r2-update-kb4022720)。

若要启用审核，请通过权限提升的 PowerShell 会话执行以下 cmdlet：

```powershell
Set-SmbServerConfiguration –AuditSmb1Access $true
```

### <a name="removing-smb-1-from-windows-server"></a>从 Windows Server 中删除 SMB 1
> 适用于 Windows Server 2019、Windows Server 半年频道（版本 1709 和 1803）、Windows Server 2016、Windows Server 2012 R2

若要从 Windows Server 实例中删除 SMB 1，请通过权限提升的 PowerShell 会话执行以下 cmdlet：

```powershell
Remove-WindowsFeature -Name FS-SMB1
```

若要完成删除过程，请重启服务器。 

> [!Note]  
> 从 Windows 10 和 Windows Server 版本 1709 开始，SMB 1 不再默认安装，且 SMB 1 客户端和 SMB 1 服务器有不同的 Windows 功能。 我们始终建议将 SMB 1 服务器 (`FS-SMB1-SERVER`) 和 SMB 1 客户端 (`FS-SMB1-CLIENT`) 都卸载掉。

### <a name="removing-smb-1-from-windows-client"></a>从 Windows 客户端中删除 SMB 1
> 适用于 Windows 10（版本 1507、1607、1703、1709 和 1803）和 Windows 8.1

若要从 Windows 客户端中删除 SMB 1，请通过权限提升的 PowerShell 会话执行以下 cmdlet：

```powershell
Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol
```

若要完成删除过程，请重启电脑。

### <a name="disabling-smb-1-on-legacy-versions-of-windowswindows-server"></a>在旧版 Windows/Windows Server 上禁用 SMB 1
> 适用于 Windows Server 2012、Windows Server 2008 R2 和 Windows 7

不能在旧版 Windows/Windows Server 上彻底删除 SMB 1，但可以通过注册表来禁用它。 若要禁用 SMB 1，请在 `HKEY_LOCAL_MACHINE > SYSTEM > CurrentControlSet > Services > LanmanServer > Parameters` 下新建类型为 `DWORD` 且值为 `0` 的注册表项 `SMB1`。

也可使用以下 PowerShell cmdlet 轻松完成该操作：

```powershell
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
- [常见问题](../storage-files-faq.md)
- [在 Windows 上进行故障排除](storage-troubleshoot-windows-file-connection-problems.md)      
