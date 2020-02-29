---
title: 利用 Azure 导入/导出服务播种脱机备份
description: 了解如何使用 azure 备份通过 Azure 导入/导出服务从网络中发送数据。 本文介绍了如何使用 Azure 导入/导出服务对初始备份数据进行脱机种子设定。
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/17/2018
ms.openlocfilehash: 60295d1c534dbd1181533190df260ed4507ec11f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197109"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Azure 备份中的脱机备份工作流

Azure 备份有多个可提升效率的内置功能，能在数据初始完整备份到 Azure 期间节省网络和存储成本。 初始完整备份通常传输大量数据，且需要较多网络带宽，相比之下，后续备份只传输增量部分。 通过脱机种子设定，Azure 备份可以使用磁盘将脱机备份数据上传到 Azure。

Azure 备份的脱机种子设定过程与[Azure 导入/导出服务](../storage/common/storage-import-export-service.md)紧密集成。 可以使用此服务通过磁盘将初始备份数据传输到 Azure。 如果有 tb （Tb）的初始备份数据需要通过高延迟和低带宽网络传输，则可以使用脱机种子设定工作流将一个或多个硬盘驱动器上的初始备份副本传送到 Azure 数据中心。 下图提供了工作流中步骤的概述。

  ![脱机导入工作流进程概述](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

脱机备份过程包括以下步骤：

1. 不是通过网络发送备份数据，而是将备份数据写入暂存位置。
1. 使用*AzureOfflineBackupDiskPrep*实用工具将暂存位置中的数据写入一个或多个 SATA 磁盘。
1. 在准备工作过程中， *AzureOfflineBackupDiskPrep*实用程序会创建 Azure 导入作业。 将 SATA 驱动器寄送到最近的 Azure 数据中心，并引用导入作业来连接活动。
1. 在 Azure 数据中心，磁盘上的数据将复制到 Azure 存储帐户。
1. Azure 备份将备份数据从存储帐户复制到恢复服务保管库，并计划增量备份。

## <a name="supported-configurations"></a>支持的配置

以下 Azure 备份功能或工作负荷支持将脱机备份用于：

> [!div class="checklist"]
>
> * 将文件和文件夹备份到 Microsoft Azure 恢复服务（MARS）代理，也称为 Azure 备份代理。
> * 用 System Center Data Protection Manager （DPM）备份所有工作负荷和文件。
> * Microsoft Azure 备份服务器备份所有工作负荷和文件。
 
   > [!NOTE]
   > 使用 Azure 备份代理执行的系统状态备份不支持脱机备份。

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>必备条件

  > [!NOTE]
  > 以下先决条件和工作流仅适用于使用[最新的 Azure 恢复服务代理](https://aka.ms/azurebackup_agent)对文件和文件夹进行脱机备份。 若要使用 System Center DPM 或 Azure 备份服务器为工作负荷执行脱机备份，请参阅[DPM 和 Azure 备份服务器的脱机备份工作流](backup-azure-backup-server-import-export-.md)。

在开始脱机备份工作流之前，请完成以下先决条件：

* 创建[恢复服务保管库](backup-azure-recovery-services-vault-overview.md)。 若要创建保管库，请按照[创建恢复服务保管库](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)中的步骤操作。
* 请确保 Windows Server 或 Windows 客户端上仅安装了[最新版本的 Azure 备份代理](https://aka.ms/azurebackup_agent)，并在恢复服务保管库中注册了计算机。
* 运行 Azure 备份代理的计算机上需要 Azure PowerShell 3.7.0。 下载并[安装 Azure PowerShell 的3.7.0 版本](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)。
* 在运行 Azure 备份代理的计算机上，确保已安装 Microsoft Edge 或 Internet Explorer 11 并启用了 JavaScript。
* 在与恢复服务保管库相同的订阅中创建一个 Azure 存储帐户。
* 确保拥有创建 Azure Active Directory 应用程序的[所需权限](../active-directory/develop/howto-create-service-principal-portal.md)。 脱机备份工作流在与 Azure 存储帐户关联的订阅中创建一个 Azure Active Directory 应用程序。 应用程序的目标是为 Azure 导入/导出服务提供安全且具有范围的访问权限，这是脱机备份工作流所必需的。
* 将*ImportExport*资源提供程序注册到包含 Azure 存储帐户的订阅。 若要注册资源提供程序：
    1. 在主菜单上，选择 "**订阅**"。
    1. 如果你订阅了多个订阅，请选择你计划用于脱机备份的订阅。 如果只使用一个订阅，则屏幕上会显示该订阅。
    1. 在 "订阅" 菜单上，选择 "**资源提供程序**" 查看提供程序列表。
    1. 在提供程序列表中，向下滚动到 " *ImportExport*"。 如果**状态**为**NotRegistered**，请选择 "**注册**"。

        ![注册资源提供程序](./media/backup-azure-backup-import-export/registerimportexport.png)

* 创建了一个暂存位置，它可以是计算机上的网络共享或任何其他内部或外部驱动器，并且有足够的磁盘空间来保存初始副本。 例如，如果要备份 500 GB 的文件服务器，请确保暂存区域至少为 500 GB。 （由于压缩，实际使用量更少。）
* 向 Azure 发送磁盘时，请仅使用2.5 英寸 SSD 或2.5 英寸或3.5 英寸 SATA II/III 内部硬盘驱动器。 可以使用容量最高为 10 TB 的硬盘驱动器。 查看 [Azure 导入/导出服务文档](../storage/common/storage-import-export-requirements.md#supported-hardware)，了解服务支持的最新驱动器集。
* 必须将 SATA 驱动器连接到计算机（称为*副本计算机*），将备份数据从暂存位置复制到 SATA 驱动器。 确保在复制计算机上启用 BitLocker。

## <a name="workflow"></a>工作流

本部分介绍脱机备份工作流，以便可以将数据传送到 Azure 数据中心并上载到 Azure 存储。 如果你有关于导入服务或流程的任何方面的问题，请参阅[Azure 导入/导出服务概述文档](../storage/common/storage-import-export-service.md)。

## <a name="initiate-offline-backup"></a>启动脱机备份

1. 在恢复服务代理上计划备份时，会看到此页。

    ![导入页](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

1. 选择 "**使用我自己的磁盘传输**" 选项。

    > [!NOTE]
    > 使用 Azure Data Box 选项可脱机传输初始备份数据。 此选项可节省购买你自己的 Azure 兼容磁盘所需的工作量。 它提供 Microsoft 专有、安全和防伪 Azure Data Box 设备，可通过恢复服务代理直接将备份数据写入到其中。

1. 选择 "**下一步**"，并仔细填写框。

    ![输入磁盘详细信息](./media/backup-azure-backup-import-export/your-disk-details.png)

   填写的框包括：

    * **暂存位置** — 初始备份副本写入到的临时存储位置。 暂存位置可能位于网络共享或本地计算机上。 如果副本计算机与源计算机不同，则指定暂存位置的完整网络路径。
    * **Azure 资源管理器存储帐户**：任何 Azure 订阅中资源管理器类型存储帐户的名称（常规用途 v1 或常规用途 v2）。
    * **Azure 存储容器**： azure 存储帐户中的目标存储 blob 的名称，在该帐户中会将备份数据导入到恢复服务保管库中。
    * **Azure 订阅 id**：创建 azure 存储帐户的 azure 订阅的 id。
    * **Azure 导入作业名称**： Azure 导入/导出服务和 azure 备份跟踪磁盘上发送到 Azure 的数据传输的唯一名称。
  
   填写框后，选择 "**下一步**"。 保存**暂存位置**和**Azure 导入作业名称**信息。 需要准备磁盘。

1. 出现提示时，请登录到 Azure 订阅。 必须登录才能创建 Azure Active Directory 应用程序。 输入访问 Azure 导入/导出服务所需的权限。

    ![Azure 订阅登录页](./media/backup-azure-backup-import-export/azure-login.png)

1. 完成工作流。 在 Azure 备份代理控制台上，选择 "**立即备份**"。

    ![立即备份](./media/backup-azure-backup-import-export/backupnow.png)

1. 在向导的 "**确认**" 页上，选择 "**备份**"。 初始备份将写入到设置过程中指定的暂存区域。

   ![确认你现在已准备好进行备份](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    操作完成后，暂存位置已准备就绪，可用于准备磁盘。

   !["立即备份" 向导页](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>准备 SATA 驱动器并寄送到 Azure

*AzureOfflineBackupDiskPrep* 实用工具会准备送到最近 Azure 数据中心的 SATA 驱动器。 此实用程序在 Azure 备份代理安装目录的以下路径中提供：

    *\Microsoft Azure Recovery Services Agent\Utils\\*

1. 中转到该目录，然后将*AzureOfflineBackupDiskPrep*目录复制到连接 SATA 驱动器的另一台计算机。 在连接了 SATA 驱动器的计算机上，确保：

   * 副本计算机可以通过使用在 "启动脱机备份" 部分中提供的相同网络路径，访问脱机种子设定工作流的暂存位置。
   * 已在副本计算机上启用 BitLocker。
   * Azure PowerShell 3.7.0 已安装。
   * 将安装最新兼容的浏览器（Microsoft Edge 或 Internet Explorer 11），并启用 JavaScript。
   * 副本计算机可以访问 Azure 门户。 必要时，副本计算机可与源计算机相同。

     > [!IMPORTANT]
     > 如果源计算机是虚拟机，则复制计算机必须是与源计算机不同的物理服务器或客户端计算机。

1. 在复制计算机上打开提升的命令提示符，并将*AzureOfflineBackupDiskPrep*实用工具目录作为当前目录。 运行以下命令：

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | 参数 | 说明 |
    | --- | --- |
    | s:&lt;暂存位置路径&gt; |此必需的输入用于提供在 "启动脱机备份" 部分的工作流中输入的暂存位置的路径。 |
    | p:&lt;发布设置文件的路径&gt; |此可选输入用于提供在 "启动脱机备份" 部分的工作流中输入的 Azure 发布设置文件的路径。 |

    运行该命令时，该实用工具将请求选择与需要准备的驱动器对应的 Azure 导入作业。 如果仅有一个导入作业与提供的暂存位置相关联，则会看到类似于下面的页面。

    ![Azure 磁盘准备工具输入](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

1. 输入想要准备传输到 Azure 的已装载磁盘的驱动器号（不要包含尾部的冒号）。
1. 出现提示时，请确认格式化驱动器。
1. 系统将提示你登录到 Azure 订阅。 输入凭据。

    ![Azure 订阅登录](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    然后，该工具将开始准备磁盘并复制备份数据。 如果提供的磁盘没有足够空间来备份数据，则可能需要在工具提示时附加更多磁盘。 <br/>

    成功结束该工具的执行时，命令提示符会提供三段信息：

   1. 准备好提供的一个或多个磁盘，以便寄送到 Azure。
   1. 你会收到有关你的导入作业已创建的确认。 导入作业使用提供的名称。
   1. 该工具会显示 Azure 数据中心的寄送地址。

      ![Azure 磁盘准备已完成](./media/backup-azure-backup-import-export/console2.png)<br/>

1. 结束命令的执行时，可以更新寄送信息。

1. 将磁盘寄送到工具提供的地址。 保留跟踪号以供将来参考。

   > [!IMPORTANT]
   > 两个 Azure 导入作业不能具有相同的跟踪号。 确保单个 Azure 导入作业下的实用程序准备的驱动器在单个包中一起发货，并为包提供单个唯一的跟踪号。 不要将准备的驱动器合并为单个包中单独的 Azure 导入作业的一部分。

## <a name="update-shipping-details-on-the-azure-import-job"></a>在 Azure 导入作业中更新发货详细信息

以下过程将更新 Azure 导入作业寄送详细信息。 此信息包括以下各项的详细信息：

* 向 Azure 提供磁盘的运营商的名称。
* 返回磁盘的发货详细信息。

1. 登录到 Azure 订阅。
1. 在主菜单上，选择 "**所有服务**"。 在 "**所有服务**" 对话框中，输入**导入**。 当你看到**导入/导出作业**时，请选择它。

    ![输入寄送信息](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    此时将打开 "**导入/导出作业**" 菜单，并显示所选订阅中的所有导入/导出作业的列表。

1. 如果有多个订阅，请选择用于导入备份数据的订阅。 然后选择新创建的导入作业，以打开其详细信息。

    ![查看装运信息](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

1. 在导入作业的 "**设置**" 菜单上，选择 "**管理装运信息**"。 输入退货寄送详细信息。

    ![商店发货信息](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

1. 如果你有来自装运承运人的跟踪号，请在 "Azure 导入作业概述" 页中选择标题，并输入以下详细信息。

   > [!IMPORTANT]
   > 确保快递公司信息和跟踪号在创建 Azure 导入作业后的两周内更新。 如果在两周内无法验证此信息，则可能会导致删除作业，并且不会处理驱动器。

   ![跟踪信息更新警报](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![承运人信息和跟踪编号](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>处理驱动器的时间

处理 Azure 导入作业所需的时间会有所不同。 处理时间基于如下因素：运输时间、作业类型、要复制的数据的类型和大小，以及所提供磁盘的大小。 Azure 导入/导出服务没有 SLA。 收到磁盘后，服务会在7到10天内完成将备份数据复制到 Azure 存储帐户。

### <a name="monitor-azure-import-job-status"></a>监视 Azure 导入作业状态

你可以从 Azure 门户监视导入作业的状态。 请在 "**导入/导出作业**" 页上，选择你的作业。 有关导入作业状态的详细信息，请参阅[什么是 Azure 导入/导出服务？](../storage/common/storage-import-export-service.md)。

### <a name="finish-the-workflow"></a>完成工作流

成功完成导入作业后，存储帐户中的初始备份数据可供使用。 在下一次计划的备份时，Azure 备份会将数据的内容从存储帐户复制到恢复服务保管库。

   ![将数据复制到恢复服务保管库](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

在执行下一次计划的备份时，Azure 备份会执行增量备份。

### <a name="clean-up-resources"></a>清理资源

完成初始备份后，可以安全地删除导入到 Azure 存储容器中的数据以及暂存位置中的备份数据。

## <a name="next-steps"></a>后续步骤

* 有关 Azure 导入/导出服务工作流的任何问题，请参阅[使用 Microsoft Azure 导入/导出服务将数据传输到 Blob 存储](../storage/common/storage-import-export-service.md)。
