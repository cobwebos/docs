---
title: 使用 Azure AD 凭据运行 PowerShell 命令以访问 blob 或队列数据
titleSuffix: Azure Storage
description: PowerShell 支持 Azure AD 凭据登录以在 Azure 存储 blob 和队列数据上运行命令。 针对该会话提供了一个访问令牌，该访问令牌用于授权调用操作。 权限取决于分配给 Azure AD 安全主体的 RBAC 角色。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 373b939ac63f31ccaf6a9f01fac92920e19074ed
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75553444"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>使用 Azure AD 凭据运行 PowerShell 命令以访问 blob 或队列数据

Azure 存储为 PowerShell 提供扩展，使你能够使用 Azure Active Directory （Azure AD）凭据登录和运行脚本命令。 使用 Azure AD 凭据登录到 PowerShell 时，将返回 OAuth 2.0 访问令牌。 PowerShell 会自动使用该令牌对 Blob 或队列存储的后续数据操作授权。 对于支持的操作，无需再通过命令传递帐户密钥或 SAS 令牌。

可以通过基于角色的访问控制（RBAC）向 Azure AD 安全主体分配 blob 和队列数据的权限。 有关 Azure 存储中 RBAC 角色的详细信息，请参阅[使用 RBAC 管理对 Azure 存储数据的访问权限](storage-auth-aad-rbac.md)。

## <a name="supported-operations"></a>支持的操作

对 blob 和队列数据的操作支持 Azure 存储扩展。 你可以调用哪些操作取决于授予的 Azure AD 安全主体的权限，你可以在其中登录到 PowerShell。 通过 RBAC 分配对 Azure 存储容器或队列的权限。 例如，如果已将**Blob 数据读取器**角色分配给你，则可以运行脚本命令，以便从容器或队列中读取数据。 如果已为你分配了 " **Blob 数据参与者**" 角色，则可以运行脚本命令来读取、写入或删除容器或队列或它们所包含的数据。

有关容器或队列中每个 Azure 存储操作所需权限的详细信息，请参阅[使用 OAuth 令牌调用存储操作](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)。  

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>使用 Azure AD 凭据调用 PowerShell 命令

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

若要使用 Azure PowerShell 使用 Azure AD 凭据登录并运行针对 Azure 存储的后续操作，请创建存储上下文来引用存储帐户，并包括 `-UseConnectedAccount` 参数。

下面的示例演示如何使用 Azure AD 凭据 Azure PowerShell 在新存储帐户中创建容器。 请务必将尖括号中的占位符值替换为你自己的值：

1. 通过[AzAccount](/powershell/module/az.accounts/connect-azaccount)命令登录到 Azure 帐户：

    ```powershell
    Connect-AzAccount
    ```

    有关通过 PowerShell 登录到 Azure 的详细信息，请参阅[登录 Azure PowerShell](/powershell/azure/authenticate-azureps)。

1. 通过调用[AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)创建 Azure 资源组。 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. 通过调用[AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)创建存储帐户。

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. 通过调用[AzStorageContext](/powershell/module/az.storage/new-azstoragecontext)获取指定新存储帐户的存储帐户上下文。 在存储帐户上操作时，可以引用上下文，而不是重复传入凭据。 包括 `-UseConnectedAccount` 参数，以使用您的 Azure AD 凭据调用任何后续数据操作：

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. 创建容器之前，请将[存储 Blob 数据参与者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)角色分配给自己。 即使你是帐户所有者，也需要对存储帐户执行数据操作的显式权限。 有关分配 RBAC 角色的详细信息，请参阅[在 Azure 门户中使用 RBAC 授予对 Azure blob 和队列数据的访问权限](storage-auth-aad-rbac.md)。

    > [!IMPORTANT]
    > RBAC 角色分配可能需要几分钟才能传播。

1. 通过调用[AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer)创建容器。 由于此调用使用在前面的步骤中创建的上下文，因此会使用 Azure AD 凭据创建容器。

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>后续步骤

- [使用 PowerShell 分配用于访问 blob 和队列数据的 RBAC 角色](storage-auth-aad-rbac-powershell.md)
- [使用 Azure 资源的托管标识授予对 blob 和队列数据的访问权限](storage-auth-aad-msi.md)
