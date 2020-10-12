---
title: DPM 和 Azure 备份服务器的脱机备份
description: 借助 Azure 备份，可以使用 Azure 导入/导出服务在网外发送数据。 本文说明 DPM 和 Azure 备份服务器的脱机备份工作流。
ms.topic: conceptual
ms.date: 05/24/2020
ms.openlocfilehash: 368ae846a24ec04ee4b7da9b5971c00180be611d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89378451"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server-mabs"></a>DPM 和 Azure 备份服务器 (MABS) 的脱机备份工作流

>[!IMPORTANT]
> 这些步骤适用于 DPM 2019 UR1（或更高版本）和 MABS v3 UR1（或更高版本）。

System Center Data Protection Manager 和 Azure 备份服务器 (MABS) 与 Azure 备份集成，并使用多个内置效率，在将数据初始完整备份到 Azure 期间节省网络和存储成本。 与仅传输增量/增量的后续备份相比，初始完整备份通常传输大量数据，并且需要更多的网络带宽。 Azure 备份会压缩初始备份。 通过脱机种子设定过程，Azure 备份可以使用磁盘将压缩后的初始备份数据脱机上传到 Azure。

Azure 备份的脱机种子设定过程与 [Azure 导入/导出服务](../storage/common/storage-import-export-service.md)紧密集成。 可以借助此服务使用磁盘将数据传输到 Azure。 如果要通过高延迟、低带宽网络传输 TB 计的初始备份数据，可以使用脱机种子设定工作流将一个或多个硬盘驱动器中的初始备份副本传送到 Azure 数据中心。 本文将会概述如何对 System Center Data Protection Manager (DPM) 和 Azure 备份服务器 (MABS) 完成此工作流，并提供其他相关步骤。

> [!NOTE]
> Microsoft Azure 恢复服务 (MARS) 代理的脱机备份过程不同于 DPM 和 MABS。 有关使用 MARS 代理进行脱机备份的信息，请参阅 [Azure 备份中的脱机备份工作流](backup-azure-backup-import-export.md)。 使用 Azure 备份代理完成的系统状态备份不支持脱机备份。
>
> MABS UR1 更新还使用 MABS 中的 Azure Data Box 提供脱机备份预览。 联系 [SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com) 以了解详细信息。

## <a name="overview"></a>概述

借助 Azure 备份的脱机种子设定功能及 Azure 导入/导出服务，可以方便地通过磁盘将数据脱机上传到 Azure。 脱机备份过程涉及以下步骤：

> [!div class="checklist"]
>
> * 备份数据不会通过网络发送，而是写入到暂存位置。
> * 然后使用 *AzureOfflineBackupDiskPrep* 实用工具将暂存位置上的数据写入到一个或多个 SATA 磁盘。
> * 该实用工具会自动创建 Azure 导入作业。
> * 然后将 SATA 驱动器发送到最近的 Azure 数据中心。
> * 将备份数据上传到 Azure 的操作完成之后，Azure 备份将备份数据复制到备份保管库，并计划进行增量备份。

## <a name="prerequisites"></a>先决条件

确保在启动脱机备份工作流之前满足以下先决条件：

* 已创建[恢复服务保管库](backup-azure-recovery-services-vault-overview.md)。 若要创建一个，请按照[创建恢复服务保管库](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)中的步骤进行操作。
* 确保仅在 SC DPM 或 MABS 上安装了 [最新版本的 Microsoft Azure 恢复服务代理](https://aka.ms/azurebackup_agent) ，并将其注册到了恢复服务保管库。
* 更新汇总 1 安装在 SC DPM 2019 或 MABS v3 上。

  > [!NOTE]
  > 借助 DPM 2019 UR1 和 MABS v3 UR1，脱机种子设定使用 Azure Active Directory 进行身份验证。

* 在 DPM 或 MABS 服务器上，确保已安装 Microsoft Edge 或 Internet Explorer 11，并已启用 JavaScript。
* 在与恢复服务保管库相同的订阅中创建一个 Azure 存储帐户。
* 确保你具有创建 Azure Active Directory 应用程序的[必要权限](../active-directory/develop/howto-create-service-principal-portal.md)。 脱机备份工作流在与 Azure 存储帐户关联的订阅中创建一个 Azure Active Directory 应用程序。 该应用程序的目的是为 Azure 备份提供脱机备份工作流所需的对 Azure 导入服务的安全且范围内的访问。
* 使用包含 Azure 存储帐户的订阅注册 Microsoft.ImportExport 资源提供程序。 要注册资源提供程序，请执行以下操作：
    1. 在主菜单中选择“订阅”。
    2. 如果你订阅了多个订阅，请选择用于脱机备份的订阅。 如果仅使用一个订阅，则会显示你的订阅。
    3. 在订阅菜单中，选择“资源提供程序”以查看提供程序列表。
    4. 在提供程序列表中，向下滚动到 Microsoft.ImportExport。 如果状态为“NotRegistered”，请选择“注册”。

       ![注册资源提供程序](./media/backup-azure-backup-server-import-export/register-import-export.png)

* 创建了一个暂存位置，它可以是计算机上的网络共享或任何其他内部或外部驱动器，并且有足够的磁盘空间来保存初始副本。 例如，若要备份 500 GB 文件服务器，请确保暂存区域至少有 500 GB 空间。 （由于压缩，实际使用量更少）。
* 对于发送到 Azure 的磁盘，请确保仅使用 2.5 英寸 SSD 或 2.5 英寸或 3.5 英寸 SATA II/III 内部硬盘驱动器。 可以使用容量最高为 10 TB 的硬盘驱动器。 查看 [Azure 导入/导出服务文档](../storage/common/storage-import-export-requirements.md#supported-hardware)，了解服务支持的最新驱动器集。
* SATA 驱动器必须连接到一台计算机（称为“副本计算机”），将在这台计算机上完成将备份数据从暂存位置复制到 SATA 驱动器的过程。 请确保已在副本计算机上启用 BitLocker。

## <a name="workflow"></a>工作流

本部分中的信息有助于完成“脱机备份工作流”，以便可以将你的数据传输到 Azure 数据中心并上转到 Azure 存储。 如果你对导入服务或该过程的任何方面有疑问，请参阅上面引用的[导入服务概述](../storage/common/storage-import-export-service.md)。

## <a name="initiate-offline-backup"></a>启动脱机备份

1. 在创建具有联机保护的新保护组或将联机保护添加到现有保护组时，将看到以下屏幕。 若要选择初始在线复制方法，请选择“使用我自己的磁盘传输”并选择“下一步”。

    ![导入屏幕](./media/backup-azure-backup-server-import-export/create-new-protection-group.png)

2. Azure 登录页面随即打开。 使用 Azure 用户帐户登录，该帐户对 Azure 订阅具有所有者角色权限。

    ![Azure 登录页面](./media/backup-azure-backup-server-import-export/choose-initial-online-replication.png)

3. 在“使用自己的磁盘”页上提供输入。

   ![“使用自己的磁盘”的输入](./media/backup-azure-backup-server-import-export/use-your-own-disk.png)

   输入说明如下所示：

   * **暂存位置**：写入初始备份副本的临时存储位置。 暂存位置可以是网络共享或本地计算机。 如果副本计算机与源计算机不同，请指定暂存位置的完整网络路径。
   * **Azure 资源管理器存储帐户**：任何 Azure 订阅中的资源管理器类型存储帐户（常规用途 v1 或常规用途 v2）的名称。
   * **Azure 存储容器**：导入备份数据的 Azure 存储帐户中目标 blob 存储容器的名称。
   * **Azure 订阅 ID**：在其中创建 Azure 存储帐户的 Azure 订阅的 ID。
   * **Azure 导入作业名称**：Azure 导入服务和 Azure 备份用来跟踪在磁盘上发送到 Azure 的数据传输的唯一名称。

    保存提供的“暂存位置”和“Azure 导入作业名称”信息。  准备磁盘时需要用到这些信息。

4. 完成工作流以创建或更新保护。 要启动脱机备份副本，请右键单击“保护组”，然后选择“创建恢复点”选项。 然后，选择“联机保护”选项。

   ![创建恢复点](./media/backup-azure-backup-server-import-export/create-recovery-point.png)

5. 在“监视”窗格中监视“联机副本创建”作业。 该作业应成功完成，但显示警告“等待 Azure 导入作业完成”。

   ![完成恢复点](./media/backup-azure-backup-server-import-export/complete-recovery-point.png)

6. 操作完成后，暂存位置已准备就绪，可用于磁盘准备。

## <a name="prepare-sata-drives-and-ship-to-azure"></a>准备好 SATA 驱动器并寄送到 Azure

AzureOfflineBackupDiskPrep 实用工具准备好要发送到最近的 Azure 数据中心的 SATA 驱动器。 位于以下路径的 Azure 备份代理安装目录中提供了此实用工具：`*\Microsoft Azure Recovery Services Agent\Utils\\*`

1. 转到目录，将 AzureOfflineBackupDiskPrep 目录复制到连接 SATA 驱动器的另一台计算机上。 在连接了 SATA 驱动器的计算机上，确保：

   * 副本计算机可以使用“启动脱机备份”部分的工作流中提供的相同网络路径，来访问脱机种子设定工作流的暂存位置。
   * 已在副本计算机上启用 BitLocker。
   * 在副本计算机上安装 Azure PowerShell 3.7.0（如果你在 DPM 或 MABS 服务器上运行 AzureOfflineBackupDiskPrep 实用工具，则没有此要求）。
   * 安装最新的兼容浏览器（Microsoft Edge 或 Internet Explorer 11）并启用 JavaScript。
   * 复制计算机可以访问 Azure 门户。 必要时，副本计算机可与源计算机相同。

     > [!IMPORTANT]
     > 如果源计算机是虚拟机，则它必须使用其他物理服务器或客户端计算机作为副本计算机。

1. 使用 *AzureOfflineBackupDiskPrep* 实用工具目录作为当前目录，在副本计算机上打开权限提升的命令提示符。 运行以下命令：

    ```console
    .\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>
    ```

    | 参数 | 说明 |
    | --- | --- |
    | s:&lt;*Staging Location Path*&gt; |此项必需的输入用于提供在“启动脱机备份”部分的工作流中所输入的暂存位置的路径。 |
    | p:&lt;*Path to PublishSettingsFile*&gt; |此可选输入用于提供 Azure 发布设置文件的路径。 |

    运行命令时，该实用工具会请求选择与需要准备的驱动器相对应的 Azure 导入作业。 如果仅单个导入作业与提供的暂存位置相关联，则会看到类似下面的屏幕。

      ![磁盘准备控制台](./media/backup-azure-backup-server-import-export/disk-prep-console.png)

1. 输入要准备传输到 Azure 的载入磁盘的驱动器号，不包含尾随冒号。
1. 提示时，请确认驱动器的格式。
1. 系统会提示你登录到 Azure 订阅。 提供凭据。

    ![Azure 登录屏幕](./media/backup-azure-backup-server-import-export/signin-disk-prep.png)

    该工具随后便开始准备磁盘和复制备份数据。 在该工具提示你附加更多磁盘时，你可能需要附加更多磁盘（如果提供的磁盘没有足够空间来容纳备份数据）。 <br/>

    成功结束该工具的执行时，命令提示符会提供三段信息：
    * 所提供的一个或多个磁盘已准备好寄送到 Azure。
    * 你会收到确认已创建导入作业。 导入作业使用你提供的名称。
    * 该工具显示 Azure 数据中心的送货地址。

     ![Azure 磁盘准备完成](./media/backup-azure-backup-server-import-export/console.png)

1. 命令执行完成后，还会看到用于更新发货信息的选项。

1. 将磁盘寄送到工具提供的地址，保留跟踪号码供日后参考。

   > [!IMPORTANT]
   > 不能有两个 Azure 导入作业使用同一个跟踪号。 确保使用一个包裹寄送实用工具在单次 Azure 导入作业中准备的驱动器，该包裹有一个唯一的跟踪号。 请勿在一个包裹中混合不同 Azure 导入作业中准备的驱动器。

## <a name="update-shipping-details-on-the-azure-import-job"></a>更新 Azure 导入作业的送货详细信息

以下过程会更新 Azure 导入作业的送货详细信息。 该信息包括以下内容的详细信息：

* 将磁盘交付到 Azure 的运营商的名称
* 返回磁盘的送货详细信息

   1. 登录到你的 Azure 订阅。
   2. 在主菜单中选择“所有服务”，然后在“所有服务”对话框中键入“导入”。 看到“导入/导出作业”时，请选择它。
       ![输入送货信息](./media/backup-azure-backup-server-import-export/search-import-job.png)

       “导入/导出作业”菜单列表将打开，并显示所选订阅中所有导入/导出作业的列表。

   3. 如果有多个订阅，请确保选中用于导入备份数据的订阅。 然后，选择新创建的导入作业以打开其详细信息。

       ![查看送货信息](./media/backup-azure-backup-server-import-export/import-job-found.png)

   4. 在导入作业的“设置”菜单中，选择“管理寄送信息”并输入回件寄送详细信息。

       ![存储送货信息](./media/backup-azure-backup-server-import-export/shipping-info.png)

   5. 在收到快递公司提供的跟踪号后，请选择 Azure 导入作业概述页中的横幅，并输入以下详细信息：

      > [!IMPORTANT]
      > 确保在创建 Azure 导入作业后的两周内更新承运人信息和跟踪号。 两周内未能验证此信息可能导致删除作业，且不会处理驱动器。

      ![作业概述](./media/backup-azure-backup-server-import-export/job-overview.png)

      ![跟踪信息](./media/backup-azure-backup-server-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>处理驱动器的时间

处理 Azure 导入作业的时间是不确定的。 处理时间取决于发货时间、作业类型、要复制的数据的类型和大小，以及所提供磁盘的大小等因素。 Azure 导入/导出服务不附带 SLA。 收到磁盘之后，该服务力求在 7 到 10 天内完成将备份数据复制到 Azure 存储帐户的过程。 下一部分将介绍如何监视 Azure 导入作业的状态。

### <a name="monitor-azure-import-job-status"></a>监视 Azure 导入作业状态

可以通过导航到“导入/导出作业”页面并选中你的作业，从 Azure 门户监视导入作业的状态。 有关导入作业状态的详细信息，请参阅[存储导入导出服务](../storage/common/storage-import-export-service.md)一文。

### <a name="complete-the-workflow"></a>完成工作流

导入作业完成后，存储帐户中将提供初始备份数据。 下一次运行计划的备份时，Azure 备份会将数据内容从存储帐户复制到恢复服务保管库。

在下一个计划的联机副本创建作业时，Data Protection Manager 会对初始备份复制执行增量备份。

## <a name="next-steps"></a>后续步骤

* 如果遇到任何有关 Azure 导入/导出服务工作流的问题，请参阅[使用 Microsoft Azure 导入/导出服务将数据传输到 Blob 存储](../storage/common/storage-import-export-service.md)。
