---
title: 使用 Azure CLI 授权访问 Blob 或队列数据
titleSuffix: Azure Storage
description: 指定如何使用 Azure CLI 授权针对 Blob 或队列数据执行数据操作。 您可以使用 Azure AD 凭据、帐户访问密钥或共享访问签名 （SAS） 令牌授权数据操作。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/26/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c7091592f8806b6f6655315ae1faace286c2c1f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78207688"
---
# <a name="authorize-access-to-blob-or-queue-data-with-azure-cli"></a>使用 Azure CLI 授权访问 Blob 或队列数据

Azure 存储为 Azure CLI 提供扩展，使您能够指定如何授权对 Blob 或队列数据的操作。 您可以通过以下方式授权数据操作：

- 具有 Azure 活动目录 （Azure AD） 安全主体。 Microsoft 建议使用 Azure AD 凭据，以提供卓越的安全性和易用性。
- 使用帐户访问密钥或共享访问签名 （SAS） 令牌。

## <a name="specify-how-data-operations-are-authorized"></a>指定数据操作的授权方式

用于读取和写入 Blob 和队列数据的 Azure CLI`--auth-mode`命令包括可选参数。 指定此参数以指示如何授权数据操作：

- 将`--auth-mode`参数`login`设置为使用 Azure AD 安全主体登录（建议）。
- 将`--auth-mode`参数设置为旧`key`值，以尝试检索用于授权的帐户访问密钥。 如果省略了参数，`--auth-mode`则 Azure CLI 还会尝试检索访问密钥。

要使用`--auth-mode`参数，请确保已安装 Azure CLI 版本 2.0.46 或更高版本。 运行 `az --version` 以查看已安装版本。

> [!IMPORTANT]
> 如果省略参数`--auth-mode`或将其设置为`key`，则 Azure CLI 会尝试使用帐户访问密钥进行授权。 在这种情况下，Microsoft 建议您在命令或**AZURE_STORAGE_KEY**环境变量中提供访问密钥。 有关环境变量的详细信息，请参阅标题为["设置授权参数的环境变量"的部分](#set-environment-variables-for-authorization-parameters)。
>
> 如果不提供访问密钥，Azure CLI 将尝试调用 Azure 存储资源提供程序来检索每个操作。 执行许多需要调用资源提供程序的数据操作可能会导致限制。 有关资源提供程序限制的详细信息，请参阅[Azure 存储资源提供程序 的可伸缩性和性能目标](scalability-targets-resource-provider.md)。

## <a name="authorize-with-azure-ad-credentials"></a>使用 Azure AD 凭据进行授权

使用 Azure AD 凭据登录 Azure CLI 时，会返回 OAuth 2.0 访问令牌。 Azure CLI 自动使用该令牌针对 Blob 或队列存储进行后续数据操作授权。 对于支持的操作，无需再通过命令传递帐户密钥或 SAS 令牌。

可通过基于角色的访问控制 (RBAC) 向 Azure AD 安全主体分配对 Blob 和队列数据的权限。 有关 Azure 存储中 RBAC 角色的详细信息，请参阅[通过 RBAC 管理 Azure 存储数据访问权限](storage-auth-aad-rbac.md)。

### <a name="permissions-for-calling-data-operations"></a>调用数据操作的权限

Azure 存储扩展支持针对 blob 和队列数据的操作。 可调用的操作取决于向 Azure AD 安全主体授予的权限，此安全主体用于登录 Azure CLI。 Azure 存储容器或队列的权限通过 RBAC 进行分配。 例如，如果为你分配了“Blob 数据读取者”角色，你可以运行从容器或队列读取数据的脚本命令。**** 如果为你分配了“Blob 数据参与者”角色，你可以运行脚本命令来读取、写入或删除容器、队列或其中所含数据。****

若要详细了解针对容器或队列的每个 Azure 存储操作所需的权限，请参阅[使用 OAuth 令牌调用存储操作](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)。  

### <a name="example-authorize-an-operation-to-create-a-container-with-azure-ad-credentials"></a>示例：授权操作以创建具有 Azure AD 凭据的容器

下面的示例演示如何使用 Azure AD 凭据从 Azure CLI 创建容器。 要创建容器，您需要登录到 Azure CLI，并且需要资源组和存储帐户。 要了解如何创建这些资源，请参阅[快速入门：使用 Azure CLI 创建、下载和列出 blob。](../blobs/storage-quickstart-blobs-cli.md)

1. 创建容器之前，请向自己分配[存储 Blob 数据参与者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)角色。 即使你是帐户所有者，也需要显式权限才能针对存储帐户执行数据操作。 有关如何分配 RBAC 角色的详细信息，请参阅[在 Azure 门户中使用 RBAC 授予对 Azure Blob 和队列数据的访问权限](storage-auth-aad-rbac.md)。

    > [!IMPORTANT]
    > 传播 RBAC 角色分配可能需要花费几分钟时间。

1. 使用`--auth-mode`参数集`login`使用 Azure AD 凭据创建容器的 az[存储容器创建](/cli/azure/storage/container#az-storage-container-create)命令。 请务必将尖括号中的占位符值替换为你自己的值：

    ```azurecli
    az storage container create \
        --account-name <storage-account> \
        --name sample-container \
        --auth-mode login
    ```

## <a name="authorize-with-the-account-access-key"></a>使用帐户访问密钥进行授权

如果拥有帐户密钥，可以调用任何 Azure 存储数据操作。 通常，使用帐户密钥不太安全。 如果帐户密钥被泄露，您的帐户中的所有数据都可能受到威胁。

下面的示例演示如何使用帐户访问密钥创建容器。 指定帐户密钥，`--auth-mode`并提供参数`key`的值：

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

## <a name="authorize-with-a-sas-token"></a>使用 SAS 令牌进行授权

如果您拥有 SAS 令牌，则可以调用 SAS 允许的数据操作。 下面的示例演示如何使用 SAS 令牌创建容器：

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --sas-token <token>
```

## <a name="set-environment-variables-for-authorization-parameters"></a>为授权参数设置环境变量

可以在环境变量中指定授权参数，以避免在每次调用 Azure 存储数据操作时都包括它们。 下表描述了可用的环境变量。

| 环境变量                  | 描述                                                                                                                                                                                                                                                                                                                                                                     |
|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    AZURE_STORAGE_ACCOUNT              |    存储帐户名称。 此变量应与存储帐户密钥或 SAS 令牌结合使用。 如果两者都不存在，Azure CLI 会尝试使用经过身份验证的 Azure AD 帐户检索存储帐户访问密钥。 如果一次执行大量命令，则可能会达到 Azure 存储资源提供程序限制。 有关资源提供程序限制的详细信息，请参阅[Azure 存储资源提供程序 的可伸缩性和性能目标](scalability-targets-resource-provider.md)。             |
|    AZURE_STORAGE_KEY                  |    存储帐户密钥。 此变量必须与存储帐户名称结合使用。                                                                                                                                                                                                                                                                          |
|    AZURE_STORAGE_CONNECTION_STRING    |    包含存储帐户密钥或 SAS 令牌的连接字符串。 此变量必须与存储帐户名称结合使用。                                                                                                                                                                                                                       |
|    AZURE_STORAGE_SAS_TOKEN            |    共享访问签名 （SAS） 令牌。 此变量必须与存储帐户名称结合使用。                                                                                                                                                                                                                                                            |
|    AZURE_STORAGE_AUTH_MODE            |    运行命令的授权模式。 允许的值为`login`（推荐）或`key`。 如果指定`login`，Azure CLI 将使用 Azure AD 凭据来授权数据操作。 如果指定旧模式`key`，Azure CLI 将尝试查询帐户访问密钥，并授权使用该键的命令。    |

## <a name="next-steps"></a>后续步骤

- [使用 Azure CLI 为 blob 和队列数据分配 RBAC 角色](storage-auth-aad-rbac-cli.md)
- [使用 Azure 资源托管标识授予对 Blob 和队列数据的访问权限](storage-auth-aad-msi.md)
