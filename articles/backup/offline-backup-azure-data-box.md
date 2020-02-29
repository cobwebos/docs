---
title: 使用 Azure Data Box 进行脱机备份
description: 了解如何使用 Azure Data Box 从 MARS 代理脱机将较大的初始备份数据植入恢复服务保管库。
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 9a8288939adba8ec1b0cbe38d2243b1bdd84fa2e
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196257"
---
# <a name="azure-backup-offline-backup-by-using-azure-data-box"></a>Azure 备份使用 Azure Data Box 进行脱机备份

你可以使用[Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-overview)将大型初始 MICROSOFT AZURE 恢复服务（MARS）备份从脱机状态（无需使用网络）植入恢复服务保管库。 此过程可节省时间和网络带宽，在这种情况下，将在高延迟网络中联机移动大量的备份数据。 此增强功能目前处于预览阶段。 基于 Azure Data Box 的脱机备份在[基于 Azure 导入/导出服务的脱机备份](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export)方面提供了两个不同的优点：

* 无需购买你自己的与 Azure 兼容的磁盘和连接器。 Azure Data Box 附带与所选[DATA BOX SKU](https://azure.microsoft.com/services/databox/data/)关联的磁盘。
* Azure 备份（MARS 代理）可以将备份数据直接写入 Azure Data Box 支持的 Sku。 此功能无需为初始备份数据预配暂存位置。 你还不需要实用程序来设置数据的格式并将其复制到磁盘。

## <a name="azure-data-box-with-the-mars-agent"></a>与 MARS 代理 Azure Data Box

本文介绍如何使用 Azure Data Box 从 MARS 代理脱机将较大的初始备份数据植入恢复服务保管库。

## <a name="supported-platforms"></a>支持的平台

以下 Windows Sku 支持通过使用 Azure Data Box 从 MARS 代理播种数据的过程。

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

## <a name="backup-data-size-and-supported-data-box-skus"></a>备份数据大小和支持的 Data Box Sku

| 备份数据大小（按 MARS 压缩后） * 每服务器 | 支持的 Azure Data Box SKU                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| < = 7.2 TB                                                    | [Azure Data Box 磁盘](https://docs.microsoft.com/azure/databox/data-box-disk-overview) |
| > 7.2 TB 和 < = 80 TB * *                                      | [Azure Data Box （100 TB）](https://docs.microsoft.com/azure/databox/data-box-overview) |

\* 典型的压缩率在10% 到20% 之间变化。 <br>
\* * 如果你预计单个 MARS 服务器的初始备份数据超过 80 TB，请联系[AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)。

>[!IMPORTANT]
>单个服务器的初始备份数据必须包含在单个 Azure Data Box 实例或 Azure Data Box 磁盘中，并且不能在相同或不同 Sku 的多个设备之间共享。 但 Azure Data Box 设备可以包含来自多个服务器的初始备份。

## <a name="prerequisites"></a>必备条件

### <a name="azure-subscription-and-required-permissions"></a>Azure 订阅和所需权限

* 此过程需要 Azure 订阅。
* 此过程要求指定执行脱机备份策略的用户是 Azure 订阅的所有者。
* Data Box 作业和恢复服务保管库（需要为数据提供种子）必须位于同一订阅中。
* 建议与 Azure Data Box 作业和恢复服务保管库关联的目标存储帐户位于同一区域。 但这并不是必需的。

### <a name="get-azure-powershell-370"></a>获取 Azure PowerShell 3.7。0

*这是此过程的最重要的先决条件*。 在安装 Azure PowerShell 3.7.0 版本之前，请执行以下检查。

#### <a name="step-1-check-the-powershell-version"></a>步骤1：检查 PowerShell 版本

1. 打开 Windows PowerShell 并运行以下命令：

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

1.  如果输出显示的版本高于3.7.0，请执行 "步骤 2"。 否则，请跳到 "步骤 3"。

#### <a name="step-2-uninstall-the-powershell-version"></a>步骤2：卸载 PowerShell 版本

卸载最新版本的 PowerShell。

1. 通过在 PowerShell 中运行以下命令来删除从属模块：

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

2. 若要确保成功删除所有依赖模块，请运行以下命令：

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>步骤3：安装 PowerShell 版本3.7。0

验证没有 AzureRM 模块后，请使用以下方法之一安装版本3.7.0：

* 从 GitHub 使用[此链接](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)。

或者可以：

* 在 PowerShell 窗口中运行以下命令：

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

还可以通过使用 msi 文件安装 Azure PowerShell。 若要删除它，请使用控制面板中的 "**卸载程序**" 选项将其卸载。

### <a name="order-and-receive-the-data-box-device"></a>订购并接收 Data Box 设备

使用 MARS 和 Azure Data Box 的脱机备份过程要求 Data Box 设备处于交付状态，然后才能使用 MARS 代理触发脱机备份。 若要为你的要求订购最适合的 SKU，请参阅[备份数据大小和支持的 Data Box sku](#backup-data-size-and-supported-data-box-skus)。 按照[教程：对 Azure Data Box 磁盘](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-ordered)进行排序和接收 Data Box 设备教程中的步骤进行操作。

> [!IMPORTANT]
> 不要为**帐户类型**选择*BlobStorage* 。 MARS 代理需要支持页 blob 的帐户，在选择*BlobStorage*时，不支持页 blob。 创建 Azure Data Box 作业的目标存储帐户时，请选择 "**存储 V2 （常规用途 V2）** " 作为**帐户类型**。

![在实例详细信息中选择帐户类型](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-set-up-the-mars-agent"></a>安装和设置 MARS 代理

1. 请确保卸载以前安装的任何 MARS 代理。
1. 从此[网站](https://aka.ms/azurebackup_agent)下载最新的 MARS 代理。
1. 运行*marsagentinstaller.exe*，并*仅*执行将[代理安装并注册](https://docs.microsoft.com/azure/backup/backup-configure-vault#install-and-register-the-agent)到恢复服务保管库（要在其中存储备份）的步骤。

   > [!NOTE]
   > 恢复服务保管库必须与 Azure Data Box 作业位于同一订阅中。

   将代理注册到恢复服务保管库后，请按照后续部分中的步骤进行操作。

## <a name="set-up-azure-data-box-devices"></a>设置 Azure Data Box 设备

根据你订购的 Azure Data Box SKU，请执行以下相应部分中所述的步骤。 这些步骤说明了如何为 MARS 代理设置和准备 Data Box 设备，以确定和传输初始备份数据。

### <a name="set-up-azure-data-box-disks"></a>设置 Azure Data Box 磁盘

如果为一个或多个 Azure Data Box 磁盘排序（每个磁盘最多 8 TB），请按照此处提到的步骤[解压缩、连接和解锁 Data Box 磁盘](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-set-up)。

>[!NOTE]
>使用 MARS 代理的服务器可能没有 USB 端口。 在这种情况下，你可以将 Azure Data Box 磁盘连接到另一台服务器或客户端，并将设备的根目录公开为网络共享。

### <a name="set-up-azure-data-box"></a>设置 Azure Data Box

如果你订购了 Azure Data Box 实例（最多 100 TB），请按照此处的步骤[设置你的 Data Box 实例](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up)。

#### <a name="mount-your-azure-data-box-instance-as-a-local-system"></a>将 Azure Data Box 实例作为本地系统安装

MARS 代理在本地系统上下文中运行，因此需要向连接 Azure Data Box 实例的装载路径提供相同级别的特权。 

若要确保可以使用 NFS 协议将 Data Box 设备装载为本地系统，请执行以下操作：

1. 在安装了 MARS 代理的 Windows server 上启用 NFS 功能的客户端。 指定备用源*WIM： D： \Sources\Install.wim： 4*。
1. 将 PSExec 从 <https://download.sysinternals.com/files/PSTools.zip> 下载到安装了 MARS 代理的服务器。
1. 打开提升的命令提示符，并运行以下命令，并在目录中包含*PSExec*作为当前目录。

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

   作为前一个命令的结果打开的命令窗口位于本地系统上下文中。 使用此命令窗口执行将 Azure 页 blob 共享装载为 Windows server 上的网络驱动器的步骤。
1. 按照[连接到 Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-nfs#connect-to-data-box)中的步骤，将你的服务器与 MARS 代理连接到通过 NFS 连接到 Data Box 设备。 在本地系统命令提示符下运行以下命令，以装载 Azure 页 blob 共享。

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

   装载共享后，请检查是否可以从服务器访问 X：。 如果可以，请继续阅读本文的下一部分。

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>将初始备份数据传输到 Azure Data Box 设备

1. 打开服务器上的**Microsoft Azure 备份**应用程序。
1. 在 "**操作**" 窗格中，选择 "**计划备份**"。

    ![选择计划备份](./media/offline-backup-azure-data-box/schedule-backup.png)

1. 按照**计划备份向导**中的步骤进行操作。

1. 通过选择 "**添加项**" 按钮添加项。 将项的总大小保留在您订购并接收的[AZURE DATA BOX SKU 所支持的大小限制](#backup-data-size-and-supported-data-box-skus)内。

    ![添加要备份的项](./media/offline-backup-azure-data-box/add-items.png)

1. 为**文件和文件夹**以及**系统状态**选择适当的备份计划和保留策略。 系统状态仅适用于 Windows 服务器，不适用于 Windows 客户端。
1. 在向导的 "**选择初始备份类型（文件和文件夹）** " 页上，选择 "**使用 Microsoft Azure Data Box 磁盘传输**" 选项，然后选择 "**下一步**"。

    ![选择初始备份类型](./media/offline-backup-azure-data-box/initial-backup-type.png)

1. 通过使用在 Azure 订阅上拥有所有者访问权限的用户凭据出现提示时，登录到 Azure。 成功执行此操作后，会看到与此类似的页面。

    ![创建资源和应用所需权限](./media/offline-backup-azure-data-box/creating-resources.png)

   然后，MARS 代理将获取订阅中处于 "已交付" 状态的 Data Box 作业。

    ![获取订阅 ID Data Box 作业](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

1. 选择已解压缩、已连接并解锁 Data Box 磁盘的正确 Data Box 顺序。 选择“**下一页**”。

    ![选择 Data Box 订单](./media/offline-backup-azure-data-box/select-databox-order.png)

1. 选择 " **Data Box 设备检测**" 页上的 "**检测设备**"。 此操作使 MARS 代理扫描本地连接 Azure Data Box 磁盘，并对其进行检测。

    ![Data Box 设备检测](./media/offline-backup-azure-data-box/databox-device-detection.png)

    如果已将 Azure Data Box 实例连接为网络共享（因为 USB 端口不可用，或由于你已订购并装载了 100-TB Data Box 设备），则检测将首先失败。 你可以选择输入 Data Box 设备的网络路径。

    ![输入网络路径](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > 提供 Azure Data Box 磁盘的根目录的网络路径。 此目录必须包含名为*PageBlob*的目录。
    >
    >![Azure Data Box 磁盘的根目录](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >例如，如果磁盘的路径是 `\\mydomain\myserver\disk1\` 的，并且*disk1*包含一个名为*PageBlob*的目录，则在 MARS 代理向导页上输入的路径将 `\\mydomain\myserver\disk1\`。
    >
    >如果[设置 Azure Data Box 100-TB 设备](#set-up-azure-data-box-devices)，请输入 `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` 作为设备的网络路径。

1. 选择 "**下一步**"，然后在下一页上选择 "**完成**"，使用 Azure Data Box 配置脱机备份的配置，以保存备份和保留策略。

   以下页面确认策略已成功保存。

    ![已成功保存策略](./media/offline-backup-azure-data-box/policy-saved.png)

1. 选择上一页中的 "**关闭**"。

1. 在 MARS 代理控制台的 "**操作**" 窗格中选择 "**立即备份**"。 在向导页上选择 "**备份**"。

    ![立即备份向导](./media/offline-backup-azure-data-box/backup-now.png)

MARS 代理将开始备份您选择的 Azure Data Box 设备上的数据。 此过程可能需要几个小时到几天的时间。 时间量取决于服务器与 MARS 代理和 Azure Data Box 磁盘之间的文件数和连接速度。

数据备份完成后，MARS 代理上会显示一个与此类似的页面。

![显示的备份进度](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>备份后步骤

本部分介绍在将数据备份到 Azure Data Box Disk 成功之后要执行的步骤。

* 按照本文中的步骤将[Azure Data Box 磁盘寄送到 Azure](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-picked-up)。 如果使用 Azure Data Box 100 TB 设备，请按照以下步骤将[Azure Data Box 设备寄送到 Azure](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up)。

* [监视 Azure 门户中的 Data Box 作业](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-upload-verify)。 Azure Data Box 作业完成后，MARS 代理会在下一次计划备份时自动将数据从存储帐户移动到恢复服务保管库。 然后，在成功创建恢复点时，它会将备份作业标记为 "*已完成*"。

    >[!NOTE]
    >MARS 代理会在创建策略期间触发备份。 在作业完成之前，这些作业标志 "等待 Azure Data Box 作业完成"。

* MARS 代理成功创建对应于初始备份的恢复点之后，可以删除与 Azure Data Box 作业关联的存储帐户或特定内容。

## <a name="troubleshooting"></a>故障排除

Microsoft Azure 备份（MAB）代理在租户中为你创建 Azure Active Directory （Azure AD）应用程序。 此应用程序需要一个证书，用于在配置脱机种子设定策略时创建和上载的身份验证。 我们使用 Azure PowerShell 创建证书并将其上传到 Azure AD 应用程序。

### <a name="problem"></a>问题

配置脱机备份时，可能会面临问题，因为 Azure PowerShell cmdlet 中存在 bug。 您可能无法将多个证书添加到 MAB 代理创建的同一个 Azure AD 应用程序。 如果为相同或不同的服务器配置了脱机种子设定策略，此问题将会受到影响。

### <a name="verify-if-the-problem-is-caused-by-this-specific-root-cause"></a>验证问题是否是由特定的根本原因导致的

若要查看问题是否与前面所述的问题相同，请执行以下步骤之一。

#### <a name="step-1"></a>步骤 1

检查是否在配置了脱机备份时在 MAB 控制台中显示以下错误消息。

![无法为当前 Azure 帐户创建脱机备份策略](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2"></a>步骤 2

1. 打开安装路径中的**临时**文件夹。 默认临时文件夹路径为 " *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp*"。查找*CBUICurr*文件，并打开文件。

1. 在*CBUICurr*文件中，滚动到最后一行，查看是否存在与此错误消息中的问题相同的问题： `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed`。

### <a name="workaround"></a>解决方法

若要解决此问题，请执行以下步骤，然后重试策略配置。

#### <a name="step-1"></a>步骤 1

使用在将创建导入或导出作业的订阅上具有管理员访问权限的其他帐户登录到 MAB UI 上显示的 PowerShell。

#### <a name="step-2"></a>步骤 2

如果没有其他服务器配置了脱机种子设定，而且没有其他服务器依赖于 `AzureOfflineBackup_<Azure User Id>` 的应用程序，请删除此应用程序。 选择**Azure 门户** > **Azure Active Directory** > **应用注册**。

>[!NOTE]
> 检查 `AzureOfflineBackup_<Azure User Id>` 应用程序是否未配置任何其他脱机种子设定，同时是否还没有其他服务器依赖于此应用程序。 请参阅 "**公钥**" 部分下的 "**设置**" > **项**。 不应添加任何其他公钥。 有关参考，请参阅以下屏幕截图。
>
>![公钥](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="step-3"></a>步骤 3

在尝试配置脱机备份的服务器上，执行以下操作。

1. 请在 "**管理计算机证书应用程序** > **个人**" 选项卡上，查找名称为 `CB_AzureADCertforOfflineSeeding_<ResourceId>`的证书。

2. 选择证书，右键单击 "**所有任务**"，并选择 "**导出**" 而不使用 .cer 格式的私钥。

3. 请参阅步骤2中提到的 Azure 脱机备份应用程序。 选择 "**设置**" > **项** > **上传公钥**"。 上传在上一步骤中导出的证书。

    ![上传公钥](./media/offline-backup-azure-data-box/upload-public-key.png)

4. 在服务器中，在 "运行" 窗口中输入**regedit**打开注册表。

5. 请参阅注册表*计算机 \ HKEY_LOCAL_MACHINE \Software\microsoft\windows Azure Backup\Config\CloudBackupProvider.* 右键单击**CloudBackupProvider**，然后添加一个名称 `AzureADAppCertThumbprint_<Azure User Id>`的新字符串值。

    >[!NOTE]
    > 若要获取 Azure 用户 ID，请执行以下操作之一：
    >
    >* 在 Azure 连接的 PowerShell 中运行 `Get-AzureRmADUser -UserPrincipalName “Account Holder’s email as defined in the portal”` 命令。
    > * 通过名称*CurrentUserId*，中转到注册表路径*计算机 \ HKEY_LOCAL_MACHINE \software\microsoft\windows Azure Backup\DbgSettings\OnlineBackup* 。

6. 右键单击在上一步中添加的字符串，然后选择 "**修改**"。 在 "值" 中，提供你在步骤2中导出的证书的指纹。 选择“确定”。

7. 若要获取该指纹的值，请双击该证书。 选择 "**详细信息**" 选项卡，然后向下滚动，直到看到 "指纹" 字段。 选择 "**指纹**" 并复制值。

    ![证书的指纹字段](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>问题

如有任何疑问或对您所遇到的任何问题的说明，请联系[AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)。
