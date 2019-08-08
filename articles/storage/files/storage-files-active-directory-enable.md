---
title: 启用 Azure 文件的 SMB Azure Active Directory 身份验证-Azure 存储
description: 了解如何通过 Azure Active Directory 域服务, 为 Azure 文件通过服务器消息块 (SMB) 启用基于身份的身份验证。 然后, 已加入域的 Windows 虚拟机 (Vm) 可以使用 Azure AD 凭据访问 Azure 文件共享。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: rogarana
ms.openlocfilehash: 060c47cc25d04bccc253bcebf6479d660621f6d2
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855329"
---
# <a name="enable-azure-active-directory-domain-services-authentication-over-smb-for-azure-files"></a>启用 Azure 文件的通过 SMB Azure Active Directory 域服务身份验证
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

有关 Azure 文件的 SMB Azure AD 身份验证的概述, 请参阅[Azure 文件的通过 smb Azure Active Directory 身份验证的概述](storage-files-active-directory-overview.md)。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview-of-the-workflow"></a>工作流概述
在 Azure 文件的 SMB 上启用 Azure AD DS 身份验证之前, 请验证是否正确配置了 Azure AD 和 Azure 存储环境。 建议你逐步完成[先决条件](#prerequisites), 确保你已完成所有必需的步骤。

接下来，按照以下步骤授予使用 Azure AD 凭据访问 Azure 文件资源的权限： 

1. 启用针对存储帐户的 SMB Azure AD DS 身份验证, 以便向关联的 Azure AD DS 部署注册存储帐户。
2. 将共享的访问权限分配给 Azure AD 标识（用户、组或服务主体）。
3. 通过 SMB 为目录和文件配置 NTFS 权限。
4. 从加入域的 VM 装载 Azure 文件共享。

下图说明了用于实现 Azure 文件的通过 SMB 进行 Azure AD DS 身份验证的端到端工作流。 

![显示通过 SMB 为 Azure 文件启用 Azure AD 的工作流的图表](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="prerequisites"></a>先决条件

在通过 SMB 为 Azure 文件启用 Azure AD 之前，请确保满足以下先决条件：

1.  **选择或创建 Azure AD 租户。**

    可以使用新的或现有的租户进行通过 SMB 的 Azure AD 身份验证。 要访问的租户和文件共享必须与同一订阅相关联。

    若要创建一个新的 Azure AD 租户，可以[添加 Azure AD 租户和 Azure AD 订阅](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription)。 如果已有一个 Azure AD 租户，但想要创建新租户以便与 Azure 文件一同使用，请参阅[创建 Azure Active Directory 租户](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant)。

2.  **启用 Azure AD 租户上的 Azure AD 域服务。**

    若要支持使用 Azure AD 凭据进行身份验证, 必须为 Azure AD 租户启用 Azure AD DS。 如果你不是 Azure AD 租户的管理员, 请与管理员联系, 并按照分步指南操作,[通过使用 Azure 门户启用 Azure Active Directory 域服务](../../active-directory-domain-services/create-instance.md)。

    完成 Azure AD DS 部署通常需要大约15分钟。 在继续下一步之前, 请验证Azure AD DS 的运行状况状态显示为 "已启用密码哈希同步"。

3.  **使用 Azure AD DS 将 Azure VM 加入域。**

    若要通过使用 Azure AD 虚拟机中的凭据来访问文件共享, 你的 VM 必须已加入域 Azure AD DS。 有关如何将 VM 加入域的详细信息，请参阅[将 Windows Server 虚拟机加入托管域](../../active-directory-domain-services/join-windows-vm.md)。

    > [!NOTE]
    > 仅支持在 Windows 7 或 Windows Server 2008 R2 以上的操作系统版本上运行的 Azure Vm 对 Azure 文件 Azure AD DS 身份验证。

4.  **选择或创建 Azure 文件共享。**

    选择与 Azure AD 租户相同订阅关联的新的或现有文件共享。 有关创建新的文件共享的信息，请参阅[在 Azure 文件中创建文件共享](storage-how-to-create-file-share.md)。 
    为了获得最佳性能, 建议将文件共享与计划从中访问共享的 VM 位于同一区域。

5.  **通过使用存储帐户密钥装载 Azure 文件共享来验证 Azure 文件连接。**

    要验证是否已正确配置 VM 和文件共享，请尝试使用存储帐户密钥装载文件共享。 有关详细信息，请参阅[在 Windows 中装载 Azure 文件共享并对其进行访问](storage-how-to-use-files-windows.md)。

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>为帐户启用 Azure AD DS 身份验证

若要启用针对 Azure 文件的 SMB Azure AD DS 身份验证, 可以使用 Azure 门户、Azure PowerShell 或 Azure CLI 在2018年9月24日之后创建的存储帐户上设置属性。 设置此属性将向关联的 Azure AD DS 部署注册存储帐户。 然后, 将对存储帐户中的所有新的和现有文件共享启用通过 SMB 进行的 Azure AD DS 身份验证。

请记住, 仅当已成功将 Azure AD DS 部署到 Azure AD 租户之后, 才能通过 SMB 启用 Azure AD DS 身份验证。 有关详细信息, 请参阅[先决条件](#prerequisites)。

### <a name="azure-portal"></a>Azure 门户

若要通过[Azure 门户](https://portal.azure.com)启用 SMB Azure AD DS 身份验证, 请执行以下步骤:

1. 在 Azure 门户中, 请前往现有的存储帐户, 或者[创建一个存储帐户](../common/storage-quickstart-create-account.md)。
2. 在“设置”部分选择“配置”。
3. 从 " **Azure 文件身份验证的基于标识的目录服务**" 下拉列表中选择 " **Azure Active Directory 域服务 (Azure AD DS)** "。

下图显示了如何为存储帐户启用通过 SMB 进行的 Azure AD DS 身份验证。

![在 Azure 门户中通过 SMB 启用 Azure AD 身份验证](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)
  
### <a name="powershell"></a>PowerShell  

若要启用 Azure PowerShell 的 SMB Azure AD DS 身份验证, 请安装最新的 Az 模块 (2.4 或更高版本) 或 Az 模块 (1.5 或更高版本)。 有关安装 PowerShell 的详细信息, 请参阅[在 Windows 上通过 PowerShellGet 安装 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps)。

若要创建新的存储帐户, 请调用[AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/New-azStorageAccount?view=azps-2.5.0), 然后将**EnableAzureActiveDirectoryDomainServicesForFile**参数设置为**true**。 在下面的示例中, 请记住将占位符值替换为自己的值。 (如果使用的是以前的预览模块, 则功能启用参数为**EnableAzureFilesAadIntegrationForSMB**。)

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```

若要在现有存储帐户上启用此功能, 请使用以下命令:

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


### <a name="azure-cli"></a>Azure CLI

若要启用 Azure CLI 通过 SMB 进行 Azure AD 身份验证, 请安装最新的 CLI 版本 (版本2.0.70 或更高版本)。 有关安装 Azure CLI 的详细信息, 请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

若要创建新的存储帐户, 请调用[az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create), 并`--enable-files-aadds`将属性设置为**true**。 在下面的示例中, 请记住将占位符值替换为自己的值。 (如果使用的是以前的预览模块, 则功能启用参数为**文件-aad**。)

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

若要在现有存储帐户上启用此功能, 请使用以下命令:

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```


## <a name="assign-access-permissions-to-an-identity"></a>为标识分配访问权限

若要使用 Azure AD 凭据访问 Azure 文件资源, 标识 (用户、组或服务主体) 必须具有共享级别的必要权限。 此过程类似于指定 Windows 共享权限, 你可以在其中指定特定用户对文件共享的访问类型。 本部分中的指导演示如何将文件共享的读取、写入或删除权限分配给标识。

我们引入了两个 Azure 内置角色用于向用户授予共享级权限:

- **存储文件数据 Smb 共享读取器**允许通过 Smb 在 Azure 存储文件共享中进行读取访问。
- **存储文件数据 SMB 共享参与者**允许通过 SMB 在 Azure 存储文件共享中进行读取、写入和删除访问。
- **存储文件数据 SMB 共享提升的参与者**允许通过 SMB 在 Azure 存储文件共享中读取、写入、删除和修改 NTFS 权限。

> [!IMPORTANT]
> 对文件共享的完全管理控制（包括将角色分配给标识的控制权限）需要使用存储帐户密钥。 Azure AD 凭据不支持管理控制。

您可以使用 Azure PowerShell 或 Azure CLI 将内置角色分配给用户的 Azure AD 标识, 以便授予共享级权限。

#### <a name="powershell"></a>PowerShell

以下 PowerShell 命令显示了如何基于登录名将 RBAC 角色分配到 Azure AD 标识。 有关如何使用 PowerShell 分配 RBAC 角色的详细信息，请参阅[使用 RBAC 和 Azure PowerShell 管理访问权限](../../role-based-access-control/role-assignments-powershell.md)。

在运行以下示例脚本之前, 请记得将占位符值 (包括括号) 替换为自己的值。

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>CLI
  
以下 CLI 2.0 命令显示了如何基于登录名将 RBAC 角色分配到 Azure AD 标识。 有关 Azure CLI 分配 RBAC 角色的详细信息, 请参阅[使用 rbac 和 Azure CLI 管理访问权限](../../role-based-access-control/role-assignments-cli.md)。 

在运行以下示例脚本之前, 请记得将占位符值 (包括括号) 替换为自己的值。

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="configure-ntfs-permissions-over-smb"></a>通过 SMB 配置 NTFS 权限 
使用 RBAC 分配共享级别权限后，必须在根目录、目录或文件级别分配正确的 NTFS 权限。 将共享级权限视为用于确定用户是否可以访问共享的高级身份确认程序。 NTFS 权限的作用更精细, 以确定用户可以在目录或文件级别执行哪些操作。

Azure 文件支持全套 NTFS 基本和高级权限。 你可以通过装入并使用 Windows 文件资源管理器或运行 Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)或[Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl)命令来查看和配置 Azure 文件共享中的目录和文件的 NTFS 权限。 

若要使用超级用户权限配置 NTFS, 必须使用已加入域的 VM 中的存储帐户密钥装载共享。 按照下一部分中的说明从命令提示符装载 Azure 文件共享, 并相应地配置 NTFS 权限。

文件共享的根目录支持以下权限集：

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>从命令提示符装载文件共享

使用 Windows net use 命令装载 Azure 文件共享。 请记住, 将以下示例中的占位符值替换为自己的值。 有关装载文件共享的详细信息，请参阅[在 Windows 中装载 Azure 文件共享并对其进行访问](storage-how-to-use-files-windows.md)。

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```
### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>为 Windows 文件资源管理器配置 NTFS 权限
使用 Windows 文件资源管理器向文件共享下的所有目录和文件 (包括根目录) 授予完全权限。

1. 打开 Windows 文件资源管理器, 右键单击文件/目录, 然后选择 "**属性**"
2. 单击 "**安全**" 选项卡
3. 单击 "**编辑 ...** "用于更改权限的按钮
4. 您可以更改现有用户的权限, 或者单击 "**添加 ...** " 向新用户授予权限
5. 在添加新用户的提示窗口中, 在 "**输入要选择的对象名称**" 框中输入要向其授予权限的目标用户名称, 然后单击 "**检查名称**" 以查找目标用户的完整 UPN 名称。
7.  单击 **"确定"**
8.  在 "安全" 选项卡中, 选择要授予新添加用户的所有权限
9.  单击“应用”

### <a name="configure-ntfs-permissions-with-icacls"></a>使用 icacls 配置 NTFS 权限
使用以下 Windows 命令为文件共享（包括根目录）下的所有目录和文件授予完全权限。 请务必将示例中的占位符值替换为你自己的值。

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

有关如何使用 icacls 设置 NTFS 权限以及不同类型的支持权限的详细信息, 请参阅[icacls 的命令行参考](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)。

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>从加入域的 VM 装载文件共享

以下过程将验证 Azure AD 凭据是否设置正确, 以及是否可以从已加入域的 VM 访问 Azure 文件共享: 

使用已向其授予权限的 Azure AD 标识登录到 VM, 如下图所示。

![显示用户身份验证的 Azure AD 登录屏幕的屏幕截图](media/storage-files-active-directory-enable/azure-active-directory-authentication-dialog.png)

使用以下命令装载 Azure 文件共享。 请务必将占位符值替换为你自己的值。 由于你已经过身份验证, 因此不需要提供存储帐户密钥或 Azure AD 用户名和密码。 SMB 上的 Azure AD 使用 Azure AD 凭据支持单一登录体验。

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```

你现在已成功启用通过 SMB Azure AD 身份验证, 并分配了自定义角色, 该角色提供对具有 Azure AD 标识的 Azure 文件共享的访问权限。 若要向其他用户授予对文件共享的访问权限, 请按照将[访问权限分配给某个标识](#assign-access-permissions-to-an-identity)并在[SMB 部分配置 NTFS 权限](#configure-ntfs-permissions-over-smb)中的说明进行操作。

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure 文件以及如何通过 SMB 使用 Azure AD, 请参阅以下资源:

- [Azure 文件简介](storage-files-introduction.md)
- [Azure 文件的 SMB Azure Active Directory 身份验证概述](storage-files-active-directory-overview.md)
- [常见问题](storage-files-faq.md)
