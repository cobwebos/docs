---
title: 使用 ASDK 验证 Azure Stack 备份 | Microsoft Docs
description: 如何验证使用 ASDK 的 Azure Stack 集成的系统备份。
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 02/15/2019
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 02/15/2019
ms.openlocfilehash: 31c5d068c8fcd0b6edea7cff63098131d848a14e
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2019
ms.locfileid: "56416372"
---
# <a name="use-the-asdk-to-validate-an-azure-stack-backup"></a>使用 ASDK 验证 Azure Stack 备份
在部署 Azure Stack 并预配用户资源（例如套餐、计划、配额、订阅）以后，应[启用 Azure Stack 基础结构备份](../azure-stack-backup-enable-backup-console.md)。 计划并运行定期基础结构备份可确保在硬件或服务出现灾难性故障时基础结构管理数据不会丢失。

> [!TIP]
> 我们建议您[运行按需备份](../azure-stack-backup-back-up-azure-stack.md)之前开始此过程以确保拥有最新可用的基础结构数据的副本。 确保在备份成功完成以后捕获备份 ID。 在云恢复过程中，将需要此 ID。 

Azure Stack 基础结构备份包含有关云的重要数据，这些数据可以在重新部署 Azure Stack 的过程中还原。 可以使用 ASDK 来验证这些备份，不影响生产云。 

以下方案支持在 ASDK 上验证备份：

|场景|目的|
|-----|-----|
|通过集成解决方案验证基础结构备份。|短暂验证，验证备份中的数据是否有效。|
|了解端到端恢复工作流。|使用 ASDK 验证整个备份和还原体验。|
|     |     |

在 ASDK 上验证备份时，以下方案**不**受支持：

|场景|目的|
|-----|-----|
|ASDK 内部版本到内部版本备份和还原。|将备份数据从旧版 ASDK 还原到新版。|
|     |     |


## <a name="cloud-recovery-deployment"></a>云恢复部署
对 ASDK 执行云恢复部署即可验证从集成系统部署进行的基础结构备份。 在此类部署中，将 ASDK 安装在主机上以后，即可从备份还原特定的服务数据。

### <a name="prereqs"></a>云恢复的先决条件
在开始对 ASDK 进行云恢复部署之前，请确保有以下信息：

**UI 安装程序要求**

*当前 UI 安装程序仅支持加密密钥*

|先决条件|描述|
|-----|-----|
|备份共享路径|最新 Azure Stack 备份的 UNC 文件共享路径，该备份将用于恢复 Azure Stack 基础结构信息。 此本地共享将在云恢复部署过程中创建。|
|要还原的备份 ID|备份 ID，采用的字母数字形式为“xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx”，用于确定需要在云恢复过程中还原的备份。|
|时间服务器 IP|有效的时间服务器 IP（例如 132.163.97.2）是 Azure Stack 部署所需的。|
|外部证书密码|Azure Stack 使用的外部证书的密码。 CA 备份包含外部证书，这些证书需使用此密码来还原。|
|备份加密密钥|如果已升级到 Azure Stack 版本 1901年或更高版本和备份设置仍配置使用的加密密钥所需。 加密密钥已弃用从 1901 年开始。 安装程序将支持中的加密密钥向后兼容性模式至少 3 个版本。 更新后备份设置为使用的证书，请参阅下一步表中的所需的信息。|

|     |     | 

**PowerShell 安装程序要求**

*当前的 PowerShell 安装程序支持的加密密钥或解密证书*

|先决条件|描述|
|-----|-----|
|备份共享路径|最新 Azure Stack 备份的 UNC 文件共享路径，该备份将用于恢复 Azure Stack 基础结构信息。 此本地共享将在云恢复部署过程中创建。|
|要还原的备份 ID|备份 ID，采用的字母数字形式为“xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx”，用于确定需要在云恢复过程中还原的备份。|
|时间服务器 IP|有效的时间服务器 IP（例如 132.163.97.2）是 Azure Stack 部署所需的。|
|外部证书密码|Azure Stack 使用的外部证书的密码。 CA 备份包含外部证书，这些证书需使用此密码来还原。|
|解密证书密码|可选。 才被必需的使用证书加密备份。 为自签名证书的 (.pfx)，其中包含需要备份的数据进行解密的私钥密码。|
|备份加密密钥|可选。 如果已升级到 Azure Stack 版本 1901年或更高版本和备份设置仍配置使用的加密密钥所需。 安装程序将支持中的加密密钥向后兼容性模式至少 3 个版本。 更新后备份设置为使用的证书，必须提供解密证书的密码。|
|     |     | 

## <a name="prepare-the-host-computer"></a>准备主机 
与在正常的 ASDK 部署中一样，ASDK 主机系统环境必须进行安装准备。 准备好开发工具包主机之后，该主机会从 CloudBuilder.vhdx 虚拟机硬盘启动，以开始进行 ASDK 部署。

在 ASDK 主机上下载新的 cloudbuilder.vhdx（对应于 Azure Stack 的已备份版本），然后按说明[准备 ASDK 主机](asdk-prepare-host.md)。

在主机服务器从 cloudbuilder.vhdx 重启以后，必须创建一个文件共享并将备份数据复制到其中。 文件共享应该可供运行设置的帐户（即下述示例 PowerShell 命令中的管理员）访问： 

```powershell
$shares = New-Item -Path "c:\" -Name "Shares" -ItemType "directory"
$azsbackupshare = New-Item -Path $shares.FullName -Name "AzSBackups" -ItemType "directory"
New-SmbShare -Path $azsbackupshare.FullName -FullAccess ($env:computername + "\Administrator")  -Name "AzSBackups"
```

接下来，将最新的 Azure Stack 备份文件复制到新创建的共享。 共享中的文件夹结构应该是：`\\<ComputerName>\AzSBackups\MASBackup\<BackupID>\`。

最后，将解密证书 (.pfx) 复制到的证书目录：`C:\CloudDeployment\Setup\Certificates\`到该文件重命名`BackupDecryptionCert.pfx`。

## <a name="deploy-the-asdk-in-cloud-recovery-mode"></a>在云恢复模式下部署 ASDK

> [!IMPORTANT]
> 1. 当前安装程序 UI 仅支持加密密钥。 你只能验证来自系统继续使用加密密钥的备份。 如果备份位于集成的系统或 ASDK 使用证书加密，则必须使用 PowerShell 安装程序 (**下面的 InstallAzureStackPOC.ps1**)。 
> 2. PowerShell 安装程序 (**下面的 InstallAzureStackPOC.ps1**) 支持加密密钥或证书。
> 3. ASDK 安装支持使用一个网络接口卡 (NIC) 进行网络连接。 如果有多个 NIC，请确保只启用一个（所有其他 NIC 均禁用），然后再运行部署脚本。

### <a name="use-the-installer-ui-to-deploy-the-asdk-in-recovery-mode"></a>使用安装程序 UI 将在恢复模式下 ASDK
在本部分中的步骤说明如何使用图形用户界面 (GUI) 通过下载并运行部署 ASDK **asdk-installer.ps1** PowerShell 脚本。

> [!NOTE]
> Azure Stack 开发工具包的安装程序用户界面是一种基于 WCF 和 PowerShell 的开源脚本。

> [!IMPORTANT]
> 当前安装程序 UI 仅支持加密密钥。

1. 在主机成功启动到 CloudBuilder.vhdx 映像后，使用管理员凭据登录指定当您[准备开发工具包主机](asdk-prepare-host.md)用于 ASDK 安装。 此凭据应与开发工具包主机本地管理员凭据相同。
2. 打开提升的 PowerShell 控制台并运行**&lt;驱动器号 > \AzureStack_Installer\asdk-installer.ps1** PowerShell 脚本。 该脚本现在可能 CloudBuilder.vhdx 映像中除 C:\ 以外的驱动器上。 单击“**恢复**”。

    ![ASDK 安装程序脚本](media/asdk-validate-backup/1.PNG) 

3. 在标识提供程序和凭据页上将 ASDK 主机计算机输入 Azure AD 目录信息 （可选） 和本地管理员密码。 单击“下一步”。

    ![标识和凭据页](media/asdk-validate-backup/2.PNG) 

4. 选择要使用 ASDK 主机上，然后单击网络适配器**下一步**。 在 ASDK 安装过程中，将禁用所有其他网络接口。 

    ![网络适配器接口](media/asdk-validate-backup/3.PNG) 

5. 在网络配置页上，提供有效的时间服务器和 DNS 转发器 IP 地址。 单击“下一步”。

    ![网络配置页](media/asdk-validate-backup/4.PNG) 

6. 验证网络接口卡属性后，单击**下一步**。 

    ![网络卡设置验证](media/asdk-validate-backup/5.PNG) 

7. 提供所需的信息中所述[先决条件部分](#prereqs)备份设置页的用户名和密码要用来对其进行访问。 单击“下一步”：  

   ![备份设置页](media/asdk-validate-backup/6.PNG) 

8. 查看要用于部署 ASDK 摘要页上的部署脚本。 单击**部署**以开始部署。 

    ![“摘要”页](media/asdk-validate-backup/7.PNG) 


### <a name="use-powershell-to-deploy-the-asdk-in-recovery-mode"></a>使用 PowerShell 部署 ASDK 处于恢复模式

针对环境修改以下 PowerShell 命令，然后运行它们，以便在云恢复模式下部署 ASDK：

**使用下面的 InstallAzureStackPOC.ps1 脚本启动云恢复使用加密密钥。**

```powershell
cd C:\CloudDeployment\Setup     
$adminpass = Read-Host -AsSecureString -Prompt "Local Administrator password"
$certPass = Read-Host -AsSecureString -Prompt "Password for the external certificate"
$backupstorecredential = Read-Host -AsSecureString -Prompt "Credential for backup share"
$key = Read-Host -AsSecureString -Prompt "Your backup encryption key"

.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass `
 -BackupStorePath ("\\" + $env:COMPUTERNAME + "\AzSBackups") `
 -BackupEncryptionKeyBase64 $key `
 -BackupStoreCredential $backupstorecredential `
 -BackupId "<Backup ID to restore>" `
 -TimeServer "<Valid time server IP>" -ExternalCertPassword $certPass
```

**使用下面的 InstallAzureStackPOC.ps1 脚本启动云恢复解密证书。**

```powershell
cd C:\CloudDeployment\Setup     
$adminpass = Read-Host -AsSecureString -Prompt "Local Administrator password"
$certPass = Read-Host -AsSecureString -Prompt "Password for the external certificate"
$backupstorecredential = Read-Host -AsSecureString -Prompt "Credential for backup share"
$decryptioncertpassword  = Read-Host -AsSecureString -Prompt "Password for the decryption certificate"

.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass `
 -BackupStorePath ("\\" + $env:COMPUTERNAME + "\AzSBackups") `
 -BackupDecryptionCertPassword $decryptioncertpassword `
 -BackupStoreCredential $backupstorecredential `
 -BackupId "<Backup ID to restore>" `
 -TimeServer "<Valid time server IP>" -ExternalCertPassword $certPass
```

## <a name="complete-cloud-recovery"></a>完整的云恢复 
成功地进行云恢复部署以后，需使用 **Restore-AzureStack** cmdlet 完成还原操作。 

Azure Stack 操作员在登录后[安装 Azure Stack PowerShell](asdk-post-deploy.md#install-azure-stack-powershell)并运行以下命令，以指定的证书和从备份还原时使用的密码：

**使用证书文件的恢复模式**

> [!NOTE] 
> Azure Stack 部署不会保留出于安全原因的解密证书。 你将需要再次提供的解密证书和关联的密码。

```powershell
$decryptioncertpassword = Read-Host -AsSecureString -Prompt "Password for the decryption certificate"
Restore-AzsBackup -ResourceId "<BackupID>" `
 -DecryptionCertPath "<path to decryption certificate with file name (.pfx)>" `
 -DecryptionCertPassword $decryptioncertpassword
```

**恢复模式下使用加密密钥**
```powershell
$decryptioncertpassword = Read-Host -AsSecureString -Prompt "Password for the decryption certificate"
Restore-AzsBackup -ResourceId "<BackupID>"
```

调用此 cmdlet 后，先等待 60 分钟，然后开始在进行云恢复的 ASDK 上验证备份数据。

## <a name="next-steps"></a>后续步骤
[注册 Azure Stack](asdk-register.md)

