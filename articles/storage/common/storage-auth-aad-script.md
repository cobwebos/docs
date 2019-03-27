---
title: Azure CLI 或 PowerShell 下运行命令的 Azure AD 标识来访问 blob 和队列数据 |Microsoft Docs
description: Azure CLI 和 PowerShell 支持使用 Azure 存储 blob 和队列数据上运行命令的 Azure AD 标识登录。 针对该会话提供了一个访问令牌，该访问令牌用于授权调用操作。 权限取决于分配给 Azure AD 标识的 RBAC 角色。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/26/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d1fdafaaecd448fd09fc40cf5f6173ce600ac4f9
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2019
ms.locfileid: "58483199"
---
# <a name="use-an-azure-ad-identity-to-access-blob-and-queue-data-with-cli-or-powershell"></a>使用 CLI 或 PowerShell 使用 Azure AD 标识来访问 blob 和队列数据

Azure 存储提供的 Azure CLI 和 PowerShell，您可以登录并运行基于 Azure Active Directory (Azure AD) 标识的脚本命令扩展。 Azure AD 标识可以是用户、组或应用程序服务主体，也可以是 [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。 可以分配权限以访问 blob 和队列数据到 Azure AD 标识通过基于角色的访问控制 (RBAC)。 有关在 Azure 存储中的 RBAC 角色的详细信息，请参阅[到 Azure 存储数据，使用 RBAC 管理访问权限](storage-auth-aad-rbac.md)。

当您登录到 Azure CLI 或 PowerShell 与 Azure AD 标识时，会返回访问令牌以该身份访问 Azure 存储。 CLI 或 PowerShell 之后自动使用该令牌针对 Azure 存储进行操作授权。 对于支持的操作，无需再通过命令传递帐户密钥或 SAS 令牌。

## <a name="supported-operations"></a>支持的操作

容器和队列上的操作支持扩展。 可以调用哪些操作取决于与你登录到 Azure CLI 或 PowerShell 的 Azure AD 标识授予的权限。 Azure 存储容器或队列的权限通过基于角色的访问控制 (RBAC) 进行分配。 例如，如果向该标识分配数据读取者角色，则可运行从容器或队列读取数据的脚本命令。 如果向该标识分配数据贡献者角色，则可运行脚本命令来读取、写入或删除容器、队列或其中所含数据。 

若要详细了解针对容器或队列的每个 Azure 存储操作所需的权限，请参阅[用于调用 REST 操作的权限](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations)。  

## <a name="call-cli-commands-using-azure-ad-credentials"></a>调用 CLI 命令中使用 Azure AD 凭据

Azure CLI 支持`--auth-mode`blob 和队列数据操作的参数：

- 设置`--auth-mode`参数`login`使用 Azure AD 安全主体进行登录。
- 将 `--auth-mode` 参数设置为旧的 `key` 值，以便在未提供帐户的身份验证参数时，查询帐户密钥。 

下面的示例演示如何创建一个容器中新的存储帐户从 Azure CLI 使用 Azure AD 凭据。 请记住将尖括号中的占位符值替换为你自己的值： 

1. 请确保已安装 Azure CLI 版本 2.0.46 或更高版本。 运行 `az --version` 以查看已安装版本。

1. 运行`az login`并在浏览器窗口中进行身份验证： 

    ```azurecli
    az login
    ```
    
1. 指定你所需的订阅。 使用 [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) 创建资源组。 创建存储帐户中资源组使用[az 存储帐户创建](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create): 

    ```azurecli
    az account set --subscription <subscription-id>

    az group create \
        --name sample-resource-group-cli \
        --location eastus

    az storage account create \
        --name <storage-account> \
        --resource-group sample-resource-group-cli \
        --location eastus \
        --sku Standard_LRS \
        --encryption-services blob
    ```
    
1. 在创建容器之前，将分配[存储 Blob 数据参与者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview)给自己的角色。 即使你是帐户所有者，你将需要显式权限才能执行针对存储帐户的数据操作。 有关分配 RBAC 角色的详细信息，请参阅[授予对 Azure blob 和队列数据使用 RBAC 在 Azure 门户中访问](storage-auth-aad-rbac.md)。

    > [!IMPORTANT]
    > RBAC 角色分配可能需要几分钟来传播。
    
1. 调用[az 存储容器创建](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create)命令`--auth-mode`参数设置为`login`若要创建使用 Azure AD 凭据的容器：

    ```azurecli
    az storage container create \ 
        --account-name <storage-account> \ 
        --name sample-container \
        --auth-mode login
    ```

与 `--auth-mode` 参数关联的环境变量为 `AZURE_STORAGE_AUTH_MODE`。 您可以在环境变量，以避免将其包括在 Azure 存储数据操作的每个调用中指定适当的值。

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>调用 PowerShell 命令中使用 Azure AD 凭据

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

若要使用 Azure PowerShell 登录，并使用 Azure AD 凭据的 Azure 存储模拟器运行后续操作，创建存储上下文，以引用存储帐户，并包括`-UseConnectedAccount`参数。

下面的示例演示如何创建一个容器中新的存储帐户从 Azure PowerShell 使用 Azure AD 凭据。 请记住将尖括号中的占位符值替换为你自己的值：

1. 登录到 Azure 订阅与`Connect-AzAccount`命令并按照屏幕说明操作以输入你的 Azure AD 凭据： 

    ```powershell
    Connect-AzAccount
    ```
    
1. 通过调用创建的 Azure 资源组[新建 AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)。 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. 通过调用创建的存储帐户[新建 AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)。

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. 获取通过调用指定新的存储帐户的存储帐户上下文[新建 AzStorageContext](/powershell/module/az.storage/new-azstoragecontext)。 时作用于存储帐户，可以引用而不是重复的凭据中传递上下文。 包括`-UseConnectedAccount`参数来调用使用 Azure AD 凭据的任何后续数据操作：

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. 在创建容器之前，将分配[存储 Blob 数据参与者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview)给自己的角色。 即使你是帐户所有者，你将需要显式权限才能执行针对存储帐户的数据操作。 有关分配 RBAC 角色的详细信息，请参阅[授予对 Azure blob 和队列数据使用 RBAC 在 Azure 门户中访问](storage-auth-aad-rbac.md)。

    > [!IMPORTANT]
    > RBAC 角色分配可能需要几分钟来传播。

1. 通过调用创建一个容器[新建 AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer)。 因为此调用将使用在上一步骤中创建的上下文，使用你的 Azure AD 凭据创建容器。 

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>后续步骤

- 若要了解有关 Azure 存储的 RBAC 角色的详细信息，请参阅[存储的数据使用 RBAC 管理访问权限](storage-auth-aad-rbac.md)。
- 若要了解如何使用适用于使用 Azure 存储的 Azure 资源管理的标识，请参阅[blob 和队列使用 Azure 进行身份验证访问 Azure 资源的托管标识](storage-auth-aad-msi.md)。
- 若要了解如何从存储应用程序内授予容器和队列访问权限，请参阅[将 Azure AD 与存储应用程序配合使用](storage-auth-aad-app.md)。
