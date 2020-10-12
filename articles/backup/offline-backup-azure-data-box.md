---
title: 使用 Azure Data Box 进行脱机备份
description: 了解如何使用 Azure Data Box 以脱机方式将较大初始备份数据从 MARS 代理植入到恢复服务保管库。
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 5a4aeebeddcca4adcac511c7c225c8809dd29c93
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89180926"
---
# <a name="azure-backup-offline-backup-by-using-azure-data-box"></a>使用 Azure Data Box 进行 Azure 备份脱机备份

你可以使用 [Azure Data Box](../databox/data-box-overview.md) 将大型初始 MICROSOFT AZURE 恢复服务 (MARS) 脱机备份，而无需将网络 (用于恢复服务保管库。 此过程可节省通过高延迟网络联机移动大量备份数据所需的时间和网络带宽。 此增强功能目前为预览版。 与[基于 Azure 导入/导出服务的脱机备份](./backup-azure-backup-import-export.md)相比，基于 Azure Data Box 的脱机备份具有两个明显优势：

- 无需购买你自己的与 Azure 兼容的磁盘和连接器。 Azure Data Box 随附与选定 [Data Box SKU](https://azure.microsoft.com/services/databox/data/) 关联的磁盘。
- Azure 备份（MARS 代理）可以直接将备份数据写入支持的 Azure Data Box SKU。 此功能使你无需为初始备份数据预配暂存位置。 也不需要使用实用工具来格式化数据并将其复制到磁盘上。

## <a name="azure-data-box-with-the-mars-agent"></a>将 Azure Data Box 与 MARS 代理配合使用

本文介绍如何使用 Azure Data Box 以脱机方式将较大初始备份数据从 MARS 代理植入到恢复服务保管库。

## <a name="supported-platforms"></a>支持的平台

以下 Windows SKU 支持通过 Azure Data Box 从 MARS 代理植入数据的过程。

| **OS**                                 | **SKU**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **工作站**                        |                                                              |
| Windows 10 64 位                     | Enterprise、Pro、Home                                       |
| Windows 8.1 64 位                    | Enterprise、Pro                                             |
| Windows 8 64 位                      | Enterprise、Pro                                             |
| Windows 7 64 位                      | Ultimate、Enterprise、Professional、Home Premium、Home Basic、Starter |
| **Server**                             |                                                              |
| Windows Server 2019 64 位            | Standard、Datacenter、Essentials                            |
| Windows Server 2016 64 位            | Standard、Datacenter、Essentials                            |
| Windows Server 2012 R2 64 位         | Standard、Datacenter、Foundation                            |
| Windows Server 2012 64 位            | Datacenter、Foundation、Standard                            |
| Windows Storage Server 2016 64 位    | Standard、Workgroup                                         |
| Windows Storage Server 2012 R2 64 位 | Standard、Workgroup、Essential                              |
| Windows Storage Server 2012 64 位    | Standard、Workgroup                                         |
| Windows Server 2008 R2 SP1 64 位     | Standard、Enterprise、Datacenter、Foundation                |
| Windows Server 2008 SP2 64 位        | Standard、Enterprise、Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>备份数据大小和支持的 Data Box SKU

| 每个服务器的备份数据大小（通过 MARS 压缩后）* | 支持的 Azure Data Box SKU                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <=7.2 TB                                                    | [Azure Data Box Disk](../databox/data-box-disk-overview.md) |
| >7.2 TB 和 <= 80 TB * *                                      | [Azure Data Box (100 TB)](../databox/data-box-overview.md) |

*典型的压缩率在 10% 到 20% 之间变化。 <br>
* * 如果你预计单个 MARS 服务器的初始备份数据超过 80 TB，请联系 [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) 。

>[!IMPORTANT]
>单个服务器的初始备份数据必须包含在单个 Azure Data Box 实例或 Azure Data Box 磁盘中，不能在相同或不同 SKU 的多个设备之间共享。 但 Azure Data Box 设备可以包含来自多个服务器的初始备份。

## <a name="prerequisites"></a>先决条件

### <a name="azure-subscription-and-required-permissions"></a>Azure 订阅和所需权限

- 此过程需要 Azure 订阅。
- 此过程要求被指定执行脱机备份策略的用户是 Azure 订阅的所有者。
- Data Box 作业和恢复服务保管库（需将数据植入到的位置）必须位于同一订阅中。
- 建议目标存储帐户（与 Azure Data Box 作业关联）和恢复服务保管库位于同一区域。 但这不是必需的。

### <a name="get-azure-powershell-370"></a>获取 Azure PowerShell 3.7.0

这是此过程最重要的先决条件。 在安装 Azure PowerShell 版本 3.7.0 之前，请执行以下检查。

#### <a name="step-1-check-the-powershell-version"></a>步骤 1：检查 PowerShell 版本

1. 打开 Windows PowerShell，运行以下命令：

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

1. 如果输出显示的版本高于 3.7.0，则执行“步骤 2”。 否则，请跳至“步骤 3”。

#### <a name="step-2-uninstall-the-powershell-version"></a>步骤 2：卸载 PowerShell 版本

卸载 PowerShell 的当前版本。

1. 通过在 PowerShell 中运行以下命令来删除依赖模块：

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

2. 若要确保成功删除所有依赖模块，请运行以下命令：

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>步骤 3：安装 PowerShell 版本 3.7.0

确认任何 AzureRM 模块都不存在之后，请使用以下方法之一安装版本 3.7.0：

- 从 GitHub 中使用[此链接](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)。

或者可以：

- 在 PowerShell 窗口中运行以下命令：

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

也可以使用 msi 文件安装 Azure PowerShell。 若要删除它，请使用控制面板中的“卸载程序”选项将其卸载。

### <a name="order-and-receive-the-data-box-device"></a>订购并接收 Data Box 设备

使用 MARS 和 Azure Data Box 的脱机备份过程要求 Data Box 设备在你使用 MARS 代理触发脱机备份之前处于“已交货”状态。 若要订购最符合要求的 SKU，请参阅[备份数据大小和支持的 Data Box SKU](#backup-data-size-and-supported-data-box-skus)。 按[教程：订购 Azure Data Box 磁盘](../databox/data-box-disk-deploy-ordered.md)中的步骤订购和接收 Data Box 设备。

> [!IMPORTANT]
> 请勿选择“BlobStorage”作为“帐户类型”。 MARS 代理需要一个支持页 blob 的帐户，而在选择 BlobStorage 的情况下，此需求无法满足。 为 Azure Data Box 作业创建目标存储帐户时，请选择“存储 V2 (常规用途 V2)”作为“帐户类型”。 

![在实例详细信息中选择帐户类型](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-set-up-the-mars-agent"></a>安装和设置 MARS 代理

1. 确保卸载以前安装的任何 MARS 代理。
1. 从[此网站](https://aka.ms/azurebackup_agent)下载最新的 MARS 代理。
1. 运行 MARSAgentInstaller.exe，只执行[安装并注册代理](./install-mars-agent.md#install-and-register-the-agent)所需的步骤，以便将代理注册到要在其中存储备份的恢复服务保管库。

   > [!NOTE]
   > 恢复服务保管库必须与 Azure Data Box 作业位于同一订阅中。

   将代理注册到恢复服务保管库后，请按后续部分的步骤操作。

## <a name="set-up-azure-data-box-devices"></a>设置 Azure Data Box 设备

请根据订购的 Azure Data Box SKU 执行下述相应部分所述的步骤。 这些步骤说明了如何为 MARS 代理设置和准备 Data Box 设备，以标识和传输初始备份数据。

### <a name="set-up-azure-data-box-disks"></a>设置 Azure Data Box 磁盘

如果订购了一个或多个 Azure Data Box 磁盘（每个磁盘最多 8 TB），请按照此处提到的步骤[拆开 Data Box 磁盘的包装并连接和解锁 Data Box 磁盘](../databox/data-box-disk-deploy-set-up.md)。

>[!NOTE]
>带有 MARS 代理的服务器可能没有 USB 端口。 在这种情况下，可以将 Azure Data Box 磁盘连接到另一个服务器或客户端，并将设备的根目录作为网络共享公开。

### <a name="set-up-azure-data-box"></a>设置 Azure Data Box

如果你将 Azure Data Box 实例排序 (高达 100 TB) ，请按照此处的步骤 [设置你的 Data Box 实例](../databox/data-box-deploy-set-up.md)。

#### <a name="mount-your-azure-data-box-instance-as-a-local-system"></a>将 Azure Data Box 实例作为本地系统安装

MARS 代理在本地系统上下文中运行，因此需要向连接 Azure Data Box 实例的装载路径提供相同级别的特权。

若要确保可以使用 NFS 协议将 Data Box 设备装载为本地系统，请执行以下操作：

1. 在安装了 MARS 代理的 Windows server 上启用 NFS 功能的客户端。 指定备用源 *WIM： D： \Sources\Install.wim： 4*。
1. 将 PsExec 从 [Sysinternals](/sysinternals/downloads/psexec) 页下载到安装了 MARS 代理的服务器。
1. 打开提升的命令提示符，并运行以下命令，并将包含 *PSExec.exe* 的目录作为当前目录。

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

   由于上一个命令打开的命令窗口位于本地系统上下文中。 使用此命令窗口执行将 Azure 页 blob 共享装载为 Windows server 上的网络驱动器的步骤。
1. 按照 [连接到 Data Box](../databox/data-box-deploy-copy-data-via-nfs.md#connect-to-data-box) 中的步骤，将你的服务器与 MARS 代理连接到通过 NFS 连接到 Data Box 设备。 在本地系统命令提示符下运行以下命令，以装载 Azure 页 blob 共享。

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

   装载共享后，请检查是否可以从服务器访问 X：。 如果可以，请继续阅读本文的下一部分。

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>将初始备份数据传输到 Azure Data Box 设备

1. 打开服务器上的 **Microsoft Azure 备份** 应用程序。
1. 在“操作”窗格中，选择“计划备份”。 

    ![选择“计划备份”](./media/offline-backup-azure-data-box/schedule-backup.png)

1. 按照**计划备份向导**中的步骤操作。

1. 通过选择“添加项”按钮来添加项。 请将项的总大小保持在你订购并接收的 [Azure Data Box SKU 支持的大小限制](#backup-data-size-and-supported-data-box-skus)内。

    ![添加要备份的项](./media/offline-backup-azure-data-box/add-items.png)

1. 选择与“文件和文件夹”和“系统状态”相对应的备份计划和保留策略。  系统状态仅适用于 Windows Server，不适用于 Windows 客户端。
1. 在向导的 " **选择初始备份类型 (文件和文件夹) ** " 页上，选择 " **使用 Microsoft Azure Data Box 磁盘传输** " 选项，然后选择 " **下一步**"。

    ![选择初始备份类型](./media/offline-backup-azure-data-box/initial-backup-type.png)

1. 当系统进行提示时，请使用对 Azure 订阅拥有“所有者”访问权限的用户凭据登录到 Azure。 成功执行此操作后，会看到如下所示的页面。

    ![创建资源并应用所需权限](./media/offline-backup-azure-data-box/creating-resources.png)

   然后，MARS 代理会提取订阅中处于“已交货”状态的 Data Box 作业。

    ![提取与订阅 ID 相对应的 Data Box 作业](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

1. 选择已拆开包装并进行了连接和解锁的 Data Box 磁盘的相应 Data Box 订单。 选择“下一步”  。

    ![选择 Data Box 订单](./media/offline-backup-azure-data-box/select-databox-order.png)

1. 在“Data Box 设备检测”页上选择“检测设备”。  此操作使 MARS 代理扫描本地连接的 Azure Data Box 磁盘并检测它们。

    ![Data Box 设备检测](./media/offline-backup-azure-data-box/databox-device-detection.png)

    如果由于 USB 端口不可用而将 Azure Data Box 实例连接为网络共享 (，或者由于你已订购并装载了 100-TB Data Box 设备) ，检测将首先失败。 你可以选择输入 Data Box 设备的网络路径。

    ![输入网络路径](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > 提供指向 Azure Data Box Disk 的根目录的网络路径。 此目录必须包含名为 PageBlob 的目录。
    >
    >![Azure Data Box 磁盘的根目录](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >例如，如果磁盘的路径是 `\\mydomain\myserver\disk1\`，且 disk1 包含一个名为 PageBlob 的目录，则在 MARS 代理向导页上输入的路径为 `\\mydomain\myserver\disk1\`。
    >
    >如果 [设置 Azure Data Box 100-TB 设备](#set-up-azure-data-box-devices)，请输入 `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` 作为设备的网络路径。

1. 选择“下一步”，然后在下一页上选择“完成”，以便保存备份和保留策略，其中包含通过使用 Azure Data Box 进行脱机备份的配置。

   以下页面确认策略已成功保存。

    ![策略已成功保存](./media/offline-backup-azure-data-box/policy-saved.png)

1. 在上一页上选择“关闭”。

1. 在 MARS 代理控制台的“操作”窗格中选择“立即备份”。  在向导页上选择“备份”。

    ![“立即备份”向导](./media/offline-backup-azure-data-box/backup-now.png)

MARS 代理开始将你选择的数据备份到 Azure Data Box 设备。 此过程可能需要几小时到几天的时间。 时间长短取决于文件数，以及装有 MARS 代理的服务器与 Azure Data Box 磁盘之间的连接速度。

数据备份完成后，MARS 代理上会显示一个如下所示的页面。

![显示的备份进度](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>备份后步骤

此部分介绍在成功地将数据备份到 Azure Data Box Disk 之后要执行的步骤。

- 按照此文中的步骤[将 Azure Data Box 磁盘寄送到 Azure](../databox/data-box-disk-deploy-picked-up.md)。 如果使用 Azure Data Box 100 TB 设备，请按照以下步骤将 [Azure Data Box 设备寄送到 Azure](../databox/data-box-deploy-picked-up.md)。

- 在 Azure 门户中[监视 Data Box 作业](../databox/data-box-disk-deploy-upload-verify.md)。 Azure Data Box 作业完成后，MARS 代理会在下一次执行计划的备份时自动将数据从存储帐户移动到恢复服务保管库。 然后，它会将备份作业标记为“作业已完成”（如果成功创建了恢复点）。

    >[!NOTE]
    >MARS 代理会在创建策略期间按计划的时间触发备份。 这些作业在完成之前带有“等待 Azure Data Box 作业完成”标记。

- 在 MARS 代理成功创建对应于初始备份的恢复点之后，你可以删除与 Azure Data Box 作业关联的存储帐户或特定内容。

## <a name="troubleshooting"></a>故障排除

Microsoft Azure 恢复服务 (MARS) 代理在租户中为你创建一个 Azure Active Directory (Azure AD 应用程序。 此应用程序需要使用在你配置脱机种子设定策略时创建和上传的证书，来进行身份验证。 我们使用 Azure PowerShell 创建证书并将其上传到 Azure AD 应用程序。

### <a name="problem"></a>问题

由于 Azure PowerShell cmdlet 中存在 bug，你可能会在配置脱机备份时面临问题。 你可能无法将多个证书添加到 MAB 代理创建的同一个 Azure AD 应用程序中。 如果你为相同或不同的服务器配置了脱机种子设定策略，此问题会对你造成影响。

### <a name="verify-if-the-problem-is-caused-by-this-specific-root-cause"></a>验证问题是否由这个特定的根本原因导致

若要了解你的问题是否与前面所述的问题相同，请执行以下步骤之一。

#### <a name="step-1-of-verification"></a>验证步骤 1

查看当配置脱机备份时以下错误消息是否显示在 MAB 控制台中。

![无法为当前 Azure 帐户创建脱机备份策略](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2-of-verification"></a>验证步骤 2

1. 在安装路径中打开 Temp 文件夹。 默认临时文件夹路径为 " *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp*"。查找 *CBUICurr* 文件，并打开文件。

1. 在 CBUICurr 文件中滚动到最后一行，查看问题是否与以下错误消息中的问题相同：`Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed`。

### <a name="workaround"></a>解决方法

若要解决此问题，请执行以下步骤，然后重试策略配置。

#### <a name="step-1-of-workaround"></a>解决方法的步骤 1

使用在要创建导入或导出作业的订阅上具有管理员访问权限的另一个帐户，登录到 MAB UI 上显示的 PowerShell。

#### <a name="step-2-of-workaround"></a>解决方法的步骤 2

如果任何其他服务器均未配置脱机种子设定，而且任何其他服务器均不依赖于 `AzureOfflineBackup_<Azure User Id>` 应用程序，请删除此应用程序。 选择“Azure 门户” > “Azure Active Directory” > “应用注册”。  

>[!NOTE]
> 查看 `AzureOfflineBackup_<Azure User Id>` 应用程序是否未配置任何其他脱机种子设定，以及是否任何其他服务器均不依赖于此应用程序。 转到“公钥”部分下的“设置” > “密钥”。   应该还没有添加任何其他公钥。 请参阅下面的屏幕截图。
>
>![公钥](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="step-3"></a>步骤 3

在你尝试进行脱机备份配置的服务器中，执行以下操作。

1. 转到“管理计算机证书应用程序” > “个人”选项卡，找到名称为 `CB_AzureADCertforOfflineSeeding_<ResourceId>` 的证书。 

2. 选择该证书，右键单击“所有任务”并选择“导出”，以 .cer 格式导出不包含私钥的证书。 

3. 转到步骤 2 中提到的 Azure 脱机备份应用程序。 选择“设置” > “密钥” > “上传公钥”  。 上传在上一步骤中导出的证书。

    ![上传公钥](./media/offline-backup-azure-data-box/upload-public-key.png)

4. 在服务器的“运行”窗口中，输入 regedit 以打开注册表。

5. 请参阅注册表 *Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider。* 右键单击“CloudBackupProvider”并添加名称为 `AzureADAppCertThumbprint_<Azure User Id>` 的新字符串值。

    >[!NOTE]
    > 若要获取 Azure 用户 ID，请执行以下操作之一：
    >
    >- 在已连接到 Azure 的 PowerShell 中运行 `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` 命令。
    > - 转到名称为 CurrentUserId 的注册表路径 `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup`。

6. 右键单击在上一步骤中添加的字符串并选择“修改”。 在该值中，提供在步骤 2 中导出的证书的指纹。 选择“确定”  。

7. 若要获取指纹值，请双击该证书。 选择“详细信息”选项卡，并向下滚动，直到看到指纹字段。 选择“指纹”并复制其值。

    ![证书的指纹字段](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>问题

如有任何疑问或对所遇到的任何问题的说明，请联系 [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) 。
