---
title: 启用对 Azure 文件共享的 AD DS 身份验证
description: 了解如何为 Azure 文件共享启用通过 SMB 进行 Active Directory 域服务身份验证。 然后，已加入域的 Windows 虚拟机可以使用 AD DS 凭据访问 Azure 文件共享。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 09/13/2020
ms.author: rogarana
ms.openlocfilehash: d77abe1f69aff416b5fc446d8fdc844bda64d35b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320297"
---
# <a name="part-one-enable-ad-ds-authentication-for-your-azure-file-shares"></a>第一部分：为 Azure 文件共享启用 AD DS 身份验证 

在启用 Active Directory 域服务 (AD DS) 身份验证之前，请确保已阅读 [概述文章](storage-files-identity-auth-active-directory-enable.md) 以了解支持的方案和要求。

本文介绍了在存储帐户上启用 Active Directory 域服务 (AD DS) 身份验证所需的过程。 启用该功能后，必须配置存储帐户和 AD DS，才能使用 AD DS 凭据对 Azure 文件共享进行身份验证。 若要为 Azure 文件共享启用通过 SMB 进行 AD DS 身份验证，需要向 AD DS 注册存储帐户，然后在存储帐户上设置所需的域属性。

若要将存储帐户注册到 AD DS，请在 AD DS 中创建表示该帐户的帐户。 可以将此过程视为类似于在 AD DS 中创建表示本地 Windows 文件服务器的帐户。 在存储帐户上启用此功能后，它将应用于帐户中的所有新的和现有的文件共享。

## <a name="option-one-recommended-use-azfileshybrid-powershell-module"></a>选项一 (建议) ：使用 AzFilesHybrid PowerShell 模块

AzFilesHybrid PowerShell 模块中的 cmdlet 进行必要的修改，并为你启用此功能。 由于一些 cmdlet 部分与本地 AD DS 交互，我们将介绍 cmdlet 的作用，因此，你可以确定更改是否符合你的符合性和安全策略，并确保你具有执行 cmdlet 的适当权限。 尽管我们建议使用 AzFilesHybrid 模块，但如果无法执行此操作，我们将提供这些步骤，以便你可以手动执行这些步骤。

### <a name="download-azfileshybrid-module"></a>下载 AzFilesHybrid 模块

- [下载并解压缩 AzFilesHybrid 模块 (GA 模块： v 为 0.2.0 +) ](https://github.com/Azure-Samples/azure-files-samples/releases) 请注意，v 0.2.2 或更高版本支持 AES 256 kerberos 加密。 如果已启用 AzFilesHybrid 版本低于 v 0.2.2 的功能，并且想要更新以支持 AES 256 Kerberos 加密，请参阅 [此文](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems#azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption)。 
- 使用有权在目标 AD 中创建服务登录帐户或计算机帐户的 AD DS 凭据在加入本地 AD DS 域的设备中安装和执行模块。
-  使用同步到 Azure AD 的本地 AD DS 凭据运行该脚本。 本地 AD DS 凭据必须具有存储帐户所有者或参与者 Azure 角色权限。

### <a name="run-join-azstorageaccountforauth"></a>运行 AzStorageAccountForAuth

`Join-AzStorageAccountForAuth`Cmdlet 代表指定的存储帐户执行与脱机域联接等效的操作。 此脚本使用 cmdlet 在 AD 域中创建 [计算机帐户](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) 。 如果出于任何原因而无法使用计算机帐户，则可以改为改为创建 [服务登录帐户](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) 。 如果你选择手动运行命令，则应选择最适合你的环境的帐户。

Cmdlet 创建的 AD DS 帐户表示存储帐户。 如果在强制密码过期的组织单位 (OU 下创建 AD DS 帐户) ，则必须在密码最长期限前更新密码。 在该日期之前未能更新帐户密码会导致访问 Azure 文件共享时的身份验证失败。 若要了解如何更新密码，请参阅 [更新 AD DS 帐户密码](storage-files-identity-ad-ds-update-password.md)。

在 PowerShell 中执行占位符值之前，请在下面的参数中将其替换为自己的值。
> [!IMPORTANT]
> 域加入 cmdlet 将创建一个 AD 帐户来表示 AD 中 (文件共享) 的存储帐户。 你可以选择注册为计算机帐户或服务登录帐户，有关详细信息，请参阅 [常见问题解答](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) 。 对于计算机帐户，AD 中的默认密码过期期限设置为30天。 同样，服务登录帐户可能在 AD 域或组织单位 (OU) 上设置了默认密码过期期限。
> 对于这两种帐户类型，我们建议你检查 AD 环境中配置的密码过期期限，并计划在最长密码期限之前更新 AD 帐户的 [存储帐户标识的密码](storage-files-identity-ad-ds-update-password.md) 。 可以考虑 [在 ad 中创建新的 Ad 组织单位 (OU) ](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit?view=win10-ps) ，并相应地在 [计算机帐户](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)?redirectedfrom=MSDN) 或服务登录帐户上禁用密码过期策略。 

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer Azure role assignment
Connect-AzAccount

#Define parameters
$SubscriptionId = "<your-subscription-id-here>"
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId $SubscriptionId 

# Register the target storage account with your active directory environment under the target OU (for example: specify the OU with Name as "UserAccounts" or DistinguishedName as "OU=UserAccounts,DC=CONTOSO,DC=COM"). 
# You can use to this PowerShell cmdlet: Get-ADOrganizationalUnit to find the Name and DistinguishedName of your target OU. If you are using the OU Name, specify it with -OrganizationalUnitName as shown below. If you are using the OU DistinguishedName, you can set it with -OrganizationalUnitDistinguishedName. You can choose to provide one of the two names to specify the target OU.
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account (default parameter value), depends on the AD permission you have and preference. 
# Run Get-Help Join-AzStorageAccountForAuth for more details on this cmdlet.

Join-AzStorageAccountForAuth `
        -ResourceGroupName $ResourceGroupName `
        -StorageAccountName $StorageAccountName `
        -DomainAccountType "<ComputerAccount|ServiceLogonAccount>" # Default is set as ComputerAccount
        -OrganizationalUnitDistinguishedName "<ou-distinguishedname-here>" # If you don't provide the OU name as an input parameter, the AD identity that represents the storage account is created under the root directory.
        -EncryptionType "<AES,RC4/AES/RC4>" # Specify the encryption agorithm used for Kerberos authentication. Default is configured as "'RC4','AES256'" which supports both 'RC4' and 'AES256' encryption.

#Run the command below if you want to enable AES 256 authentication. If you plan to use RC4, you can skip this step.
Update-AzStorageAccountSetupForAES256 -ResourceGroupName $ResourceGroupName -StorageAccountName $StorageAccountName

#You can run the Debug-AzStorageAccountAuth cmdlet to conduct a set of basic checks on your AD configuration with the logged on AD user. This cmdlet is supported on AzFilesHybrid v0.1.2+ version. For more details on the checks performed in this cmdlet, see Azure Files Windows troubleshooting guide.
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```

## <a name="option-2-manually-perform-the-enablement-actions"></a>选项2：手动执行启用操作

如果已 `Join-AzStorageAccountForAuth` 成功执行上述脚本，请参阅 [确认启用此功能](#confirm-the-feature-is-enabled) 部分。 不需要执行以下手动步骤。

### <a name="checking-environment"></a>检查环境

首先，您必须检查您的环境的状态。 具体而言，必须检查是否安装了 [Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) ，以及是否正在用管理员权限执行 shell。 然后查看是否已安装 [Az.Storage 2.0 module](https://www.powershellgallery.com/packages/Az.Storage/2.0.0)，如果未安装，请立即安装。 完成这些检查后，请检查你的 AD DS，查看是否存在已使用 SPN/UPN 创建的 [计算机帐户](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (默认) 或 [服务登录帐户](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) ，该帐户是否为 "cifs/你的"。 如果该帐户不存在，请创建一个，如下一节中所述。

### <a name="creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>手动创建代表 AD 中的存储帐户的标识

若要手动创建此帐户，请为你的存储帐户创建新的 Kerberos 密钥。 然后，将该 Kerberos 密钥用作你的帐户的密码，使用下面的 PowerShell cmdlet。 此密钥仅在安装过程中使用，不能用于针对存储帐户的任何控制或数据平面操作。 

```PowerShell
# Create the Kerberos key on the storage account and get the Kerb1 key as the password for the AD identity to represent the storage account
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

New-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -KeyName kerb1
Get-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -ListKerbKey | where-object{$_.Keyname -contains "kerb1"}
```

获得该密钥后，请在 OU 下创建服务或计算机帐户。 使用以下规范 (记得将示例文本替换为你的存储帐户名称) ：

SPN： "cifs/你的----name-name-net.tcp" Password：存储帐户的 Kerberos 密钥。

如果你的 OU 强制密码过期，则必须在最长密码期限之前更新密码，以防止访问 Azure 文件共享时的身份验证失败。 有关详细信息，请参阅 [更新 AD 中的存储帐户标识的密码](storage-files-identity-ad-ds-update-password.md) 。

保留新创建的标识的 SID，下一步需要用到它。 你创建的表示存储帐户的标识无需同步到 Azure AD。

### <a name="enable-the-feature-on-your-storage-account"></a>在存储帐户上启用该功能

现在，你可以在存储帐户上启用此功能。 在以下命令中为域属性提供一些配置详细信息，然后运行该命令。 以下命令中所需的存储帐户 SID 是在 [上一节的](#creating-an-identity-representing-the-storage-account-in-your-ad-manually)AD DS 中创建的标识的 sid。

```PowerShell
# Set the feature flag on the target storage account and provide the required AD domain information
Set-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>" `
        -EnableActiveDirectoryDomainServicesForFile $true `
        -ActiveDirectoryDomainName "<your-domain-name-here>" `
        -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" `
        -ActiveDirectoryForestName "<your-forest-name-here>" `
        -ActiveDirectoryDomainGuid "<your-guid-here>" `
        -ActiveDirectoryDomainsid "<your-domain-sid-here>" `
        -ActiveDirectoryAzureStorageSid "<your-storage-account-sid>"
```

### <a name="debugging"></a>调试

你可以运行 AzStorageAccountAuth cmdlet，以使用已登录的 AD 用户对 AD 配置执行一组基本检查。 AzFilesHybrid v0.1.2+ 版本支持此 cmdlet。 有关此 cmdlet 中执行的检查的详细信息，请参阅 Windows 故障排除指南中的 [无法装载 Azure 文件和 AD 凭据](storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials) 。

```PowerShell
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```

## <a name="confirm-the-feature-is-enabled"></a>确认已启用该功能

你可以通过以下脚本检查以确认是否已在存储帐户上启用了此功能：

```PowerShell
# Get the target storage account
$storageaccount = Get-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>"

# List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

# List the directory domain information if the storage account has enabled AD DS authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

## <a name="next-steps"></a>后续步骤

你现在已成功在你的存储帐户上启用了该功能。 若要使用该功能，必须分配共享级权限。 转到下一节。

[第二部分：向标识分配共享级权限](storage-files-identity-ad-ds-assign-permissions.md)