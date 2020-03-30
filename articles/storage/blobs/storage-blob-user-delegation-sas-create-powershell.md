---
title: 使用 PowerShell 为容器或 Blob 创建用户委派 SAS
titleSuffix: Azure Storage
description: 了解如何使用 PowerShell 使用 Azure 活动目录凭据创建用户委派 SAS。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 5250a27e6c5fcf012207f1edb95ad46c0aabfe63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536167"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-powershell"></a>使用 PowerShell 为容器或 Blob 创建用户委派 SAS

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

本文演示如何使用 Azure 活动目录 （Azure AD） 凭据为具有 Azure PowerShell 的容器或 Blob 创建用户委派 SAS。

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-powershell-module"></a>安装 Powershell 模块

要使用 PowerShell 创建用户委派 SAS，请安装版本 1.10.0 或更高版本的 Az.Storage 模块。 按照以下步骤安装最新版本的模块：

1. 卸载以前安装的所有 Azure PowerShell：

    - 使用“设置”**** 下的“应用和功能”**** 设置从 Windows 中删除以前安装的所有 Azure PowerShell。
    - 从`%Program Files%\WindowsPowerShell\Modules`中删除所有**Azure**模块。

1. 确保已安装 PowerShellGet 最新版本。 打开 Windows PowerShell 窗口，然后运行以下命令以安装最新版本：

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. 安装 PowerShellGet 后关闭并重新打开 PowerShell 窗口。

1. 安装最新版本的 Azure PowerShell：

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. 请确保已安装 Azure PowerShell 版本 3.2.0 或更高版本。 运行以下命令以安装最新版本的 Azure 存储 PowerShell 模块：

    ```powershell
    Install-Module -Name Az.Storage -Repository PSGallery -Force
    ```

1. 关闭并重新打开 PowerShell 窗口。

要检查安装了哪个版本的 Az.Storage 模块，请运行以下命令：

```powershell
Get-Module -ListAvailable -Name Az.Storage -Refresh
```

有关安装 Azure PowerShell 的详细信息，请参阅[使用 PowerShell 安装 Azure PowerShell。](/powershell/azure/install-az-ps)

## <a name="sign-in-to-azure-powershell-with-azure-ad"></a>使用 Azure AD 登录到 Azure 电源外壳

调用[连接-AzAccount](/powershell/module/az.accounts/connect-azaccount)命令以使用 Azure AD 帐户登录：

```powershell
Connect-AzAccount
```

有关使用 PowerShell 登录的详细信息，请参阅[使用 Azure PowerShell 登录](/powershell/azure/authenticate-azureps)。

## <a name="assign-permissions-with-rbac"></a>使用 RBAC 分配权限

要从 Azure PowerShell 创建用户委派 SAS，必须为用于登录到 PowerShell 的 Azure AD 帐户分配一个角色，其中包括**Microsoft.存储/存储帐户/blob 服务/生成User委派键**操作。 此权限使 Azure AD 帐户能够请求*用户委派密钥*。 用户委派密钥用于对用户委派 SAS 进行签名。 提供**Microsoft.存储/存储帐户/blob 服务/生成User委派键**操作的角色必须在存储帐户、资源组或订阅级别分配。 有关创建用户委派 SAS 的 RBAC 权限的详细信息，请参阅[创建用户委派 SAS](/rest/api/storageservices/create-user-delegation-sas)中的 RBAC 部分中的 **"分配具有 RBAC 权限"** 部分。

如果没有足够的权限将 RBAC 角色分配给 Azure AD 安全主体，则可能需要要求帐户所有者或管理员分配必要的权限。

下面的示例分配**存储 Blob 数据参与者**角色，其中包括**Microsoft.存储/存储帐户/blob 服务/生成User委派键**操作。 角色在存储帐户级别进行限定。

请务必将尖括号中的占位符值替换为你自己的值：

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

有关包括**Microsoft.存储/存储帐户/blob 服务/生成User委派键**操作的内置角色的详细信息，请参阅[Azure 资源的内置角色](../../role-based-access-control/built-in-roles.md)。

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>使用 Azure AD 凭据来保护 SAS

使用 Azure PowerShell 创建用户委派 SAS 时，将隐式创建用于对 SAS 进行签名的用户委派密钥。 您为 SAS 指定的开始时间和到期时间也用作用户委派密钥的开始时间和到期时间。 

由于用户委派密钥的有效最大间隔为自开始日期起 7 天，因此应指定在开始时间 7 天内为 SAS 的到期日期。 SAS 在用户委派密钥过期后无效，因此过期时间大于 7 天的 SAS 仍然仅有效 7 天。

要使用 Azure PowerShell 为容器或 Blob 创建用户委派 SAS，请先创建新的 Azure`-UseConnectedAccount`存储上下文对象，指定参数。 该`-UseConnectedAccount`参数指定该命令在您登录的 Azure AD 帐户下创建上下文对象。

请务必将尖括号中的占位符值替换为你自己的值：

```powershell
$ctx = New-AzStorageContext -StorageAccountName <storage-account> -UseConnectedAccount
```

### <a name="create-a-user-delegation-sas-for-a-container"></a>为容器创建用户委派 SAS

要返回容器的用户委派 SAS 令牌，请调用[New-AzStorage 容器SASToken](/powershell/module/az.storage/new-azstoragecontainersastoken)命令，在以前创建的 Azure 存储上下文对象中传递。

下面的示例返回容器的用户委派 SAS 令牌。 请记住将括号中的占位符值替换为您自己的值：

```powershell
New-AzStorageContainerSASToken -Context $ctx `
    -Name <container> `
    -Permission racwdl `
    -ExpiryTime <date-time>
```

返回的用户委派 SAS 令牌将类似于：

```output
?sv=2018-11-09&sr=c&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-05T22%3A24%3A36Z&ske=2019-08-07T07%3A
00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=rwdl
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>为 Blob 创建用户委派 SAS

要返回 Blob 的用户委派 SAS 令牌，请调用[New-AzStorageBlobSASToken](/powershell/module/az.storage/new-azstorageblobsastoken)命令，在以前创建的 Azure 存储上下文对象中传递。

以下语法返回 Blob 的用户委派 SAS。 该示例指定参数`-FullUri`，该参数返回附加 SAS 令牌的 blob URI。 请记住将括号中的占位符值替换为您自己的值：

```powershell
New-AzStorageBlobSASToken -Context $ctx `
    -Container <container> `
    -Blob <blob> `
    -Permission racwd `
    -ExpiryTime <date-time>
    -FullUri
```

返回的用户委派 SAS URI 将类似于：

```output
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?sv=2018-11-09&sr=b&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-06T21%3A16%3A54Z&ske=2019-08-07T07%3A00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=racwd
```

> [!NOTE]
> 用户委派 SAS 不支持使用存储的访问策略定义权限。

## <a name="revoke-a-user-delegation-sas"></a>撤消用户委派 SAS

要从 Azure PowerShell 撤消用户委派 SAS，请调用 **"撤消-AzstorageAccountUser委派键"** 命令。 此命令撤消与指定存储帐户关联的所有用户委派密钥。 与这些密钥关联的任何共享访问签名将失效。

请务必将尖括号中的占位符值替换为你自己的值：

```powershell
Revoke-AzStorageAccountUserDelegationKeys -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
```

> [!IMPORTANT]
> 用户委派密钥和 RBAC 角色分配都由 Azure 存储缓存，因此在启动吊销过程和现有用户委派 SAS 无效之间可能存在延迟。

## <a name="next-steps"></a>后续步骤

- [创建用户委派 SAS （REST API）](/rest/api/storageservices/create-user-delegation-sas)
- [获取用户委派密钥操作](/rest/api/storageservices/get-user-delegation-key)
