---
title: 启用 Azure 文件的通过 SMB 进行 Active Directory 身份验证
description: 了解如何通过 Active Directory 通过 SMB 为 Azure 文件共享启用基于身份的身份验证。 然后，已加入域的 Windows 虚拟机（Vm）可以使用 AD 凭据访问 Azure 文件共享。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: rogarana
ms.openlocfilehash: 4a80565df10b470fe6d3fe590578681f85a3bdd9
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/23/2020
ms.locfileid: "77566278"
---
# <a name="enable-active-directory-authentication-over-smb-for-azure-file-shares"></a>启用对 Azure 文件共享的 SMB 的 Active Directory 身份验证

[Azure 文件](storage-files-introduction.md) 通过两种类型的域服务，支持通过服务器消息块（SMB）进行基于身份的身份验证： Azure Active Directory 域服务（Azure AD DS）（GA）和 ACTIVE DIRECTORY （AD）（预览版）。 本文重点介绍利用 Active Directory 域 Service 对 Azure 文件共享进行身份验证的新引入（预览版）支持。 如果你有兴趣为 Azure 文件共享启用 Azure AD DS （GA）身份验证，请参阅[主题中的文章](storage-files-active-directory-enable.md)。 

> [!NOTE]
> Azure 文件共享仅支持针对一个域服务进行身份验证，Azure Active Directory 域服务（Azure AD DS）或 Active Directory （AD）。 
>
> 用于 Azure 文件共享身份验证的 AD 标识必须同步到 Azure AD。 密码哈希同步是可选的。 
> 
> AD 身份验证不支持对在 Azure AD DS 中创建的计算机帐户进行身份验证。 
> 
> 只能对向其中注册存储帐户的一个 AD 林支持 AD 身份验证。 默认情况下，只能使用单个 AD 林中的 AD 凭据访问 Azure 文件共享。 如果需要从其他林访问 Azure 文件共享，请确保配置了正确的林信任。  
> 
> Azure 文件同步管理的 Azure 文件共享支持 SMB 访问和 ACL 持久性的 AD 身份验证。

在通过 SMB 为 Azure 文件共享启用 AD 时，已加入 AD 域的计算机可以使用现有 AD 凭据装载 Azure 文件共享。 此功能可以通过托管在本地的计算机上或托管在 Azure 中的 AD 环境启用。

用于访问 Azure 文件共享的 AD 标识必须同步到 Azure AD，才能通过标准的[基于角色的访问控制（RBAC）](../../role-based-access-control/overview.md)模型来强制执行共享级别文件权限。 将保留并强制执行从现有文件服务器中执行的文件/目录上的[Windows 样式 dacl](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) 。 这提供了与企业 AD 域基础结构的无缝集成。 将本地文件服务器替换为 Azure 文件共享时，现有用户可以通过单一登录体验从当前客户端访问 Azure 文件共享，而无需更改所使用的凭据。  
 
## <a name="prerequisites"></a>必备条件 

在为 Azure 文件共享启用 AD 身份验证之前，请确保已完成以下先决条件： 

- 选择或创建 AD 环境，并将其同步到 Azure AD。 

    可以在新的或现有的 AD 环境中启用该功能。 用于访问的标识必须同步到 Azure AD。 要访问的 Azure AD 租户和文件共享必须与相同的订阅相关联。 

    若要设置 AD 域环境，请参阅[Active Directory 域服务概述](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)。 如果尚未将 AD 同步到 Azure AD，请按照[什么是混合标识与 Azure Active Directory 的相关](../../active-directory/hybrid/whatis-hybrid-identity.md)指南操作，以确定你的首选身份验证方法和 Azure AD Connect 设置选项。 

- 使用 AD DS 或 AD 将本地计算机或 Azure VM 加入域。 

    若要通过使用计算机或 VM 中的 AD 凭据来访问文件共享，设备必须已加入域才能 AD DS。 有关如何向 AD 加入域的信息，请参阅将[计算机加入到域](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain)。 

- 选择或创建[受支持区域](#regional-availability)中的 Azure 存储帐户。 

    请确保尚未为包含文件共享的存储帐户配置 Azure AD DS 身份验证。 如果在存储帐户上启用了 Azure 文件 Azure AD DS 身份验证，则需要在更改为使用 AD 之前禁用它。 这意味着，将需要重新配置在 Azure AD DS 环境中配置的现有 Acl，以实现适当的权限。
    
    有关创建新文件共享的信息，请参阅 [在 Azure 文件中创建文件共享](storage-how-to-create-file-share.md)。
    
    为了获得最佳性能，建议你的存储帐户与你计划从中访问共享的 VM 位于同一区域。 

- 通过使用存储帐户密钥装载 Azure 文件共享来验证连接性。 

    若要验证是否已正确配置设备和文件共享，请尝试使用存储帐户密钥装载文件共享。 有关详细信息，请参阅 [将 Azure 文件共享用于 Windows](storage-how-to-use-files-windows.md)。

## <a name="regional-availability"></a>区域可用性

Azure 文件 AD 身份验证在[大多数公共区域](https://azure.microsoft.com/global-infrastructure/regions/)中提供，其中尚未提供的区域子集为：

- 美国西部
- 美国西部 2
- 美国东部
- 美国东部 2
- 西欧
- 北欧

## <a name="workflow-overview"></a>工作流概述

在通过 SMB 为 Azure 文件共享启用 AD 身份验证之前，我们建议你逐步完成 [先决条件](#prerequisites) ，并确保已完成所有步骤。 先决条件验证 AD、Azure AD 和 Azure 存储环境是否已正确配置。 

接下来，使用 AD 凭据授予对 Azure 文件资源的访问权限： 

- 在存储帐户上启用 Azure 文件 AD 身份验证。  

- 将共享的访问权限分配给与目标 AD 标识同步的 Azure AD 标识（用户、组或服务主体）。 

- 为目录和文件配置基于 SMB 的 Acl。 

- 从已加入 AD 域的 VM 装载 Azure 文件共享。 

下图说明了用于启用对 Azure 文件共享的 SMB 进行 Azure AD 身份验证的端到端工作流。 

![文件广告工作流关系图](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> Azure 文件共享的 Azure 文件共享的 AD 身份验证仅在操作系统版本低于 Windows 7 或 Windows Server 2008 R2 的操作系统版本上受支持。 

## <a name="enable-ad-authentication-for-your-account"></a>为你的帐户启用 AD 身份验证 

若要为 Azure 文件共享启用通过 SMB 进行 AD 身份验证，需要首先向 AD 注册存储帐户，然后在存储帐户上设置所需的域属性。 在存储帐户上启用此功能后，它将应用于帐户中的所有新的和现有的文件共享。 使用 `join-AzStorageAccountForAuth` 启用此功能。 可以在以下部分中找到端到端工作流的详细说明。 

> [!IMPORTANT]
> `join-AzStorageAccountForAuth` cmdlet 将修改 AD 环境。 阅读以下说明，以更好地了解要执行的操作，以确保您具有执行命令的适当权限，并且应用的更改符合合规性和安全策略。 

`join-AzStorageAccountForAuth` cmdlet 代表指定的存储帐户执行与脱机域联接等效的操作。 它将在你的 AD 域中创建一个帐户，可以是[计算机帐户](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory)，也可以是[服务登录帐户](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts)。 创建的 AD 帐户表示 AD 域中的存储帐户。 如果 AD 帐户是在强制密码过期的 AD 组织单位（OU）下创建的，则必须在 "密码最长期限" 之前更新密码。 未能更新 AD 帐户密码将导致访问 Azure 文件共享时的身份验证失败。 若要了解如何更新密码，请参阅[更新 AD 帐户密码](#update-ad-account-password)。

您可以使用以下脚本来执行注册并启用该功能，也可以手动执行脚本所需的操作。 脚本后面的部分介绍了这些操作。 不需要同时执行这两项操作。

### <a name="script-prerequisites"></a>脚本先决条件

- [下载并解压缩 AzFilesHybrid 模块](https://github.com/Azure-Samples/azure-files-samples/releases)
- 在已加入 AD 的域中安装并执行模块，该设备具有在目标 AD 中创建服务登录帐户或计算机帐户的权限。
-  使用同步到 Azure AD 的 AD 凭据运行该脚本。 AD 凭据必须具有存储帐户所有者或参与者 RBAC 角色权限。
- 请确保存储帐户位于[受支持的区域](#regional-availability)。

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope Currentuser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer RBAC assignment
Connect-AzAccount

#Select the target subscription for the current session
Select-AzureSubscription -SubscriptionId "<yourSubscriptionIdHere>"

#Register the target storage account with your active directory environment under the target OU
join-AzStorageAccountForAuth -ResourceGroupName "<resource-group-name-here>" -Name "<storage-account-name-here>" -DomainAccountType "<ServiceLogonAccount|ComputerAccount>" -OrganizationUnitName "<ou-name-here>"
```


下面是使用 `join-AzStorageAccountForAuth` 命令时执行的操作的说明。 如果不想使用命令，则可以手动执行这些步骤：

### <a name="checking-environment"></a>检查环境

首先，它将检查您的环境。 特别是，它会检查是否安装了[Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) ，以及是否通过管理员权限执行了外壳。 然后，它会检查[Az 1.11.1 模块](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview)是否已安装，如果不是，则安装该模块。 如果这些检查通过，则它会检查你的广告，查看是否存在已使用 SPN/UPN 创建的[计算机帐户](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory)（默认）或[服务登录帐户](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts)，并在不存在的情况下创建一个帐户/UPN. s. e s。

### <a name="creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>手动创建代表 AD 中的存储帐户的标识

若要手动创建此帐户，请使用 `New-AzStorageAccountKey -Keynam "yourKeyName"`为你的存储帐户创建新的 kerberos 密钥。 然后，将该 kerberos 密钥用作你的帐户的密码。 此密钥仅在安装过程中使用，不能用于针对存储帐户的任何控制或数据平面操作。

获得该密钥后，请在 OU 下创建服务或计算机帐户。 为你的存储帐户使用以下规范： SPN： "cifs/你的---name-name-name-name-name： Kerberos 密钥"。

如果你的 OU 强制密码过期，则必须在最长密码期限之前更新密码，以防止访问 Azure 文件共享时的身份验证失败。 有关详细信息，请参阅[更新 AD 帐户密码](#update-ad-account-password)。

保留新创建的帐户的 SID，下一步需要用到它。

### <a name="enable-the-feature-on-your-storage-account"></a>在存储帐户上启用该功能

然后，该脚本将在存储帐户上启用该功能。 若要手动执行此操作，请在以下命令中为域属性提供一些配置详细信息，然后运行该命令。 以下命令中所需的存储帐户 SID 是在 AD 中创建的标识的 SID。

```PowerShell
#Set the feature flag on the target storage account and provide the required AD domain information

Set-AzStorageAccount -ResourceGroupName "<your-resource-group-name-here>" -Name "<your-storage-account-name-here>" -EnableActiveDirectoryDomainServiesForFile $true -ActiveDirectoryDomainName "<your-domain-name-here>" -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" -ActiveDirectoryForestName "<your-forest-name-here>" -ActiveDirectoryDomainGuid "<your-guid-here>" -ActiveDirectoryDomainsid "<your-domain-sid-here>" -ActiveDirectoryAzureStirageSid "<your-storage-account-sid>"
```


### <a name="check-if-the-feature-is-enabled"></a>检查此功能是否已启用

如果要检查是否已在存储帐户上启用此功能，可以使用以下脚本：

```PowerShell
#Get the target storage account
$storageaccount = Get-AzStorageAccount -ResourceGroupName "<your-resource-group-name-here>" -Name "<your-storage-account-name-here>"

#List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

#List the directory domain information if the storage account has enabled AD authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

你现在已成功在你的存储帐户上启用了该功能。 即使已启用此功能，仍需要完成其他操作才能正确使用此功能。

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

你现在已成功启用了通过 SMB 进行 Azure AD 身份验证，并分配了自定义角色，该角色提供对具有 AD 标识的 Azure 文件共享的访问权限。 若要向其他用户授予对文件共享的访问权限，请按照 "[分配访问权限](#assign-access-permissions-to-an-identity)" 中的说明，使用一个标识，并在[SMB 部分配置 NTFS 权限](#configure-ntfs-permissions-over-smb)。

## <a name="update-ad-account-password"></a>更新 AD 帐户密码

如果在强制密码过期时间的 OU 下注册了代表存储帐户的 AD 帐户，则必须在密码最长期限前轮替密码。 未能更新 AD 帐户的密码将导致身份验证失败，无法访问 Azure 文件共享。  

若要触发密码轮换，可以从[AzFilesHybrid 模块](#script-prerequisites)运行 `Update-AzStorageAccountADObjectPassword` 命令。 Cmdlet 执行与存储帐户密钥轮换类似的操作。 它将获取存储帐户的第二个 Kerberos 密钥，并使用它来更新 AD 中注册帐户的密码。 然后，它将重新生成存储帐户的目标 Kerberos 密钥并更新 AD 中注册帐户的密码。 必须在已加入 AD 域的环境中运行此 cmdlet。

```PowerShell
#Update the password of the AD account registered for the storage account
Update-AzStorageAccountADObjectPassword -RotateToKerbKey kerb2 -ResourceGroupName "your-resource-group-name-here" -StorageAccountName "your-storage-account-name-here"
```

## <a name="next-steps"></a>后续步骤

有关 Azure 文件以及如何使用 AD over SMB 的详细信息，请参阅以下资源：

- [适用于 SMB 访问的 Azure 文件基于标识的身份验证支持概述](storage-files-active-directory-overview.md)
- [常见问题](storage-files-faq.md)