---
title: "向 Azure 文件同步（预览版）注册/注销服务器 | Microsoft Docs"
description: "了解如何向 Azure 文件同步存储同步服务注册和注销 Windows Server。"
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
ms.openlocfilehash: 13a75d5cafd94435346660614721399f2d77919b
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2017
---
# <a name="registerunregister-a-server-with-azure-file-sync-preview"></a>向 Azure 文件同步（预览版）注册/注销服务器
借助 Azure 文件同步（预览版），既可将组织的文件共享集中在 Azure 文件中，又不失本地文件服务器的灵活性、性能和兼容性。 它通过将 Windows Server 转换为 Azure 文件共享的快速缓存来实现这一点。 可以使用 Windows Server 上任何可用协议在本地访问你的数据（包括 SMB、NFS 和 FTPS），并且可以根据需要在世界各地拥有尽可能多的缓存。

下面的文章说明如何向存储同步服务注册和注销服务器。 如果不再使用某台服务器，或如果在同步组中需要新服务器终结点，则可能需要执行此过程。 若要了解如何部署端到端的 Azure 文件同步，请参阅[如何部署 Azure 文件同步（预览版）](storage-sync-files-deployment-guide.md)。

## <a name="prerequisites"></a>先决条件
若要向存储同步服务注册 Windows Server，首先必须准备好具有所需先决条件的 Windows Server：

* 确保已部署存储同步服务。 有关如何部署存储同步服务的详细信息，请参阅[如何部署 Azure 文件同步（预览版）](storage-sync-files-deployment-guide.md)。
* 确保服务器已连接到 Internet，并且 Azure 可以访问。
* 使用服务器管理器 UI 禁用适用于管理员的 IE 增强的安全配置。
    
    ![突出显示“IE 增强的安全配置”的服务器管理器 UI](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* 确保服务器上安装了 AzureRM PowerShell 模块。 如果服务器是故障转移群集的成员，则群集中的每个节点都需要 AzureRM 模块。 有关如何安装 AzureRM 模块的更多详细信息可以在[安装和配置 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) 上找到。

    > [!Note]  
    > 我们建议使用最新版本的 AzureRM PowerShell 模块注册/注销服务器。 如果 AzureRM 包之前已安装在此服务器上（并且此服务器上的 PowerShell 版本是 5.* 或更高版本），则可以使用 `Update-Module` cmdlet 更新此包。 

## <a name="register-a-server-with-storage-sync-service"></a>向存储同步服务注册服务器
Windows Server 必须先向存储同步服务注册，然后才能在 Azure 文件同步同步组中用作服务器终结点。 服务器一次只能向一个存储同步服务注册。

### <a name="install-the-azure-file-sync-agent"></a>安装 Azure 文件同步代理
1. [下载 Azure 文件同步代理](https://go.microsoft.com/fwlink/?linkid=858257)。
2. 启动 Azure 文件同步代理安装程序。
    
    ![Azure 文件同步代理安装程序的第一个窗格](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. 请务必使用 Microsoft 更新启用 Azure 文件同步代理更新。 这非常重要，因为服务器包的关键安全修补和功能增强通过 Microsoft 更新提供。

    ![确保在 Azure 文件同步代理安装程序的“Microsoft 更新”窗格中启用 Microsoft 更新](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. 如果以前尚未注册服务器，则服务器注册 UI 会在完成安装之后立即弹出。

> [!Important]  
> 如果服务器是故障转移群集的成员，则需要在群集中的每个节点上安装 Azure 文件同步代理。

### <a name="register-the-server-using-the-server-registration-ui"></a>使用服务器注册 UI 注册服务器

> [!Important]  
> 云解决方案提供商订阅不能使用服务器注册 UI， 请改用 PowerShell（在本部分下面介绍）。

1. 如果服务器注册 UI 在完成 Azure 文件同步代理安装之后未立即启动，则可以通过执行 `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe` 来手动启动它。
2. 单击“登录”以访问 Azure 订阅。 

    ![服务器注册 UI 的打开对话框](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. 从对话框中选取正确的订阅、资源组和存储同步服务。

    ![存储同步服务信息](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. 在预览版中，需要再进行一次登录来完成过程。 

    ![“登录”对话框](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> 如果服务器是故障转移群集的成员，则每台服务器都需要运行服务器注册。 在 Azure 门户中查看已注册的服务器时，Azure 文件同步会自动将每个节点识别为相同故障转移群集的成员，并相应地将它们分组在一起。

### <a name="register-the-server-with-powershell"></a>使用 PowerShell 注册服务器
也可以通过 PowerShell 执行服务器注册。 这是云解决方案提供商订阅唯一支持的服务器注册方法：

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<your-subscription-id>" -TenantID "<your-tenant-id>"
Register-AzureRmStorageSyncServer -SubscriptionId "<your-subscription-id>" - ResourceGroupName "<your-resource-group-name>" - StorageSyncService "<your-storage-sync-service-name>"
```

## <a name="unregister-the-server-with-storage-sync-service"></a>向存储同步服务注销服务器
向存储同步服务注销服务器需要几个步骤。 让我们看看如何正确注销服务器。

### <a name="optional-recall-all-tiered-data"></a>（可选）召回所有分层数据
为服务器终结点启用时，云分层会将文件分层到 Azure 文件共享。 这使本地文件共享可以充当缓存（而不是数据集的完整副本），以便高效使用文件服务器上的空间。 但是，如果在分层文件仍处于服务器本地的情况下删除服务器终结点，则这些文件会不可访问。 因此，如果需要继续进行文件访问，则必须从 Azure 文件召回所有分层文件，然后再继续进行注销。 

这可以使用 PowerShell cmdlet 完成，如下所示：

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

> [!Warning]  
> 如果承载服务器的本地卷没有足够可用空间可用于召回所有分层数据，则 `Invoke-StorageSyncFileRecall` cmdlet 会失败。  

### <a name="remove-the-server-from-all-sync-groups"></a>从所有同步组中删除服务器
必须删除服务器的所有服务器终结点，然后才能在存储同步服务中注销该服务器。 可以通过门户完成此操作：

1. 导航到在其中注册服务器的存储同步服务。
2. 在存储同步服务中的每个同步组中，删除此服务器的所有服务器终结点。 这可以通过在“同步组”窗格中右键单击相关服务器终结点来完成。

    ![从同步组中删除服务器终结点](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

这还可以通过简单的 PowerShell 脚本来实现：

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"

$accountInfo = Login-AzureRmAccount
Login-AzureRmStorageSync -SubscriptionId $accountInfo.Context.Subscription.Id -TenantId $accountInfo.Context.Tenant.Id -ResourceGroupName "<your-resource-group>"

$StorageSyncService = "<your-storage-sync-service>"

Get-AzureRmStorageSyncGroup -StorageSyncServiceName $StorageSyncService | ForEach-Object { 
    $SyncGroup = $_; 
    Get-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name | Where-Object { $_.DisplayName -eq $env:ComputerName } | ForEach-Object { 
        Remove-AzureRmStorageSyncServerEndpoint -StorageSyncServiceName $StorageSyncService -SyncGroupName $SyncGroup.Name -ServerEndpointName $_.Name 
    } 
}
```

### <a name="unregister-the-server"></a>注销服务器
现在已召回数据并从所有同步组中删除了服务器，服务器可以进行注销。 

1. 在 Azure 门户中，导航到存储同步服务的“已注册的服务器”部分。
2. 右键单击要注销的服务器，然后单击“注销服务器”。

    ![注销服务器](media/storage-sync-files-server-registration/unregister-server-1.png)