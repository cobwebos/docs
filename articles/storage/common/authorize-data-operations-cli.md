---
title: 使用 Azure CLI 授权访问 blob 或队列数据
titleSuffix: Azure Storage
description: 指定如何针对 Azure CLI 的 blob 或队列数据授权数据操作。 你可以使用 Azure AD 凭据、帐户访问密钥或共享访问签名（SAS）令牌授权数据操作。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/26/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c7091592f8806b6f6655315ae1faace286c2c1f5
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/01/2020
ms.locfileid: "78207688"
---
# <a name="authorize-access-to-blob-or-queue-data-with-azure-cli"></a>使用 Azure CLI 授权访问 blob 或队列数据

Azure 存储提供用于 Azure CLI 的扩展，使你能够指定要如何对 blob 或队列数据授权操作。 可以通过以下方式授权数据操作：

- 具有 Azure Active Directory （Azure AD）安全主体的。 Microsoft 建议使用 Azure AD 凭据以获得高级安全和易用性。
- 使用帐户访问密钥或共享访问签名（SAS）令牌。

## <a name="specify-how-data-operations-are-authorized"></a>指定数据操作的授权方式

用于读取和写入 blob 和队列数据 Azure CLI 命令包括可选的 `--auth-mode` 参数。 指定此参数以指示如何授权数据操作：

- 将 `--auth-mode` 参数设置为 `login` 使用 Azure AD 安全主体（推荐）登录。
- 将 `--auth-mode` 参数设置为旧 `key` 值，以尝试检索用于授权的帐户访问密钥。 如果省略 `--auth-mode` 参数，则 Azure CLI 还将尝试检索访问密钥。

若要使用 `--auth-mode` 参数，请确保已安装 Azure CLI 版本2.0.46 或更高版本。 运行 `az --version` 以查看已安装版本。

> [!IMPORTANT]
> 如果省略 `--auth-mode` 参数或将其设置为 `key`，则 Azure CLI 尝试使用帐户访问密钥进行授权。 在这种情况下，Microsoft 建议你在命令或**AZURE_STORAGE_KEY**环境变量中提供访问密钥。 有关环境变量的详细信息，请参阅 "为[授权参数设置环境变量](#set-environment-variables-for-authorization-parameters)" 一节。
>
> 如果未提供访问密钥，则 Azure CLI 会尝试调用 Azure 存储资源提供程序来检索每个操作的访问密钥。 执行需要对资源提供程序的调用的许多数据操作可能导致限制。 有关资源提供程序限制的详细信息，请参阅[Azure 存储资源提供程序的可伸缩性和性能目标](scalability-targets-resource-provider.md)。

## <a name="authorize-with-azure-ad-credentials"></a>授权 Azure AD 凭据

使用 Azure AD 凭据登录到 Azure CLI 时，将返回 OAuth 2.0 访问令牌。 Azure CLI 将自动使用该令牌对 Blob 或队列存储的后续数据操作授权。 对于支持的操作，无需再通过命令传递帐户密钥或 SAS 令牌。

可以通过基于角色的访问控制（RBAC）向 Azure AD 安全主体分配 blob 和队列数据的权限。 有关 Azure 存储中 RBAC 角色的详细信息，请参阅[使用 RBAC 管理对 Azure 存储数据的访问权限](storage-auth-aad-rbac.md)。

### <a name="permissions-for-calling-data-operations"></a>用于调用数据操作的权限

对 blob 和队列数据的操作支持 Azure 存储扩展。 你可以调用哪些操作取决于授予 Azure AD 安全主体的权限，你可以通过该权限登录 Azure CLI。 通过 RBAC 分配对 Azure 存储容器或队列的权限。 例如，如果分配了**Blob 数据读取器**角色，则可以运行脚本命令，以便从容器或队列中读取数据。 如果分配了 " **Blob 数据参与者**" 角色，则可以运行脚本命令来读取、写入或删除容器或队列或其中包含的数据。

有关容器或队列中每个 Azure 存储操作所需权限的详细信息，请参阅[使用 OAuth 令牌调用存储操作](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)。  

### <a name="example-authorize-an-operation-to-create-a-container-with-azure-ad-credentials"></a>示例：授权操作以创建具有 Azure AD 凭据的容器

下面的示例演示如何使用 Azure AD 凭据从 Azure CLI 创建容器。 若要创建容器，需要登录到 Azure CLI，并需要资源组和存储帐户。 若要了解如何创建这些资源，请参阅[快速入门：创建、下载和列出 Azure CLI 的 blob](../blobs/storage-quickstart-blobs-cli.md)。

1. 创建容器之前，请将[存储 Blob 数据参与者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)角色分配给自己。 即使你是帐户所有者，也需要对存储帐户执行数据操作的显式权限。 有关分配 RBAC 角色的详细信息，请参阅[在 Azure 门户中使用 RBAC 授予对 Azure blob 和队列数据的访问权限](storage-auth-aad-rbac.md)。

    > [!IMPORTANT]
    > RBAC 角色分配可能需要几分钟才能传播。

1. 调用[az storage container create](/cli/azure/storage/container#az-storage-container-create)命令，并将 `--auth-mode` 参数设置为 `login`，以便使用您的 Azure AD 凭据创建容器。 请务必将尖括号中的占位符值替换为你自己的值：

    ```azurecli
    az storage container create \
        --account-name <storage-account> \
        --name sample-container \
        --auth-mode login
    ```

## <a name="authorize-with-the-account-access-key"></a>使用帐户访问密钥授权

如果拥有帐户密钥，则可以调用任何 Azure 存储数据操作。 通常，使用帐户密钥的安全性较低。 如果帐户密钥已泄露，则帐户中的所有数据可能会受到威胁。

下面的示例演示如何使用帐户访问密钥创建容器。 指定帐户密钥，并提供 `--auth-mode` 参数与 `key` 值：

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

## <a name="authorize-with-a-sas-token"></a>使用 SAS 令牌授权

如果拥有 SAS 令牌，可以调用 SAS 允许的数据操作。 下面的示例演示如何使用 SAS 令牌创建容器：

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --sas-token <token>
```

## <a name="set-environment-variables-for-authorization-parameters"></a>为授权参数设置环境变量

可以在环境变量中指定授权参数，以避免在每次调用 Azure 存储数据操作时都包含这些参数。 下表描述了可用的环境变量。

| 环境变量                  | 说明                                                                                                                                                                                                                                                                                                                                                                     |
|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    AZURE_STORAGE_ACCOUNT              |    存储帐户名称。 此变量应与存储帐户密钥或 SAS 令牌一起使用。 如果两者都不存在，则 Azure CLI 尝试使用经过身份验证的 Azure AD 帐户检索存储帐户访问密钥。 如果一次执行大量命令，可能达到 Azure 存储资源提供程序限制。 有关资源提供程序限制的详细信息，请参阅[Azure 存储资源提供程序的可伸缩性和性能目标](scalability-targets-resource-provider.md)。             |
|    AZURE_STORAGE_KEY                  |    存储帐户密钥。 此变量必须与存储帐户名称一起使用。                                                                                                                                                                                                                                                                          |
|    AZURE_STORAGE_CONNECTION_STRING    |    一个连接字符串，其中包含存储帐户密钥或 SAS 令牌。 此变量必须与存储帐户名称一起使用。                                                                                                                                                                                                                       |
|    AZURE_STORAGE_SAS_TOKEN            |    共享访问签名（SAS）令牌。 此变量必须与存储帐户名称一起使用。                                                                                                                                                                                                                                                            |
|    AZURE_STORAGE_AUTH_MODE            |    用于运行命令的授权模式。 允许的值为 `login` （推荐）或 `key`。 如果指定 `login`，Azure CLI 将使用您的 Azure AD 凭据来授权数据操作。 如果指定旧的 `key` 模式，则 Azure CLI 尝试查询帐户访问密钥并使用密钥对命令进行授权。    |

## <a name="next-steps"></a>后续步骤

- [使用 Azure CLI 分配用于访问 blob 和队列数据的 RBAC 角色](storage-auth-aad-rbac-cli.md)
- [使用 Azure 资源的托管标识授予对 blob 和队列数据的访问权限](storage-auth-aad-msi.md)
