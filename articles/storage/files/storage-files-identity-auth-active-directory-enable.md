---
title: 通过 SMB 为 Azure 文件启用活动目录身份验证
description: 了解如何通过活动目录通过 SMB 为 Azure 文件共享启用基于身份的身份验证。 然后，加入域的 Windows 虚拟机 （VM） 可以使用 AD 凭据访问 Azure 文件共享。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: rogarana
ms.openlocfilehash: 081ee364b3ddee5d1d1be75613309a4ae427066f
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666833"
---
# <a name="enable-active-directory-authentication-over-smb-for-azure-file-shares"></a>通过 SMB 为 Azure 文件共享启用活动目录身份验证

[Azure 文件](storage-files-introduction.md) 通过两种类型的域服务支持通过服务器消息块 （SMB） 进行基于身份的身份验证：Azure 活动目录域服务 （Azure AD DS） （GA） 和活动目录 （AD） （预览）。 本文重点介绍新引入的（预览）支持利用 Active 目录域服务对 Azure 文件共享进行身份验证。 如果您有兴趣为 Azure 文件共享启用 Azure AD DS （GA） 身份验证，请参阅[我们有关主题的文章](storage-files-identity-auth-active-directory-domain-service-enable.md)。

> [!NOTE]
> Azure 文件仅共享支持针对一个域服务的身份验证，即 Azure 活动目录域服务 （Azure AD DS） 或活动目录 （AD）。 
>
> 用于 Azure 文件共享身份验证的 AD 标识必须同步到 Azure AD。 密码哈希同步是可选的。 
> 
> AD 身份验证不支持对 AD 中创建的计算机帐户进行身份验证。 
> 
> AD 身份验证只能针对存储帐户注册的一个 AD 林进行支持。 默认情况下，您只能从单个 AD 林中访问具有 AD 凭据的 Azure 文件共享。 如果需要从其他林访问 Azure 文件共享，请确保配置了正确的林信任，有关详细信息，请参阅[常见问题解答](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control)。  
> 
> 对于 Azure 文件同步管理的 Azure 文件共享，支持用于 SMB 访问的 AD 身份验证和 ACL 持久性。
>
> Azure 文件支持使用 RC4-HMAC 加密的 AD 进行 Kerberos 身份验证。 AES Kerberos 加密尚不受支持。

通过 SMB 为 Azure 文件共享启用 AD 时，AD 域联接的计算机可以使用现有的 AD 凭据装载 Azure 文件共享。 此功能可以通过托管在预处理计算机或 Azure 中托管的 AD 环境启用。

用于访问 Azure 文件共享的 AD 标识必须同步到 Azure AD，以便通过基于角色的标准[访问控制 （RBAC）](../../role-based-access-control/overview.md)模型强制实施共享级别文件权限。 将保留并强制执行从现有文件服务器结转的文件/目录上的[Windows 样式 DACL。](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) 此功能提供与企业 AD 域基础架构的无缝集成。 当您使用 Azure 文件共享替换上置前文件服务器时，现有用户可以通过单个登录体验从其当前客户端访问 Azure 文件共享，而无需对正在使用的凭据进行任何更改。  

> [!NOTE]
> 为了帮助您为常见用例设置 Azure 文件 AD 身份验证，我们发布了[两个视频](https://docs.microsoft.com/azure/storage/files/storage-files-introduction#videos)，并逐步指导将本地文件服务器替换为 Azure 文件，并使用 Azure 文件作为 Windows 虚拟桌面的配置文件容器。
 
## <a name="prerequisites"></a>先决条件 

在为 Azure 文件共享启用 AD 身份验证之前，请确保已完成以下先决条件： 

- 选择或创建 AD 环境并将其同步到 Azure AD。 

    您可以在新的或现有的 AD 环境中启用该功能。 用于访问的身份必须同步到 Azure AD。 Azure AD 租户和要访问的文件共享必须与同一订阅关联。 

    要设置 AD 域环境，请参阅[活动目录域服务概述](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)。 如果 AD 尚未将 AD 同步到 Azure AD，请按照[Azure 活动目录的混合标识指南进行操作](../../active-directory/hybrid/whatis-hybrid-identity.md)，以确定首选身份验证方法和 Azure AD 连接设置选项。 

- 域将本地计算机或 Azure VM 连接到 AD（也称为 AD DS）。 

    要使用计算机或 VM 中的 AD 凭据访问文件共享，您的设备必须加入到 AD 的域。 有关如何加入 AD 的域的信息，请参阅[将计算机加入域](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain)。 

- 在[受支持的区域](#regional-availability)中选择或创建 Azure 存储帐户。 

    确保包含文件共享的存储帐户尚未配置为 Azure AD DS 身份验证。 如果在存储帐户上启用 Azure 文件 Azure AD DS 身份验证，则需要在更改为使用 AD 之前禁用该身份验证。 这意味着需要在 Azure AD DS 环境中配置的现有 ACL 进行重新配置，以便进行适当的权限实施。
    
    有关创建新文件共享的信息，请参阅 [在 Azure 文件 中创建文件共享](storage-how-to-create-file-share.md)。
    
    为了获得最佳性能，我们建议您将存储帐户部署在同一区域中，以便从哪个 VM 访问共享。 

- 通过使用存储帐户密钥安装 Azure 文件共享来验证连接。 

    要验证您的设备和文件共享是否已正确配置，请尝试使用存储帐户密钥安装文件共享。 有关详细信息，请参阅使用 [Windows 的 Azure 文件共享](storage-how-to-use-files-windows.md)。

## <a name="regional-availability"></a>区域可用性

Azure 文件 AD 身份验证（预览）在公共[云中的所有区域](https://azure.microsoft.com/global-infrastructure/regions/)都可用。

## <a name="workflow-overview"></a>工作流概述

在为 Azure 文件共享启用通过 SMB 进行 AD 身份验证之前，我们建议您演练 [先决条件](#prerequisites) 并确保已完成所有步骤。 先决条件验证 AD、Azure AD 和 Azure 存储环境是否已正确配置。 

接下来，按照以下步骤设置用于 AD 身份验证的 Azure 文件： 

1. 在存储帐户上启用 Azure 文件 AD 身份验证。 

2. 将共享的访问权限分配给与目标 AD 标识同步的 Azure AD 标识（用户、组或服务主体）。 

3. 通过 SMB 配置目录和文件的 ACL。 

4. 从 AD 域联接的 VM 装载 Azure 文件共享。 

5. 旋转广告帐户密码（可选）

下图说明了用于通过 SMB 为 Azure 文件共享启用 Azure AD 身份验证的端到端工作流。 

![文件 AD 工作流图](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> Azure 文件共享的 SMB 上的 AD 身份验证仅在运行在比 Windows 7 或 Windows Server 2008 R2 更新的操作系统版本的计算机上或 VM 上支持。 

## <a name="1-enable-ad-authentication-for-your-account"></a>1. 为您的帐户启用 AD 身份验证 

要通过 SMB 对 Azure 文件共享启用 AD 身份验证，需要先向 AD 注册存储帐户，然后在存储帐户上设置所需的域属性。 在存储帐户上启用该功能时，它适用于帐户中的所有新文件和现有文件共享。 用于`join-AzStorageAccountForAuth`启用该功能。 您可以在下面的部分中找到端到端工作流的详细说明。 

> [!IMPORTANT]
> `Join-AzStorageAccountForAuth` cmdlet 将修改您的 AD 环境。 阅读以下说明，以更好地了解它正在采取哪些措施来确保您具有执行命令的适当权限，并确保应用的更改与合规性和安全策略保持一致。 

cmdlet`Join-AzStorageAccountForAuth`将代表指示的存储帐户执行等效的脱机域联接。 它将在 AD 域中创建一个帐户，即[计算机帐户](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory)（默认）[或服务登录帐户](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts)。 创建的 AD 帐户表示 AD 域中的存储帐户。 如果 AD 帐户是在强制密码过期的 AD 组织单位 （OU） 下创建的，则必须在最大密码期限之前更新密码。 访问 Azure 文件共享时，无法更新 AD 帐户密码将导致身份验证失败。 要了解如何更新密码，请参阅[更新 AD 帐户密码](#5-update-ad-account-password)。

您可以使用以下脚本执行注册并启用该功能，也可以手动执行脚本将执行的操作。 这些操作在脚本之后的部分中介绍。 您不需要同时执行这两个操作。

### <a name="11-check-prerequisites"></a>1.1 检查先决条件
- [下载并解压缩 AzFiles 混合模块](https://github.com/Azure-Samples/azure-files-samples/releases)
- 在具有 AD 凭据的域加入 AD 的设备中安装并执行模块，该凭据具有在目标 AD 中创建服务登录帐户或计算机帐户的权限。
-  使用同步到 Azure AD 的 AD 凭据运行脚本。 AD 凭据必须具有存储帐户所有者或参与者 RBAC 角色权限。
- 请确保您的存储帐户位于[受支持的区域](#regional-availability)。

### <a name="12-domain-join-your-storage-account"></a>1.2 域加入您的存储帐户
在 PowerShell 中执行占位符之前，请记住在下面的参数中将占位符值替换为您自己的占位符值。
> [!IMPORTANT]
> 我们建议您提供不强制密码过期的 AD 组织单位 （OU）。 如果使用配置了密码过期的 OU，则必须在最大密码期限之前更新密码。 访问 Azure 文件共享时，无法更新 AD 帐户密码将导致身份验证失败。 要了解如何更新密码，请参阅[更新 AD 帐户密码](#5-update-ad-account-password)。


```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer RBAC assignment
Connect-AzAccount

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId "<your-subscription-id-here>"

# Register the target storage account with your active directory environment under the target OU (for example: specify the OU with Name as "UserAccounts" or DistinguishedName as "OU=UserAccounts,DC=CONTOSO,DC=COM"). 
# You can use to this PowerShell cmdlet: Get-ADOrganizationalUnit to find the Name and DistinguishedName of your target OU. If you are using the OU Name, specify it with -OrganizationalUnitName as shown below. If you are using the OU DistinguishedName, you can set it with -OrganizationalUnitDistinguishedName. You can choose to provide one of the two names to specify the target OU.
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account, depends on the AD permission you have and preference. 
Join-AzStorageAccountForAuth `
        -ResourceGroupName "<resource-group-name-here>" `
        -Name "<storage-account-name-here>" `
        -DomainAccountType "ComputerAccount" `
        -OrganizationalUnitName "<ou-name-here>" or -OrganizationalUnitDistinguishedName "<ou-distinguishedname-here>"
```

以下说明总结了执行`Join-AzStorageAccountForAuth`cmdlet 时执行的所有操作。 如果您不希望使用以下命令，则可以手动执行这些步骤：

> [!NOTE]
> 如果您已成功执行上述`Join-AzStorageAccountForAuth`脚本，请转到下一节"1.3 确认功能已启用"。 您无需再次执行以下操作。

#### <a name="a-checking-environment"></a>a. 检查环境

首先，它会检查您的环境。 具体来说，它检查是否安装了[Active Directory PowerShell，](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps)以及是否使用管理员权限执行 shell。 然后，它会检查是否安装了[Az.Storage 1.11.1 预览模块](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview)，如果没有安装该模块，则安装该模块。 如果这些检查通过，它将检查您的 AD，以查看是否有计算机[帐户](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory)（默认）[或服务登录帐户](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts)已创建 SPN/UPN 为"cifs/您的存储帐户名称-此处.file.core.windows.net"。 如果帐户不存在，它将创建一个，如下 b 节所述。

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. 手动创建表示 AD 中的存储帐户的标识

要手动创建此帐户，请使用`New-AzStorageAccountKey -KeyName kerb1`创建存储帐户的新 kerberos 密钥。 然后，使用该 kerberos 密钥作为帐户的密码。 此密钥仅在设置期间使用，不能用于针对存储帐户的任何控制或数据平面操作。

拥有该密钥后，在 OU 下创建服务或计算机帐户。 使用以下规范：SPN："cIFs/您的存储帐户名称-此处.file.core.windows.net"密码：存储帐户的 Kerberos 密钥。

如果 OU 强制密码过期，则必须在最大密码期限之前更新密码，以防止在访问 Azure 文件共享时出现身份验证失败。 有关详细信息，请参阅[更新 AD 帐户密码](#5-update-ad-account-password)。

保留新创建的帐户的 SID，下一步将需要它。 您刚刚创建的表示存储帐户的 AD 标识不需要同步到 Azure AD。

##### <a name="c-enable-the-feature-on-your-storage-account"></a>c. 在存储帐户上启用该功能

然后，该脚本将在存储帐户上启用该功能。 要手动执行此设置，请提供以下命令中的域属性的一些配置详细信息，然后运行它。 以下命令中所需的存储帐户 SID 是您在 AD 中创建的标识的 SID（上面的 b 节）。

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

# List the directory domain information if the storage account has enabled AD authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

您现在已成功在存储帐户上启用该功能。 即使启用了该功能，您仍需要完成其他操作才能正确使用该功能。

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

现在，您已成功通过 SMB 启用 AD 身份验证，并分配了一个自定义角色，该角色提供对具有 AD 标识的 Azure 文件共享的访问。 要授予其他用户对文件共享的访问权限，请按照["分配访问权限"](#2-assign-access-permissions-to-an-identity)中的说明使用标识并在[SMB 部分上配置 NTFS 权限](#3-configure-ntfs-permissions-over-smb)。

## <a name="5-update-ad-account-password"></a>5. 更新 AD 帐户密码

如果您在强制密码过期时间的 OU 下注册了表示存储帐户的 AD 标识/帐户，则必须在最大密码期限之前轮换密码。 无法更新 AD 帐户的密码将导致身份验证无法访问 Azure 文件共享。  

要触发密码轮换，可以从 AzFilesHybrid 模块运行`Update-AzStorageAccountADObjectPassword`该命令。 cmdlet 执行类似于存储帐户密钥轮换的操作。 它获取存储帐户的第二个 Kerberos 密钥，并用它来更新 AD 中注册帐户的密码。 然后，它重新生成存储帐户的目标 Kerberos 密钥，并更新 AD 中注册帐户的密码。 您必须在 AD 域联接的环境中运行此 cmdlet。

```PowerShell
#Update the password of the AD account registered for the storage account
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```

## <a name="next-steps"></a>后续步骤

有关 Azure 文件以及如何在 SMB 上使用 AD 的详细信息，请参阅以下资源：

- [基于 Azure 文件的基于身份的 SMB 访问的身份验证支持的概述](storage-files-active-directory-overview.md)
- [常见问题解答](storage-files-faq.md)
