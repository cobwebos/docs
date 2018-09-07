---
title: 验证使用 ASDK 的 Azure Stack 备份 |Microsoft Docs
description: 如何验证使用 ASDK 的 Azure Stack integerated 系统备份。
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 09/05/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: 6fa3ba36dca45d5b99c6b5f2ba24367bcd077024
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2018
ms.locfileid: "44028814"
---
# <a name="use-the-asdk-to-validate-an-azure-stack-backup"></a>使用 ASDK 来验证 Azure Stack 备份
部署 Azure Stack 并预配用户资源，例如产品/服务、 计划、 配额和订阅之后，您应该[启用 Azure Stack 基础结构备份](..\azure-stack-backup-enable-backup-console.md)。 计划和运行正则基础结构备份会确保基础结构管理数据不丢失如果灾难性的硬件或服务失败。

> [!TIP]
> 我们建议您[运行按需备份](..\azure-stack-backup-back-up-azure-stack.md)之前开始此过程以确保拥有最新可用的 infrastrcuture 数据的副本。 请确保已成功完成备份后捕获备份 ID。 在云恢复期间，都会要求提供此 ID。 

Azure Stack 基础结构备份包含有关你可以在重新部署 Azure Stack 期间还原的云的重要数据。 可以使用 ASDK 来验证这些备份，而不会影响生产云。 

验证在 ASDK 上的备份支持以下方案：

|场景|目的|
|-----|-----|
|验证从集成解决方案的基础结构备份。|短生存期的验证备份中的数据有效。|
|了解端到端恢复工作流。|使用 ASDK 来验证整个备份和还原体验。|
|     |     |

下面的方案**不是**时验证备份在 ASDK 上的支持：

|场景|目的|
|-----|-----|
|ASDK 生成来生成备份和还原。|从以前版本的 ASDK 的备份数据还原到较新版本。|
|     |     |


## <a name="cloud-recovery-deployment"></a>云恢复部署
从集成的系统部署的基础结构备份可通过执行云恢复部署 ASDK 的验证。 在部署此类型，特定的服务数据从备份中还原主计算机上安装 ASDK 之后。



### <a name="cloud-recovery-prerequisites"></a>云恢复的先决条件
在开始之前 ASDK 的云恢复部署，请确保具有以下信息：

|先决条件|说明|
|-----|-----|
|备份共享路径。|将用于恢复 Azure Stack 基础结构信息的最新的 Azure Stack 备份 UNC 文件共享路径。 将云恢复部署过程中创建此本地共享。|
|备份加密密钥。|用于计划要运行使用 Azure Stack 管理门户的基础结构备份加密密钥。|
|若要还原的备份 ID。|"Xxxxxxxx xxxx-xxxx-xxxx-在左右加上"，字母数字窗体中的备份 ID，它标识要在云恢复期间还原的备份。|
|时间服务器 IP。|有效的时间服务器 IP，例如 132.163.97.2，是 Azure Stack 部署所必需的。|
|外部证书的密码。|使用 Azure Stack 的外部证书的密码。 CA 备份包含需要使用此密码还原的外部证书。|
|     |     | 

## <a name="prepare-the-host-computer"></a>准备主机 
如下所示的普通 ASDK 部署中，必须安装准备 ASDK 主机系统环境。 准备好开发工具包主机之后，该主机会从 CloudBuilder.vhdx 虚拟机硬盘启动，以开始进行 ASDK 部署。

ASDK 主机计算机上下载新 cloudbuilder.vhdx 对应于相同版本的 Azure Stack 备份，并按照说明[准备 asdk 主机](asdk-prepare-host.md)。

在主机服务器重启从 cloudbuilder.vhdx 后，必须创建文件共享并复制到你的备份数据。 文件共享应可访问的帐户运行安装程序;在这些示例 PowerShell 命令中的管理员： 

```powershell
$shares = New-Item -Path "c:\" -Name "Shares" -ItemType "directory"
$azsbackupshare = New-Item -Path $shares.FullName -Name "AzSBackups" -ItemType "directory"
New-SmbShare -Path $azsbackupshare.FullName -FullAccess ($env:computername + "\Administrator")  -Name "AzSBackups"
```

接下来，将最新的 Azure Stack 备份文件复制到新创建的共享。 在共享内的文件夹结构应为： `\\<ComputerName>\AzSBackups\MASBackup\<BackupID>\`。

## <a name="deploy-the-asdk-in-cloud-recovery-mode"></a>部署在云恢复模式下 ASDK
**下面的 InstallAzureStackPOC.ps1**脚本用于启动云恢复。 

> [!IMPORTANT]
> ASDK 安装支持使用一个网络接口卡 (NIC) 进行网络连接。 如果有多个 NIC，请确保只启用一个（所有其他 NIC 均禁用），然后再运行部署脚本。

修改你的环境的以下 PowerShell 命令，并运行它们以在云恢复模式下 ASDK 部署：

```powershell
cd C:\CloudDeployment\Setup     
$adminPass = Get-Credential Administrator
$key = ConvertTo-SecureString "<Your backup encryption key>" -AsPlainText -Force ` 
$certPass = Read-Host -AsSecureString  

.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -BackupStorePath ("\\" + $env:COMPUTERNAME + "\AzSBackups") `
-BackupEncryptionKeyBase64 $key -BackupStoreCredential $adminPass -BackupId "<Backup ID to restore>" `
-TimeServer "<Valid time server IP>" -ExternalCertPassword $certPass
```

## <a name="restore-infrastructure-data-from-backup"></a>从备份中还原基础结构数据
成功的云恢复完成部署后，需要先完成还原使用**还原 AzureStack** cmdlet。 

Azure Stack 操作员在登录后[安装 Azure Stack PowerShell](asdk-post-deploy.md#install-azure-stack-powershell)然后，替换为你备份的 ID 和`Name`参数，运行以下命令：

```powershell
Restore-AzsBackup -Name "<BackupID>"
```
等待 60 分钟后调用此 cmdlet 可在云上启动的备份数据验证恢复 ASDK。

## <a name="next-steps"></a>后续步骤
[注册 Azure Stack](asdk-register.md)

