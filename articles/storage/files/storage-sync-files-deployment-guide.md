---
title: "如何部署 Azure 文件同步（预览版）| Microsoft Docs"
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
ms.openlocfilehash: b31b6ae413f72c626e2601ba860aad44ddaa29cd
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2017
---
# <a name="how-to-deploy-azure-file-sync-preview"></a>如何部署 Azure 文件同步（预览版）
借助 Azure 文件同步（预览版），既可将组织的文件共享集中在 Azure 文件中，又不失本地文件服务器的灵活性、性能和兼容性。 它通过将 Windows Server 转换为 Azure 文件共享的快速缓存来实现这一点。 可以使用 Windows Server 上任何可用协议在本地访问你的数据（包括 SMB、NFS 和 FTPS），并且可以根据需要在世界各地拥有尽可能多的缓存。

强烈建议先阅读[规划 Azure 文件部署](storage-files-planning.md)和[规划 Azure 文件同步部署](storage-sync-files-planning.md)，再按照本指南中的步骤进行操作。

## <a name="prerequisites"></a>先决条件
* 存储帐户和 Azure 文件共享位于要部署 Azure 文件同步的相同区域。有关详细信息，请参阅：
    - Azure 文件同步的[上市区域](storage-sync-files-planning.md#region-availability)
    - [创建存储帐户](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)，了解创建存储帐户的分步说明，以及
    - [创建文件共享](storage-how-to-create-file-share.md)，了解创建文件共享的分步说明。
* 至少一个支持与 Azure 文件同步进行同步的 Windows Server 或 Windows Server 群集。有关支持的 Windows Server 版本的详细信息，请参阅 [Windows Server 的互操作性](storage-sync-files-planning.md#azure-file-sync-interoperability)。

## <a name="deploy-the-storage-sync-service"></a>部署存储同步服务 
存储同步服务是表示 Azure 文件同步的顶级 Azure 资源。要部署存储同步服务，请导航到 [Azure 门户](https://portal.azure.com/)，并搜索 Azure 文件同步。从搜索结果中选择“Azure 文件同步（预览版）”，然后选择“创建”，在弹出窗口中打开“部署存储同步”选项卡。

需要在出现的边栏选项卡中提供以下信息：

- 名称：存储同步服务的唯一名称（按订阅）。
- 订阅：要在其中创建存储同步服务的订阅。 根据组织的配置策略，可能有权访问一个或多个订阅。 Azure 订阅是对每项云服务（如 Azure 文件）计费的最基本容器。
- 资源组：资源组是 Azure 资源（如存储帐户或存储同步服务）的逻辑组。 可为 Azure 文件同步创建新的资源组，也可对其使用现有资源组（建议使用资源组作为用于从逻辑上隔离组织资源的容器，例如对 HR 资源或特定项目的资源进行分组）。
- 位置：要在其中部署 Azure 文件同步的区域。此列表仅提供支持的区域。

完成“部署存储同步”窗体后，单击“创建”即可部署存储同步服务。

## <a name="prepare-windows-servers-for-use-with-azure-file-sync"></a>准备 Windows Server，用于 Azure 文件同步
对于要与 Azure 文件同步配合使用的每个服务器（包括故障转移群集中的服务器节点），请完成以下步骤：

对于要与 Azure 文件同步配合使用的每个服务器（包括故障转移群集中的服务器节点），请完成以下步骤：

1. 禁用 Internet Explorer 增强的安全性配置。 只需在最初注册服务器时启用，并且可在注册服务器后重新启用。
    1. 打开服务器管理器。
    2. 单击“本地服务器”：  
        ![服务器管理器 UI 左侧的“本地服务器”](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
    3. 在“属性”子窗格上选择“IE 增强的安全性配置”的链接：  
        ![服务器管理器 UI 中的“IE 增强的安全性配置”](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
    4. 对于“Internet Explorer 增强的安全性配置”弹出窗口中的“管理员”和“用户”，都选择“关”：  
        ![选定“关”的“Internet Explorer 增强的安全性配置”弹出窗口](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

2. 确保至少正在运行 PowerShell 5.1.\*（PowerShell 5.1 是 Windows Server 2016 上的默认版本）。 可通过查看 $PSVersionTable 对象的 PSVersion 属性值来验证是否正在运行 PowerShell 5.1.\*：

    ```PowerShell
    $PSVersionTable.PSVersion
    ```

    - 与 Windows Server 2012 R2 大多数安装一样，如果 PSVersion 低于 5.1.\*，可通过下载并安装 [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616) 轻松升级。 需下载和安装的 Windows Server 2012 R2 的相应包为 Win8.1AndW2K12R2-KB\*\*\*\*\*\*\*-x64.msu。

3. [安装和配置 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)。 始终建议使用最新版本的 Azure PowerShell 模块。

## <a name="install-the-azure-file-sync-agent"></a>安装 Azure 文件同步代理
Azure 文件同步代理是一个可下载包，可实现 Windows 服务器与 Azure 文件共享的同步。 可从 [Microsoft 下载中心](https://go.microsoft.com/fwlink/?linkid=858257)下载该代理。 下载完成后，双击 MSI 包，开始安装 Azure 文件同步代理。

> [!Important]  
> 如果要对故障转移群集使用 Azure 文件同步，则 需要在群集中的每个节点上安装 Azure 文件同步代理。

Azure 文件同步代理安装包的安装速度应相对较快，没有太多其他提示。 我们建议：
- 保留默认安装路径 `C:\Program Files\Azure\StorageSyncAgent`，以简化故障排除和服务器维护。
- 启用 Microsoft 更新，使 Azure 文件同步保持最新。 Azure 文件同步代理的所有更新（包括功能更新和修补程序）都可从 Microsoft 更新进行。 始终建议采用 Azure 文件同步的最新更新。有关详细信息，请参阅 [Azure 文件同步更新策略](storage-sync-files-planning.md#azure-file-sync-agent-update-policy)。

Azure 文件同步代理安装结束时，服务器注册 UI 自动启动。 请参阅下节，了解如何向 Azure 文件同步注册此服务器。

## <a name="register-windows-server-with-storage-sync-service"></a>向存储同步服务注册 Windows Server
向存储同步服务注册 Windows Server 可在服务器（或群集）与存储同步服务之间建立信任关系。 服务器注册 UI 应在安装 Azure 文件同步代理后自动启动，但如果未启动，可从其位置手动打开它：`C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`。 打开服务器注册 UI 后，单击“登录”开始注册。

登录后，系统会提示输入以下信息：

![服务器注册 UI 的屏幕快照](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- Azure 订阅：包含存储同步服务的订阅（请参阅上文的[部署存储同步服务](#deploy-the-storage-sync-service)）。 
- 资源组：包含存储同步服务的资源组。
- 存储同步服务：想要向其注册的存储同步服务的名称。

从下拉列表中选择相应的信息之后，单击“注册”完成服务器注册。 在注册过程中，系统会提示进行其他登录。

## <a name="create-a-sync-group"></a>创建同步组
同步组定义一组文件的同步拓扑。 同步组中的终结点保持彼此同步。 同步组中必须包含至少一个云终结点和一个服务器终结点，前者表示 Azure 文件共享，后者表示 Windows Server 上的路径。 要创建同步组，请导航到 [Azure 门户](https://portal.azure.com/)中的存储同步服务，然后单击“+ 同步组”：

![在 Azure 门户中创建新的同步组](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

出现的窗格会要求用户提供以下信息，以创建具有云终结点的同步组：

- 同步组名称：要创建的同步组的名称。 此名称在存储同步服务内必须是唯一的，但可以是符合逻辑的任何名称。
- 订阅：在上文[部署存储同步服务](#deploy-the-storage-sync-service)中用于部署存储同步服务的订阅。
- 存储帐户：单击“选择存储帐户”会弹出另一个窗格，可在其中选择包含要与其同步的 Azure 文件共享的存储帐户。
- Azure 文件共享：要与其同步的 Azure 文件共享的名称。

要添加服务器终结点，请导航到新创建的同步组，并单击“添加服务器终结点”。

![在“同步组”窗格中添加一个新的服务器终结点](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

出现的“添加服务器终结点”窗格需要以下信息来创建服务器终结点：

- **已注册服务器**：要在其中创建服务器终结点的服务器或群集的名称。
- **路径**：要作为同步组的一部分进行同步的 Windows Server 上的路径。
- **云分层**：用于启用或禁用云分层的开关，它使很少使用或访问的文件能够分层为 Azure 文件。
- **卷可用空间**：要在服务器终结点所在的卷上保留的可用空间量。 例如，如果有一个服务器终结点的卷上的卷可用空间设置为 50%，则约有一半数据会分层为 Azure 文件。 请注意，无论是否启用云分层，Azure 文件共享在同步组中始终具有完整的数据副本。

单击“创建”添加服务器终结点。 文件现在跨 Azure 文件共享和 Windows Server 同步进行保存。 

> [!Important]  
> 可对同步组中的任何云或服务器终结点进行更改，并将文件同步到同步组中的其他终结点。 如果直接对云终结点（Azure 文件共享）进行更改，请注意，首先需要通过 Azure 文件同步更改检测作业来发现更改，该作业每 24 小时启动一次，仅针对云终结点。 有关详细信息，请参阅 [Azure 文件 FAQ](storage-files-faq.md#afs-change-detection)。

## <a name="next-steps"></a>后续步骤
- [添加/删除 Azure 文件同步服务器终结点](storage-sync-files-server-endpoint.md)
- [向 Azure 文件同步注册/注销服务器](storage-sync-files-server-registration.md)