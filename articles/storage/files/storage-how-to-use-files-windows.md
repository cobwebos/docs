---
title: 将 Azure 文件共享与 Windows 配合使用 | Microsoft Docs
description: 了解如何在 Windows 和 Windows Server 中使用 Azure 文件共享。 在本地或 Azure Vm 上运行的 Windows 安装上，使用 SMB 3.0 的 Azure 文件共享。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c8a1d1c0f8de742bdafa130cce6927a472efd8f7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91329340"
---
# <a name="use-an-azure-file-share-with-windows"></a>将 Azure 文件共享与 Windows 配合使用
[Azure 文件](storage-files-introduction.md)是 Microsoft 推出的易用云文件系统。 Azure 文件共享可以在 Windows 和 Windows Server 中无缝使用。 本文介绍在 Windows 和 Windows Server 中使用 Azure 文件共享时的注意事项。

若要在某个 Azure 文件共享的托管 Azure 区域（例如本地或其他 Azure 区域）外部使用该文件共享，OS 必须支持 SMB 3.0。 

可在 Azure VM 或本地运行的 Windows 安装中使用 Azure 文件共享。 下表说明了哪些 OS 版本支持在哪个环境中访问文件共享：

| Windows 版本        | SMB 版本 | 可以在 Azure VM 中装载 | 可以在本地装载 |
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

<sup>1</sup>Windows 10 版本1507、1607、1803、1809、1903、1909和2004。  
<sup>2</sup>Windows Server，版本1809、1903、1909、2004。  
<sup>3</sup>Microsoft 对 Windows 7 和 Windows Server 2008 R2 的常规支持已结束。 只有通过[扩展安全更新 (ESU) 程序](https://support.microsoft.com/help/4497181/lifecycle-faq-extended-security-updates)才能购买对安全更新的附加支持。 我们强烈建议从这些操作系统中迁移。

> [!Note]  
> 我们始终建议使用相对于 Windows 版本来说最新的 KB。

## <a name="prerequisites"></a>先决条件 

确保端口 445 处于打开状态：SMB 协议要求 TCP 端口 445 处于打开状态；如果端口 445 已被阻止，连接将会失败。 可以使用 `Test-NetConnection` cmdlet 检查防火墙是否正在阻止端口 445。 若要了解如何解决 445 端口被阻止的问题，请参阅 Windows 故障排除指南的[原因 1：端口 445 被阻止](storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked)部分。

## <a name="using-an-azure-file-share-with-windows"></a>在 Windows 中使用 Azure 文件共享
若要在 Windows 中使用某个 Azure 文件共享，必须装载该文件共享（为其分配驱动器号或装载点路径），或通过其 [UNC 路径](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx)来访问它。 

本文使用存储帐户密钥来访问文件共享。 存储帐户密钥是用于存储帐户的管理员密钥，包括对你要访问的文件共享中所有文件和文件夹的管理员权限，以及对所有文件共享和其他包含在存储帐户中的存储资源（Blob、队列、表等）的权限。 如果这对你的工作负载来说还不够，可使用 [Azure 文件同步](storage-sync-files-planning.md)，或者可使用[通过 SMB 的基于标识的身份验证](storage-files-active-directory-overview.md)。

若要将预期使用 SMB 文件共享的业务线 (LOB) 应用程序直接迁移到 Azure，通常的模式是使用 Azure 文件共享，而不是在 Azure VM 中运行专用的 Windows 文件服务器。 成功迁移业务线应用程序以使用 Azure 文件共享的一个重要注意事项是，许多业务线应用程序在具有有限系统权限的专用服务帐户的上下文中运行，而不是在 VM 的管理帐户下运行。 因此，必须确保装载/保存服务帐户上下文（而不是管理帐户）中 Azure 文件共享的凭据。

### <a name="mount-the-azure-file-share"></a>装载 Azure 文件共享

Azure 门户为你提供了一个脚本，你可以使用该脚本将文件共享直接装载到主机。 我们建议使用这个提供的脚本。

若要获取此脚本，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 导航到包含要装载的文件共享的存储帐户。
1. 选择“文件共享”。
1. 选择要装载的文件共享。

    :::image type="content" source="media/storage-how-to-use-files-windows/select-file-shares.png" alt-text="示例":::

1. 选择“连接” 。

    :::image type="content" source="media/storage-how-to-use-files-windows/file-share-connect-icon.png" alt-text="文件共享的“连接”图标的屏幕截图。":::

1. 选择要将共享装载到的驱动器号。
1. 复制所提供的脚本。

    :::image type="content" source="media/storage-how-to-use-files-windows/files-portal-mounting-cmdlet-resize.png" alt-text="示例文本":::

1. 将脚本粘贴到你要将文件共享装载到的主机上的 shell 中，然后运行该脚本。

现已装载 Azure 文件共享。

### <a name="mount-the-azure-file-share-with-file-explorer"></a>使用文件资源管理器装载 Azure 文件共享
> [!Note]  
> 请注意，以下说明是在 Windows 10 上显示的，在较旧的版本上可能稍有不同。 

1. 打开文件资源管理器。 可以从“开始”菜单打开，也可以按 Win+E 快捷键打开文件资源管理器。

1. 导航到窗口左侧的“此电脑”。 这样会更改功能区中的可用菜单。 在“计算机”菜单中，选择“映射网络驱动器”。
    
    ![“映射网络驱动器”下拉菜单的屏幕截图](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

1. 选择驱动器号并输入 UNC 路径，UNC 路径格式为 `\\<storageAccountName>.file.core.windows.net\<fileShareName>`。 例如：`\\anexampleaccountname.file.core.windows.net\example-share-name`。
    
    ![“映射网络驱动器”对话框的屏幕截图](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

1. 使用带 `AZURE\` 前缀的存储帐户名称作为用户名，使用存储帐户密钥作为密码。
    
    ![网络凭据对话框的屏幕快照](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

1. 根据需要使用 Azure 文件共享。
    
    ![Azure 文件共享现已装载](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

1. 做好卸载 Azure 文件共享的准备后，可在文件资源管理器中右键单击“网络位置”下对应于共享的条目，并选择“断开连接”。 

### <a name="accessing-share-snapshots-from-windows"></a>从 Windows 访问共享快照
如果已手动或通过脚本或 Azure 备份等服务自动获取共享快照，则可以从 Windows 上的文件共享查看以前版本的共享、目录或特定文件。 可以使用 [Azure PowerShell](storage-how-to-use-files-powershell.md)、[Azure CLI](storage-how-to-use-files-cli.md) 或 [Azure 门户](storage-how-to-use-files-portal.md)创建共享快照。

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
若要在 Windows 上装载 Azure 文件共享，端口 445 必须可访问。 由于 SMB 1 固有的安全风险，许多组织会阻止端口 445。 SMB 1（也称为通用 Internet 文件系统，简称 CIFS）是 Windows 和 Windows Server 中随附的一个传统文件系统协议。 SMB 1 是一个已过时的低效协议，最重要的是，它不安全。 好消息是 Azure 文件不支持 SMB 1，所有支持的 Windows 和 Windows Server 版本允许删除或禁用 SMB 1。 我们始终[强烈建议](https://aka.ms/stopusingsmb1)在生产环境中使用 Azure 文件共享之前，删除或禁用 Windows 中的 SMB 1 客户端和服务器。

下表提供了有关每个 Windows 版本上 SMB 1 状态的详细信息：

| Windows 版本                           | SMB 1 默认状态 | 禁用/删除方法       | 
|-------------------------------------------|----------------------|-----------------------------|
| Windows Server 2019                       | 已禁用             | 使用 Windows 功能删除 |
| Windows Server 版本 1709+            | 已禁用             | 使用 Windows 功能删除 |
| Windows 10 版本 1709+                | 已禁用             | 使用 Windows 功能删除 |
| Windows Server 2016                       | 已启用              | 使用 Windows 功能删除 |
| Windows 10 版本 1507、1607 和 1703 | 已启用              | 使用 Windows 功能删除 |
| Windows Server 2012 R2                    | 已启用              | 使用 Windows 功能删除 | 
| Windows 8.1                               | 已启用              | 使用 Windows 功能删除 | 
| Windows Server 2012                       | 已启用              | 使用注册表禁用       | 
| Windows Server 2008 R2                    | 已启用              | 使用注册表禁用       |
| Windows 7                                 | 已启用              | 使用注册表禁用       | 

### <a name="auditing-smb-1-usage"></a>审核 SMB 1 使用情况
> 适用于 Windows Server 2019、Windows Server 半年通道（版本 1709 和 1803）、Windows Server 2016、Windows 10（版本 1507、1607、1703、1709 和 1803）、Windows Server 2012 R2 和 Windows 8.1

在环境中删除 SMB 1 之前，可以审核 SMB 1 使用情况，以确定所做的更改是否会中断任何客户端。 如果针对使用 SMB 1 的 SMB 共享发出了任何请求，将在事件日志中的 `Applications and Services Logs > Microsoft > Windows > SMBServer > Audit` 下面记录一个审核事件。 

> [!Note]  
> 若要在 Windows Server 2012 R2 和 Windows 8.1 上启用审核支持，至少应安装 [KB4022720](https://support.microsoft.com/help/4022720/windows-8-1-windows-server-2012-r2-update-kb4022720)。

若要启用审核，请在权限提升的 PowerShell 会话中执行以下 cmdlet：

```powershell
Set-SmbServerConfiguration –AuditSmb1Access $true
```

### <a name="removing-smb-1-from-windows-server"></a>从 Windows Server 中删除 SMB 1
> 适用于 Windows Server 2019、Windows Server 半年通道（版本 1709 和 1803）、Windows Server 2016、Windows Server 2012 R2

若要从 Windows Server 实例中删除 SMB 1，请在权限提升的 PowerShell 会话中执行以下 cmdlet：

```powershell
Remove-WindowsFeature -Name FS-SMB1
```

若要完成删除过程，请重启服务器。 

> [!Note]  
> 从 Windows 10 和 Windows Server 版本 1709 开始，默认不会安装 SMB 1，SMB 1 客户端和 SMB 1 服务器有独立的 Windows 功能。 我们始终建议保持卸载 SMB 1 服务器 (`FS-SMB1-SERVER`) 和 SMB 1 客户端 (`FS-SMB1-CLIENT`)。

### <a name="removing-smb-1-from-windows-client"></a>从 Windows 客户端中删除 SMB 1
> 适用于 Windows 10（版本 1507、1607、1703、1709 和 1803）和 Windows 8.1

若要从 Windows 客户端中删除 SMB 1，请在权限提升的 PowerShell 会话中执行以下 cmdlet：

```powershell
Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol
```

若要完成删除过程，请重启电脑。

### <a name="disabling-smb-1-on-legacy-versions-of-windowswindows-server"></a>在早期版本的 Windows/Windows Server 上禁用 SMB 1
> 适用于 Windows Server 2012、Windows Server 2008 R2 和 Windows 7

无法在早期版本的 Windows/Windows Server 上完全删除 SMB 1，但可以通过注册表将其禁用。 若要禁用 SMB 1，请创建 `DWORD` 类型的新注册表项 `SMB1`，并在 `HKEY_LOCAL_MACHINE > SYSTEM > CurrentControlSet > Services > LanmanServer > Parameters` 下面添加值 `0`。

也可以使用以下 PowerShell cmdlet 轻松实现此目的：

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
