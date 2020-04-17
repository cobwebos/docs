---
title: 通过 SMB 为 Azure 文件启用活动目录身份验证
description: 了解如何通过活动目录通过 SMB 为 Azure 文件共享启用基于身份的身份验证。 然后，加入域的 Windows 虚拟机 （VM） 可以使用 AD 凭据访问 Azure 文件共享。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: rogarana
ms.openlocfilehash: 8d1e1262c592f0120b191e18a5c16b97b887a6a2
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536518"
---
# <a name="enable-on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>通过 SMB 为 Azure 文件共享启用本地活动目录域服务身份验证

[Azure 文件](storage-files-introduction.md) 通过两种类型的域服务支持通过服务器消息块 （SMB） 进行基于身份的身份验证：Azure 活动目录域服务 （Azure AD DS） 和本地活动目录域服务 （AD DS）（预览）。 本文重点介绍新引入的（预览）支持利用 Active 目录域服务对 Azure 文件共享进行身份验证。 如果您有兴趣为 Azure 文件共享启用 Azure AD DS （GA） 身份验证，请参阅[我们有关主题的文章](storage-files-identity-auth-active-directory-domain-service-enable.md)。

> [!NOTE]
> Azure 文件仅共享支持针对一个域服务的身份验证，即 Azure 活动目录域服务 （Azure AD DS） 或本地活动目录域服务 （AD DS）。 
>
> 用于 Azure 文件共享身份验证的 AD DS 标识必须同步到 Azure AD。 密码哈希同步是可选的。 
> 
> 本地 AD DS 身份验证不支持对 AD DS 中创建的计算机帐户进行身份验证。 
> 
> 本地 AD DS 身份验证只能针对存储帐户注册的一个 AD 林进行支持。 默认情况下，您只能从单个林访问具有 AD DS 凭据的 Azure 文件共享。 如果需要从其他林访问 Azure 文件共享，请确保配置了正确的林信任，有关详细信息，请参阅[常见问题解答](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control)。  
> 
> 对于 Azure 文件同步管理的 Azure 文件共享，支持针对 SMB 访问和 ACL 持久性的 AD DS 身份验证。
>
> Azure 文件支持使用 RC4-HMAC 加密的 AD 进行 Kerberos 身份验证。 AES Kerberos 加密尚不受支持。

通过 SMB 为 Azure 文件共享启用 AD DS 时，AD DS 联接的计算机可以使用现有的 AD 凭据装载 Azure 文件共享。 此功能可以通过托管在预处理计算机或 Azure 中托管的 AD DS 环境启用。

用于访问 Azure 文件共享的标识必须同步到 Azure AD，以便通过[基于角色的访问控制 （RBAC）](../../role-based-access-control/overview.md)模型强制实施共享级别文件权限。 将保留并强制执行从现有文件服务器结转的文件/目录上的[Windows 样式 DACL。](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) 此功能提供与企业 AD DS 环境的无缝集成。 当您使用 Azure 文件共享替换上置前文件服务器时，现有用户可以通过单个登录体验从其当前客户端访问 Azure 文件共享，而无需对正在使用的凭据进行任何更改。  

> [!NOTE]
> 为了帮助您为某些常见用例设置 Azure 文件 AD 身份验证，我们发布了[两个视频](https://docs.microsoft.com/azure/storage/files/storage-files-introduction#videos)，并逐步指导：
> - 将本地文件服务器替换为 Azure 文件（包括在文件和 AD 身份验证的专用链路上设置）
> - 使用 Azure 文件作为 Windows 虚拟桌面的配置文件容器（包括 AD 身份验证和 FSLogix 配置的设置）

## <a name="prerequisites"></a>先决条件 

在为 Azure 文件共享启用 AD DS 身份验证之前，请确保已完成以下先决条件： 

- 选择或创建 AD DS 环境[并将其同步到 Azure AD](../../active-directory/hybrid/how-to-connect-install-roadmap.md)。 

    您可以在新的或现有的本地 AD DS 环境中启用该功能。 用于访问的身份必须同步到 Azure AD。 Azure AD 租户和要访问的文件共享必须与同一订阅关联。 

    要设置 AD 域环境，请参阅[活动目录域服务概述](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)。 如果 AD 尚未将 AD 同步到 Azure AD，请按照[Azure AD 连接和 Azure AD 连接运行状况安装路线图](../../active-directory/hybrid/how-to-connect-install-roadmap.md)中的指南来配置和设置 Azure AD 连接。 

- 域将本地计算机或 Azure VM 连接到本地 AD DS。 

    要使用计算机或 VM 中的 AD 凭据访问文件共享，您的设备必须加入 AD DS。 有关如何加入域的信息，请参阅[将计算机加入域](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain)。 

- 在[受支持的区域](#regional-availability)中选择或创建 Azure 存储帐户。 

    确保包含文件共享的存储帐户尚未配置为 Azure AD DS 身份验证。 如果在存储帐户上启用 Azure 文件 Azure AD DS 身份验证，则需要在更改为使用本地 AD DS 之前禁用该身份验证。 这意味着需要在 Azure AD DS 环境中配置的现有 ACL 进行重新配置，以便进行适当的权限实施。
    
    有关创建新文件共享的信息，请参阅 [在 Azure 文件 中创建文件共享](storage-how-to-create-file-share.md)。
    
    为了获得最佳性能，我们建议您将存储帐户部署在同一区域中，以便从哪个 VM 访问共享。 

- 通过使用存储帐户密钥安装 Azure 文件共享来验证连接。 

    要验证您的设备和文件共享是否已正确配置，请尝试使用存储帐户密钥安装文件共享。 有关详细信息，请参阅使用 [Windows 的 Azure 文件共享](storage-how-to-use-files-windows.md)。

## <a name="regional-availability"></a>区域可用性

Azure 文件身份验证与 AD DS（预览）在大多数[公共区域](https://azure.microsoft.com/global-infrastructure/regions/)都可用。

具有本地 AD DS 的 Azure 文件身份验证不在：
- 美国西部


## <a name="workflow-overview"></a>工作流概述

在为 Azure 文件共享启用通过 SMB 进行 AD DS 身份验证之前，我们建议您阅读并完成 [先决条件](#prerequisites)部分。 先决条件验证 AD、Azure AD 和 Azure 存储环境是否已正确配置。 

如果您计划在文件共享上启用任何网络配置，我们建议您在启用 AD DS 身份验证之前评估[网络注意事项](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview)并首先完成相关配置。

接下来，按照以下步骤设置用于 AD 身份验证的 Azure 文件： 

1. 在存储帐户上启用 Azure 文件 AD DS 身份验证。 

1. 将共享的访问权限分配给与目标 AD 标识同步的 Azure AD 标识（用户、组或服务主体）。 

1. 通过 SMB 配置目录和文件的 ACL。 
 
1. 将 Azure 文件共享装载到加入 AD DS 的 VM。 

1. 在 AD DS 中更新存储帐户标识的密码。

下图说明了用于通过 SMB 为 Azure 文件共享启用 Azure AD 身份验证的端到端工作流。 

![文件 AD 工作流图](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> Azure 文件共享的通过 SMB 进行 AD DS 身份验证仅在运行在比 Windows 7 或 Windows Server 2008 R2 更新的操作系统版本的计算机上或 VM 上支持。 

## <a name="1-enable-ad-authentication-for-your-account"></a>1. 为您的帐户启用 AD 身份验证 

要通过 SMB 对 Azure 文件共享启用 AD DS 身份验证，您需要首先在 AD DS 注册存储帐户，然后在存储帐户上设置所需的域属性。 在存储帐户上启用该功能时，它适用于帐户中的所有新文件和现有文件共享。 用于`join-AzStorageAccountForAuth`启用该功能。 您可以在本节中的脚本中找到端到端工作流的详细说明。 

> [!IMPORTANT]
> `Join-AzStorageAccountForAuth` cmdlet 将修改您的 AD 环境。 阅读以下说明，以更好地了解它正在采取哪些措施来确保您具有执行命令的适当权限，并确保应用的更改与合规性和安全策略保持一致。 

cmdlet`Join-AzStorageAccountForAuth`将代表指示的存储帐户执行等效的脱机域联接。 该脚本使用 cmdlet 在 AD 域中创建帐户，即[计算机帐户](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory)（默认）[或服务登录帐户](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts)。 如果选择手动执行此操作，则应选择最适合您的环境的帐户。

cmdlet 创建的 AD DS 帐户表示 AD 域中的存储帐户。 如果 AD DS 帐户是在强制密码过期的组织单位 （OU） 下创建的，则必须在最大密码期限之前更新密码。 访问 Azure 文件共享时，未能更新帐户密码将导致身份验证失败。 要了解如何更新密码，请参阅[更新 AD DS 帐户密码](#5-update-the-password-of-your-storage-account-identity-in-ad-ds)。

您可以使用以下脚本执行注册并启用该功能，也可以手动执行脚本将执行的操作。 这些操作在脚本之后的部分中介绍。 您不需要同时执行这两个操作。

### <a name="11-script-prerequisites"></a>1.1 脚本先决条件
- [下载并解压缩 AzFiles 混合模块](https://github.com/Azure-Samples/azure-files-samples/releases)
- 使用 AD DS 凭据在已连接到本地 AD DS 的域的设备中安装和执行模块，该凭据具有在目标 AD 中创建服务登录帐户或计算机帐户的权限。
-  使用同步到 Azure AD 的本地 AD DS 凭据运行脚本。 本地 AD DS 凭据必须具有存储帐户所有者或参与者 RBAC 角色权限。
- 请确保您的存储帐户位于[受支持的区域](#regional-availability)。

### <a name="12-domain-join-your-storage-account"></a>1.2 域加入您的存储帐户
在 PowerShell 中执行占位符之前，请记住在下面的参数中将占位符值替换为您自己的占位符值。
> [!IMPORTANT]
> 域联接 cmdlet 将创建一个 AD 帐户来表示 AD 中的存储帐户（文件共享）。 您可以选择注册为计算机帐户或服务登录帐户，有关详细信息，请参阅[常见问题解答](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control)。 对于计算机帐户，AD 中默认密码过期期限设置为 30 天。 同样，服务登录帐户可能在 AD 域或组织单位 （OU） 上设置了默认密码过期期限。
> 对于这两种帐户类型，我们强烈建议您检查 AD 环境中配置的密码过期期限，并计划在最大密码期限之前更新以下 AD 帐户[AD 中的存储帐户标识密码](#5-update-the-password-of-your-storage-account-identity-in-ad-ds)。 访问 Azure 文件共享时，无法更新 AD 帐户密码将导致身份验证失败。 您可以考虑在[AD 中创建新的 AD 组织单位 （OU），](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit?view=win10-ps)并相应地禁用[计算机帐户](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)?redirectedfrom=MSDN)或服务登录帐户上的密码过期策略。 

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
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account, depends on the AD permission you have and preference. 
#You can run Get-Help Join-AzStorageAccountForAuth to find more details on this cmdlet.

Join-AzStorageAccountForAuth `
        -ResourceGroupName $ResourceGroupName `
        -Name $StorageAccountName `
        -DomainAccountType "<ComputerAccount|ServiceLogonAccount>" ` #Default set to "ComputerAccount"
        -OrganizationalUnitName "<ou-name-here>" #You can also use -OrganizationalUnitDistinguishedName "<ou-distinguishedname-here>" instead. If you don't provide the OU name as an input parameter, the AD identity that represents the storage account will be created under the root directory.

#You can run the Debug-AzStorageAccountAuth cmdlet to conduct a set of basic checks on your AD configuration with the logged on AD user. This cmdlet is supported on AzFilesHybrid v0.1.2+ version. For more details on the checks performed in this cmdlet, go to Azure Files FAQ.
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose

```

以下说明总结了执行`Join-AzStorageAccountForAuth`cmdlet 时执行的所有操作。 如果您不希望使用以下命令，则可以手动执行这些步骤：

> [!NOTE]
> 如果您已成功执行上述`Join-AzStorageAccountForAuth`脚本，请转到下一节"1.3 确认功能已启用"。 您无需再次执行以下操作。

#### <a name="a-checking-environment"></a>a. 检查环境

首先，脚本检查您的环境。 具体而言，它检查是否安装了[Active Directory PowerShell，](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps)以及是否使用管理员权限执行 shell。 然后，它会检查是否安装了[Az.Storage 1.11.1 预览模块](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview)，如果没有安装该模块，则安装该模块。 如果这些检查通过，它将检查您的 AD DS 以查看是否有计算机[帐户](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory)（默认）[或服务登录帐户](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts)已创建 SPN/UPN 为"cifs/您的存储帐户-名称-此处.file.core.windows.net"。 如果帐户不存在，它将创建一个，如下 b 节所述。

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. 手动创建表示 AD 中的存储帐户的标识

要手动创建此帐户，请使用`New-AzStorageAccountKey -KeyName kerb1`为您的存储帐户创建新的 Kerberos 密钥。 然后，使用该 Kerberos 密钥作为您的帐户的密码。 此密钥仅在设置期间使用，不能用于针对存储帐户的任何控制或数据平面操作。

拥有该密钥后，在 OU 下创建服务或计算机帐户。 使用以下规范：SPN："cIFs/您的存储帐户名称-此处.file.core.windows.net"密码：存储帐户的 Kerberos 密钥。

如果 OU 强制密码过期，则必须在最大密码期限之前更新密码，以防止在访问 Azure 文件共享时出现身份验证失败。 有关详细信息[，请参阅 AD DS 中的存储帐户标识更新密码](#5-update-the-password-of-your-storage-account-identity-in-ad-ds)。

保留新创建的帐户的 SID，下一步将需要它。 您创建的表示存储帐户的标识不需要同步到 Azure AD。

##### <a name="c-enable-the-feature-on-your-storage-account"></a>c. 在存储帐户上启用该功能

然后，该脚本将在存储帐户上启用该功能。 要手动执行此设置，请提供以下命令中的域属性的一些配置详细信息，然后运行它。 以下命令中所需的存储帐户 SID 是您在[上一节中](#b-creating-an-identity-representing-the-storage-account-in-your-ad-manually)AD DS 中创建的标识的 SID。

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


### <a name="13-confirm-that-the-feature-is-enabled"></a>1.3 确认功能已启用

您可以检查以确认该功能是否已在存储帐户上启用，您可以使用以下脚本：

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

您现在已成功在存储帐户上启用该功能。 启用该功能后，必须执行更多步骤才能使用该功能。

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

您现在已成功通过 SMB 启用 AD DS 身份验证，并分配了一个自定义角色，该角色提供对具有 AD DS 标识的 Azure 文件共享的访问。 要授予其他用户对文件共享的访问权限，请按照["分配访问权限"](#2-assign-access-permissions-to-an-identity)中的说明使用标识并在[SMB 部分上配置 NTFS 权限](#3-configure-ntfs-permissions-over-smb)。

## <a name="5-update-the-password-of-your-storage-account-identity-in-ad-ds"></a>5. 在 AD DS 中更新存储帐户标识的密码

如果您在强制密码过期时间的 OU 下注册了表示存储帐户的 AD DS 标识/帐户，则必须在最大密码期限之前轮换密码。 无法更新 AD DS 帐户的密码将导致访问 Azure 文件共享的身份验证失败。  

要触发密码轮换，可以从 AzFilesHybrid 模块运行`Update-AzStorageAccountADObjectPassword`该命令。 cmdlet 执行类似于存储帐户密钥轮换的操作。 它获取存储帐户的第二个 Kerberos 密钥，并用它来更新 AD DS 中注册帐户的密码。 然后，它重新生成存储帐户的目标 Kerberos 密钥，并在 AD DS 中更新注册帐户的密码。 您必须在本地 AD DS 域联接环境中运行此 cmdlet。

```PowerShell
# Update the password of the AD DS account registered for the storage account
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```

## <a name="next-steps"></a>后续步骤

有关 Azure 文件以及如何在 SMB 上使用 AD 的详细信息，请参阅以下资源：

- [基于 Azure 文件的基于身份的 SMB 访问的身份验证支持的概述](storage-files-active-directory-overview.md)
- [常见问题解答](storage-files-faq.md)
