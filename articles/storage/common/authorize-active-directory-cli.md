---
title: 使用 Azure AD 凭据运行 Azure CLI 命令来访问 blob 或队列数据
titleSuffix: Azure Storage
description: Azure CLI 支持通过 Azure AD 凭据登录以在 Azure 存储 blob 和队列数据上运行命令。 针对该会话提供了一个访问令牌，该访问令牌用于授权调用操作。 权限取决于分配给 Azure AD 安全主体的 RBAC 角色。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/26/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 4133c081823f1cc319480c1bc847b672df0374d4
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77660608"
---
# <a name="run-azure-cli-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>使用 Azure AD 凭据运行 Azure CLI 命令来访问 blob 或队列数据

Azure 存储为 Azure CLI 提供扩展，使你能够使用 Azure Active Directory （Azure AD）凭据登录和运行脚本命令。 使用 Azure AD 凭据登录到 Azure CLI 时，将返回 OAuth 2.0 访问令牌。 Azure CLI 将自动使用该令牌对 Blob 或队列存储的后续数据操作授权。 对于支持的操作，无需再通过命令传递帐户密钥或 SAS 令牌。

可以通过基于角色的访问控制（RBAC）向 Azure AD 安全主体分配 blob 和队列数据的权限。 有关 Azure 存储中 RBAC 角色的详细信息，请参阅[使用 RBAC 管理对 Azure 存储数据的访问权限](storage-auth-aad-rbac.md)。

## <a name="supported-operations"></a>支持的操作

对 blob 和队列数据的操作支持 Azure 存储扩展。 你可以调用哪些操作取决于授予 Azure AD 安全主体的权限，你可以通过该权限登录 Azure CLI。 通过 RBAC 分配对 Azure 存储容器或队列的权限。 例如，如果分配了**Blob 数据读取器**角色，则可以运行脚本命令，以便从容器或队列中读取数据。 如果分配了 " **Blob 数据参与者**" 角色，则可以运行脚本命令来读取、写入或删除容器或队列或其中包含的数据。

有关容器或队列中每个 Azure 存储操作所需权限的详细信息，请参阅[使用 OAuth 令牌调用存储操作](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)。  

## <a name="call-azure-cli-commands-using-azure-ad-credentials"></a>使用 Azure AD 凭据调用 Azure CLI 命令

Azure CLI 支持 blob 和队列数据操作的 `--auth-mode` 参数：

- 将 `--auth-mode` 参数设置为 `login` 使用 Azure AD 安全主体登录。
- 将 `--auth-mode` 参数设置为旧的 `key` 值，以便在未提供帐户的身份验证参数时，查询帐户密钥。

下面的示例演示如何使用 Azure AD 凭据 Azure CLI 在新存储帐户中创建容器。 请务必将尖括号中的占位符值替换为你自己的值：

1. 请确保已安装 Azure CLI 版本2.0.46 或更高版本。 运行 `az --version` 以查看已安装版本。

1. 在浏览器窗口中运行 `az login` 和身份验证：

    ```azurecli
    az login
    ```

1. 指定所需的订阅。 使用 [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) 创建资源组。 使用[az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)在该资源组中创建存储帐户：

    ```azurecli
    az account set --subscription <subscription-id>

    az group create \
        --name sample-resource-group-cli \
        --location eastus

    az storage account create \
        --name <storage-account> \
        --resource-group sample-resource-group-cli \
        --location eastus \
        --sku Standard_ZRS \
        --encryption-services blob
    ```

1. 创建容器之前，请将[存储 Blob 数据参与者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)角色分配给自己。 即使你是帐户所有者，也需要对存储帐户执行数据操作的显式权限。 有关分配 RBAC 角色的详细信息，请参阅[在 Azure 门户中使用 RBAC 授予对 Azure blob 和队列数据的访问权限](storage-auth-aad-rbac.md)。

    > [!IMPORTANT]
    > RBAC 角色分配可能需要几分钟才能传播。

1. 调用[az storage container create](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create)命令，并将 `--auth-mode` 参数设置为 `login`，以便使用您的 Azure AD 凭据创建容器：

    ```azurecli
    az storage container create \
        --account-name <storage-account> \
        --name sample-container \
        --auth-mode login
    ```

与 `--auth-mode` 参数关联的环境变量为 `AZURE_STORAGE_AUTH_MODE`。 可以在环境变量中指定适当的值，以避免在每次调用 Azure 存储数据操作时都包含此值。

## <a name="next-steps"></a>后续步骤

- [使用 Azure CLI 分配用于访问 blob 和队列数据的 RBAC 角色](storage-auth-aad-rbac-cli.md)
- [使用 Azure 资源的托管标识授予对 blob 和队列数据的访问权限](storage-auth-aad-msi.md)
