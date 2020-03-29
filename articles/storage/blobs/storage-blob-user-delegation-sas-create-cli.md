---
title: 使用 Azure CLI 为容器或 Blob 创建用户委派 SAS
titleSuffix: Azure Storage
description: 了解如何使用 Azure CLI 使用 Azure 活动目录凭据创建用户委派 SAS。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: e1a81b25042501a166cee122279d21e3702cd419
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75371983"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-the-azure-cli"></a>使用 Azure CLI 为容器或 Blob 创建用户委派 SAS

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

本文演示如何使用 Azure 活动目录 （Azure AD） 凭据使用 Azure CLI 为容器或 Blob 创建用户委派 SAS。

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-latest-version-of-the-azure-cli"></a>安装最新版本的 Azure CLI

要使用 Azure CLI 使用 Azure AD 凭据保护 SAS，请先确保您已安装最新版本的 Azure CLI。 有关安装 Azure CLI 的详细信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

要使用 Azure CLI 创建用户委派 SAS，请确保已安装版本 2.0.78 或更高版本。 要检查已安装的版本，请使用`az --version`命令。

## <a name="sign-in-with-azure-ad-credentials"></a>使用 Azure AD 凭据登录

使用 Azure AD 凭据登录到 Azure CLI。 有关详细信息，请参阅[使用 Azure CLI 登录](/cli/azure/authenticate-azure-cli)。

## <a name="assign-permissions-with-rbac"></a>使用 RBAC 分配权限

要从 Azure PowerShell 创建用户委派 SAS，必须为用于登录到 Azure CLI 的 Azure AD 帐户分配一个角色，其中包括**Microsoft.存储/存储帐户/blob 服务/生成User委派键**操作。 此权限使 Azure AD 帐户能够请求*用户委派密钥*。 用户委派密钥用于对用户委派 SAS 进行签名。 提供**Microsoft.存储/存储帐户/blob 服务/生成User委派键**操作的角色必须在存储帐户、资源组或订阅级别分配。

如果没有足够的权限将 RBAC 角色分配给 Azure AD 安全主体，则可能需要要求帐户所有者或管理员分配必要的权限。

下面的示例分配**存储 Blob 数据参与者**角色，其中包括**Microsoft.存储/存储帐户/blob 服务/生成User委派键**操作。 角色在存储帐户级别进行限定。

请务必将尖括号中的占位符值替换为你自己的值：

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

有关包括**Microsoft.存储/存储帐户/blob 服务/生成User委派键**操作的内置角色的详细信息，请参阅[Azure 资源的内置角色](../../role-based-access-control/built-in-roles.md)。

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>使用 Azure AD 凭据来保护 SAS

使用 Azure CLI 创建用户委派 SAS 时，将隐式创建用于对 SAS 进行签名的用户委派密钥。 您为 SAS 指定的开始时间和到期时间也用作用户委派密钥的开始时间和到期时间。

由于用户委派密钥的有效最大间隔为自开始日期起 7 天，因此应指定在开始时间 7 天内为 SAS 的到期日期。 SAS 在用户委派密钥过期后无效，因此过期时间大于 7 天的 SAS 仍然仅有效 7 天。

创建用户委派 SAS 时，`--auth-mode login`需要`--as-user parameters`和 为`--auth-mode`参数指定*登录名*，以便使用 Azure AD 凭据授权对 Azure 存储发出的请求。 指定参数`--as-user`以指示返回的 SAS 应为用户委派 SAS。

### <a name="create-a-user-delegation-sas-for-a-container"></a>为容器创建用户委派 SAS

要使用 Azure CLI 为容器创建用户委派 SAS，请调用[az 存储容器生成 sas](/cli/azure/storage/container#az-storage-container-generate-sas)命令。

受支持的用户委派容器上的 SAS 的权限包括添加、创建、删除、列表、读取和写入。 可以单独指定权限或组合权限。 有关这些权限的详细信息，请参阅[创建用户委派 SAS](/rest/api/storageservices/create-user-delegation-sas)。

下面的示例返回容器的用户委派 SAS 令牌。 请记住将括号中的占位符值替换为您自己的值：

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions acdlrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

返回的用户委派 SAS 令牌将类似于：

```
se=2019-07-27&sp=r&sv=2018-11-09&sr=c&skoid=<skoid>&sktid=<sktid>&skt=2019-07-26T18%3A01%3A22Z&ske=2019-07-27T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>为 Blob 创建用户委派 SAS

要使用 Azure CLI 为 Blob 创建用户委派 SAS，请调用[az 存储 Blob 生成 sas](/cli/azure/storage/blob#az-storage-blob-generate-sas)命令。

用户委派 SAS 在 blob 上的权限包括添加、创建、删除、读取和写入。 可以单独指定权限或组合权限。 有关这些权限的详细信息，请参阅[创建用户委派 SAS](/rest/api/storageservices/create-user-delegation-sas)。

以下语法返回 Blob 的用户委派 SAS。 该示例指定参数`--full-uri`，该参数返回附加 SAS 令牌的 blob URI。 请记住将括号中的占位符值替换为您自己的值：

```azurecli-interactive
az storage blob generate-sas \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --permissions acdrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
    --full-uri
```

返回的用户委派 SAS URI 将类似于：

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?se=2019-08-03&sp=rw&sv=2018-11-09&sr=b&skoid=<skoid>&sktid=<sktid>&skt=2019-08-02T2
2%3A32%3A01Z&ske=2019-08-03T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

> [!NOTE]
> 用户委派 SAS 不支持使用存储的访问策略定义权限。

## <a name="revoke-a-user-delegation-sas"></a>撤消用户委派 SAS

要从 Azure CLI 撤消用户委派 SAS，请调用[az 存储帐户撤消委派密钥](/cli/azure/storage/account#az-storage-account-revoke-delegation-keys)命令。 此命令撤消与指定存储帐户关联的所有用户委派密钥。 与这些密钥关联的任何共享访问签名将失效。

请务必将尖括号中的占位符值替换为你自己的值：

```azurecli-interactive
az storage account revoke-delegation-keys \
    --name <storage-account> \
    --resource-group <resource-group>
```

> [!IMPORTANT]
> 用户委派密钥和 RBAC 角色分配都由 Azure 存储缓存，因此在启动吊销过程和现有用户委派 SAS 无效之间可能存在延迟。

## <a name="next-steps"></a>后续步骤

- [创建用户委派 SAS （REST API）](/rest/api/storageservices/create-user-delegation-sas)
- [获取用户委派密钥操作](/rest/api/storageservices/get-user-delegation-key)
