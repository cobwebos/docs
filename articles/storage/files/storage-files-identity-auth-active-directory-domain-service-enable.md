---
title: 使用 Azure AD 域服务授权通过 SMB 访问文件数据
description: 了解如何通过 Azure 活动目录域服务通过服务器消息块 （SMB） 为 Azure 文件启用基于身份的身份验证。 然后，加入域的 Windows 虚拟机 （VM） 可以使用 Azure AD 凭据访问 Azure 文件共享。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: cb173bcbf7cd163dca16c211d45018e0fe056edd
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666856"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>在 Azure 文件上启用 Azure 活动目录域服务身份验证

[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

有关 Azure 文件共享的 Azure AD 身份验证概述，请参阅[Azure 文件通过 SMB 进行 Azure 活动目录身份验证的概述](storage-files-active-directory-overview.md)。 本文重点介绍如何在 Azure 文件上启用 Azure 活动目录域服务 （Azure AD DS） 的身份验证。

> [!NOTE]
> Azure 文件支持使用 RC4-HMAC 加密的 Azure AD DS 进行 Kerberos 身份验证。 AES Kerberos 加密尚不受支持。

## <a name="prerequisites"></a>先决条件

在为 Azure 文件共享启用 Azure AD 过 SMB 之前，请确保已完成以下先决条件：

1.  **选择或创建 Azure AD 租户。**

    可以使用新的或现有的租户进行通过 SMB 的 Azure AD 身份验证。 要访问的租户和文件共享必须与同一订阅相关联。

    若要创建一个新的 Azure AD 租户，可以[添加 Azure AD 租户和 Azure AD 订阅](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription)。 如果您有现有的 Azure AD 租户，但希望创建新租户以与 Azure 文件共享一起使用，请参阅[创建 Azure 活动目录租户](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant)。

1.  **启用 Azure AD 租户上的 Azure AD 域服务。**

    若要支持使用 Azure AD 凭据进行身份验证，必须启用 Azure AD 租户的 Azure AD 域服务。 如果你不是 Azure AD 租户的管理员，请与管理员联系并按照分步指南操作，以[使用 Azure 门户启用 Azure Active Directory 域服务](../../active-directory-domain-services/tutorial-create-instance.md)。

    Azure AD DS 部署通常需要大约 15 分钟才能完成。 在继续下一步之前，验证 Azure AD DS 的运行状况是否显示 **"正在运行**"，并启用了密码哈希同步。

1.  **域将 Azure VM 与 Azure AD DS 联接。**

    要使用来自 VM 的 Azure AD 凭据访问文件共享，VM 必须已加入 Azure AD DS。 有关如何将 VM 加入域的详细信息，请参阅[将 Windows Server 虚拟机加入托管域](../../active-directory-domain-services/join-windows-vm.md)。

    > [!NOTE]
    > Azure AD DS 身份验证上具有 Azure 文件共享，仅在 Windows 7 或 Windows 服务器 2008 R2 上方操作系统版本的 Azure VM 上支持。

1.  **选择或创建 Azure 文件共享。**

    选择与 Azure AD 租户相同订阅关联的新的或现有文件共享。 有关创建新的文件共享的信息，请参阅[在 Azure 文件中创建文件共享](storage-how-to-create-file-share.md)。
    为了获得最佳性能，我们建议您的文件共享与计划从其中访问共享的 VM 位于同一区域。

1.  **通过使用存储帐户密钥装载 Azure 文件共享来验证 Azure 文件连接。**

    要验证是否已正确配置 VM 和文件共享，请尝试使用存储帐户密钥装载文件共享。 有关详细信息，请参阅[在 Windows 中装载 Azure 文件共享并对其进行访问](storage-how-to-use-files-windows.md)。

## <a name="overview-of-the-workflow"></a>工作流概述

在为 Azure 文件共享启用通过 SMB 进行 Azure AD DS 身份验证之前，请验证 Azure AD 和 Azure 存储环境是否配置正确。 我们建议您演练[先决条件](#prerequisites)，以确保已完成所有必需的步骤。

接下来，执行以下操作以授予使用 Azure AD 凭据的 Azure 文件资源的权限：

1. 为存储帐户通过 SMB 启用 Azure AD DS 身份验证，以便将存储帐户注册到关联的 Azure AD DS 部署。
2. 将共享的访问权限分配给 Azure AD 标识（用户、组或服务主体）。
3. 通过 SMB 为目录和文件配置 NTFS 权限。
4. 从加入域的 VM 装载 Azure 文件共享。

下图说明了用于通过 SMB 对 Azure 文件启用 Azure AD DS 身份验证的端到端工作流。

![显示通过 SMB 为 Azure 文件启用 Azure AD 的工作流的图表](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="1-enable-azure-ad-ds-authentication-for-your-account"></a>1. 为您的帐户启用 Azure AD DS 身份验证

要通过 Azure 文件的 SMB 启用 Azure AD DS 身份验证，可以使用 Azure 门户、Azure PowerShell 或 Azure CLI 在存储帐户上设置属性。 将此属性隐式设置"域联接"存储帐户与关联的 Azure AD DS 部署。 然后，对存储帐户中的所有新文件和现有文件共享启用通过 SMB 的 Azure AD DS 身份验证。

请记住，只有在成功地将 Azure AD DS 部署到 Azure AD 租户后，才能通过 SMB 启用 Azure AD DS 身份验证。 有关详细信息，请参阅[先决条件](#prerequisites)。

### <a name="azure-portal"></a>Azure 门户

要使用[Azure 门户](https://portal.azure.com)通过 SMB 启用 Azure AD DS 身份验证，请按照以下步骤操作：

1. 在 Azure 门户中，转到现有存储帐户，或[创建存储帐户](../common/storage-account-create.md)。
1. 在“设置”部分选择“配置”。********
1. 在**基于身份的文件共享访问**下，将**Azure 活动目录域服务 （AAD DS） 的**切换切换到**启用**。
1. 选择“保存”。 

下图演示如何通过存储帐户的 SMB 启用 Azure AD DS 身份验证。

![在 Azure 门户中通过 SMB 启用 Azure AD DS 身份验证](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)

### <a name="powershell"></a>PowerShell  

要使用 Azure PowerShell 通过 SMB 启用 Azure AD DS 身份验证，请安装最新的 Az 模块（2.4 或更高版本）或 Az.Storage 模块（1.5 或更高）。 有关安装 PowerShell 的详细信息，请参阅[使用 PowerShell 在 Windows 上安装 Azure PowerShell。](https://docs.microsoft.com/powershell/azure/install-Az-ps)

要创建新的存储帐户，请调用[New-AzStorage 帐户](https://docs.microsoft.com/powershell/module/az.storage/New-azStorageAccount?view=azps-2.5.0)，然后将**启用 AzureActiveDirectoryDomain服务ForFile**参数设置为**true**。 在下面的示例中，请记住将占位符值替换为您自己的值。 （如果使用上一个预览模块，则功能启用的参数为**启用 AzureFilesAad 集成为 SMB**。

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```

要在现有存储帐户上启用此功能，请使用以下命令：

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


### <a name="azure-cli"></a>Azure CLI

要使用 Azure CLI 通过 SMB 启用 Azure AD 身份验证，请安装最新的 CLI 版本（版本 2.0.70 或更高版本）。 有关安装 Azure CLI 的详细信息，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

要创建新的存储帐户，请调用[az 存储帐户创建](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)，`--enable-files-aadds`并将属性设置为**true**。 在下面的示例中，请记住将占位符值替换为您自己的值。 （如果使用上一个预览模块，则功能启用的参数为**file-ad**.）

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

要在现有存储帐户上启用此功能，请使用以下命令：

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

现在，您已成功通过 SMB 启用 Azure AD DS 身份验证，并分配了一个自定义角色，该角色提供使用 Azure AD 标识访问 Azure 文件共享的功能。 要授予其他用户对文件共享的访问权限，请按照["分配访问权限"](#2-assign-access-permissions-to-an-identity)中的说明使用标识并在[SMB 部分上配置 NTFS 权限](#3-configure-ntfs-permissions-over-smb)。

## <a name="next-steps"></a>后续步骤

有关 Azure 文件以及如何在 SMB 上使用 Azure AD 的详细信息，请参阅以下资源：

- [基于 Azure 文件的基于身份的 SMB 访问的身份验证支持的概述](storage-files-active-directory-overview.md)
- [常见问题解答](storage-files-faq.md)
