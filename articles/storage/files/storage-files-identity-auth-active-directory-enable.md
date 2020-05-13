---
title: 启用 Azure 文件的通过 SMB 进行 Active Directory 身份验证
description: 了解如何通过 Active Directory 通过 SMB 为 Azure 文件共享启用基于身份的身份验证。 然后，已加入域的 Windows 虚拟机（Vm）可以使用 AD 凭据访问 Azure 文件共享。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: rogarana
ms.openlocfilehash: febb796a47b9f5e78906d513c115b62b35c7c7d5
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196505"
---
# <a name="enable-on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>启用 Azure 文件共享的通过 SMB 进行本地 Active Directory 域服务身份验证

[Azure 文件](storage-files-introduction.md)  支持通过以下两种类型的域服务通过服务器消息块（SMB）进行基于身份的身份验证： Azure Active Directory 域服务（Azure AD DS）和本地 Active Directory 域服务（AD DS）（预览版）。 本文重点介绍利用 Active Directory 域 Service 对 Azure 文件共享进行身份验证的新引入（预览版）支持。 如果你有兴趣为 Azure 文件共享启用 Azure AD DS （GA）身份验证，请参阅[主题中的文章](storage-files-identity-auth-active-directory-domain-service-enable.md)。

> [!NOTE]
> Azure 文件共享仅支持针对一个域服务进行身份验证，Azure Active Directory 域服务（Azure AD DS）或本地 Active Directory 域服务（AD DS）。 
>
> 用于 Azure 文件共享身份验证的 AD DS 标识必须同步到 Azure AD。 密码哈希同步是可选的。 
> 
> 本地 AD DS 身份验证不支持对 AD DS 中创建的计算机帐户进行身份验证。 
> 
> 只能对向其中注册存储帐户的一个 AD 林支持本地 AD DS 身份验证。 默认情况下，只能使用单个林中的 AD DS 凭据访问 Azure 文件共享。 如果需要从其他林访问 Azure 文件共享，请确保配置了正确的林信任，有关详细信息，请参阅[常见问题解答](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control)。  
> 
> Azure 文件同步管理的 Azure 文件共享支持 SMB 访问和 ACL 持久性 AD DS 身份验证。
>
> Azure 文件支持通过具有 RC4-HMAC 加密的 AD 进行 Kerberos 身份验证。 目前尚不支持 AES Kerberos 加密。

通过 SMB 为 Azure 文件共享启用 AD DS 时，AD DS 联接的计算机可以使用现有 AD 凭据装载 Azure 文件共享。 此功能可以通过本地的计算机托管 AD DS，也可以在 Azure 中托管。

用于访问 Azure 文件共享的标识必须同步到 Azure AD，才能通过[基于角色的访问控制（RBAC）](../../role-based-access-control/overview.md)模型来强制执行共享级别文件权限。 将保留并强制执行从现有文件服务器中执行的文件/目录上的[Windows 样式 dacl](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) 。 此功能提供与企业 AD DS 环境的无缝集成。 将本地文件服务器替换为 Azure 文件共享时，现有用户可以通过单一登录体验从当前客户端访问 Azure 文件共享，而无需更改所使用的凭据。  

> [!NOTE]
> 为了帮助你在一些常见用例中设置 Azure 文件 AD 身份验证，我们发布了[两个视频](https://docs.microsoft.com/azure/storage/files/storage-files-introduction#videos)，其中包含分步指导：
> - 将本地文件服务器替换为 Azure 文件（包括在文件和 AD 身份验证的专用链接上安装）
> - 使用 Azure 文件作为 Windows 虚拟机的配置文件容器（包括在 AD 身份验证和 FsLogix 配置上设置）

## <a name="prerequisites"></a>必备条件 

在为 Azure 文件共享启用 AD DS 身份验证之前，请确保已完成以下先决条件： 

- 选择或创建 AD DS 环境，并将[其同步到 Azure AD](../../active-directory/hybrid/how-to-connect-install-roadmap.md)。 

    可以在新的或现有的本地 AD DS 环境中启用该功能。 用于访问的标识必须同步到 Azure AD。 要访问的 Azure AD 租户和文件共享必须与相同的订阅相关联。 

    若要设置 AD 域环境，请参阅[Active Directory 域服务概述](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)。 如果尚未将 AD 同步到 Azure AD，请按照[Azure AD Connect 和 Azure AD Connect Health 安装路线图](../../active-directory/hybrid/how-to-connect-install-roadmap.md)中的指导配置和设置 Azure AD Connect。 

- 域-将本地计算机或 Azure VM 加入本地 AD DS。 

    若要通过使用计算机或 VM 中的 AD 凭据来访问文件共享，设备必须已加入域才能 AD DS。 有关如何加入域的信息，请参阅将[计算机加入域](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain)。 

- 选择或创建一个 Azure 存储帐户。  为了获得最佳性能，我们建议你将存储帐户部署到计划从中访问共享的 VM 所在的同一区域中。

    请确保尚未为包含文件共享的存储帐户配置 Azure AD DS 身份验证。 如果在存储帐户上启用了 Azure 文件 Azure AD DS 身份验证，则需要在更改后将其禁用，才能使用本地 AD DS。 这意味着，将需要重新配置在 Azure AD DS 环境中配置的现有 Acl，以实现适当的权限。
    
    有关创建新文件共享的信息，请参阅 [在 Azure 文件中创建文件共享](storage-how-to-create-file-share.md)。

- 通过使用存储帐户密钥装载 Azure 文件共享来验证连接性。 

    若要验证是否已正确配置设备和文件共享，请尝试使用存储帐户密钥[装载文件共享](storage-how-to-use-files-windows.md)。 如果在连接到 Azure 文件时遇到问题，请参阅在[Windows 上为 Azure 文件装载错误发布的疑难解答工具](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5)。 我们还提供了有关在阻止端口445时解决方案的[指南](https://docs.microsoft.com/azure/storage/files/storage-files-faq#on-premises-access)。 

## <a name="regional-availability"></a>区域可用性

使用 AD DS （预览版）的 Azure 文件身份验证在[所有公共区域和 Azure Gov 区域](https://azure.microsoft.com/global-infrastructure/locations/)中可用。

## <a name="overview"></a>概述

如果你计划启用文件共享上的任何网络配置，我们建议你先评估[网络注意事项](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview)，并先完成相关配置，然后再启用 AD DS 身份验证。

如果为 Azure 文件共享启用 AD DS 身份验证，则可以通过本地 AD DS 凭据对 Azure 文件共享进行身份验证。 此外，它还允许您更好地管理权限，以允许粒度访问控制。 这样做需要将标识从本地 AD DS 同步到 AD connect Azure AD。 使用本地 AD DS 凭据管理文件/共享级别访问时，可以使用同步到 Azure AD 的标识控制共享级别访问。

接下来，请按照以下步骤设置 Azure 文件以进行 AD DS 身份验证： 

1. [在存储帐户上启用 Azure 文件 AD DS 身份验证](#1-enable-ad-ds-authentication-for-your-account)

1. [将共享的访问权限分配给与目标 AD 标识同步的 Azure AD 标识（用户、组或服务主体）](#2-assign-access-permissions-to-an-identity)

1. [为目录和文件配置基于 SMB 的 Acl](#3-configure-ntfs-permissions-over-smb)
 
1. [将 Azure 文件共享装载到加入到 AD DS 的 VM](#4-mount-a-file-share-from-a-domain-joined-vm)

1. [更新中的存储帐户标识的密码 AD DS](#5-update-the-password-of-your-storage-account-identity-in-ad-ds)

下图说明了用于启用对 Azure 文件共享的 SMB 进行 Azure AD 身份验证的端到端工作流。 

![文件广告工作流关系图](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> 仅在版本低于 Windows 7 或 Windows Server 2008 R2 的操作系统版本上运行的计算机或 Vm 才支持针对 Azure 文件共享的 SMB 进行 AD DS 身份验证。 

## <a name="1-enable-ad-ds-authentication-for-your-account"></a>1为你的帐户启用 AD DS 身份验证 

若要启用对 Azure 文件共享的 SMB AD DS 身份验证，需要首先向 AD DS 注册存储帐户，然后在存储帐户上设置所需的域属性。 在存储帐户上启用此功能后，它将应用于帐户中的所有新的和现有的文件共享。 下载 AzFilesHybrid Powershell 模块，并使用 `join-AzStorageAccountForAuth` 启用该功能。 可以在本部分中的脚本中找到端到端工作流的详细说明。 

> [!IMPORTANT]
> `Join-AzStorageAccountForAuth`Cmdlet 将对 AD 环境进行修改。 阅读以下说明，以更好地了解要执行的操作，以确保您具有执行命令的适当权限，并且应用的更改符合合规性和安全策略。 

`Join-AzStorageAccountForAuth`Cmdlet 将代表指定的存储帐户执行与脱机域联接等效的操作。 此脚本使用 cmdlet 在 AD 域（[计算机帐户](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory)（默认）或[服务登录帐户](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts)）中创建帐户。 如果你选择手动执行此操作，则应选择最适合你的环境的帐户。

Cmdlet 创建的 AD DS 帐户表示 AD 域中的存储帐户。 如果 AD DS 帐户是在强制密码过期的组织单位（OU）下创建的，则必须在最长密码期限前更新密码。 未能更新帐户密码将导致访问 Azure 文件共享时的身份验证失败。 若要了解如何更新密码，请参阅[更新 AD DS 帐户密码](#5-update-the-password-of-your-storage-account-identity-in-ad-ds)。

您可以使用以下脚本来执行注册并启用该功能，也可以手动执行脚本所需的操作。 脚本后面的部分介绍了这些操作。 不需要同时执行这两项操作。

### <a name="11-script-prerequisites"></a>1.1 脚本先决条件
- [下载并解压缩 AzFilesHybrid 模块](https://github.com/Azure-Samples/azure-files-samples/releases)
- 使用有权在目标 AD 中创建服务登录帐户或计算机帐户的 AD DS 凭据在加入本地 AD DS 域的设备中安装和执行模块。
-  使用同步到 Azure AD 的本地 AD DS 凭据运行该脚本。 本地 AD DS 凭据必须具有存储帐户所有者或参与者 RBAC 角色权限。
- 请确保存储帐户位于[受支持的区域](#regional-availability)。

### <a name="12-domain-join-your-storage-account"></a>1.2 域加入存储帐户
请记住，在下面的参数中执行占位符值，然后在 PowerShell 中执行它。
> [!IMPORTANT]
> 域加入 cmdlet 将创建一个 AD 帐户来表示 AD 中的存储帐户（文件共享）。 你可以选择注册为计算机帐户或服务登录帐户，有关详细信息，请参阅[常见问题解答](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control)。 对于计算机帐户，AD 中的默认密码过期期限设置为30天。 同样，服务登录帐户在 AD 域或组织单位（OU）上可能设置了默认密码过期期限。
> 对于这两种帐户类型，我们强烈建议你检查 AD 环境中配置的密码过期期限，并计划将下面 AD 帐户[ad 中的存储帐户标识的密码更新](#5-update-the-password-of-your-storage-account-identity-in-ad-ds)到最长密码期限之前。 可以考虑[在 ad 中创建新的 Ad 组织单位（OU）](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit?view=win10-ps) ，并相应地在[计算机帐户](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)?redirectedfrom=MSDN)或服务登录帐户上禁用密码过期策略。 

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer RBAC assignment
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
# You can run Get-Help Join-AzStorageAccountForAuth to find more details on this cmdlet.

Join-AzStorageAccountForAuth `
        -ResourceGroupName $ResourceGroupName `
        -Name $StorageAccountName `
        -DomainAccountType "<ComputerAccount|ServiceLogonAccount>" `
        -OrganizationalUnitName "<ou-name-here>" #You can also use -OrganizationalUnitDistinguishedName "<ou-distinguishedname-here>" instead. If you don't provide the OU name as an input parameter, the AD identity that represents the storage account will be created under the root directory.

#You can run the Debug-AzStorageAccountAuth cmdlet to conduct a set of basic checks on your AD configuration with the logged on AD user. This cmdlet is supported on AzFilesHybrid v0.1.2+ version. For more details on the checks performed in this cmdlet, go to Azure Files FAQ.
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose

```

以下说明汇总了执行 cmdlet 时执行的所有操作 `Join-AzStorageAccountForAuth` 。 如果不想使用命令，则可以手动执行这些步骤：

> [!NOTE]
> 如果已 `Join-AzStorageAccountForAuth` 成功执行上述脚本，请参阅下一节 "1.3 确认已启用此功能"。 不需要再次执行以下操作。

#### <a name="a-checking-environment"></a>a. 检查环境

首先，该脚本会检查您的环境。 具体来说，它会检查是否安装了[Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) ，以及是否以管理员权限执行了外壳。 然后，它会检查[Az 1.11.1 模块](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview)是否已安装，如果不是，则安装该模块。 如果这些检查通过，则它将检查你的 AD DS，以确定是否存在已使用 SPN/UPN 创建的[计算机帐户](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory)（默认）或[服务登录帐户](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts)是否为 "cifs/您的存储帐户名称- 如果该帐户不存在，它将创建一个，如下面的 b 部分所述。

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. 手动创建代表 AD 中的存储帐户的标识

若要手动创建此帐户，请使用为存储帐户创建新的 Kerberos 密钥 `New-AzStorageAccountKey -KeyName kerb1` 。 然后，将该 Kerberos 密钥用作你的帐户的密码。 此密钥仅在安装过程中使用，不能用于针对存储帐户的任何控制或数据平面操作。

获得该密钥后，请在 OU 下创建服务或计算机帐户。 为你的存储帐户使用以下规范： SPN： "cifs/你的---name-name-name-name-name： Kerberos 密钥"。

如果你的 OU 强制密码过期，则必须在最长密码期限之前更新密码，以防止访问 Azure 文件共享时的身份验证失败。 有关详细信息，请参阅[中的更新存储帐户标识的密码 AD DS](#5-update-the-password-of-your-storage-account-identity-in-ad-ds) 。

保留新创建的帐户的 SID，下一步需要用到它。 你创建的表示存储帐户的标识无需同步到 Azure AD。

##### <a name="c-enable-the-feature-on-your-storage-account"></a>c. 在存储帐户上启用该功能

然后，该脚本将在存储帐户上启用该功能。 若要手动执行此设置，请在以下命令中为域属性提供一些配置详细信息，然后运行该命令。 以下命令中所需的存储帐户 SID 是在[上一节的](#b-creating-an-identity-representing-the-storage-account-in-your-ad-manually)AD DS 中创建的标识的 sid。

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


### <a name="13-confirm-that-the-feature-is-enabled"></a>1.3 确认已启用该功能

你可以检查以确认是否已在存储帐户上启用此功能，你可以使用以下脚本：

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

你现在已成功在你的存储帐户上启用了该功能。 启用此功能后，必须执行更多步骤才能使用此功能。

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

你现在已成功启用通过 SMB AD DS 身份验证，并分配了自定义角色，该角色提供对具有 AD DS 标识的 Azure 文件共享的访问权限。 若要向其他用户授予对文件共享的访问权限，请按照 "[分配访问权限](#2-assign-access-permissions-to-an-identity)" 中的说明，使用一个标识，并在[SMB 部分配置 NTFS 权限](#3-configure-ntfs-permissions-over-smb)。

## <a name="5-update-the-password-of-your-storage-account-identity-in-ad-ds"></a>5更新存储帐户标识的密码 AD DS

如果在强制密码过期时间的 OU 下注册了表示存储帐户的 AD DS 标识/帐户，则必须在密码最长期限前轮替密码。 未能更新 AD DS 帐户的密码将导致身份验证失败，无法访问 Azure 文件共享。  

若要触发密码轮换，可以 `Update-AzStorageAccountADObjectPassword` 从 AzFilesHybrid 模块运行命令。 该 cmdlet 将执行类似于存储帐户密钥轮换的操作。 它将获取存储帐户的第二个 Kerberos 密钥，并使用它来更新 AD DS 中注册帐户的密码。 然后，它将重新生成存储帐户的目标 Kerberos 密钥，并在 AD DS 中更新已注册帐户的密码。 必须在已加入域 AD DS 域的本地环境中运行此 cmdlet。

```PowerShell
# Update the password of the AD DS account registered for the storage account
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```

## <a name="next-steps"></a>后续步骤

有关 Azure 文件以及如何使用 AD over SMB 的详细信息，请参阅以下资源：

- [适用于 SMB 访问的 Azure 文件基于标识的身份验证支持概述](storage-files-active-directory-overview.md)
- [常见问题解答](storage-files-faq.md)
