---
title: 使用 Azure 导入/导出服务进行种子脱机备份
description: 了解如何使用 Azure 备份将数据从网络外发送，使用 Azure 导入/导出服务。 本文介绍使用 Azure 导入/导出服务的初始备份数据的脱机种子设定。
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/17/2018
ms.openlocfilehash: 1359616e074f36a1324a418d5b2c889076ced52d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206752"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Azure 备份中的脱机备份工作流

Azure 备份有多个可提升效率的内置功能，能在数据初始完整备份到 Azure 期间节省网络和存储成本。 初始完整备份通常传输大量数据，且需要较多网络带宽，相比之下，后续备份只传输增量部分。 通过脱机种子设定，Azure 备份可以使用磁盘将脱机备份数据上传到 Azure。

Azure 备份脱机种子设定过程与[Azure 导入/导出服务](../storage/common/storage-import-export-service.md)紧密集成。 您可以使用此服务使用磁盘将初始备份数据传输到 Azure。 如果具有需要通过高延迟和低带宽网络传输的初始备份数据 TB （TB），则可以使用脱机种子工作流将初始备份副本（一个或多个硬盘驱动器）传送到 Azure 数据中心。 下图提供了工作流中步骤的概述。

  ![脱机导入工作流流程概述](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

脱机备份过程涉及以下步骤：

1. 不是通过网络发送备份数据，而是将备份数据写入暂存位置。
1. 使用*AzureOfflineBackupDiskPrep*实用程序将暂存位置中的数据写入一个或多个 SATA 磁盘。
1. 作为准备工作的一部分 *，AzureOfflineBackupDiskPrep*实用程序将创建 Azure 导入作业。 将 SATA 驱动器寄送到最近的 Azure 数据中心，并引用导入作业来连接活动。
1. 在 Azure 数据中心，磁盘上的数据将复制到 Azure 存储帐户。
1. Azure 备份将备份数据从存储帐户复制到恢复服务保管库，并计划增量备份。

## <a name="supported-configurations"></a>支持的配置

以下 Azure 备份功能或工作负载支持对以下人员使用脱机备份：

> [!div class="checklist"]
>
> * 使用 Microsoft Azure 恢复服务 （MARS） 代理（也称为 Azure 备份代理）备份文件和文件夹。
> * 使用系统中心数据保护管理器 （DPM） 备份所有工作负载和文件。
> * 使用 Microsoft Azure 备份服务器备份所有工作负荷和文件。
 
   > [!NOTE]
   > 使用 Azure 备份代理完成的系统状态备份不支持脱机备份。

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>先决条件

  > [!NOTE]
  > 以下先决条件和工作流仅适用于使用[最新的 Azure 恢复服务代理](https://aka.ms/azurebackup_agent)脱机备份文件和文件夹。 要使用系统中心 DPM 或 Azure 备份服务器对工作负载执行脱机备份，请参阅[DPM 和 Azure 备份服务器的脱机备份工作流](backup-azure-backup-server-import-export-.md)。

在启动脱机备份工作流之前，完成以下先决条件：

* 创建[恢复服务保管库](backup-azure-recovery-services-vault-overview.md)。 要创建保管库，请按照[创建恢复服务保管库](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)中的步骤操作。
* 确保仅在 Windows 服务器或 Windows 客户端上安装[最新版本的 Azure 备份代理](https://aka.ms/azurebackup_agent)（如适用），并且计算机已注册到恢复服务保管库。
* 运行 Azure 备份代理的计算机需要 Azure PowerShell 3.7.0。 下载并[安装 Azure PowerShell 的 3.7.0 版本](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)。
* 在运行 Azure 备份代理的计算机上，请确保安装了 Microsoft 边缘或 Internet 资源管理器 11 并启用了 JavaScript。
* 在与恢复服务保管库相同的订阅中创建 Azure 存储帐户。
* 确保拥有创建 Azure Active Directory 应用程序的[所需权限](../active-directory/develop/howto-create-service-principal-portal.md)。 脱机备份工作流在与 Azure 存储帐户关联的订阅中创建 Azure 活动目录应用程序。 应用程序的目标是向 Azure 备份提供安全和范围的 Azure 导入/导出服务访问权限，这是脱机备份工作流所必需的。
* 使用包含 Azure 存储帐户的订阅注册*Microsoft.ImportExport*资源提供程序。 若要注册资源提供程序：
    1. 在主菜单上，选择 **"订阅**"。
    1. 如果您订阅了多个订阅，请选择计划用于脱机备份的订阅。 如果只使用一个订阅，则屏幕上会显示该订阅。
    1. 在订阅菜单上，选择 **"资源提供程序**"以查看提供程序的列表。
    1. 在提供程序列表中，向下滚动到*Microsoft.ImportExport*。 如果**状态****未注册**，请选择 **"注册**"。

        ![注册资源提供程序](./media/backup-azure-backup-import-export/registerimportexport.png)

* 创建了一个暂存位置，它可以是计算机上的网络共享或任何其他内部或外部驱动器，并且有足够的磁盘空间来保存初始副本。 例如，如果要备份 500 GB 文件服务器，请确保暂存区域至少为 500 GB。 （由于压缩，实际使用量更少。）
* 将磁盘发送到 Azure 时，请仅使用 2.5 英寸 SSD 或 2.5 英寸或 3.5 英寸 SATA II/III 内部硬盘驱动器。 可以使用容量最高为 10 TB 的硬盘驱动器。 查看 [Azure 导入/导出服务文档](../storage/common/storage-import-export-requirements.md#supported-hardware)，了解服务支持的最新驱动器集。
* SATA 驱动器必须连接到一台计算机（称为“副本计算机”），在这台计算机中完成将备份数据从暂存位置复制到 SATA 驱动器**。 确保在复制计算机上启用了 BitLocker。

## <a name="workflow"></a>工作流

本节介绍脱机备份工作流，以便数据可以传递到 Azure 数据中心并上载到 Azure 存储。 如果对导入服务或该过程的任何方面有疑问，请参阅[Azure 导入/导出服务概述文档](../storage/common/storage-import-export-service.md)。

## <a name="initiate-offline-backup"></a>启动脱机备份

1. 在恢复服务代理上安排备份时，您将看到此页面。

    ![导入页面](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

1. **选择使用我自己的磁盘传输**的选项。

    > [!NOTE]
    > 使用 Azure 数据框选项脱机传输初始备份数据。 此选项可节省采购自己的 Azure 兼容磁盘所需的工作量。 它提供 Microsoft 专有、安全和防篡改的 Azure 数据盒设备，恢复服务代理可以直接将备份数据写入这些设备。

1. 选择 **"下一步**"，然后仔细填写框。

    ![输入磁盘详细信息](./media/backup-azure-backup-import-export/your-disk-details.png)

   您填写的框包括：

    * **暂存位置** — 初始备份副本写入到的临时存储位置。 暂存位置可能位于网络共享或本地计算机上。 如果复制计算机和源计算机不同，请指定暂存位置的完整网络路径。
    * **Azure 资源管理器存储帐户**：任何 Azure 订阅中的资源管理器类型存储帐户（通用 v1 或通用 v2）的名称。
    * **Azure 存储容器**：在将备份数据复制到恢复服务保管库之前，Azure 存储帐户中的目标存储 Blob 的名称。
    * **Azure 订阅 ID**：创建 Azure 存储帐户的 Azure 订阅的 ID。
    * **Azure 导入作业名称**：Azure 导入/导出服务和 Azure 备份跟踪磁盘上发送到 Azure 的数据传输的唯一名称。
  
   填写完框后，选择 **"下一步**"。 保存**暂存位置**和**Azure 导入作业名称**信息。 准备磁盘是必需的。

1. 出现提示后，请登录到 Azure 订阅。 您必须登录，以便 Azure 备份可以创建 Azure 活动目录应用程序。 输入访问 Azure 导入/导出服务所需的权限。

    ![Azure 订阅登录页](./media/backup-azure-backup-import-export/azure-login.png)

1. 完成工作流。 在 Azure 备份代理控制台上，选择 **"立即备份**"。

    ![立即备份](./media/backup-azure-backup-import-export/backupnow.png)

1. 在向导的**确认**页面上，选择 **"备份**"。 初始备份将写入到设置过程中指定的暂存区域。

   ![确认您已准备好立即备份](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    操作完成后，暂存位置已准备就绪，可用于准备磁盘。

   ![立即备份向导页面](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>准备 SATA 驱动器并寄送到 Azure

*AzureOfflineBackupDiskPrep* 实用工具会准备送到最近 Azure 数据中心的 SATA 驱动器。 此实用程序在 Azure 备份代理安装目录中提供，如下所示：

```*\Microsoft Azure Recovery Services Agent\Utils\\*```

1. 转到目录，并将*AzureOfflineBackupDiskPrep*目录复制到连接 SATA 驱动器的另一台计算机。 在具有连接 SATA 驱动器的计算机上，确保：

   * 复制计算机可以使用"启动脱机备份"部分中工作流中提供的相同网络路径访问脱机种子工作流的暂存位置。
   * 已在副本计算机上启用 BitLocker。
   * Azure PowerShell 3.7.0 已安装。
   * 安装了最新的兼容浏览器（微软边缘或 Internet 资源管理器 11），并启用了 JavaScript。
   * 副本计算机可以访问 Azure 门户。 必要时，副本计算机可与源计算机相同。

     > [!IMPORTANT]
     > 如果源计算机是虚拟机，则复制计算机必须是与源计算机不同的物理服务器或客户端计算机。

1. 在复制计算机上打开一个提升的命令提示，将*AzureOfflineBackupDiskPrep*实用程序目录作为当前目录。 运行以下命令：

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | 参数 | 描述 |
    | --- | --- |
    | s：&lt;*暂存位置路径*&gt; |此强制输入用于提供在"启动脱机备份"部分的工作流中输入的暂存位置的路径。 |
    | p：&lt;*发布设置文件的路径*&gt; |此可选输入用于提供您在"启动脱机备份"部分的工作流中输入的 Azure 发布设置文件的路径。 |

    运行该命令时，实用程序请求选择与需要准备的驱动器对应的 Azure 导入作业。 如果只有一个导入作业与提供的暂存位置相关联，您将看到一个像这样的页面。

    ![Azure 磁盘准备工具输入](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

1. 输入想要准备传输到 Azure 的已装载磁盘的驱动器号（不要包含尾部的冒号）。
1. 出现提示时，请确认格式化驱动器。
1. 系统将提示您登录到 Azure 订阅。 输入凭据。

    ![Azure 订阅登录](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    然后，该工具开始准备磁盘并复制备份数据。 当工具提示时，您可能需要附加其他磁盘，以防提供的磁盘没有足够的空间用于备份数据。 <br/>

    成功结束该工具的执行时，命令提示符会提供三段信息：

   1. 准备好提供的一个或多个磁盘，以便寄送到 Azure。
   1. 您将收到已创建导入作业的确认。 导入作业使用提供的名称。
   1. 该工具会显示 Azure 数据中心的寄送地址。

      ![Azure 磁盘准备已完成](./media/backup-azure-backup-import-export/console2.png)<br/>

1. 结束命令的执行时，可以更新寄送信息。

1. 将磁盘运送到工具提供的地址。 保留跟踪编号以供将来参考。

   > [!IMPORTANT]
   > 没有两个 Azure 导入作业可以具有相同的跟踪编号。 确保实用程序在单个 Azure 导入作业下准备的驱动器在单个包中一起运送，并且包有一个唯一的跟踪编号。 不要将作为单独 Azure 导入作业的一部分准备的驱动器合并到单个包中。

## <a name="update-shipping-details-on-the-azure-import-job"></a>更新 Azure 导入作业的发货详细信息

以下过程更新 Azure 导入作业传送详细信息。 此信息包括以下各项的详细信息：

* 将磁盘传递到 Azure 的运营商的名称。
* 返回磁盘的运输详细信息。

1. 登录到 Azure 订阅。
1. 在主菜单上，选择 **"所有服务**"。 在"**所有服务**"对话框中，输入 **"导入**"。 当您看到**导入/导出作业**时，请选择它。

    ![输入运输信息](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    将打开 **"导入/导出作业"** 菜单，并显示所选订阅中的所有导入/导出作业的列表。

1. 如果您有多个订阅，请选择用于导入备份数据的订阅。 然后选择新创建的导入作业以打开其详细信息。

    ![查看运输信息](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

1. 在导入作业的 **"设置"** 菜单上，选择 **"管理发货信息**"。 输入退货运输详细信息。

    ![存储运输信息](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

1. 当您从运输承运人获得跟踪编号时，请在 Azure 导入作业概述页中选择横幅，然后输入以下详细信息。

   > [!IMPORTANT]
   > 确保快递公司信息和跟踪号在创建 Azure 导入作业后的两周内更新。 未能在两周内验证此信息可能会导致作业被删除，并且驱动器无法处理。

   ![跟踪信息更新警报](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![承运商信息和跟踪号码](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>处理驱动器的时间

处理 Azure 导入作业所需的时间各不相同。 处理时间基于装运时间、作业类型、要复制的数据的类型和大小以及提供的磁盘大小等因素。 Azure 导入/导出服务没有 SLA。 收到磁盘后，该服务将努力在 7 到 10 天内完成 Azure 存储帐户的备份数据副本。

### <a name="monitor-azure-import-job-status"></a>监视 Azure 导入作业状态

可以从 Azure 门户监视导入作业的状态。 转到 **"导入/导出作业"页**并选择作业。 有关导入作业状态的详细信息，请参阅[什么是 Azure 导入/导出服务？](../storage/common/storage-import-export-service.md)

### <a name="finish-the-workflow"></a>完成工作流

成功完成导入作业后，存储帐户中的初始备份数据可供使用。 在下一次计划备份时，Azure 备份会将数据内容从存储帐户复制到恢复服务保管库。

   ![将数据复制到恢复服务保管库](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

在执行下一次计划的备份时，Azure 备份会执行增量备份。

### <a name="clean-up-resources"></a>清理资源

完成初始备份后，可以安全地删除导入到 Azure 存储容器的数据和暂存位置中的备份数据。

## <a name="next-steps"></a>后续步骤

* 有关 Azure 导入/导出服务工作流的任何问题，请参阅[使用 Microsoft Azure 导入/导出服务将数据传输到 Blob 存储](../storage/common/storage-import-export-service.md)。
