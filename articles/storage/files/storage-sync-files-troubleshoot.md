---
title: "对 Azure 文件同步（预览版）进行故障排除 | Microsoft Docs"
description: "对 Azure 文件同步的常见问题进行故障排除"
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 6bc5f2da2b8628671037b9257db746e73cd3afad
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2017
---
# <a name="troubleshoot-azure-file-sync-preview"></a>对 Azure 文件同步（预览版）进行故障排除
借助 Azure 文件同步（预览版），既可将组织的文件共享集中在 Azure 文件中，又不失本地文件服务器的灵活性、性能和兼容性。 它通过将 Windows Server 转换为 Azure 文件共享的快速缓存来实现这一点。 可以使用 Windows Server 上任何可用协议在本地访问你的数据（包括 SMB、NFS 和 FTPS），并且可以根据需要在世界各地拥有尽可能多的缓存。

本文旨在帮助排查和解决在 Azure 文件同步部署中遇到的问题。 否则，本指南说明如何从系统收集重要日志，以帮助进行更深入的问题调查。 如果本文未能帮你解答疑惑，欢迎通过以下方式联系我们（以升序排列）：

1. 在本文注释部分。
2. [Azure 存储论坛](https://social.msdn.microsoft.com/Forums/home?forum=windowsazuredata)
3. [Azure 文件 UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) 
4. Microsoft 支持部门：若要创建新的支持案例，请在 Azure 门户上导航到“帮助 + 支持”选项卡，然后单击“新建支持请求”。

## <a name="agent-installation-and-server-registration"></a>代理安装和服务器注册
<a id="agent-installation-failures"></a>如何对代理安装失败进行故障排除  
如果 Azure 文件同步代理安装失败，请从提升的命令提示符运行以下命令以在代理安装过程中启用日志记录：

```
StorageSyncAgent.msi /l*v Installer.log
```

一旦安装失败，检查 installer.log 以确定原因。 

> [!Note]  
> 如果选择使用 Microsoft 更新，代理安装将失败，并且 Windows 更新服务不会运行。

<a id="server-registration-missing"></a>服务器未在 Azure 门户中的“已注册的服务器”下列出  
如果对于存储同步服务，服务器未在“已注册的服务器”下列出，请执行以下步骤：
1. 登录要注册的服务器。
2. 打开文件资源管理器并导航到存储同步代理安装目录（默认位置是 `C:\Program Files\Azure\StorageSyncAgent`）。 
3. 运行 ServerRegistration.exe 并按照向导向存储同步服务注册服务器。

<a id="server-already-registered"></a>服务器注册会在安装 Azure 文件同步代理之后显示以下消息：“此服务器已注册”  
![包含“此服务器已注册”错误消息的服务器注册对话框屏幕截图](media/storage-sync-files-troubleshoot/server-registration-1.png)

如果以前向存储同步服务注册了服务器，则会显示此消息。 若要向当前存储同步服务注销服务器并向新存储同步服务进行注册，请执行步骤[向 Azure 文件同步注销服务器](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)。

如果存储同步服务中的已注册服务器下未列出该服务器，则在你想要取消注册的服务器上运行下列 PowerShell 命令：

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> 如果该服务器是群集的一部分，则存在还将删除群集注册的可选 `Reset-StorageSyncServer -CleanClusterRegistration` 参数。

<a id="web-site-not-trusted"></a>注册服务器时，我收到了许多“网站不受信任”的响应，为什么？  
发生此错误的原因是在服务器注册过程中启用了增强 Internet Explorer 安全策略。 有关如何以正确的方式禁用增强 Internet Explorer 安全策略的详细信息，请参阅[准备 Windows Server 以使用 Azure 文件同步](storage-sync-files-deployment-guide.md#prepare-windows-servers-for-use-with-azure-file-sync)，以及[如何部署 Azure 文件同步（预览版）](storage-sync-files-deployment-guide.md)。

## <a name="sync-group-management"></a>同步组管理
<a id="cloud-endpoint-using-share"></a>云终结点创建失败，并显示以下错误：“指定的 Azure 文件共享已被其他 CloudEndpoint 使用”  
如果 Azure 文件共享已被其他云终结点使用，则会发生此错误。 

如果收到此错误，但 Azure 文件共享当前未被云终结点使用，请执行以下步骤以清除 Azure 文件共享上的 Azure 文件同步元数据：

> [!Warning]  
> 删除云终结点当前正在使用的 Azure 文件共享上的元数据将导致 Azure 文件同步操作失败。 

1. 在 Azure 门户中导航到“Azure 文件共享”。  
2. 右键单击 Azure 文件共享，然后选择“编辑元数据”
3. 右键单击 SyncService，并选择“删除”。

<a id="cloud-endpoint-authfailed"></a>云终结点创建失败，并显示错误：AuthorizationFailed  
当用户帐户缺少足够权限来创建云终结点时，将出现此问题。 

要创建云终结点，用户帐户必须具有下列 Microsoft 授权权限：  
* 读取：获取角色定义
* 写入：创建或更新自定义角色定义
* 读取：获取角色分配
* 写入：创建角色分配

下列内置角色具有适当的 Microsoft 授权权限：  
* 所有者
* 用户访问管理员

要判断自己的用户帐户角色色否具有适当的权限，请执行以下操作：  
* 在 Azure 门户中，单击“资源组”
* 选择存储帐户所在的资源组，并单击“访问控制(IAM)”
* 单击用户帐户的“角色”（如“所有者”或“参与者”）。
* 在“资源提供程序”列表中，选择“Microsoft 授权” 
* “角色分配”应具有“读取”和“写入”权限
* “角色定义”应具有“读取”和“写入”权限

<a id="cloud-endpoint-deleteinternalerror"></a>终结点删除失败，并显示错误：MgmtInternalError  
如果在删除云终结点前先删除了 Azure 文件共享或存储帐户，则可能出现此问题。 此问题将在未来版本中得到修复，可以删除云终结点。

要防止此问题的发生，请在删除 Azure 文件共享或存储帐户前，先删除云终结点。

## <a name="sync"></a>同步
<a id="afs-change-detection"></a>**我通过 SMB 或门户在 Azure 文件共享中直接创建了一个文件。多久后该文件才会同步到同步组中的服务器上？**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="broken-sync"></a>如何对未在服务器上运行的同步进行故障排除  
如果同步在服务器上失败，则执行以下操作：
- 验证对于要同步到 Azure 文件共享的目录，Azure 门户中是否存在服务器终结点：
    
    ![在 Azure 门户中具有云和服务器终结点的同步组屏幕截图](media/storage-sync-files-troubleshoot/sync-troubleshoot-1.png)

- 使用事件查看器检查位于 `Applications and Services\Microsoft\FileSync\Agent` 下的操作和诊断事件日志。
- 确认服务器具有 Internet 连接。
- 通过打开服务 MMC 管理单元来验证 Azure 文件同步服务是否正在服务器上运行，并验证存储同步代理服务 (FileSyncSvc) 是否正在运行。

<a id="replica-not-ready"></a>同步失败，并显示错误：0x80c8300f - 副本未准备就绪，无法执行所选操作  
如果创建云终结点并使用包含数据的 Azure 文件共享，则可能出现此错误。 完成 Azure 文件共享上的更改检测（最久可能需要 24 小时）后，同步应可以正常运行。

<a id="broken-sync-files"></a>如何对未能同步的单个文件进行故障排除  
如果单个文件未能同步，请执行以下操作：
- 在事件查看器中检查位于 `Applications and Services\Microsoft\FileSync\Agent` 下的操作和诊断事件日志
- 验证文件上是否不存在打开的句柄
    - 注意：Azure 文件同步会定期拍摄 VSS 快照以同步具有打开的句柄的文件

## <a name="cloud-tiering"></a>云分层 
<a id="files-fail-tiering"></a>如何对未能分层的文件进行故障排除  
如果文件未能分层到 Azure 文件，请执行以下步骤：

- 验证文件是否存在于 Azure 文件共享中
    - 注意：文件必须先同步到 Azure 文件共享，然后才能进行分层
- 在事件查看器中检查位于 `Applications and Services\Microsoft\FileSync\Agent` 下的操作和诊断事件日志
- 确认服务器具有 Internet 连接 
- 验证 Azure 文件同步筛选器驱动程序 (StorageSync.sys & StorageSyncGuard.sys) 是否正在运行
    - 打开提升的命令提示符，运行 `fltmc` 并验证 StorageSync.sys 和 StorageSyncGuard.sys 文件系统筛选器驱动程序是否列出

<a id="files-fail-recall"></a>如何对未能召回的文件进行故障排除  
如果文件未能召回，请执行以下步骤：
- 在事件查看器中检查位于 `Applications and Services\Microsoft\FileSync\Agent` 下的操作和诊断事件日志
- 验证文件是否存在于 Azure 文件共享中
- 确认服务器具有 Internet 连接 
- 验证 Azure 文件同步筛选器驱动程序 (StorageSync.sys & StorageSyncGuard.sys) 是否正在运行
    - 打开提升的命令提示符，运行 `fltmc` 并验证 StorageSync.sys 和 StorageSyncGuard.sys 文件系统筛选器驱动程序是否列出

<a id="files-unexpectedly-recalled"></a>如何对服务器上意外召回的文件进行故障排除  
读取大量文件的防病毒、备份和其他应用程序会导致不需要的召回，除非它们遵从脱机属性并跳过读取这些文件的内容。 跳过支持它的这些产品的脱机文件可帮助避免在执行防病毒软件扫描或备份作业这类操作时出现不需要的召回。

请与软件供应商协商如何配置其解决方案以跳过读取脱机文件。

在其他情况（如在文件资源管理器中浏览文件）下可能会发生其他不需要的召回。 在服务器上的文件资源管理器中打开包含云分层文件的文件夹可能会导致不需要的召回，在服务器上启用了防病毒时更是如此。

## <a name="general-troubleshooting"></a>常规故障排除
如果在服务器上遇到 Azure 文件同步问题，则首先执行以下操作：
- 在事件查看器中检查诊断和操作事件日志
    - 同步、分层和召回问题会记录在位于 `Applications and Services\Microsoft\FileSync\Agent` 下的诊断和操作事件日志中
    - 管理服务器（例如配置设置）方面的问题会记录在位于 `Applications and Services\Microsoft\FileSync\Management` 下的诊断和操作事件日志中
- 验证 Azure 文件同步服务是否正在服务器上运行
    - 打开服务 MMC 管理单元，然后验证存储同步代理服务 (FileSyncSvc) 是否正在运行
- 验证 Azure 文件同步筛选器驱动程序 (StorageSync.sys & StorageSyncGuard.sys) 是否正在运行
    - 打开提升的命令提示符，运行 fltmc 并验证 StorageSync.sys 和 StorageSyncGuard.sys 文件系统筛选器驱动程序是否列出

如果在执行以上步骤之后未解决问题，请通过执行以下步骤来运行 AFSDiag 工具：
1. 创建将用于保存 AFSDiag 输出的目录（例如，c:\output）。
2. 打开提升的 PowerShell 窗口并运行以下命令（在输入每个命令之后按 Enter）：

    ```PowerShell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: use the path created in step 1
    ```

3. 对于 Azure 文件同步内核模式跟踪级别中，输入 1（除非指定为创建更详细的跟踪），然后按 Enter。
4. 对于 Azure 文件同步用户模式跟踪级别中，输入 1（除非指定为创建更详细的跟踪），然后按 Enter。
5. 重现问题，然后在完成时按 D。
6. 包含日志和跟踪文件的 .zip 文件会处于指定的输出目录中。

## <a name="see-also"></a>另请参阅
- [Azure 文件常见问题解答](storage-files-faq.md)
- [在 Windows 中排查 Azure 文件问题](storage-troubleshoot-windows-file-connection-problems.md)
- [在 Linux 中排查 Azure 文件问题](storage-troubleshoot-linux-file-connection-problems.md)
