---
title: 使用 Azure 数据框脱机备份
description: 了解如何使用 Azure 数据框将大型初始备份数据从 MARS 代理脱机设定为恢复服务保管库。
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: a031a8cac357e7d212f8f6a3a5dbec749fbccc21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672969"
---
# <a name="azure-backup-offline-backup-by-using-azure-data-box"></a>使用 Azure 数据框进行 Azure 备份脱机备份

可以使用[Azure 数据框](https://docs.microsoft.com/azure/databox/data-box-overview)将大型初始 Microsoft Azure 恢复服务 （MARS） 备份脱机（不使用网络）设定为恢复服务保管库。 此过程可节省时间和网络带宽，否则将消耗大量备份数据通过高延迟网络在线移动。 此增强功能当前处于预览状态。 基于 Azure 数据框的脱机备份提供了两个明显的优势，而不是[基于 Azure 导入/导出服务的脱机备份](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export)：

* 无需采购自己的与 Azure 兼容的磁盘和连接器。 Azure 数据框提供与所选[数据框 SKU](https://azure.microsoft.com/services/databox/data/)关联的磁盘。
* Azure 备份 （MARS 代理） 可以直接将备份数据写入 Azure 数据框的受支持 SKU。 此功能无需为初始备份数据预配暂存位置。 您也不需要实用程序来格式化这些数据并将其复制到磁盘上。

## <a name="azure-data-box-with-the-mars-agent"></a>带有 MARS 代理的 Azure 数据框

本文介绍如何使用 Azure 数据框将大型初始备份数据从 MARS 代理脱机设定为恢复服务保管库。

## <a name="supported-platforms"></a>支持的平台

使用 Azure 数据框从 MARS 代理中设定数据种子的过程在以下 Windows SKU 上受支持。

| **操作系统**                                 | **Sku**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **工作站**                        |                                                              |
| Windows 10 64 位                     | Enterprise、Pro、Home                                       |
| Windows 8.1 64 位                    | Enterprise、Pro                                             |
| Windows 8 64 位                      | Enterprise、Pro                                             |
| Windows 7 64 位                      | Ultimate、Enterprise、Professional、Home Premium、Home Basic、Starter |
| **服务器**                             |                                                              |
| Windows Server 2019 64 位            | Standard、Datacenter、Essentials                            |
| Windows Server 2016 64 位            | Standard、Datacenter、Essentials                            |
| Windows Server 2012 R2 64 位         | Standard、Datacenter、Foundation                            |
| Windows Server 2012 64 位            | Datacenter、Foundation、Standard                            |
| Windows Storage Server 2016 64 位    | Standard、Workgroup                                         |
| Windows Storage Server 2012 R2 64 位 | Standard、Workgroup、Essential                              |
| Windows Storage Server 2012 64 位    | Standard、Workgroup                                         |
| Windows Server 2008 R2 SP1 64 位     | Standard、Enterprise、Datacenter、Foundation                |
| Windows 服务器 2008 SP2 64 位        | Standard、Enterprise、Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>备份数据大小和支持的数据框 SKU

| 每个服务器备份数据大小（按 MARS 压缩后）* | 受支持的 Azure 数据框 SKU                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <±7.2 TB                                                    | [Azure 数据框磁盘](https://docs.microsoft.com/azure/databox/data-box-disk-overview) |
| >7.2 TB 和 <[80 TB]                                      | [Azure 数据框（100 TB）](https://docs.microsoft.com/azure/databox/data-box-overview) |

*典型压缩率在 10% 到 20% 之间变化。 <br>
*如果您希望单个 MARS 服务器的初始备份数据超过 80 TB，请与[AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com).

>[!IMPORTANT]
>单个服务器的初始备份数据必须包含在单个 Azure 数据框实例或 Azure 数据盒磁盘中，并且不能在同一或不同 SKU 的多个设备之间共享。 但是，Azure 数据盒设备可以包含来自多个服务器的初始备份。

## <a name="prerequisites"></a>先决条件

### <a name="azure-subscription-and-required-permissions"></a>Azure 订阅和所需权限

* 该过程需要 Azure 订阅。
* 该过程要求指定用于执行脱机备份策略的用户是 Azure 订阅的所有者。
* 数据框作业和恢复服务保管库（需要对数据进行种子设定）需要位于同一订阅中。
* 我们建议与 Azure 数据框作业和恢复服务保管库关联的目标存储帐户位于同一区域中。 但是，这是没有必要的。

### <a name="get-azure-powershell-370"></a>获取 Azure 电源外壳 3.7.0

*这是这个过程最重要的先决条件*。 在安装 Azure PowerShell 之前，版本 3.7.0 将执行以下检查。

#### <a name="step-1-check-the-powershell-version"></a>第 1 步：检查电源外壳版本

1. 打开 Windows PowerShell，并运行以下命令：

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

1.  如果输出显示的版本高于 3.7.0，则执行"步骤 2"。 否则，请跳到"步骤 3"。

#### <a name="step-2-uninstall-the-powershell-version"></a>第 2 步：卸载 PowerShell 版本

卸载当前版本的 PowerShell。

1. 通过在 PowerShell 中运行以下命令来删除相关模块：

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

2. 为确保成功删除所有从属模块，运行以下命令：

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>第 3 步：安装 PowerShell 版本 3.7.0

验证不存在 AzureRM 模块后，请使用以下方法之一安装版本 3.7.0：

* 从 GitHub，使用此[链接](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)。

或者可以：

* 在 PowerShell 窗口中运行以下命令：

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

Azure PowerShell 也可以通过使用 msi 文件进行安装。 要将其删除，请使用"控制面板"中的 **"卸载程序**"选项卸载它。

### <a name="order-and-receive-the-data-box-device"></a>订购并接收数据盒设备

使用 MARS 和 Azure 数据盒的脱机备份过程要求数据盒设备在使用 MARS 代理触发脱机备份之前处于"已交付"状态。 要根据您的要求订购最合适的 SKU，请参阅[备份数据大小和支持的数据框 SKU](#backup-data-size-and-supported-data-box-skus)。 按照教程中的步骤[操作：订购 Azure 数据盒磁盘](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-ordered)以订购和接收数据盒设备。

> [!IMPORTANT]
> 不要为**帐户类型**选择*Blob 存储*。 MARS 代理需要一个帐户，该帐户支持页面 Blob，在选择*Blob 存储*时不支持该帐户。 在为 Azure 数据框作业创建目标存储帐户时，选择**存储 V2（通用 v2）** 作为**帐户类型**。

![在实例详细信息中选择帐户类型](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-set-up-the-mars-agent"></a>安装并设置 MARS 代理

1. 请确保卸载 MARS 代理的任何以前安装。
1. 从[本网站](https://aka.ms/azurebackup_agent)下载最新的MARS代理。
1. 运行*MARSAgentInstaller.exe*，并*仅*执行[步骤，将代理安装并注册](https://docs.microsoft.com/azure/backup/install-mars-agent#install-and-register-the-agent)到希望存储备份的恢复服务保管库。

   > [!NOTE]
   > 恢复服务保管库必须与 Azure 数据框作业处于同一订阅中。

   在代理注册到恢复服务保管库后，请按照以下部分中的步骤操作。

## <a name="set-up-azure-data-box-devices"></a>设置 Azure 数据框设备

根据所订购的 Azure 数据框 SKU，执行以下相应部分中介绍的步骤。 这些步骤演示如何为 MARS 代理设置和准备数据盒设备，以识别和传输初始备份数据。

### <a name="set-up-azure-data-box-disks"></a>设置 Azure 数据框磁盘

如果订购了一个或多个 Azure 数据框磁盘（每个磁盘最多 8 TB），请按照此处提到的步骤[解压缩、连接和解锁数据框磁盘](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-set-up)。

>[!NOTE]
>具有 MARS 代理的服务器可能没有 USB 端口。 在这种情况下，您可以将 Azure 数据盒磁盘连接到另一台服务器或客户端，并将设备的根公开为网络共享。

### <a name="set-up-azure-data-box"></a>设置 Azure 数据框

如果订购了 Azure 数据框实例（最多 100 TB），请按照此处的步骤[设置数据框实例](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up)。

#### <a name="mount-your-azure-data-box-instance-as-a-local-system"></a>将 Azure 数据框实例装载为本地系统

MARS 代理在本地系统上下文中运行，因此需要向连接 Azure 数据框实例的装载路径提供相同级别的权限。 

为确保可以使用 NFS 协议将数据盒设备装载为本地系统， 请：

1. 为安装了 MARS 代理的 Windows 服务器上的 NFS 功能启用客户端。 指定备用源*WIM：D：\源\安装.wim：4*。
1. 安装 MARS<https://download.sysinternals.com/files/PSTools.zip>代理后，将 PSExec 从服务器下载。
1. 打开提升的命令提示符，并将包含*PSExec.exe*的目录作为当前目录运行以下命令。

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

   由于上一个命令而打开的命令窗口位于本地系统上下文中。 使用此命令窗口执行将 Azure 页 Blob 共享装载为 Windows 服务器上的网络驱动器的步骤。
1. 按照["连接到数据框](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-nfs#connect-to-data-box)"中的步骤，通过 NFS 将服务器与 MARS 代理连接到数据盒设备。 在本地系统命令提示符上运行以下命令以装载 Azure 页 blob 共享。

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

   装载共享后，请检查是否可以从服务器访问 X： 。 如果可以，请继续本文的下一部分。

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>将初始备份数据传输到 Azure 数据盒设备

1. 在服务器上打开**Microsoft Azure 备份**应用程序。
1. 在 **"操作"** 窗格中，选择 **"计划备份**"。

    ![选择计划备份](./media/offline-backup-azure-data-box/schedule-backup.png)

1. 按照**计划备份向导**中的步骤操作。

1. 通过选择"**添加项目"按钮添加项目**。 将项目的总大小保持在排序和接收[的 Azure 数据框 SKU 支持的大小限制](#backup-data-size-and-supported-data-box-skus)内。

    ![将项目添加到备份](./media/offline-backup-azure-data-box/add-items.png)

1. 为 **"文件"和"文件夹"和****"系统状态**"选择适当的备份计划和保留策略。 系统状态仅适用于 Windows 服务器，不适用于 Windows 客户端。
1. 在向导的 **"选择初始备份类型（文件和文件夹）"** 页上，选择使用 Microsoft **Azure 数据框磁盘进行传输**的选项，然后选择 **"下一步**"。

    ![选择初始备份类型](./media/offline-backup-azure-data-box/initial-backup-type.png)

1. 使用 Azure 订阅上具有所有者访问权限的用户凭据提示时，请登录到 Azure。 成功完成之后，您应该会看到一个类似于此页面的页面。

    ![创建资源并应用所需的权限](./media/offline-backup-azure-data-box/creating-resources.png)

   然后，MARS 代理获取处于"已交付"状态的订阅中的数据框作业。

    ![获取订阅 ID 的数据框作业](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

1. 选择已为其解压缩、连接和解锁数据盒磁盘的正确数据框顺序。 选择“下一步”。

    ![选择数据框订单](./media/offline-backup-azure-data-box/select-databox-order.png)

1. 在 **"数据箱设备检测"** 页上选择 **"检测设备**"。 此操作使 MARS 代理扫描本地附加的 Azure 数据框磁盘并检测它们。

    ![数据框设备检测](./media/offline-backup-azure-data-box/databox-device-detection.png)

    如果将 Azure 数据盒实例连接到网络共享（由于 USB 端口不可用或由于您订购和装载了 100 TB 数据盒设备），则检测最初将失败。 您可以选择输入到数据盒设备的网络路径。

    ![输入网络路径](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > 提供 Azure 数据框磁盘根目录的网络路径。 此目录必须包含名称*PageBlob*的目录。
    >
    >![Azure 数据框磁盘的根目录](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >`\\mydomain\myserver\disk1\`例如，如果磁盘的路径是，*并且磁盘 1*包含名为*PageBlob*的目录，则您在 MARS 代理向导页上输入的`\\mydomain\myserver\disk1\`路径为 。
    >
    >如果[设置 Azure 数据盒 100 TB 设备](#set-up-azure-data-box-devices)，`\\<DeviceIPAddress>\<StorageAccountName>_PageBlob`请输入为设备的网络路径。

1. 选择 **"下一步**"，然后在下一页上选择 **"完成"，** 使用 Azure 数据框使用脱机备份的配置保存备份和保留策略。

   以下页确认策略已成功保存。

    ![已成功保存策略](./media/offline-backup-azure-data-box/policy-saved.png)

1. 选择上一页上的 **"关闭**"。

1. 在 MARS 代理控制台的 **"操作"** 窗格中选择 **"立即备份**"。 在向导页面上选择 **"备份**"。

    ![立即备份向导](./media/offline-backup-azure-data-box/backup-now.png)

MARS 代理开始备份所选数据盒设备的数据。 此过程可能需要几个小时到几天。 时间量取决于具有 MARS 代理和 Azure 数据盒磁盘的服务器之间的文件数量和连接速度。

数据备份完成后，您将在 MARS 代理上看到类似于此页面的页面。

![显示备份进度](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>备份后步骤

本节介绍将数据备份到 Azure 数据盒磁盘成功后要采取的步骤。

* 按照本文中的步骤将[Azure 数据框磁盘运送到 Azure](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-picked-up)。 如果使用 Azure 数据盒 100 TB 设备，请按照以下步骤将[Azure 数据框设备运送到 Azure](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up)。

* 监视 Azure 门户中[的数据框作业](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-upload-verify)。 Azure 数据框作业完成后，MARS 代理会在下次计划备份时将数据从存储帐户移动到恢复服务保管库。 然后，如果成功创建恢复点，它将备份作业标记为 *"作业已完成*"。

    >[!NOTE]
    >MARS 代理在策略创建期间计划的时间触发备份。 这些作业标记"等待 Azure 数据框作业完成"，直到作业完成。

* 在 MARS 代理成功创建与初始备份对应的恢复点后，可以删除与 Azure 数据框作业关联的存储帐户或特定内容。

## <a name="troubleshooting"></a>疑难解答

Microsoft Azure 备份 （MAB） 代理会为租户中为您创建 Azure 活动目录 （Azure AD） 应用程序。 此应用程序需要一个证书来进行身份验证，该证书在配置脱机种子设定策略时创建和上载。 我们使用 Azure PowerShell 创建证书并将其上载到 Azure AD 应用程序。

### <a name="problem"></a>问题

配置脱机备份时，由于 Azure PowerShell cmdlet 中的错误，可能会遇到问题。 可能无法向 MAB 代理创建的同一 Azure AD 应用程序添加多个证书。 如果为同一服务器或其他服务器配置脱机种子设定策略，则此问题将影响您。

### <a name="verify-if-the-problem-is-caused-by-this-specific-root-cause"></a>验证问题是否由此特定根本原因引起

要查看您的问题是否与前面描述的问题相同，请执行以下步骤之一。

#### <a name="step-1"></a>步骤 1

在配置脱机备份时，请检查 MAB 控制台中是否显示以下错误消息。

![无法为当前 Azure 帐户创建脱机备份策略](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2"></a>步骤 2

1. 打开安装路径中的 **"临时"** 文件夹。 默认临时文件夹路径为*C：\程序文件\微软 Azure 恢复服务代理\Temp*。查找*CBUICurr*文件，然后打开该文件。

1. 在*CBUICurr*文件中，滚动到最后一行，并检查问题是否与此错误消息中的相同： `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed`。

### <a name="workaround"></a>解决方法

作为解决此问题的解决方法，请执行以下步骤并重试策略配置。

#### <a name="step-1"></a>步骤 1

使用具有管理员访问权限的订阅上具有管理员访问权限的不同帐户登录到 MAB UI 上显示的 PowerShell，该帐户将创建导入或导出作业。

#### <a name="step-2"></a>步骤 2

如果没有其他服务器配置了脱机种子设定，并且没有其他服务器依赖于该应用程序`AzureOfflineBackup_<Azure User Id>`，请删除此应用程序。 选择**Azure 门户** > **Azure 活动目录** > **应用注册**。

>[!NOTE]
> 检查`AzureOfflineBackup_<Azure User Id>`应用程序是否未配置任何其他脱机种子设定，以及没有其他服务器依赖于此应用程序。 转到 **"公钥**"部分下的 **"设置** > **密钥**"。 它不应该添加任何其他公钥。 请参阅以下屏幕截图供参考。
>
>![公钥](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="step-3"></a>步骤 3

从您尝试为脱机备份配置的服务器中，执行以下操作。

1. 转到 **"管理计算机证书应用程序** > **个人"** 选项卡，然后查找名称 的证书`CB_AzureADCertforOfflineSeeding_<ResourceId>`。

2. 选择证书，右键单击 **"所有任务**"，然后选择 .cer 格式的 **"** 导出"而不带私钥。

3. 转到步骤 2 中提到的 Azure 脱机备份应用程序。 选择**设置** > **键** > **上传公钥**。 上传您在上一步中导出的证书。

    ![上传公钥](./media/offline-backup-azure-data-box/upload-public-key.png)

4. 在服务器中，通过在运行窗口中输入**注册来**打开注册表。

5. 转到注册表*计算机[HKEY_LOCAL_MACHINE_SOFTWARE_微软_Windows Azure 备份\配置\云备份提供程序。* 右键单击**CloudBackupProvider**，并添加名称的新字符串值`AzureADAppCertThumbprint_<Azure User Id>`。

    >[!NOTE]
    > 要获取 Azure 用户 ID，可以执行以下操作之一：
    >
    >* 从 Azure 连接的 PowerShell`Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"`运行命令。
    > * 转到注册表路径*计算机\HKEY_LOCAL_MACHINE\软件\微软\Windows Azure 备份\DbgSettings_在线备份*的名称为*CurrentUserId*。

6. 右键单击上一步中添加的字符串，然后选择 **"修改**"。 在 值中，提供步骤 2 中导出的证书的指纹。 选择“确定”。

7. 要获取指纹的值，请双击证书。 选择"**详细信息**"选项卡，然后向下滚动，直到看到指纹字段。 选择 **"拇指打印"** 并复制该值。

    ![证书的指纹字段](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>问题

有关您遇到的任何问题或澄清，请联系[AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)。
