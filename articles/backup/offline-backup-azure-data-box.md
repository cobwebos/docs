---
title: 使用 Azure Data Box 进行脱机备份
description: 了解如何使用 Azure Data Box 从 MARS 代理脱机将较大的初始备份数据植入 Azure 恢复服务保管库。
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 553939df8aa7a1b097b2cd6c7f29365302101324
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027025"
---
# <a name="azure-backup-offline-backup-using-azure-data-box"></a>Azure 备份脱机-使用 Azure Data Box 备份

你可以使用[Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-overview)服务在 Azure 恢复服务保管库中脱机（无需使用网络）的大型初始 MARS 备份。  这同时节省了时间和网络带宽，在这种情况下，将在高延迟网络中联机移动大量的备份数据。 此增强功能目前处于预览阶段。 基于 Azure Data Box 的脱机备份提供了两个不同于[Azure 导入/导出基于服务的脱机备份](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export)的优势。

1. 无需购买你自己的与 Azure 兼容的磁盘和连接器。 Azure Data Box service 附带与所选[DATA BOX SKU](https://azure.microsoft.com/services/databox/data/)关联的磁盘

2. Azure 备份（MARS 代理）可以将备份数据直接写入 Azure Data Box 支持的 Sku。 这样就无需为初始备份数据预配暂存位置，并且无需对实用程序进行格式化并将数据复制到磁盘。

## <a name="azure-data-box-with-mars-agent"></a>通过 MARS 代理 Azure Data Box

本文介绍如何使用 Azure Data Box 从 MARS 代理脱机将较大的初始备份数据植入 Azure 恢复服务保管库。 本文分为以下几个部分：

* 支持的平台
* 支持的 Data Box Sku
* 必备组件
* 设置 MARS 代理
* 设置 Azure Data Box
* 备份数据传输到 Azure Data Box
* 备份后步骤

## <a name="supported-platforms"></a>支持的平台

以下 Windows Sku 支持使用 Azure Data Box 从 MARS 代理播种数据的过程：

| **OS**                                 | **SKU**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **工作站**                        |                                                              |
| Windows 10 64 位                     | Enterprise、Pro、Home                                       |
| Windows 8.1 64 位                    | 企业版、专业版                                             |
| Windows 8 64 位                      | 企业版、专业版                                             |
| Windows 7 64 位                      | 旗舰版、企业版、专业版、家庭高级版、家庭普通版、初学者版 |
| **Server**                             |                                                              |
| Windows Server 2019 64 位            | Standard、Datacenter、Essentials                            |
| Windows Server 2016 64 位            | Standard、Datacenter、Essentials                            |
| Windows Server 2012 R2 64 位         | Standard、Datacenter、Foundation                            |
| Windows Server 2012 64 位            | Datacenter、Foundation、Standard                            |
| Windows Storage Server 2016 64 位    | 标准、工作组                                         |
| Windows Storage Server 2012 R2 64 位 | 标准版、工作组版、基本版                              |
| Windows Storage Server 2012 64 位    | 标准、工作组                                         |
| Windows Server 2008 R2 SP1 64 位     | Standard、Enterprise、Datacenter、Foundation                |
| Windows Server 2008 SP2 64 位        | 标准版、企业版、数据中心版                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>备份数据大小和支持的 Data Box Sku

| 备份数据大小（按 MARS 压缩后） * 每个服务器 | 支持的 Azure Data Box SKU                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| < = 7.2 TB                                                    | [Azure Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview) |
| > 7.2 TB 和 < = 80 TB * *                                      | [Azure Data Box （100 TB）](https://docs.microsoft.com/azure/databox/data-box-overview) |

\* 典型的压缩率在10-20% 之间有所不同 <br>
\* * 如果预计单个 MARS 服务器的初始备份数据超过 80 TB，请[AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) 。

>[!IMPORTANT]
>来自单个服务器的初始备份数据必须包含在单个 Azure Data Box 或 Azure Data Box Disk 中，并且不能在相同或不同 Sku 的多个设备之间共享。 但是，Azure Data Box 设备可能包含来自多个服务器的初始备份。

## <a name="pre-requisites"></a>必备组件

### <a name="azure-subscription-and-required-permissions"></a>Azure 订阅和所需权限

* 此过程需要 Azure 订阅
* 此过程要求指定执行脱机备份策略的用户是 Azure 订阅的 "所有者"
* Data Box 作业和恢复服务保管库（需要为数据提供种子）必须位于同一订阅中。
* 建议与 Azure Data Box 作业和恢复服务保管库关联的目标存储帐户位于同一区域。 但这并不是必需的。

### <a name="get-azure-powershell-370"></a>获取 Azure PowerShell 3.7。0

**这是此过程的最重要的先决条件**。 安装 Azure PowerShell （ver。 3.7.0），执行以下检查 * *

#### <a name="step-1-check-powershell-version"></a>步骤1：检查 PowerShell 版本

* 打开 Windows PowerShell 并运行以下命令：

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

* 如果输出显示的版本高于**3.7.0**，请执行下面的步骤2。 否则请跳到步骤 3。

#### <a name="step-2-uninstall-the-powershell-version"></a>步骤2：卸载 PowerShell 版本

通过执行以下操作来卸载最新版本的 PowerShell：

* 通过在 PowerShell 中运行以下命令来删除从属模块：

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

* 运行以下命令以确保成功删除所有依赖模块：

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>步骤3：安装 PowerShell 版本3.7。0

验证没有 AzureRM 模块后，请使用以下方法之一安装版本3.7.0：

* 从 GitHub[链接](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)

或者

* 在 PowerShell 窗口中运行以下命令：

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

也可以使用 msi 文件安装 Azure PowerShell。 若要删除它，请使用控制面板中的 "卸载程序" 选项卸载它。

### <a name="order-and-receive-the-data-box-device"></a>订购并接收 Data Box 设备

使用 MARS 和 Azure Data Box 的脱机备份过程要求在使用 MARS 代理触发脱机备份之前，所需 Data Box 设备处于 "已发送" 状态。 请参阅[备份数据大小和受支持的 Data Box sku](#backup-data-size-and-supported-data-box-skus) ，为你的要求订购最适合的 sku。 按照[本文](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-ordered)中的步骤订购并接收 Data Box 设备。

>[!IMPORTANT]
>不要为帐户类型选择 BlobStorage。 MARS 代理需要支持页 Blob 的帐户，在选择 BlobStorage 时不支持该页 Blob。 我们强烈建议在创建 Azure Data Box 作业的目标存储帐户时，选择*存储 V2* （*常规用途 V2*）作为帐户类型。

![在实例详细信息中选择帐户类型](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-setup-the-mars-agent"></a>安装和设置 MARS 代理

* 请确保卸载以前安装的任何 MARS 代理。

* 从[此处](https://aka.ms/azurebackup_agent)下载最新的 MARS 代理。

* 运行*marsagentinstaller.exe*并**仅**执行 * 将[代理安装和注册](https://docs.microsoft.com/azure/backup/backup-configure-vault#install-and-register-the-agent)到要存储备份的恢复服务保管库的步骤。

  >[!NOTE]
  > 恢复服务保管库必须与 Azure Data Box 作业位于同一订阅中。

* 将代理注册到恢复服务保管库后，请按照以下部分中的步骤进行操作。

## <a name="setup-azure-data-box-devices"></a>设置 Azure Data Box 设备

根据你已订购的 Azure Data Box SKU，请执行以下相应部分中所述的步骤，以便为 MARS 代理设置和准备 Data Box 设备，以识别并传输初始备份数据。

### <a name="setup-azure-data-box-disk"></a>设置 Azure Data Box Disk

如果为一个或多个 Azure Data Box 磁盘排序（每个磁盘最多 8 TB），请按照此处提到的步骤[解压缩、连接和解锁 Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-set-up)。

>[!NOTE]
>使用 MARS 代理的服务器可能没有 USB 端口。 在这种情况下，你可以将 Azure Data Box Disk 连接到其他服务器/客户端，并将设备的根目录公开为网络共享。

### <a name="setup-azure-data-box"></a>设置 Azure Data Box

如果你订购了 Azure Data Box （最多 100 TB），请按照此处提到的步骤[设置你的 Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up)。

#### <a name="mount-your-azure-data-box-as-local-system"></a>将 Azure Data Box 装载为本地系统

MARS 代理在本地系统上下文中运行，因此需要向连接 Azure Data Box 的装入路径提供相同级别的特权。 按照以下步骤确保你可以使用 NFS 协议将 Data Box 设备装载为本地系统：

* 启用 Windows Server 上的客户端 NFS 功能（安装了 MARS 代理）。<br>
  指定替代源： *WIM： D： \Sources\Install.wim： 4*

* 将 PSExec 从 <https://download.sysinternals.com/files/PSTools.zip> 下载到安装了 MARS 代理的服务器。

* 打开提升的命令提示符，并执行以下命令，并将包含 PSExec 的目录作为当前目录：

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

* 由于上述命令而打开的命令窗口位于本地系统上下文中。 使用此命令窗口执行将 Azure 页 Blob 共享装载为 Windows Server 上的网络驱动器的步骤。

* 请按照[此处](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-nfs#connect-to-data-box)的步骤将服务器与 MARS 代理连接到 Data Box 设备 via NFS，并在本地系统命令提示符下执行以下命令，以装载 Azure 页 blob 共享：

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

* 装入后，请检查是否可以从服务器访问 X：。 如果是，请继续阅读本文的下一部分。

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>将初始备份数据传输到 Azure Data Box 设备

* 打开服务器上的**Microsoft Azure 备份**应用程序。

* 单击 "**操作**" 窗格中的 "**计划备份**"。

    ![单击 "计划备份"](./media/offline-backup-azure-data-box/schedule-backup.png)

* 按照**计划备份向导**中的步骤进行操作

* **添加一些项**，使项的总大小在你订购并接收[的 Azure Data Box SKU 所支持的大小限制](#backup-data-size-and-supported-data-box-skus)范围内。

    ![添加要备份的项](./media/offline-backup-azure-data-box/add-items.png)

* 为文件和文件夹以及系统状态选择适当的备份计划和保留策略（系统状态仅适用于 Windows 服务器，不适用于 Windows 客户端）

* 在向导的 "**选择初始备份类型（文件和文件夹）** " 屏幕上，选择 "**使用 Microsoft Azure Data Box 磁盘传输**" 选项，然后单击 "**下一步**"

    ![选择初始备份类型](./media/offline-backup-azure-data-box/initial-backup-type.png)

* 当使用对 Azure 订阅拥有所有者访问权限的用户凭据进行提示时，登录到 Azure。 成功执行此操作后，会看到一个类似于下面的屏幕：

    ![创建资源和应用所需权限](./media/offline-backup-azure-data-box/creating-resources.png)

* 然后，MARS 代理将获取订阅中处于 "已传递" 状态的 Data Box 作业。

    ![正在获取订阅 ID 的 databox 作业](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

* 选择已解压缩、连接并解锁 Data Box 磁盘的正确数据框顺序。 单击“下一步”。

    ![选择 Data Box 顺序](./media/offline-backup-azure-data-box/select-databox-order.png)

* 单击 " **Data Box 设备检测**" 屏幕上的 "**检测设备**"。 这会使 MARS 代理扫描本地连接 Azure Data Box 磁盘并检测它们，如下所示：

    ![Data Box 设备检测](./media/offline-backup-azure-data-box/databox-device-detection.png)

    如果已将 Azure Data Box 连接为网络共享（因为 USB 端口不可用，或者你已订购并装载了 100 TB Data Box 设备），则在第一次检测将失败，但会为你选择输入 Data Box 设备的网络路径的选项，其形式为：下面是 hown：

    ![输入网络路径](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > 提供 Azure Data Box 磁盘的根目录的网络路径。 此目录必须包含名称为*PageBlob*的目录，如下所示：
    >
    >![Azure Data Box 磁盘的根目录](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >例如，如果磁盘的路径为 `\\mydomain\myserver\disk1\`，并且*disk1*包含一个名为*PageBlob*的目录，则在 MARS 代理向导上提供的路径将 `\\mydomain\myserver\disk1\`
    >
    >如果[设置 Azure Data Box 100 TB 设备](#setup-azure-data-box)，请提供以下项作为设备的网络路径 `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob`

* 单击 "**下一步**"，然后在下一个屏幕上单击 "**完成**"，使用 Azure Data Box 的脱机备份配置保存备份和保留策略。

* 以下屏幕确认已成功保存策略：

    ![已成功保存策略](./media/offline-backup-azure-data-box/policy-saved.png)

* 在上面的屏幕上单击 "**关闭**"。

* 单击 MARS 代理控制台的 "**操作**" 窗格中的 "**立即备份**"，然后在向导屏幕中单击 "**备份**"，如下所示：

    ![立即备份向导](./media/offline-backup-azure-data-box/backup-now.png)

* MARS 代理将开始备份 Azure Data Box 设备上选择的数据。 这可能需要几个小时到几天的时间，具体取决于文件的数量和服务器与 MARS 代理和 Azure Data Box Disk 之间的连接速度。

* 数据备份完成后，会在 MARS 代理上看到如下所示的屏幕：

    ![显示的备份进度](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>备份后步骤

本部分介绍在将数据备份到 Azure Data Box Disk 成功之后要执行的步骤。

* 按照本文中的步骤将[Azure Data Box 磁盘寄送到 Azure](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-picked-up)。 如果你使用的是 Azure Data Box 100 TB 设备，请按照以下步骤将[Azure Data Box 寄送给 Azure](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up)。

* [监视 Azure 门户中的 Data Box 作业](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-upload-verify)。 Azure Data Box 作业完成后，MARS 代理会在下一次计划的备份时自动将数据从存储帐户移动到恢复服务保管库。 如果成功创建恢复点，则它会将备份作业标记为 "作业已完成"。

    >[!NOTE]
    >MARS 代理将在创建策略期间按计划触发备份。 但是，在作业完成之前，这些作业将标记为 "正在等待 Azure Data Box 作业完成"。

* MARS 代理成功创建了与初始备份相对应的恢复点后，你可以删除与 Azure Data Box 作业关联的存储帐户（或特定内容）。

## <a name="troubleshooting"></a>故障排除

Microsoft Azure 备份（MAB）代理在租户中为你创建 Azure AD 应用程序。 此应用程序需要一个证书，该证书用于在配置脱机种子设定策略时创建和上载的身份验证。 我们使用 Azure PowerShell 来创建证书并将其上传到 Azure AD 应用程序。

### <a name="issue"></a>问题

在配置脱机备份时，你可能会遇到问题，因为 Azure PowerShell cmdlet 中的 bug，你无法将多个证书添加到 MAB 代理创建的同一个 Azure AD 应用程序。 如果为相同或不同的服务器配置了脱机种子设定策略，这将会影响你。

### <a name="how-to-verify-if-the-issue-is-caused-by-this-specific-root-cause"></a>如何验证问题是否是由特定的根本原因导致的

若要确保故障是由于上述问题导致的，请执行以下步骤之一：

#### <a name="step-1"></a>步骤 1

当配置脱机备份时，请检查 MAB 控制台中是否显示以下错误消息：

![无法为当前 Azure 帐户创建脱机备份策略](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2"></a>步骤 2

* 打开安装路径中的**临时**文件夹（默认临时文件夹路径为 " *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp*"）。 查找**CBUICurr**文件并打开文件。

* 在**CBUICurr**文件中滚动到最后一行，并检查故障是否是由 `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed`造成的。

### <a name="workaround"></a>解决方法

若要解决此问题，请执行以下步骤，然后重试该策略配置。

#### <a name="first-step"></a>第一步

使用在将创建导入导出作业的订阅上具有管理员访问权限的其他帐户登录到 MAB UI 上的 PowerShell。

#### <a name="second-step"></a>第二步

如果没有其他服务器配置了脱机种子设定，而且没有其他服务器依赖于 `AzureOfflineBackup_<Azure User Id>` 应用程序，请从**Azure 门户**中删除此应用程序 > **Azure Active Directory** > **应用注册。**

>[!NOTE]
> 检查应用程序 `AzureOfflineBackup_<Azure User Id>` 是否未配置任何其他脱机种子设定，也没有其他服务器依赖于此应用程序。 请参阅 "**公钥**" 部分下的 "**设置**" > **项**，它不应添加任何其他公钥。 有关参考，请参阅以下屏幕截图：
>
>![公钥](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="third-step"></a>第三步

在尝试配置脱机备份的服务器上，执行以下操作：

1. 打开 "**管理计算机证书应用程序** > **个人**" 选项卡，并查找名为的证书 `CB_AzureADCertforOfflineSeeding_<ResourceId>`

2. 选择上述证书，右键单击 "**所有任务**" 并以 .Cer 格式**导出**（不带私钥）。

3. 请参阅**第2点**中提到的 Azure 脱机备份应用程序。 在 "**设置**" > "**密钥**" > **上传公钥，** 上传上述步骤中导出的证书。

    ![上传公钥](./media/offline-backup-azure-data-box/upload-public-key.png)

4. 在服务器中，在 "运行" 窗口中键入**regedit**打开注册表。

5. 请参阅注册表*计算机 \ HKEY_LOCAL_MACHINE \Software\microsoft\windows Azure Backup\Config\CloudBackupProvider.* 右键单击**CloudBackupProvider** ，然后添加名为 `AzureADAppCertThumbprint_<Azure User Id>` 的新字符串值

    >[!NOTE]
    > 若要获取 Azure 用户 Id，请执行以下操作之一：
    >
    >1. 在 Azure 连接的 PowerShell 中运行 `Get-AzureRmADUser -UserPrincipalName “Account Holder’s email as defined in the portal”` 命令。
    > 2. 导航到注册表路径： *Computer \ HKEY_LOCAL_MACHINE \Software\microsoft\windows Azure Backup\DbgSettings\OnlineBackup*;名称： *CurrentUserId*

6. 右键单击在上述步骤中添加的字符串，然后选择 "**修改**"。 在 "值" 中，提供在**点 2**中导出的证书的指纹，并单击 **"确定"** 。

7. 若要获取指纹值，请双击该证书，然后选择 "**详细信息**" 选项卡并向下滚动，直到看到 "指纹" 字段。 单击 "**指纹**" 并复制值。

    ![证书的指纹字段](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>问题

如有任何问题或说明，有关遇到的任何问题，请联系[AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)
