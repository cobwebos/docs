---
title: 脱机备份 Data Protection Manager (DPM) 和 Microsoft Azure 备份 Server (MABS) -早期版本
description: 借助 Azure 备份，可以使用 Azure 导入/导出服务在网外发送数据。 本文介绍以前版本的 DPM 和 Azure 备份服务器的脱机备份工作流。
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: b747fd3c682dc1caf7312ba7279470a1e6b38bd5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88890087"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server-previous-versions"></a>DPM 和 Azure 备份服务器 (早期版本的脱机备份工作流) 

>[!IMPORTANT]
>这些步骤适用于 DPM 2019 RTM 及更早版本以及 MABS v3 RTM 和更早版本。

Azure 备份有多个可提升效率的内置功能，可在将数据初始完整备份到 Azure 期间节省网络和存储成本。 初始完整备份通常会传输大量数据，且需要较多网络带宽，相比之下，后续备份只传输差异/增量部分。 Azure 备份会压缩初始备份。 通过脱机种子设定过程，Azure 备份可以使用磁盘将压缩后的初始备份数据脱机上传到 Azure。

Azure 备份的脱机种子设定过程与 [Azure 导入/导出服务](../storage/common/storage-import-export-service.md)紧密集成。 可以借助此服务使用磁盘将数据传输到 Azure。 如果要通过高延迟、低带宽网络传输 TB 计的初始备份数据，可以使用脱机种子设定工作流将一个或多个硬盘驱动器中的初始备份副本传送到 Azure 数据中心。 本文将会概述如何对 System Center Data Protection Manager (DPM) 和 Azure 备份服务器 (MABS) 完成此工作流，并提供其他相关步骤。

> [!NOTE]
> Microsoft Azure 恢复服务 (MARS) 代理的脱机备份过程不同于 DPM 和 MABS。 有关使用 MARS 代理进行脱机备份的信息，请参阅 [Azure 备份中的脱机备份工作流](backup-azure-backup-import-export.md)。 使用 Azure 备份代理完成的系统状态备份不支持脱机备份。
>

## <a name="overview"></a>概述

借助 Azure 备份的脱机种子设定功能及 Azure 导入/导出服务，可以方便地通过磁盘将数据脱机上传到 Azure。 脱机备份过程涉及以下步骤：

> [!div class="checklist"]
>
> * 备份数据不会通过网络发送，而是写入到暂存位置。
> * 然后使用 *AzureOfflineBackupDiskPrep* 实用工具将暂存位置上的数据写入到一个或多个 SATA 磁盘。
> * 该实用工具会自动创建 Azure 导入作业。
> * 然后将 SATA 驱动器发送到最近的 Azure 数据中心。
> * 将备份数据上传到 Azure 之后，Azure 备份将备份数据复制到备份保管库，并计划增量备份。

## <a name="supported-configurations"></a>支持的配置

将数据备份到 Microsoft 云的 Azure 备份的所有部署模型都支持脱机备份。 这些模型包括：

> [!div class="checklist"]
>
> * 使用 MARS 代理或 Azure 备份代理备份文件和文件夹。
> * 使用 DPM 备份所有工作负荷和文件。
> * 使用 MABS 备份所有工作负荷和文件。

>[!NOTE]
>Azure CSP 订阅不支持用于 DPM 2019 RTM 和更早版本的脱机种子设定以及 MABS v3 RTM 和更早版本。 仍支持通过网络进行联机备份。

## <a name="prerequisites"></a>先决条件

确保在启动脱机备份工作流之前满足以下先决条件：

* 已创建[恢复服务保管库](backup-azure-recovery-services-vault-overview.md)。 若要创建保管库，请按照[创建恢复服务保管库](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)中的步骤操作
* Windows Server 或 Windows 客户端上已安装 Azure 备份代理、MABS 或 DPM（若适用），并且计算机已注册到恢复服务保管库。 确保仅使用[最新版本的 Azure 备份](https://go.microsoft.com/fwlink/?linkid=229525)。
* 在你打算从中备份数据的计算机上，[下载 Azure 发布设置文件](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade)。 下载发布设置文件的订阅可以不同于包含恢复服务保管库的订阅。 如果你的订阅位于主权 Azure 云上，请相应地使用以下链接下载 Azure 发布设置文件。

    | 主权云区域 | Azure 发布设置文件链接 |
    | --- | --- |
    | 美国 | [链接](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade)。 |
    | 中国 | [链接](https://portal.azure.cn/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade)。 |

* 在下载发布设置文件的订阅中已创建了采用资源管理器部署模型的 Azure 存储帐户。 在存储帐户中，创建一个新的 blob 容器，该容器将用作目标。

  ![创建采用资源管理器开发模式的存储帐户](./media/offline-backup-dpm-mabs-previous-versions/storage-account-resource-manager.png)

* 创建了一个暂存位置，它可以是计算机上的网络共享或任何其他内部或外部驱动器，并且有足够的磁盘空间来保存初始副本。 例如，若要备份 500 GB 文件服务器，请确保暂存区域至少有 500 GB 空间。 （由于压缩，实际使用量更少）。
* 对于发送到 Azure 的磁盘，请确保仅使用 2.5 英寸 SSD 或 2.5 英寸或 3.5 英寸 SATA II/III 内部硬盘驱动器。 可以使用容量最高为 10 TB 的硬盘驱动器。 查看 [Azure 导入/导出服务文档](../storage/common/storage-import-export-requirements.md#supported-hardware)，了解服务支持的最新驱动器集。
* SATA 驱动器必须连接到一台计算机（称为“副本计算机”），将在这台计算机上完成将备份数据从暂存位置复制到 SATA 驱动器的过程。 确保已在副本计算机上启用 BitLocker。

## <a name="prepare-the-server-for-the-offline-backup-process"></a>为脱机备份过程准备服务器

>[!NOTE]
> 如果你找不到列出的实用程序（如 *AzureOfflineBackupCertGen.exe*），则在安装 MARS 代理的过程中，将写入以 AskAzureBackupTeam@microsoft.com 获取对它们的访问权限。

* 在服务器上打开权限提升的命令提示符，并运行以下命令：

    ```cmd
    AzureOfflineBackupCertGen.exe CreateNewApplication SubscriptionId:<Subs ID>
    ```

    如果不存在 Azure 脱机备份 Active Directory 应用程序，该工具会创建一个。

    如果已存在所需的应用程序，此可执行文件会要求你手动将证书上传到租户中的应用程序。 遵循[此部分](#manually-upload-an-offline-backup-certificate)中的步骤将证书手动上传到应用程序。

* *AzureOfflineBackupCertGen.exe*工具生成*OfflineApplicationParams.xml*文件。 使用 MABS 或 DPM 将此文件复制到服务器。
* 在 DPM 实例或 Azure 备份服务器上安装[最新的 MARS 代理](https://aka.ms/azurebackup_agent)。
* 将服务器注册到 Azure。
* 运行以下命令：

    ```cmd
    AzureOfflineBackupCertGen.exe AddRegistryEntries SubscriptionId:<subscriptionid> xmlfilepath:<path of the OfflineApplicationParams.xml file>  storageaccountname:<storageaccountname to be used for offline backup>
    ```

* 上述命令将创建文件 `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch\MicrosoftBackupProvider\OfflineApplicationParams_<Storageaccountname>.xml`。

## <a name="manually-upload-an-offline-backup-certificate"></a>手动上传脱机备份证书

遵循以下步骤将脱机备份证书手动上传到前面创建的用于脱机备份的 Azure Active Directory 应用程序。

1. 登录到 Azure 门户。
1. 转到“Azure Active Directory” > “应用注册”。 
1. 在“拥有的应用程序”选项卡，找到显示名称格式为 `AzureOfflineBackup _<Azure User Id` 的应用程序。

    ![在“拥有的应用程序”选项卡上找到应用程序](./media/offline-backup-dpm-mabs-previous-versions/owned-applications.png)

1. 选择应用程序。 在左侧窗格中的“管理”下，转到“证书和机密”。 
1. 检查预先存在的证书或公钥。 如果没有证书或公钥，可以选择应用程序“概述”页上的“删除”按钮安全删除该应用程序。  然后，可以重试[为脱机备份准备服务器](#prepare-the-server-for-the-offline-backup-process)过程中的步骤，并跳过后面的步骤。 否则，请在要配置脱机备份的 DPM 实例或 Azure 备份服务器服务器中继续执行以下步骤。
1. 从 " **开始** " – " **运行**"，键入 *certlm.msc*。 在 "**证书-本地计算机**" 窗口中，选择 "**证书–本地计算机**  >  **个人**" 选项卡。查找名称为的证书 `CB_AzureADCertforOfflineSeeding_<ResourceId>` 。
1. 选择该证书，右键单击“所有任务”并选择“导出”，以导出 .cer 格式的不包含私钥的证书。 
1. 在 Azure 门户中转到 Azure 脱机备份应用程序。
1. 选择“管理” > “证书和机密” > “上传证书”。   上传在上一步骤中导出的证书。

    ![上传证书](./media/offline-backup-dpm-mabs-previous-versions/upload-certificate.png)

1. 在服务器上的“运行”窗口中，输入 **regedit** 打开注册表。
1. 转到注册表项 *Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider*。
1. 右键单击“CloudBackupProvider”并添加名称为 `AzureADAppCertThumbprint_<Azure User Id>` 的新字符串值。

    >[!NOTE]
    > 若要查找 Azure 用户 ID，请执行以下步骤之一：
    >
    >* 在已连接到 Azure 的 PowerShell 中运行 `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as appears in the portal"` 命令。
    >* 转到注册表路径 `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup; Name: CurrentUserId;`。

1. 右键单击在上一步骤中添加的字符串并选择“修改”。 在“值”中，提供在步骤 7 中导出的证书的指纹。 然后选择“确定”。
1. 若要获取指纹值，请双击该证书。 选择“详细信息”选项卡，并向下滚动，直到看到指纹字段。 选择“指纹”并复制其值。

    ![复制指纹字段中的值](./media/offline-backup-dpm-mabs-previous-versions/thumbprint-field.png)

1. 转到[工作流](#workflow)部分继续执行脱机备份过程。

## <a name="workflow"></a>工作流

本部分中的信息可帮助你完成脱机备份工作流，将数据传递到 Azure 数据中心，并上传到 Azure 存储。 如果遇到有关导入服务或任何过程方面的问题，请参阅上面提到的[导入服务概述文档](../storage/common/storage-import-export-service.md)。

### <a name="initiate-offline-backup"></a>启动脱机备份

1. 计划备份时，会看到 Windows Server、Windows 客户端或 DPM 中的以下页面。

    ![导入页面](./media/offline-backup-dpm-mabs-previous-versions/offline-backup-screen-inputs.png)

    下面是 DPM 中的相应页面。 <br/>

    ![DPM 和 Azure 备份服务器导入页面](./media/offline-backup-dpm-mabs-previous-versions/dpm-offline.png)

    要填写的框包括：

   * **暂存位置**：初始备份副本写入到的临时存储位置。 暂存位置可以是网络共享或本地计算机。 如果副本计算机与源计算机不同，请指定暂存位置的完整网络路径。
   * **Azure 发布设置**：发布设置文件的本地路径。
   * **Azure 导入作业名称**：Azure 导入/导出服务和 Azure 备份在跟踪磁盘上发送到 Azure 的数据的传输活动时使用的唯一名称。
   * **Azure 订阅 ID**：下载 Azure 发布设置文件的订阅的 Azure 订阅 ID。
   * **Azure 存储帐户**：Azure 订阅中与 Azure 发布设置文件关联的存储帐户的名称。
   * **Azure 存储容器**：Azure 存储帐户中导入备份数据的目标存储 Blob 的名称。

   保存提供的“暂存位置”和“Azure 导入作业名称”信息。  准备磁盘时需要用到这些信息。

1. 完成工作流。 若要启动脱机备份复制，请在 Azure 备份代理管理控制台中选择“立即备份”。 初始备份写入到临时区域作为此步骤的一部分。

    ![立即备份](./media/offline-backup-dpm-mabs-previous-versions/backup-now.png)

    若要在 DPM 或 Azure 备份服务器中完成相应的工作流，请右键单击“保护组”。 选择“创建恢复点”选项。 然后选择“联机保护”选项。

    ![DPM 和 MABS - 立即备份](./media/offline-backup-dpm-mabs-previous-versions/dpm-backup-now.png)

    操作完成后，暂存位置已准备就绪，可用于准备磁盘。

    ![备份进度](./media/offline-backup-dpm-mabs-previous-versions/op-backup-now.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>准备 SATA 驱动器并寄送到 Azure

*AzureOfflineBackupDiskPrep* 实用工具用于准备要寄送到最近的 Azure 数据中心的 SATA 驱动器。 在以下路径的恢复服务代理安装目录中可获得此实用工具：

`*\\Microsoft Azure Recovery Services Agent\Utils\*`

1. 转到该目录，将“AzureOfflineBackupDiskPrep”目录复制到 SATA 驱动器准备连接的副本计算机上。 请确保：

   * 副本计算机可以使用“启动脱机备份”部分的工作流中提供的相同网络路径，来访问脱机种子设定工作流的暂存位置。
   * 已在副本计算机上启用 BitLocker。
   * 副本计算机可以访问 Azure 门户。 必要时，副本计算机可与源计算机相同。

     > [!IMPORTANT]
     > 如果源计算机是虚拟机，则它必须使用其他物理服务器或客户端计算机作为副本计算机。

1. 使用 *AzureOfflineBackupDiskPrep* 实用工具目录作为当前目录，在副本计算机上打开权限提升的命令提示符。 运行以下命令：

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | 参数 | 说明 |
    | --- | --- |
    | s:&lt;*Staging Location Path*&gt; |此项必需的输入用于提供在“启动脱机备份”部分的工作流中所输入的暂存位置的路径。 |
    | p:&lt;*Path to PublishSettingsFile*&gt; |此可选输入用于提供在“启动脱机备份”部分的工作流中所输入的 Azure 发布设置文件的路径。 |

    > [!NOTE]
    > 复制计算机与源计算机不同时，&lt;AzurePublishSettingFile 的路径&gt; 值是必需的。
    >
    >

    运行该命令时，该实用工具将请求选择需要准备的驱动器对应的 Azure 导入作业。 如果只有一个与提供的暂存位置关联的导入作业，会显示如下所示的页面。

    ![Azure 磁盘准备工具输入](./media/offline-backup-dpm-mabs-previous-versions/azure-disk-preparation-tool-drive-input.png) <br/>

1. 输入想要准备传输到 Azure 的已装载磁盘的驱动器号（不要包含尾部的冒号）。 根据提示确认格式化驱动器。

    该工具随后便开始准备磁盘和复制备份数据。 在该工具提示你附加更多磁盘时，你可能需要附加更多磁盘（如果提供的磁盘没有足够空间来容纳备份数据）。 <br/>

    工具成功完成后，提供的一个或多个磁盘便已准备就绪，可寄送到 Azure。 此外，还会在 Azure 中创建一个导入作业，其名称是在运行“启动脱机备份”部分中的工作流期间提供的。 最后，工具上还显示磁盘所要寄送到的 Azure 数据中心的寄送地址。

    ![Azure 磁盘准备已完成](./media/offline-backup-dpm-mabs-previous-versions/azure-disk-preparation-tool-success.png)<br/>

1. 命令执行完成后，还会看到用于更新发货信息的选项。

    ![更新发货信息的选项](./media/offline-backup-dpm-mabs-previous-versions/update-shipping-utility.png)<br/>

1. 可以立即输入详细信息。 该工具将引导你完成涉及一系列输入的过程。 如果尚未获得跟踪号或其他与发货相关的详细信息，可以结束会话。 这篇文章介绍了随后更新寄送详情的操作步骤。

1. 将磁盘寄送到工具提供的地址。 保留跟踪号供将来参考。

   > [!IMPORTANT]
   > 不能有两个 Azure 导入作业使用同一个跟踪号。 确保使用一个包裹寄送实用工具在单次 Azure 导入作业中准备的驱动器，该包裹有一个唯一的跟踪号。 请勿在一个包裹中混合不同 Azure 导入作业中准备的驱动器。

1. 如果已获得跟踪号信息，请转到正在等待导入作业完成的源计算机。 使用 *AzureOfflineBackupDiskPrep* 实用工具目录作为当前目录，在权限提升的命令提示符下运行以下命令。

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   （可选）使用 *AzureOfflineBackupDiskPrep* 实用工具目录作为当前目录，从另一台计算机（例如副本计算机）运行以下命令。

   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | 参数 | 说明 |
    | --- | --- |
    | u: | 此项必需的输入用于更新 Azure 导入作业的发货详细信息。 |
    | s:&lt;*Staging Location Path*&gt; | 不在源计算机上运行命令时，将使用此项必需的输入。 此项输入用于提供在“启动脱机备份”部分的工作流中所输入的暂存位置的路径。 |
    | p:&lt;*Path to PublishSettingsFile*&gt; | 不在源计算机上运行命令时，将使用此项必需的输入。 此项输入用于提供在“启动脱机备份”部分的工作流中所输入的 Azure 发布设置文件的路径。 |

    该实用工具会自动检测到源计算机正在等待的导入作业，或在另一台计算机上运行命令时，与暂存位置相关的导入作业。 随后它通过一系列输入提供用于更新发货信息的选项。

    ![输入发货信息](./media/offline-backup-dpm-mabs-previous-versions/shipping-inputs.png)<br/>

1. 提供所有的输入后，请仔细检查详细信息，并输入 **yes** 提交你提供的发货信息。

    ![检查发货信息](./media/offline-backup-dpm-mabs-previous-versions/review-shipping-information.png)<br/>

1. 成功更新发货信息后，该实用工具将提供输入的发货详细信息所存储到的本地位置。

    ![存储发货信息](./media/offline-backup-dpm-mabs-previous-versions/storing-shipping-information.png)<br/>

   > [!IMPORTANT]
   > 确保驱动器在使用 *AzureOfflineBackupDiskPrep* 实用工具提供发货信息后两周内送达 Azure 数据中心。 如果不能做到，可能会导致驱动器得不到处理。

完成上述步骤后，Azure 数据中心已准备好接收驱动器并作进一步处理，将备份数据从驱动器传输到创建的经典型 Azure 存储帐户。

### <a name="time-to-process-the-drives"></a>处理驱动器的时间

处理 Azure 导入作业的时间是不确定的。 处理时间取决于发货时间、作业类型、要复制的数据的类型和大小，以及所提供磁盘的大小等因素。 Azure 导入/导出服务不附带 SLA。 收到磁盘之后，该服务力求在 7 到 10 天内完成将备份数据复制到 Azure 存储帐户的过程。 下一部分将介绍如何监视 Azure 导入作业的状态。

### <a name="monitor-azure-import-job-status"></a>监视 Azure 导入作业状态

驱动器在运输过程中，或者已抵达 Azure 数据中心等待复制到存储帐户时，Azure 备份代理、DPM 或源计算机上的 MABS 控制台将显示计划备份的以下作业状态：

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

若要检查导入作业状态：

1. 在源计算机上打开权限提升的命令提示符，并运行以下命令：

     `AzureOfflineBackupDiskPrep.exe u:`

1. 输出显示导入作业的当前状态。

    ![检查导入作业状态](./media/offline-backup-dpm-mabs-previous-versions/import-job-status-reporting.png)<br/>

有关 Azure 导入作业的各种状态的详细信息，请参阅[查看 Azure 导入/导出作业的状态](../storage/common/storage-import-export-view-drive-status.md)。

### <a name="finish-the-workflow"></a>完成工作流

导入作业完成后，存储帐户中的初始备份数据可供使用。 下一次运行计划的备份时，Azure 备份会将数据内容从存储帐户复制到恢复服务保管库。

   ![将数据复制到恢复服务保管库](./media/offline-backup-dpm-mabs-previous-versions/copying-from-storage-account-to-azure-backup.png)<br/>

在进行到下一个计划备份时，Azure 备份通过初始备份副本执行增量备份。

## <a name="next-steps"></a>后续步骤

* 如果遇到任何有关 Azure 导入/导出服务工作流的问题，请参阅[使用 Microsoft Azure 导入/导出服务将数据传输到 Blob 存储](../storage/common/storage-import-export-service.md)。
