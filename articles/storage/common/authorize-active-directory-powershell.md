---
title: 使用 Azure AD 凭据运行 PowerShell 命令以访问 Blob 或队列数据
titleSuffix: Azure Storage
description: PowerShell 支持使用 Azure AD 凭据登录，以便对 Azure 存储 Blob 和队列数据运行命令。 针对该会话提供了一个访问令牌，该访问令牌用于授权调用操作。 权限取决于分配给 Azure AD 安全主体的 Azure 角色。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/18/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 75ca39ad00966928bb7887cb14255470b17579dd
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589140"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>使用 Azure AD 凭据运行 PowerShell 命令以访问 Blob 或队列数据

Azure 存储为 PowerShell 提供扩展，使用户可使用 Azure Active Directory (Azure AD) 凭据登录并运行脚本命令。 使用 Azure AD 凭据登录 PowerShell 时，会返回 OAuth 2.0 访问令牌。 PowerShell 自动使用该令牌针对 Blob 或队列存储进行后续数据操作授权。 对于支持的操作，无需再通过命令传递帐户密钥或 SAS 令牌。

可通过基于角色的访问控制 (RBAC) 向 Azure AD 安全主体分配对 Blob 和队列数据的权限。 有关 Azure 存储空间中的 Azure 角色的详细信息，请参阅 [使用 RBAC 管理对 Azure 存储数据的访问权限](storage-auth-aad-rbac.md)。

## <a name="supported-operations"></a>支持的操作

Azure 存储扩展支持针对 blob 和队列数据的操作。 可调用的操作取决于向 Azure AD 安全主体授予的权限，此安全主体用于登录 PowerShell。 Azure 存储容器或队列的权限通过 RBAC 进行分配。 例如，如果为你分配了“Blob 数据读取者”角色，你可以运行从容器或队列读取数据的脚本命令。  如果为你分配了“Blob 数据参与者”角色，你可以运行脚本命令来读取、写入或删除容器、队列或其中所含数据。 

若要详细了解针对容器或队列的每个 Azure 存储操作所需的权限，请参阅[使用 OAuth 令牌调用存储操作](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)。  

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>使用 Azure AD 凭据调用 PowerShell 命令

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

若要使用 Azure PowerShell 登录并使用 Azure AD 凭据针对 Azure 存储运行后续操作，请创建一个存储上下文用于引用存储帐户，并包含 `-UseConnectedAccount` 参数。

以下示例演示如何在 Azure PowerShell 中使用 Azure AD 凭据，在新的存储帐户中创建一个容器。 请务必将尖括号中的占位符值替换为你自己的值：

1. 使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 命令登录到 Azure 帐户。

    ```powershell
    Connect-AzAccount
    ```

    若要详细了解如何使用 PowerShell 登录 Azure，请参阅[使用 Azure PowerShell 登录](/powershell/azure/authenticate-azureps)。

1. 调用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 创建 Azure 资源组。 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. 调用 [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) 创建存储帐户。

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. 调用 [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext) 获取用于指定新存储帐户的存储帐户上下文。 对存储帐户执行操作时，可以引用上下文而不是重复传入凭据。 包含 `-UseConnectedAccount` 参数，以使用 Azure AD 凭据调用任何后续数据操作：

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. 创建容器之前，请向自己分配[存储 Blob 数据参与者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)角色。 即使你是帐户所有者，也需要显式权限才能针对存储帐户执行数据操作。 有关分配 Azure 角色的详细信息，请参阅 [在 Azure 门户中使用 RBAC 授予对 azure blob 和队列数据的访问权限](storage-auth-aad-rbac.md)。

    > [!IMPORTANT]
    > Azure 角色分配可能需要几分钟才能传播。

1. 调用 [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer) 创建容器。 由于此调用使用在前面步骤中创建的上下文，因此将使用你的 Azure AD 凭据创建容器。

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>后续步骤

- [使用 PowerShell 分配 Azure 角色以访问 blob 和队列数据](storage-auth-aad-rbac-powershell.md)
- [使用 Azure 资源托管标识授予对 Blob 和队列数据的访问权限](storage-auth-aad-msi.md)
