---
title: DPM 和 Azure 备份服务器的脱机备份
description: 使用 Azure 备份，可以通过 Azure 导入/导出服务将数据发送到网络。 本文介绍 DPM 和 Azure 备份服务器的脱机备份工作流。
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 080b0bc53b2058bd186e90f354b8f5bcda510414
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197030"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server"></a>DPM 和 Azure 备份服务器的脱机备份工作流

Azure 备份有多个可提升效率的内置功能，能在数据初始完整备份到 Azure 期间节省网络和存储成本。 初始完整备份通常传输大量数据，且需要较多网络带宽，相比之下，后续备份只传输增量部分。 Azure 备份可压缩初始备份。 通过脱机种子设定，Azure 备份可以使用磁盘将压缩后的初始备份数据脱机上传到 Azure。

Azure 备份的脱机种子设定过程与[Azure 导入/导出服务](../storage/common/storage-import-export-service.md)紧密集成。 可以使用此服务使用磁盘将数据传输到 Azure。 如果你有 tb （Tb）的初始备份数据需要通过高延迟、低带宽网络传输，则可以使用脱机种子设定工作流将一个或多个硬盘驱动器上的初始备份副本传送到 Azure 数据中心。 本文提供了针对 System Center Data Protection Manager （DPM）和 Microsoft Azure 备份服务器（MABS）完成此工作流的概述和更多步骤。

> [!NOTE]
> Microsoft Azure 恢复服务（MARS）代理的脱机备份过程与 DPM 和 MABS 不同。 有关将脱机备份用于 MARS 代理的信息，请参阅[Azure 备份中的脱机备份工作流](backup-azure-backup-import-export.md)。 使用 Azure 备份代理执行的系统状态备份不支持脱机备份。
>

## <a name="overview"></a>概述

使用 Azure 备份和 Azure 导入/导出服务的脱机播种功能，可以轻松地使用磁盘将数据脱机上载到 Azure。 脱机备份过程涉及以下步骤：

> [!div class="checklist"]
>
> * 将备份数据写入暂存位置，而不是通过网络发送。
> * 然后，使用*AzureOfflineBackupDiskPrep*实用工具将暂存位置上的数据写入一个或多个 SATA 磁盘。
> * Azure 导入作业由实用工具自动创建。
> * 然后，将 SATA 驱动器发送到最近的 Azure 数据中心。
> * 将备份数据上传到 Azure 之后，Azure 备份将备份数据复制到备份保管库，并计划增量备份。

## <a name="supported-configurations"></a>支持的配置

将数据备份到 Microsoft 云的 Azure 备份的所有部署模型都支持脱机备份。 这些模型包括：

> [!div class="checklist"]
>
> * 通过 MARS 代理或 Azure 备份代理备份文件和文件夹。
> * 用 DPM 备份所有工作负荷和文件。
> * MABS 的所有工作负荷和文件的备份。

## <a name="prerequisites"></a>必备条件

在启动脱机备份工作流之前，请确保满足以下先决条件：

* 已创建[恢复服务保管库](backup-azure-recovery-services-vault-overview.md)。 若要创建一个，请按照[创建恢复服务保管库](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)中的步骤操作
* 已在 Windows Server 或 Windows 客户端上安装 Azure 备份代理或 MABS 或 DPM （如果适用），并且计算机已注册到恢复服务保管库。 确保仅使用[最新版本的 Azure 备份](https://go.microsoft.com/fwlink/?linkid=229525)。
* 在计划备份数据的计算机上，[下载 Azure 发布设置文件](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade)。 下载发布设置文件的订阅可能不同于包含恢复服务保管库的订阅。 如果订阅位于主权 Azure 云上，请根据需要使用以下链接下载 Azure 发布设置文件。

    | 主权云区域 | Azure 发布设置文件链接 |
    | --- | --- |
    | United States | [链接](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | 中国 | [链接](https://portal.azure.cn/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* 已在下载发布设置文件的订阅中创建了具有资源管理器部署模型的 Azure 存储帐户。

  ![使用资源管理器开发创建存储帐户](./media/backup-azure-backup-import-export/storage-account-resource-manager.png)

* 创建了一个暂存位置，它可以是计算机上的网络共享或任何其他内部或外部驱动器，并且有足够的磁盘空间来保存初始副本。 例如，如果要备份 500 GB 的文件服务器，请确保暂存区域至少为 500 GB。 （由于压缩，实际使用量更少。）
* 对于发送到 Azure 的磁盘，请确保只使用2.5 英寸 SSD 或2.5 英寸或3.5 英寸 SATA II/III 内部硬盘驱动器。 可以使用容量最高为 10 TB 的硬盘驱动器。 查看 [Azure 导入/导出服务文档](../storage/common/storage-import-export-requirements.md#supported-hardware)，了解服务支持的最新驱动器集。
* 必须将 SATA 驱动器连接到计算机（称为*副本计算机*），将备份数据从暂存位置复制到 SATA 驱动器。 确保在复制计算机上启用 BitLocker。

## <a name="prepare-the-server-for-the-offline-backup-process"></a>为脱机备份过程准备服务器

>[!NOTE]
> 如果在安装 MARS 代理时找不到列出的实用程序（如*AzureOfflineBackupCertGen*），请写入 AskAzureBackupTeam@microsoft.com 以获取对它们的访问权限。

* 在服务器上打开提升的命令提示符，并运行以下命令：

    ```cmd
    AzureOfflineBackupCertGen.exe CreateNewApplication SubscriptionId:<Subs ID>
    ```

    如果应用程序不存在，则该工具会创建 Azure 脱机备份 Active Directory 应用程序。

    如果应用程序已存在，则此可执行文件会要求你将证书手动上传到租户中的应用程序。 按照[本部分](#manually-upload-an-offline-backup-certificate)中的步骤，将证书手动上传到应用程序。

* *AzureOfflineBackup*工具将生成*OfflineApplicationParams*文件。 将此文件复制到具有 MABS 或 DPM 的服务器。
* 在 DPM 实例或 Azure 备份服务器上安装[最新的 MARS 代理](https://aka.ms/azurebackup_agent)。
* 将服务器注册到 Azure。
* 运行以下命令：

    ```cmd
    AzureOfflineBackupCertGen.exe AddRegistryEntries SubscriptionId:<subscriptionid> xmlfilepath:<path of the OfflineApplicationParams.xml file>  storageaccountname:<storageaccountname configured with Azure Data Box>
    ```

* 上一个命令 `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch\MicrosoftBackupProvider\OfflineApplicationParams_<Storageaccountname>.xml`创建文件。

## <a name="manually-upload-an-offline-backup-certificate"></a>手动上传脱机备份证书

按照以下步骤，将脱机备份证书手动上传到之前创建的 Azure Active Directory 应用程序，以便进行脱机备份。

1. 登录到 Azure 门户。
1. 请参阅**Azure Active Directory** > **应用注册**。
1. 在 "**拥有的应用程序**" 选项卡上，找到显示名称格式 `AzureOfflineBackup _<Azure User Id`的应用程序。

    ![在拥有的应用程序选项卡上查找应用程序](./media/backup-azure-backup-import-export/owned-applications.png)

1. 选择应用程序。 在左侧窗格中的 "**管理**" 下，中转到**证书 & 密码**。
1. 检查是否存在已存在的证书或公钥。 如果没有，可以通过在应用程序的 "**概述**" 页上选择 "**删除**" 按钮来安全地删除应用程序。 然后，你可以重试为[脱机备份过程准备服务器](#prepare-the-server-for-the-offline-backup-process)的步骤，并跳过以下步骤。 否则，请继续执行 DPM 实例或 Azure 备份服务器（要在其中配置脱机备份）中的以下步骤。
1. 选择 "**管理计算机证书应用程序** > **个人**" 选项卡。查找名称为 `CB_AzureADCertforOfflineSeeding_<ResourceId>`的证书。
1. 选择证书，右键单击 "**所有任务**"，然后选择 "**导出**" （不带私钥，格式为 .cer）。
1. 在 Azure 门户中，请参阅 Azure 脱机备份应用程序。
1. 选择 "**管理** > **证书 & 机密** > **上载证书**"。 上传在上一步骤中导出的证书。

    ![上传证书](./media/backup-azure-backup-import-export/upload-certificate.png)

1. 在服务器上，在 "运行" 窗口中输入**regedit**打开注册表。
1. 请参阅注册表项*Computer \ HKEY_LOCAL_MACHINE \Software\microsoft\windows Azure Backup\Config\CloudBackupProvider*。
1. 右键单击**CloudBackupProvider**，然后添加一个名称 `AzureADAppCertThumbprint_<Azure User Id>`的新字符串值。

    >[!NOTE]
    > 若要查找 Azure 用户 ID，请执行以下步骤之一：
    >
    >* 在 Azure 连接的 PowerShell 中运行 `Get-AzureRmADUser -UserPrincipalName “Account Holder’s email as appears in the portal”` 命令。
    >* 中转到 "注册表路径" `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup; Name: CurrentUserId;`。

1. 右键单击在上一步中添加的字符串，然后选择 "**修改**"。 在 "值" 中，提供你在步骤7中导出的证书的指纹。 然后选择“确定”。
1. 若要获取该指纹的值，请双击该证书。 选择 "**详细信息**" 选项卡，然后向下滚动，直到看到 "指纹" 字段。 选择 "**指纹**" 并复制值。

    ![从 "指纹" 字段复制值](./media/backup-azure-backup-import-export/thumbprint-field.png)

1. 继续进入[工作流](#workflow)部分，继续执行脱机备份过程。

## <a name="workflow"></a>工作流

本部分中的信息可帮助你完成脱机备份工作流，以便可以将你的数据传送到 Azure 数据中心并上载到 Azure 存储。 如果有关于导入服务或流程的任何方面的问题，请参阅前面提到的[导入服务概述文档](../storage/common/storage-import-export-service.md)。

### <a name="initiate-offline-backup"></a>启动脱机备份

1. 在计划备份时，你将看到 Windows Server、Windows 客户端或 DPM 中的以下页面。

    ![导入页](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    下面是 DPM 中的相应页面。 <br/>
    
    ![DPM 和 Azure 备份服务器导入页](./media/backup-azure-backup-import-export/dpmoffline.png)

    填写的框包括：

   * **暂存位置** — 初始备份副本写入到的临时存储位置。 暂存位置可能位于网络共享或本地计算机上。 如果副本计算机与源计算机不同，则指定暂存位置的完整网络路径。
   * **Azure 发布设置**：发布设置文件的本地路径。
   * **Azure 导入作业名称**： Azure 导入/导出服务和 azure 备份跟踪磁盘上发送到 Azure 的数据传输的唯一名称。
   * **Azure 订阅 id**：从中下载 azure 发布设置文件的订阅的 AZURE 订阅 id。
   * **Azure 存储帐户**： azure 订阅中与 azure 发布设置文件关联的存储帐户的名称。
   * **Azure 存储容器**：Azure 存储帐户中导入备份数据的目标存储 Blob 的名称。

   保存**暂存位置**和提供的**Azure 导入作业名称**信息。 需要准备磁盘。

1. 完成工作流。 若要启动脱机备份副本，请在 Azure 备份代理管理控制台中选择 "**立即备份**"。 初始备份写入到临时区域作为此步骤的一部分。

    ![立即备份](./media/backup-azure-backup-import-export/backupnow.png)

    若要在 DPM 或 Azure 备份服务器中完成相应的工作流，请右键单击 "**保护组**"。 选择 "**创建恢复点**" 选项。 然后选择 "**联机保护**" 选项。

    ![DPM 和 MABS 立即备份](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    操作完成后，暂存位置已准备就绪，可用于准备磁盘。

    ![备份进度](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>准备 SATA 驱动器并寄送到 Azure

*AzureOfflineBackupDiskPrep*实用程序用于准备发送到最近的 Azure 数据中心的 SATA 驱动器。 此实用程序可在恢复服务代理的安装目录中找到以下路径：

`*\\Microsoft Azure Recovery Services Agent\Utils\*`

1. 转到该目录，将“AzureOfflineBackupDiskPrep”目录复制到 SATA 驱动器准备连接的副本计算机上。 请确保：

   * 副本计算机可以通过使用在 "启动脱机备份" 部分中提供的相同网络路径，访问脱机种子设定工作流的暂存位置。
   * 已在副本计算机上启用 BitLocker。
   * 副本计算机可以访问 Azure 门户。 必要时，副本计算机可与源计算机相同。

     > [!IMPORTANT]
     > 如果源计算机是虚拟机，则必须使用其他物理服务器或客户端计算机作为副本计算机。

1. 在复制计算机上打开提升的命令提示符，并将*AzureOfflineBackupDiskPrep*实用工具目录作为当前目录。 运行以下命令：

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | 参数 | 说明 |
    | --- | --- |
    | s:&lt;暂存位置路径&gt; |此必需的输入用于提供在 "启动脱机备份" 部分的工作流中输入的暂存位置的路径。 |
    | p:&lt;发布设置文件的路径&gt; |此可选输入用于提供在 "启动脱机备份" 部分的工作流中输入的 Azure 发布设置文件的路径。 |

    > [!NOTE]
    > 复制计算机与源计算机不同时，&lt;AzurePublishSettingFile 的路径&gt; 值是必需的。
    >
    >

    运行该命令时，该实用工具将请求选择与需要准备的驱动器对应的 Azure 导入作业。 如果仅有一个导入作业与提供的暂存位置相关联，则会看到类似于下面的页面。

    ![Azure 磁盘准备工具输入](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>

1. 输入想要准备传输到 Azure 的已装载磁盘的驱动器号（不要包含尾部的冒号）。 出现提示时，请为驱动器的格式设置确认。

    然后，该工具将开始准备磁盘并复制备份数据。 如果提供的磁盘没有足够空间来备份数据，则可能需要在工具提示时附加更多磁盘。 <br/>

    工具成功完成后，你提供的一个或多个磁盘将准备好寄送到 Azure。 在 Azure 中，还会在 Azure 中创建在工作流中提供的名为 "启动脱机备份" 部分的导入作业。 最后，工具上还显示磁盘所要寄送到的 Azure 数据中心的寄送地址。

    ![Azure 磁盘准备已完成](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>

1. 命令执行结束时，还会看到更新装运信息的选项。

    ![更新装运信息选项](./media/backup-azure-backup-import-export/updateshippingutility.png)<br/>

1. 可以立即输入详细信息。 该工具将指导您完成涉及一系列输入的过程。 如果没有与发货相关的信息（如跟踪号码或其他详细信息），则可以结束该会话。 这篇文章介绍了随后更新寄送详情的操作步骤。

1. 将磁盘寄送到工具提供的地址。 保留跟踪号以供将来参考。

   > [!IMPORTANT]
   > 两个 Azure 导入作业不能具有相同的跟踪号。 确保单个 Azure 导入作业下的实用程序准备的驱动器在单个包中一起发货，并为包提供单个唯一的跟踪号。 不要将准备的驱动器合并为单个包中的不同 Azure 导入作业的一部分。

1. 当你有跟踪号信息时，请前往正在等待导入作业完成的源计算机。 在提升的命令提示符下运行以下命令，并将*AzureOfflineBackupDiskPrep*实用工具目录作为当前目录运行。

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   您可以选择从另一台计算机（如副本计算机）运行以下命令，并将*AzureOfflineBackupDiskPrep*实用工具目录作为当前目录。

   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | 参数 | 说明 |
    | --- | --- |
    | u: | 此必需的输入用于更新 Azure 导入作业的发货详细信息。 |
    | s:&lt;暂存位置路径&gt; | 当命令未在源计算机上运行时，将使用此必需的输入。 它用于提供在 "启动脱机备份" 部分的工作流中输入的暂存位置的路径。 |
    | p:&lt;发布设置文件的路径&gt; | 当命令未在源计算机上运行时，将使用此必需的输入。 它用于提供在 "启动脱机备份" 部分的工作流中输入的 Azure 发布设置文件的路径。 |

    此实用程序会自动检测源计算机正在等待的导入作业，或在另一台计算机上运行命令时与暂存位置关联的导入作业。 然后，它提供了通过一系列输入更新寄送信息的选项。

    ![输入寄送信息](./media/backup-azure-backup-import-export/shippinginputs.png)<br/>

1. 提供所有输入后，请仔细查看详细信息，并通过输入 **"是"** 提交提供的发货信息。

    ![查看装运信息](./media/backup-azure-backup-import-export/reviewshippinginformation.png)<br/>

1. 成功更新寄送信息后，该实用程序会提供一个本地位置，你输入的装运详细信息将存储在该位置。

    ![商店发货信息](./media/backup-azure-backup-import-export/storingshippinginformation.png)<br/>

   > [!IMPORTANT]
   > 通过使用*AzureOfflineBackupDiskPrep*实用程序，确保驱动器在两周内到达 Azure 数据中心。 如果不能做到，可能会导致驱动器得不到处理。 

完成前面的步骤后，Azure 数据中心就可以接收驱动器，并进一步处理这些驱动器，将备份数据从驱动器传输到创建的经典类型的 Azure 存储帐户。

### <a name="time-to-process-the-drives"></a>处理驱动器的时间

处理 Azure 导入作业所需的时间会有所不同。 处理时间取决于发货时间、作业类型、要复制的数据的类型和大小，以及所提供磁盘的大小等因素。 Azure 导入/导出服务没有 SLA。 收到磁盘后，服务会在7到10天内完成将备份数据复制到 Azure 存储帐户的操作。 下一部分介绍如何监视 Azure 导入作业的状态。

### <a name="monitor-azure-import-job-status"></a>监视 Azure 导入作业状态

当驱动器在传输过程中或在要复制到存储帐户的 Azure 数据中心时，源计算机上的 Azure 备份代理或 DPM 或 MABS 控制台将显示计划备份的以下作业状态：

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

检查导入作业状态：

1. 在源计算机上打开提升的命令提示符，并运行以下命令：

     `AzureOfflineBackupDiskPrep.exe u:`

1. 输出显示导入作业的当前状态。

    ![检查导入作业状态](./media/backup-azure-backup-import-export/importjobstatusreporting.png)<br/>

有关 Azure 导入作业的各种状态的详细信息，请参阅[查看 Azure 导入/导出作业的状态](../storage/common/storage-import-export-view-drive-status.md)。

### <a name="finish-the-workflow"></a>完成工作流

导入作业完成后，存储帐户中的初始备份数据可供使用。 在下一次计划的备份时，Azure 备份会将数据的内容从存储帐户复制到恢复服务保管库。

   ![将数据复制到恢复服务保管库](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

在进行到下一个计划备份时，Azure 备份通过初始备份副本执行增量备份。

## <a name="next-steps"></a>后续步骤

* 有关 Azure 导入/导出服务工作流的任何问题，请参阅[使用 Microsoft Azure 导入/导出服务将数据传输到 Blob 存储](../storage/common/storage-import-export-service.md)。
