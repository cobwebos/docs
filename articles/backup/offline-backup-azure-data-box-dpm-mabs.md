---
title: DPM 和 MABS Azure Data Box 的脱机备份
description: 你可以使用 Azure Data Box 将初始备份数据从 DPM 和 MABS 脱机。
ms.topic: conceptual
ms.date: 08/12/2020
ms.openlocfilehash: 2fd8a137abf8b76d1587894bfa3fe8447e0d646b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91271488"
---
# <a name="offline-seeding-using-azure-data-box-for-dpm-and-mabs-preview"></a>使用 DPM 和 MABS (预览版的 Azure Data Box 进行脱机种子设定) 

> [!NOTE]
> 此功能适用于 Data Protection Manager (DPM) 2019 UR2 及更高版本。<br><br>
> 此功能目前以预览版提供 Microsoft Azure 备份 Server (MABS) 。 如果对 Azure Data Box 使用 MABS 进行脱机种子设定感兴趣，请访问我们的 [systemcenterfeedback@microsoft.com](mailto:systemcenterfeedback@microsoft.com) 。

本文介绍如何使用 Azure Data Box 将初始备份数据从 DPM 和 MABS 脱机到 Azure 恢复服务保管库。

你可以使用 [Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) 使大量初始 DPM/MABS 备份脱机 (，而无需使用网络) 到恢复服务保管库。 此过程可节省通过高延迟网络联机移动大量备份数据所需的时间和网络带宽。 此功能目前处于预览状态。

与[基于 Azure 导入/导出服务的脱机备份](backup-azure-backup-server-import-export.md)相比，基于 Azure Data Box 的脱机备份具有两个明显优势：

- 无需购买你自己的与 Azure 兼容的磁盘和连接器。 Azure Data Box 随附与选定 [Data Box SKU](https://azure.microsoft.com/services/databox/data/) 关联的磁盘。

- Azure 备份（MARS 代理）可以直接将备份数据写入支持的 Azure Data Box SKU。 此功能使你无需为初始备份数据预配暂存位置。 也不需要使用实用工具来格式化数据并将其复制到磁盘上。

## <a name="supported-platforms"></a>支持的平台

支持的平台如下：

- Windows Server 2019 64 位（Standard、Datacenter、Essentials）
- Windows Server 2016 64 位（Standard、Datacenter、Essentials）

## <a name="backup-data-size-and-supported-data-box-skus"></a>备份数据大小和支持的 Data Box SKU

支持的 Data Box SKU 如下：

| 每台服务器按 MARS 压缩后 (的备份数据大小) \* | 支持的 Azure Data Box SKU |
| --- | --- |
| \<= 7.2 TB | [Azure Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview) |
| > 7.2 TB 且 <= 80 TB\*\* | [Azure Data Box (100 TB)](https://docs.microsoft.com/azure/databox/data-box-overview) |

\*典型压缩率在 10-20% 之间变化 <br>
\*\*如果希望单个数据源的初始备份数据超过 80 TB，请联系 [SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com)。

> [!IMPORTANT]
> 单个数据源的初始备份数据必须包含在单个 Azure Data Box 或 Azure Data Box 磁盘中，并且不能在相同或不同 Sku 的多个设备之间共享。 但是，Azure Data Box 可能会包含来自多个数据源的初始备份。

## <a name="before-you-begin"></a>在开始之前

DPM/MABS 上运行的 MARS 代理应升级到 [最新版本](https://aka.ms/azurebackup_agent))  (2.0.9171.0 或更高版本。

确保以下信息：

### <a name="azure-subscription-and-required-permissions"></a>Azure 订阅和所需权限

- 有效的 Azure 订阅。
- 打算执行脱机备份策略的用户必须是 Azure 订阅的所有者。
- 需要为其提供种子的 Data Box 作业和恢复服务保管库必须位于同一订阅中。
    > [!NOTE]
    > 建议目标存储帐户和恢复服务保管库位于同一区域。 但这并不是必需的。

### <a name="order-and-receive-the-data-box-device"></a>订购并接收 Data Box 设备

在触发脱机备份之前，请确保所需 Data Box 设备处于 "已 *交付* " 状态。 若要订购最符合你要求的 SKU，请参阅[备份数据大小和支持的 Data Box SKU](#backup-data-size-and-supported-data-box-skus)。 执行[本文](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-ordered)中的步骤，订购和接收 Data Box 设备。

> [!IMPORTANT]
> 不要为**帐户类型**选择*BlobStorage* 。 DPM/MABS 服务器需要一个支持页 Blob 的帐户，在选择 *BlobStorage* 时不支持该页 blob。 在为 Azure Data Box 作业创建目标存储帐户时，选择 "  **存储 v2 (常规用途 V2) ** 作为 **帐户类型** 。

![设置 Azure Data Box](./media/offline-backup-azure-data-box-dpm-mabs/setup-azure-databox.png)

## <a name="setup-azure-data-box-devices"></a>设置 Azure Data Box 设备

收到 Azure Data Box 设备后，根据你已订购的 Azure Data Box SKU，请执行以下相应部分中的步骤，设置并准备 DPM/MABS 服务器的 Data Box 设备，以识别并传输初始备份数据。

### <a name="setup-azure-data-box-disk"></a>设置 Azure Data Box Disk

如果订购了一个或多个 Azure Data Box Disk（每个最大 8 TB），请执行[此处](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-set-up)所述的步骤，打开包装、连接和解锁 Data Box Disk。

> [!NOTE]
> DPM/MABS 服务器可能没有 USB 端口。 在这种情况下，可以将 Azure Data Box Disk 连接到另一台服务器/客户端，并将设备的根目录作为网络共享目录公开。

## <a name="setup-azure-data-box"></a>设置 Azure Data Box

如果你订购的 Azure Data Box (高达 100 TB) ，请按照 [此处](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) 所述的步骤设置 Data Box。

### <a name="mount-your-azure-data-box-as-local-system"></a>将 Azure Data Box 作为本地系统装载

DPM/MABS 服务器在系统上下文中运行，因此需要向连接 Azure Data Box 的装入路径提供相同级别的特权。 按照以下步骤确保你可以使用 NFS 协议将 Data Box 设备装载为本地系统。

1. 启用 DPM/MABS 服务器上的 NFS 客户端功能。
指定备用源：WIM:D:\Sources\Install.wim:4
2. 将 **PSExec** 从下载 [https://download.sysinternals.com/files/PSTools.zip](https://download.sysinternals.com/files/PSTools.zip) 到 DPM/MABS 服务器。
3. 打开提升的命令提示符，并以包含 PSExec.exe 的目录作为当前目录执行以下命令。

   ```cmd
   psexec.exe  -s  -i  cmd.exe
   ```

4. 由于上述命令而打开的命令窗口位于本地系统上下文中。 使用此命令窗口执行相关步骤，以将 Azure 页 Blob 共享作为网络驱动器装载到 Windows Server。
5. 按照 [此处](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-nfs#connect-to-data-box) 的步骤，通过 NFS 将 DPM/MABS 服务器连接到 Data Box 设备，并在本地系统命令提示符下执行以下命令，以装载 Azure 页 blob 共享：

    ```cmd
    mount -o nolock \\<DeviceIPAddres>\<StorageAccountName_PageBlob X:
    ```

6. 装载后，检查是否可以从服务器访问 X:。 如果可以，请继续阅读本文的下一部分。

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>将初始备份数据传输到 Azure Data Box 设备

1. 在 DPM/MABS 服务器上，按照以下步骤 [创建新的保护组](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups)。 如果要将在线保护添加到现有保护组，请右键单击现有的保护组，然后选择 " **添加在线保护** " 和 "从 **步骤 8**开始"。
2. 在“选择组成员”页，指定要备份的计算机和源。
3. 在“选择数据保护方法”页，指定处理短期和长期备份的方式。 确保选择“我需要联机保护”。

   ![新建保护组](./media/offline-backup-azure-data-box-dpm-mabs/create-new-protection-group.png)

4. 在“选择短期目标”页，指定想要在磁盘上备份到短期存储的方式。
5. 在“检查磁盘分配”页，检查为保护组分配的存储池磁盘空间。
6. 在“选择副本创建方法”页，选择“通过网络自动创建”。
7. 在“选择一致性检查选项”页，选择要自动执行一致性检查的方式。
8. 在 " **指定联机保护数据** " 页上，选择要启用在线保护的成员。

    ![指定在线保护数据](./media/offline-backup-azure-data-box-dpm-mabs/specify-online-protection-data.png)

9. 在“指定联机备份计划”页，指定增量备份到 Azure 的频率。
10. 在“指定联机保留策略”页，指定从每天/每周/每月/每年备份中创建的恢复点在 Azure 中的保留方式。
11. 在向导的 " **选择联机复制** " 屏幕上，选择 " **使用 Microsoft 拥有的磁盘传输** " 选项，然后选择 " **下一步**"。

    ![选择初始联机复制](./media/offline-backup-azure-data-box-dpm-mabs/choose-initial-online-replication.png)

    >[!NOTE]
    > **使用 Microsoft 拥有的磁盘选择传输**的选项不适用于 MABS v3，因为该功能处于预览阶段。 [systemcenterfeedback@microsoft.com](mailto:systemcenterfeedback@microsoft.com)如果你想要对 MABS v3 使用此功能，请访问我们的。

12. 出现提示时，使用对 Azure 订阅具有所有者访问权限的用户凭据登录 Azure。 成功登录后，将显示以下屏幕：

    ![成功登录后](./media/offline-backup-azure-data-box-dpm-mabs/after-successful-login.png)

     DPM/MABS 服务器随后将获取订阅中处于已 *交付* 状态的 Data Box 作业。

     > [!NOTE]
     > 第一次登录时间比平常长。 Azure PowerShell 模块将在后台安装，并且还将注册 Azure AD 应用程序。
     >
     >  - 安装了以下 PowerShell 模块：<br>
          - AzureRM.Profile     5.8.3<br>
          - AzureRM.Resources   6.7.3<br>
          - AzureRM.Storage     5.2.0<br>
          - Azure.Storage       4.6.1<br>
     >  - Azure AD 应用程序注册为 AzureOfflineBackup_\<object GUID of the user>。

13. 选择已解包、连接并解锁 Data Box 磁盘的正确数据框顺序。 选择“**下一步**”。

    ![选择 Data Box](./media/offline-backup-azure-data-box-dpm-mabs/select-databox.png)

14. 在 " **检测 DataBox** " 屏幕上，输入 Data Box 设备的路径，然后选择 " **检测设备**"。

    ![输入网络路径](./media/offline-backup-azure-data-box-dpm-mabs/enter-network-path.png)

    > [!IMPORTANT]
    > 提供指向 Azure Data Box Disk 的根目录的网络路径。 此目录必须包含名为 PageBlob 的目录，如下所示：
    >
    > ![USB 驱动器](./media/offline-backup-azure-data-box-dpm-mabs/usb-drive.png)
    >
    > 例如，如果磁盘的路径为 `\\mydomain\myserver\disk1\` ，并且 *disk1* 包含一个名为 *PageBlob*的目录，则 DPM/MABS 服务器向导上提供的路径为 `\\mydomain\myserver\disk1\` 。
    > 如果[设置 Azure Data Box 100 TB 设备](https://docs.microsoft.com/azure/backup/offline-backup-azure-data-box#setup-azure-data-box)，请提供以下信息作为设备 `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` 的网络路径。

15. 选择“**下一步**”。 在 " **摘要** " 页上，检查设置并选择 " **创建组**"。

    ![检测 Data Box](./media/offline-backup-azure-data-box-dpm-mabs/detect-databox.png)

    以下屏幕确认已成功创建保护组。

    ![已创建保护组](./media/offline-backup-azure-data-box-dpm-mabs/protection-group-created.png)

16. 选择上面屏幕上的 " **关闭** "。

    这样，就会将数据的初始复制到 DPM/MABS 磁盘。 完成保护后，组状态将在 "**保护**" 页上显示 **"正常" 的 "** 保护状态"。

17. 若要启动脱机备份副本到 Azure Data Box 设备，请右键单击 **保护组**，然后选择 " **创建恢复点** " 选项。 然后，选择“联机保护”选项。

    ![创建恢复点](./media/offline-backup-azure-data-box-dpm-mabs/create-recovery-point.png)

    ![恢复点](./media/offline-backup-azure-data-box-dpm-mabs/recovery-point.png)

   DPM/MABS 服务器将开始备份 Azure Data Box 设备上选择的数据。 这可能需要几个小时到几天的时间，具体取决于数据的大小和 DPM/MABS 服务器与 Azure Data Box Disk 之间的连接速度。

   可以在“监视”窗格中监视作业的状态。 数据备份完成后，会看到一个类似于下面的屏幕：

   ![管理员控制台](./media/offline-backup-azure-data-box-dpm-mabs/administrator-console.png)

## <a name="post-backup-steps"></a>备份后步骤

将数据成功备份到 Azure Data Box Disk 后，请执行以下步骤。

- 请执行[本文](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-picked-up)中的步骤，将 Azure Data Box Disk 寄送到 Azure。 如果使用 Azure Data Box 100 TB 设备，请执行[这些步骤](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up)，将 Azure Data Box 寄送到 Azure。
- 在 Azure 门户中[监视 Data Box 作业](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-upload-verify)。 Azure Data Box 作业 *完成*后，DPM/MABS 服务器将在下一次计划的备份时自动将数据从存储帐户移动到恢复服务保管库。 如果成功创建了恢复点，它随后会将备份作业标记为“作业已完成”。

  > [!NOTE]
  > DPM/MABS 服务器将在创建保护组期间的计划时间触发备份。 但是，这些作业将标记“等待 Azure Data Box 作业完成”，直到作业完成为止。

- DPM/MABS 服务器成功创建了对应于初始备份的恢复点后，你可以删除存储帐户 (或与 Azure Data Box 作业关联) 特定内容。

## <a name="troubleshooting"></a>疑难解答

DPM 服务器上的 Microsoft Azure 备份 (MAB) 代理会在你的租户中为你创建 Azure AD 应用程序。 此应用程序需要一个证书，用于在配置脱机种子设定策略时创建和上载的身份验证。

我们使用 Azure PowerShell 创建证书并将证书上传到 Azure AD 应用程序。

### <a name="issue"></a>问题

配置脱机备份时，由于 Azure PowerShell cmdlet 中有已知的代码缺陷，因此无法将多个证书添加到 MAB 代理创建的同一个 Azure AD 应用程序。 如果为相同或不同的服务器配置了脱机种子设定策略，这将会影响你。

### <a name="verify-if-the-issue-is-caused-by-this-specific-root-cause"></a>验证问题是否由此特定根本原因导致

若要确保失败由上述[问题](#issue)引起，请执行以下步骤之一：

#### <a name="step-1"></a>步骤 1

检查在配置脱机备份时 DPM/MABS 控制台中是否显示以下错误消息：

![Azure 恢复服务代理](./media/offline-backup-azure-data-box-dpm-mabs/azure-recovery-services-agent.png)

#### <a name="step-2"></a>步骤 2

1. 打开安装路径中的 Temp 文件夹（默认临时文件夹路径为 C:\Program Files\Microsoft Azure Recovery Services Agent\Temp）。查找 CBUICurr 文件并打开该文件。
2. 在 CBUICurr 文件中，滚动到最后一行，并检查失败是否由“无法在客户帐户中创建 Azure AD 应用程序凭据。 异常：不允许更新为 KeyId 的现有凭据 \<some guid> "。

### <a name="workaround"></a>解决方法

若要解决此问题，请执行以下步骤，然后重试策略配置。

1. 登录到 Azure 登录页，该页面在 DPM/MABS 服务器 UI 上使用具有管理访问权限的帐户（该帐户在将创建导入导出作业的订阅上）显示。
2. 如果没有其他服务器配置了脱机种子设定并且没有其他服务器依赖于 `AzureOfflineBackup_<Azure User Id>` 应用程序，则从“Azure 门户”>“Azure Active Directory”>“应用程序注册”删除此应用程序。

   > [!NOTE]
   > 检查应用程序是否 `AzureOfflineBackup_<Azure User Id>` 未配置任何其他脱机种子设定，也没有其他服务器依赖于此应用程序。 请参阅 "公钥" 部分下的 " **设置" > 项** 。 不应添加任何其他 **公钥** 。 有关参考信息，请查看以下屏幕截图：
   >
   > ![公钥](./media/offline-backup-azure-data-box-dpm-mabs/public-keys.png)

#### <a name="step-3"></a>步骤 3

在你尝试配置脱机备份的 DPM/MABS 服务器上，执行以下操作：

1. 打开“管理计算机证书应用程序” > “个人”选项卡，然后查找名为 `CB_AzureADCertforOfflineSeeding_<ResourceId>` 的证书。
2. 选择上面的证书，右键单击 " **所有任务** " 并以 .Cer 格式 **导出** （不带私钥）。
3. 转到要点 2 中提到的 Azure 脱机备份应用程序。 在“设置” > “密钥” > “上传公钥”中，上传在上述步骤中导出的证书。

   ![上传公钥](./media/offline-backup-azure-data-box-dpm-mabs/upload-public-keys.png)

4. 在服务器中，在 "**运行**" 窗口中键入**regedit**打开注册表。
5. 转到注册表 Computer\HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider。 右键单击“CloudBackupProvider”，然后添加名为 `AzureADAppCertThumbprint_<Azure User Id>` 的新字符串值。

    >[!NOTE]
    > 若要获取 Azure 用户 ID，请执行以下操作之一：
    >
    >- 在已连接到 Azure 的 PowerShell 中运行 `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` 命令。
    > - 导航到名为“CurrentUserId”的注册表路径 `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup`。

6. 右键单击在上述步骤中添加的字符串，然后选择“修改”。 在 "值" 中，提供在 **点 2** 中导出的证书的指纹，并选择 **"确定"**。
7. 若要获取指纹的值，请双击证书，然后选择“详细信息”并向下滚动，直至看到“指纹”字段。 选择 " **指纹** " 并复制值。

   ![指纹值](./media/offline-backup-azure-data-box-dpm-mabs/certificate.png)

## <a name="next-steps"></a>后续步骤

- [使用自己的磁盘进行脱机种子设定（使用 Azure 导入/导出服务）](backup-azure-backup-server-import-export.md)
