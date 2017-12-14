---
title: "部署 Azure 文件同步（预览版）| Microsoft Docs"
description: "了解如何从头到尾部署 Azure 文件同步。"
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
ms.openlocfilehash: 7b4de3e7b7e98ab76c02ea7c1cf069cee94706fc
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2017
---
# <a name="deploy-azure-file-sync-preview"></a>部署 Azure 文件同步（预览版）
使用 Azure 文件同步（预览版），即可将组织的文件共享集中在 Azure 文件中，又不失本地文件服务器的灵活性、性能和兼容性。 Azure 文件同步可将 Windows Server 转换为 Azure 文件共享的快速缓存。 可以使用 Windows Server 上可用的任意协议本地访问数据，包括 SMB、NFS 和 FTPS。 并且可以根据需要在世界各地具有多个缓存。

强烈建议先阅读[规划 Azure 文件部署](storage-files-planning.md)和[规划 Azure 文件同步部署](storage-sync-files-planning.md)，再按照本文中的步骤进行操作。

## <a name="prerequisites"></a>先决条件
* Azure 存储帐户和 Azure 文件共享位于要部署 Azure 文件同步的相同区域。有关详细信息，请参阅：
    - Azure 文件同步的[适用地区](storage-sync-files-planning.md#region-availability)。
    - [创建存储帐户](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)，了解创建存储帐户的分步说明。
    - [创建文件共享](storage-how-to-create-file-share.md)，了解创建文件共享的分步说明。
* 至少一个支持与 Azure 文件同步进行同步的 Windows Server 实例或 Windows Server 群集。有关支持的 Windows Server 版本的详细信息，请参阅 [Windows Server 的互操作性](storage-sync-files-planning.md#azure-file-sync-interoperability)。

## <a name="deploy-the-storage-sync-service"></a>部署存储同步服务 
存储同步服务是 Azure 文件同步的顶级 Azure 资源。要部署存储同步服务，请转到 [Azure 门户](https://portal.azure.com/)，然后搜索 Azure 文件同步。在搜索结果中，选择“Azure 文件同步(预览版)”，然后选择“创建”打开“部署存储同步”选项卡。

在打开的窗格中，输入以下信息：

- 名称：存储同步服务的唯一名称（按订阅）。
- 订阅：需要在其中创建存储同步服务的订阅。 根据组织的配置策略，可能有权访问一个或多个订阅。 Azure 订阅是对每项云服务（如 Azure 文件）计费的最基本容器。
- 资源组：资源组是 Azure 资源（如存储帐户或存储同步服务）的逻辑组。 可以为 Azure 文件同步创建新的资源组，也可对其使用现有资源组。（建议使用资源组作为用于从逻辑上隔离组织资源的容器，例如对 HR 资源或特定项目资源进行分组。）
- 位置：要在其中部署 Azure 文件同步的区域。此列表仅提供支持的区域。

完成后，选择“创建”部署存储同步服务。

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>准备 Windows Server，用于 Azure 文件同步
对于每个要与 Azure 文件同步配合使用的服务器（包括故障转移群集中的服务器节点），请完成以下步骤：

1. 禁用“Internet Explorer 增强的安全性配置”。 只需在最初注册服务器时禁用。 可在注册服务器后重新启用。
    1. 打开服务器管理器。
    2. 单击“本地服务器”：  
        ![服务器管理器 UI 左侧的“本地服务器”](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
    3. 在“属性”子窗格上，选择“IE 增强的安全性配置”的链接。  
        ![服务器管理器 UI 中的“IE 增强的安全性配置”窗格](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
    4. 对于“Internet Explorer 增强的安全性配置”对话框中的“管理员”和“用户”，都选择“关”：  
        ![选定“关”的“Internet Explorer 增强的安全性配置”弹出窗口](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

2. 确保至少正在运行 PowerShell 5.1.\*（PowerShell 5.1 是 Windows Server 2016 上的默认版本）。 可通过查看“$PSVersionTable”对象的“PSVersion”属性值来验证是否正在运行 PowerShell 5.1.\*：

    ```PowerShell
    $PSVersionTable.PSVersion
    ```

    与 Windows Server 2012 R2 的大多数安装一样，如果 PSVersion 值低于 5.1.\*，可通过下载并安装 [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616) 轻松升级。 需下载和安装的 Windows Server 2012 R2 的相应包为 Win8.1AndW2K12R2-KB\*\*\*\*\*\*\*-x64.msu。

3. [安装和配置 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)。 建议使用最新版本的 Azure PowerShell 模块。

## <a name="install-the-azure-file-sync-agent"></a>安装 Azure 文件同步代理
Azure 文件同步代理是一个可下载包，可实现 Windows 服务器与 Azure 文件共享的同步。 可从 [Microsoft 下载中心](https://go.microsoft.com/fwlink/?linkid=858257)下载代理。 下载完成后，双击 MSI 包，开始安装 Azure 文件同步代理。

> [!Important]  
> 如果要对故障转移群集使用 Azure 文件同步，则 必须在群集中的每个节点上安装 Azure 文件同步代理。

Azure 文件同步代理安装包的安装速度应相对较快，没有太多其他提示。 建议执行以下操作：
- 保留默认安装路径(C:\Program Files\Azure\StorageSyncAgent)，以简化故障排除和服务器维护。
- 启用 Microsoft 更新，使 Azure 文件同步保持最新。 Azure 文件同步代理的所有更新（包括功能更新和修补程序）都可从 Microsoft 更新进行。 建议安装 Azure 文件同步的最新更新。有关详细信息，请参阅 [Azure 文件同步更新策略](storage-sync-files-planning.md#azure-file-sync-agent-update-policy)。

Azure 文件同步代理安装完成后，服务器注册 UI 自动打开。 要了解如何向 Azure 文件同步注册此服务器，请参阅下一节。

## <a name="register-windows-server-with-storage-sync-service"></a>向存储同步服务注册 Windows Server
向存储同步服务注册 Windows Server 可在服务器（或群集）与存储同步服务之间建立信任关系。 服务器注册 UI 应在 Azure 文件同步代理安装后自动打开。 如果没有打开，可以手动从其文件位置 C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe 打开。 服务器注册 UI 打开时，请选择“登录”开始操作。

登录后，系统会提示输入以下信息：

![服务器注册 UI 的屏幕快照](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- Azure 订阅：包含存储同步服务的订阅（请参阅[部署存储同步服务](#deploy-the-storage-sync-service)）。 
- 资源组：包含存储同步服务的资源组。
- 存储同步服务：想要向其注册的存储同步服务的名称。

选择相应的信息之后，选择“注册”完成服务器注册。 在注册过程中，系统会提示进行其他登录。

## <a name="create-a-sync-group"></a>创建同步组
同步组定义一组文件的同步拓扑。 同步组中的终结点保持彼此同步。 同步组中必须包含至少一个云终结点和一个服务器终结点，前者表示 Azure 文件共享，后者表示 Windows Server 上的路径。 要创建同步组，请在 [Azure 门户](https://portal.azure.com/)中转到存储同步服务，然后选择“+ 同步组”：

![在 Azure 门户中创建新的同步组](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

在打开的窗格中输入以下信息，创建具有云终结点的同步组：

- **同步组名称**：要创建的同步组的名称。 此名称在存储同步服务内必须是唯一的，但可以是符合逻辑的任何名称。
- 订阅：在[部署存储同步服务](#deploy-the-storage-sync-service)中用于部署存储同步服务的订阅。
- 存储帐户：如果选择“选择存储账户”，另一个窗格随即出现，可在其中选择包含要同步的 Azure 文件共享的存储帐户。
- **Azure 文件共享**：要与其同步的 Azure 文件共享的名称。

要添加服务器终结点，请转到新创建的同步组，然后选择“添加服务器终结点”。

![在“同步组”窗格中添加一个新的服务器终结点](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

在“添加服务器终结点”窗格中，输入以下信息，创建服务器终结点：

- **已注册的服务器**：想要创建服务器终结点的服务器或群集的名称。
- **路径**：要作为同步组一部分进行同步的 Windows Server 路径。
- 云分层：启用或禁用云分层的开关。 通过云分层可以将不常使用或访问的文件分层到 Azure 文件。
- **卷可用空间**：要在服务器终结点所在的卷上保留的可用空间量。 例如，如果有一个服务器终结点的卷上的卷可用空间设置为 50%，则约有一半数据会分层为 Azure 文件。 不管是否启用云分层，Azure 文件共享在同步组中始终具有完整的数据副本。

要添加服务器终结点，请选择“创建”。 现在，文件在 Azure 文件共享和 Windows Server 之间保持保存。 

> [!Important]  
> 可对同步组中的任何云终结点或服务器终结点进行更改，并将文件同步到同步组中的其他终结点。 如果直接对云终结点（Azure 文件分享）进行更改，首先需要通过 Azure 文件同步更改检测作业来发现更改。 每 24 小时仅针对云终结点启动一次更改检测作业。 有关详细信息，请参阅 [Azure 文件常见问题解答](storage-files-faq.md#afs-change-detection)。

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>将 DFS 复制 (DFS-R) 部署迁移至 Azure 文件同步
若要将 DFS-R 部署迁移至 Azure 文件同步，请执行以下操作：

1. 创建一个同步组以表示要替换的 DFS-R 拓扑。
2. 从 DFS-R 拓扑具有完整数据集的服务器开始迁移。 在该服务器上安装 Azure 文件同步。
3. 注册该服务器，并为要迁移的第一个服务器创建服务器终结点。 请勿启用云分层。
4. 让所有数据同步至你的 Azure 文件共享（云终结点）。
5. 在剩余的每个 DFS-R 服务器上安装并注册 Azure 文件同步代理。
6. 禁用 DFS-R。 
7. 在每个 DFS-R 服务器上创建服务器终结点。 请勿启用云分层。
8. 确保同步完成，并根据需要测试拓扑。
9. 注销 DFS-R。
10. 现在应该可以根据需要在任何一个服务器终结点上启用云分层。

有关详细信息，请参阅 [Azure 文件同步与分布式文件系统 (DFS) 的互操作](storage-sync-files-planning.md#distributed-file-system-dfs)。

## <a name="next-steps"></a>后续步骤
- [添加或删除 Azure 文件同步服务器终结点](storage-sync-files-server-endpoint.md)
- [向 Azure 文件同步注册或注销服务器](storage-sync-files-server-registration.md)
