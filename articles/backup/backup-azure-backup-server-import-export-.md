---
title: DPM 和 Azure 备份服务器的脱机备份
description: 使用 Azure 备份，可以使用 Azure 导入/导出服务将数据从网络外发送。 本文介绍了 DPM 和 Azure 备份服务器的脱机备份工作流。
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 080b0bc53b2058bd186e90f354b8f5bcda510414
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197030"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server"></a>DPM 和 Azure 备份服务器的脱机备份工作流

Azure 备份有多个可提升效率的内置功能，能在数据初始完整备份到 Azure 期间节省网络和存储成本。 初始完整备份通常传输大量数据，且需要较多网络带宽，相比之下，后续备份只传输增量部分。 Azure 备份可压缩初始备份。 通过脱机种子设定，Azure 备份可以使用磁盘将压缩后的初始备份数据脱机上传到 Azure。

Azure 备份的脱机种子化过程与[Azure 导入/导出服务](../storage/common/storage-import-export-service.md)紧密集成。 您可以使用此服务使用磁盘将数据传输到 Azure。 如果具有需要通过高延迟和低带宽网络传输的初始备份数据 TB （TB），则可以使用脱机种子工作流将一个或多个硬盘驱动器上的初始备份副本传送到 Azure 数据中心。 本文为系统中心数据保护管理器 （DPM） 和 Microsoft Azure 备份服务器 （MABS） 提供了完成此工作流的概述和进一步步骤。

> [!NOTE]
> Microsoft Azure 恢复服务 （MARS） 代理的脱机备份过程不同于 DPM 和 MABS。 有关使用 MARS 代理脱机备份的信息，请参阅[Azure 备份 中的脱机备份工作流](backup-azure-backup-import-export.md)。 使用 Azure 备份代理完成的系统状态备份不支持脱机备份。
>

## <a name="overview"></a>概述

借助 Azure 备份和 Azure 导入/导出服务的脱机种子化功能，使用磁盘将数据脱机上载到 Azure 非常简单。 脱机备份过程涉及以下步骤：

> [!div class="checklist"]
>
> * 备份数据写入暂存位置，而不是通过网络发送。
> * 然后，使用*AzureOfflineBackupDiskPrep*实用程序将暂存位置上的数据写入一个或多个 SATA 磁盘。
> * 实用程序会自动创建 Azure 导入作业。
> * 然后，SATA 驱动器将发送到最近的 Azure 数据中心。
> * 将备份数据上传到 Azure 之后，Azure 备份将备份数据复制到备份保管库，并计划增量备份。

## <a name="supported-configurations"></a>支持的配置

Azure 备份的所有部署模型都支持脱机备份，这些模型将数据从本地备份到 Microsoft 云。 这些型号包括：

> [!div class="checklist"]
>
> * 使用 MARS 代理或 Azure 备份代理备份文件和文件夹。
> * 使用 DPM 备份所有工作负载和文件。
> * 使用 MABS 备份所有工作负载和文件。

## <a name="prerequisites"></a>先决条件

在启动脱机备份工作流之前，请确保满足以下先决条件：

* 已创建[恢复服务保管库](backup-azure-recovery-services-vault-overview.md)。 要创建一个，请按照[创建恢复服务保管库](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)中的步骤操作
* Azure 备份代理或 MABS 或 DPM 已安装在 Windows 服务器或 Windows 客户端（如适用）上，并且计算机已注册到恢复服务保管库。 确保仅使用[最新版本的 Azure 备份](https://go.microsoft.com/fwlink/?linkid=229525)。
* 在计划从中备份数据的计算机上[下载 Azure 发布设置文件](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade)。 从中下载发布设置文件的订阅可能不同于包含恢复服务保管库的订阅。 如果您的订阅位于主权 Azure 云上，请使用以下适当链接下载 Azure 发布设置文件。

    | 主权云区域 | Azure 发布设置文件链接 |
    | --- | --- |
    | United States | [链接](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | 中国 | [链接](https://portal.azure.cn/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* 在下载发布设置文件的订阅中创建了具有资源管理器部署模型的 Azure 存储帐户。

  ![使用资源管理器开发创建存储帐户](./media/backup-azure-backup-import-export/storage-account-resource-manager.png)

* 创建了一个暂存位置，它可以是计算机上的网络共享或任何其他内部或外部驱动器，并且有足够的磁盘空间来保存初始副本。 例如，如果要备份 500 GB 文件服务器，请确保暂存区域至少为 500 GB。 （由于压缩，实际使用量更少。）
* 对于发送到 Azure 的磁盘，请确保仅使用 2.5 英寸 SSD 或 2.5 英寸或 3.5 英寸 SATA II/III 内部硬盘驱动器。 可以使用容量最高为 10 TB 的硬盘驱动器。 查看 [Azure 导入/导出服务文档](../storage/common/storage-import-export-requirements.md#supported-hardware)，了解服务支持的最新驱动器集。
* SATA 驱动器必须连接到一台计算机（称为“副本计算机”），在这台计算机中完成将备份数据从暂存位置复制到 SATA 驱动器**。 确保在复制计算机上启用了 BitLocker。

## <a name="prepare-the-server-for-the-offline-backup-process"></a>为脱机备份过程准备服务器

>[!NOTE]
> 如果在安装 MARS 代理时找不到列出的实用程序（如*AzureOfflineBackupCertGen.exe），* 请写信AskAzureBackupTeam@microsoft.com访问它们。

* 在服务器上打开一个提升的命令提示，并运行以下命令：

    ```cmd
    AzureOfflineBackupCertGen.exe CreateNewApplication SubscriptionId:<Subs ID>
    ```

    该工具将创建 Azure 脱机备份活动目录应用程序（如果不存在）。

    如果应用程序已存在，此可执行文件要求您手动将证书上载到租户中的应用程序。 按照[本节](#manually-upload-an-offline-backup-certificate)中的步骤手动将证书上载到应用程序。

* *Azure 离线备份.exe*工具生成*脱机应用程序Params.xml*文件。 使用 MABS 或 DPM 将此文件复制到服务器。
* 在 DPM 实例或 Azure 备份服务器上安装[最新的 MARS 代理](https://aka.ms/azurebackup_agent)。
* 将服务器注册到 Azure。
* 运行以下命令：

    ```cmd
    AzureOfflineBackupCertGen.exe AddRegistryEntries SubscriptionId:<subscriptionid> xmlfilepath:<path of the OfflineApplicationParams.xml file>  storageaccountname:<storageaccountname configured with Azure Data Box>
    ```

* 前面的命令将创建文件`C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch\MicrosoftBackupProvider\OfflineApplicationParams_<Storageaccountname>.xml`。

## <a name="manually-upload-an-offline-backup-certificate"></a>手动上载脱机备份证书

按照以下步骤手动将脱机备份证书上载到以前创建的用于脱机备份的 Azure 活动目录应用程序。

1. 登录到 Azure 门户。  
1. 转到**Azure 活动目录** > **应用注册**。
1. 在 **"拥有的应用程序**"选项卡上，找到具有显示名称格式`AzureOfflineBackup _<Azure User Id`的应用程序。

    ![在"拥有的应用程序"选项卡上查找应用程序](./media/backup-azure-backup-import-export/owned-applications.png)

1. 选择应用程序。 在左侧窗格中的 **"管理"** 下，转到**证书&机密**。
1. 检查现有证书或公钥。 如果没有，则可以通过选择应用程序 **"概述"** 页上的 **"删除**"按钮来安全地删除应用程序。 然后，您可以重试为[脱机备份过程准备服务器](#prepare-the-server-for-the-offline-backup-process)的步骤，并跳过以下步骤。 否则，请继续从要配置脱机备份的 DPM 实例或 Azure 备份服务器中执行这些步骤。
1. 选择 **"管理计算机证书应用程序** > **个人"** 选项卡。查找名称的证书`CB_AzureADCertforOfflineSeeding_<ResourceId>`。
1. 选择证书，右键单击 **"所有任务**"，**然后选择导出**，而不使用私钥，以 .cer 格式。
1. 转到 Azure 门户中的 Azure 脱机备份应用程序。
1. 选择 **"管理** > **证书&机密** > **上载证书**。 上载在上一步骤中导出的证书。

    ![上传证书](./media/backup-azure-backup-import-export/upload-certificate.png)

1. 在服务器上，通过在运行窗口中输入**注册来**打开注册表。
1. 转到注册表项*计算机\HKEY_LOCAL_MACHINE\软件\微软\Windows Azure 备份\配置\云备份提供程序*。
1. 右键单击**CloudBackupProvider**，并添加名称的新字符串值`AzureADAppCertThumbprint_<Azure User Id>`。

    >[!NOTE]
    > 要查找 Azure 用户 ID，执行以下步骤之一：
    >
    >* 从 Azure 连接的 PowerShell`Get-AzureRmADUser -UserPrincipalName “Account Holder’s email as appears in the portal”`运行命令。
    >* 转到注册表路径`Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup; Name: CurrentUserId;`。

1. 右键单击上一步中添加的字符串，然后选择 **"修改**"。 在 值中，提供步骤 7 中导出的证书的指纹。 然后选择 **"确定**"。
1. 要获取指纹的值，请双击证书。 选择"**详细信息**"选项卡，然后向下滚动，直到看到指纹字段。 选择 **"拇指打印"** 并复制该值。

    ![从指纹字段中复制值](./media/backup-azure-backup-import-export/thumbprint-field.png)

1. 继续到["工作流"](#workflow)部分以继续脱机备份过程。

## <a name="workflow"></a>工作流

本节中的信息可帮助您完成脱机备份工作流，以便数据可以传递到 Azure 数据中心并上载到 Azure 存储。 如果您对导入服务或流程的任何方面有疑问，请参阅前面引用的[导入服务概述文档](../storage/common/storage-import-export-service.md)。

### <a name="initiate-offline-backup"></a>启动脱机备份

1. 计划备份时，在 Windows 服务器、Windows 客户端或 DPM 中看到以下页面。

    ![导入页面](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    下面是 DPM 中的相应页面。 <br/>
    
    ![DPM 和 Azure 备份服务器导入页](./media/backup-azure-backup-import-export/dpmoffline.png)

    您填写的框包括：

   * **暂存位置** — 初始备份副本写入到的临时存储位置。 暂存位置可能位于网络共享或本地计算机上。 如果复制计算机和源计算机不同，请指定暂存位置的完整网络路径。
   * **Azure 发布设置**：发布设置文件的本地路径。
   * **Azure 导入作业名称**：Azure 导入/导出服务和 Azure 备份跟踪磁盘上发送到 Azure 的数据传输的唯一名称。
   * **Azure 订阅 ID**：从中下载 Azure 发布设置文件的订阅的 Azure 订阅 ID。
   * **Azure 存储帐户**：与 Azure 发布设置文件关联的 Azure 订阅中的存储帐户的名称。
   * **Azure 存储容器**：Azure 存储帐户中导入备份数据的目标存储 Blob 的名称。

   保存**暂存位置**和提供的**Azure 导入作业名称**信息。 准备磁盘是必需的。

1. 完成工作流。 要启动脱机备份副本，请在 Azure 备份代理管理控制台上选择 **"立即备份**"。 初始备份写入到临时区域作为此步骤的一部分。

    ![立即备份](./media/backup-azure-backup-import-export/backupnow.png)

    要在 DPM 或 Azure 备份服务器中完成相应的工作流，请右键单击**保护组**。 选择"**创建恢复点"** 选项。 然后选择 **"联机保护**"选项。

    ![DPM 和 MABS 立即备份](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    操作完成后，暂存位置已准备就绪，可用于准备磁盘。

    ![备份进度](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>准备 SATA 驱动器并寄送到 Azure

*AzureOfflineBackupDiskPrep*实用程序用于准备发送到最近的 Azure 数据中心的 SATA 驱动器。 此实用程序在恢复服务代理的安装目录中提供，如下所示：

`*\\Microsoft Azure Recovery Services Agent\Utils\*`

1. 转到该目录，将“AzureOfflineBackupDiskPrep”目录复制到 SATA 驱动器准备连接的副本计算机上**。 请确保：

   * 复制计算机可以使用"启动脱机备份"部分中工作流中提供的相同网络路径访问脱机种子工作流的暂存位置。
   * 已在副本计算机上启用 BitLocker。
   * 副本计算机可以访问 Azure 门户。 必要时，副本计算机可与源计算机相同。

     > [!IMPORTANT]
     > 如果源计算机是虚拟机，则必须使用其他物理服务器或客户端计算机作为复制计算机。

1. 在复制计算机上打开一个提升的命令提示，将*AzureOfflineBackupDiskPrep*实用程序目录作为当前目录。 运行以下命令：

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | 参数 | 描述 |
    | --- | --- |
    | s：&lt;*暂存位置路径*&gt; |此强制输入用于提供在"启动脱机备份"部分的工作流中输入的暂存位置的路径。 |
    | p：&lt;*发布设置文件的路径*&gt; |此可选输入用于提供您在"启动脱机备份"部分的工作流中输入的 Azure 发布设置文件的路径。 |

    > [!NOTE]
    > 复制计算机与源计算机不同时，&lt;AzurePublishSettingFile 的路径&gt; 值是必需的。
    >
    >

    运行该命令时，实用程序请求选择与需要准备的驱动器对应的 Azure 导入作业。 如果只有一个导入作业与提供的暂存位置相关联，您将看到一个像这样的页面。

    ![Azure 磁盘准备工具输入](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>

1. 输入想要准备传输到 Azure 的已装载磁盘的驱动器号（不要包含尾部的冒号）。 出现提示时，请提供驱动器格式的确认。

    然后，该工具开始准备磁盘并复制备份数据。 当工具提示时，您可能需要附加其他磁盘，以防提供的磁盘没有足够的空间用于备份数据。 <br/>

    该工具成功完成后，您提供的一个或多个磁盘已准备好用于传送到 Azure。 在 Azure 中还会创建具有"启动脱机备份"部分工作流期间提供名称的导入作业。 最后，工具上还显示磁盘所要寄送到的 Azure 数据中心的寄送地址。

    ![Azure 磁盘准备已完成](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>

1. 在命令执行结束时，您还会看到更新装运信息的选项。

    ![更新运输信息选项](./media/backup-azure-backup-import-export/updateshippingutility.png)<br/>

1. 可以立即输入详细信息。 该工具将指导您完成涉及一系列输入的过程。 如果您没有跟踪编号等信息或与发货相关的其他详细信息，则可以结束会话。 这篇文章介绍了随后更新寄送详情的操作步骤。

1. 将磁盘运送到工具提供的地址。 保留跟踪编号以供将来参考。

   > [!IMPORTANT]
   > 没有两个 Azure 导入作业可以具有相同的跟踪编号。 确保实用程序在单个 Azure 导入作业下准备的驱动器在单个包中一起运送，并且包有一个唯一的跟踪编号。 不要将作为不同 Azure 导入作业的一部分准备的驱动器合并到单个包中。

1. 当您有跟踪编号信息时，转到源计算机，该计算机正在等待导入作业完成。 在提升的命令提示中运行以下命令，将*AzureOfflineBackupDiskPrep*实用程序目录作为当前目录。

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   您可以选择从其他计算机（如复制计算机）运行以下命令 *，AzureOfflineBackupDiskPrep*实用程序目录作为当前目录。

   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | 参数 | 描述 |
    | --- | --- |
    | u: | 此强制输入用于更新 Azure 导入作业的发货详细信息。 |
    | s：&lt;*暂存位置路径*&gt; | 当命令未在源计算机上运行时，使用此强制输入。 它用于提供在"启动脱机备份"部分的工作流中输入的暂存位置的路径。 |
    | p：&lt;*发布设置文件的路径*&gt; | 当命令未在源计算机上运行时，使用此强制输入。 它用于提供您在"启动脱机备份"部分的工作流中输入的 Azure 发布设置文件的路径。 |

    当命令在另一台计算机上运行时，实用程序会自动检测源计算机正在等待的导入作业或与暂存位置关联的导入作业。 然后，它提供了通过一系列输入更新运输信息的选项。

    ![输入运输信息](./media/backup-azure-backup-import-export/shippinginputs.png)<br/>

1. 提供所有输入后，请仔细查看详细信息，并通过输入**yes**提交您提供的运输信息。

    ![查看运输信息](./media/backup-azure-backup-import-export/reviewshippinginformation.png)<br/>

1. 成功更新运输信息后，实用程序会提供存储您输入的运输详细信息的本地位置。

    ![存储运输信息](./media/backup-azure-backup-import-export/storingshippinginformation.png)<br/>

   > [!IMPORTANT]
   > 确保驱动器在使用*AzureOfflineBackupDiskPrep*实用程序提供运输信息后的两周内到达 Azure 数据中心。 如果不能做到，可能会导致驱动器得不到处理。 

完成上述步骤后，Azure 数据中心已准备好接收驱动器，并进一步处理它们，以便将备份数据从驱动器传输到您创建的经典类型的 Azure 存储帐户。

### <a name="time-to-process-the-drives"></a>处理驱动器的时间

处理 Azure 导入作业所需的时间各不相同。 处理时间取决于要复制的数据的装运时间、作业类型、类型和大小以及提供的磁盘大小等因素。 Azure 导入/导出服务没有 SLA。 收到磁盘后，该服务将努力在 7 到 10 天内完成备份数据副本到 Azure 存储帐户。 下一节将介绍如何监视 Azure 导入作业的状态。

### <a name="monitor-azure-import-job-status"></a>监视 Azure 导入作业状态

当驱动器正在传输或 Azure 数据中心复制到存储帐户时，源计算机上的 Azure 备份代理或 DPM 或 MABS 控制台将显示计划备份的以下作业状态：

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

要检查导入作业状态：

1. 在源计算机上打开一个提升的命令提示，并运行以下命令：

     `AzureOfflineBackupDiskPrep.exe u:`

1. 输出显示导入作业的当前状态。

    ![检查导入作业状态](./media/backup-azure-backup-import-export/importjobstatusreporting.png)<br/>

有关 Azure 导入作业的各种状态的详细信息，请参阅[查看 Azure 导入/导出作业的状态](../storage/common/storage-import-export-view-drive-status.md)。

### <a name="finish-the-workflow"></a>完成工作流

导入作业完成后，存储帐户中的初始备份数据可供使用。 在下一次计划备份时，Azure 备份会将数据内容从存储帐户复制到恢复服务保管库。

   ![将数据复制到恢复服务保管库](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

在进行到下一个计划备份时，Azure 备份通过初始备份副本执行增量备份。

## <a name="next-steps"></a>后续步骤

* 有关 Azure 导入/导出服务工作流的任何问题，请参阅[使用 Microsoft Azure 导入/导出服务将数据传输到 Blob 存储](../storage/common/storage-import-export-service.md)。
