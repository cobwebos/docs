---
title: 配置容器和 blob 的匿名公共读取访问权限
titleSuffix: Azure Storage
description: 了解如何允许或禁止匿名访问存储帐户的 blob 数据。 设置容器公共访问设置以使容器和 blob 可供匿名访问。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/23/2020
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: daf4eb4492f723b049dc62a16351e04ffc252337
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87289251"
---
# <a name="configure-anonymous-public-read-access-for-containers-and-blobs"></a>配置容器和 blob 的匿名公共读取访问权限

Azure 存储为容器和 blob 支持可选的匿名公共读取访问。 默认情况下，不允许匿名访问数据。 除非显式启用匿名访问，否则对容器及其 blob 的所有请求都必须获得授权。 如果将容器的公共访问级别设置配置为允许匿名访问，则客户端可以读取该容器中的数据而无需对请求进行授权。

> [!WARNING]
> 当容器配置为公共访问时，任何客户端都可以读取该容器中的数据。 公共访问权限会带来潜在的安全风险，因此，如果你的方案不需要，Microsoft 建议你禁止将其用于存储帐户。 有关详细信息，请参阅[阻止对容器和 blob 的匿名公共读取访问](anonymous-read-access-prevent.md)。

本文介绍如何配置容器及其 blob 的匿名公共读取访问权限。 有关如何从客户端应用程序匿名访问 blob 数据的信息，请参阅[使用 .net 匿名访问公共容器和 blob](anonymous-read-access-client.md)。

## <a name="about-anonymous-public-read-access"></a>关于匿名公共读取访问

默认情况下，始终禁止对你的数据进行公共访问。 有两个单独的设置会影响公共访问：

1. **允许对存储帐户进行公共访问。** 默认情况下，存储帐户允许具有适当权限的用户启用对容器的公共访问。 Blob 数据不能用于公共访问，除非用户使用额外步骤显式配置容器的公共访问设置。
1. **配置容器的公共访问设置。** 默认情况下，容器的公共访问设置处于禁用状态，这意味着对容器或其数据的每个请求都需要授权。 具有适当权限的用户可以修改容器的公共访问权限设置，以便仅在允许对存储帐户进行匿名访问时才启用匿名访问。

下表总结了这两个设置如何共同影响容器的公共访问。

| 公共访问设置 | 已禁用容器的公共访问权限（默认设置） | 容器的公共访问权限设置为容器 | 公共访问容器设置为 Blob |
|--|--|--|--|
| 不允许对存储帐户进行公共访问 | 不能对存储帐户中的任何容器进行公共访问。 | 不能对存储帐户中的任何容器进行公共访问。 存储帐户设置将覆盖容器设置。 | 不能对存储帐户中的任何容器进行公共访问。 存储帐户设置将覆盖容器设置。 |
| 允许对存储帐户进行公共访问（默认设置） | 没有对此容器的公共访问权限（默认配置）。 | 允许公共访问此容器及其 blob。 | 允许公共访问此容器中的 blob，但不允许访问容器本身。 |

## <a name="allow-or-disallow-public-read-access-for-a-storage-account"></a>允许或禁止对存储帐户进行公共读取访问

默认情况下，将存储帐户配置为允许具有适当权限的用户启用对容器的公共访问。 允许公共访问时，具有相应权限的用户可以修改容器的公共访问设置，以启用对该容器中的数据的匿名公共访问。 Blob 数据永远不能用于公共访问，除非用户使用额外步骤显式配置容器的公共访问设置。

请记住，默认情况下，容器的公共访问权限始终处于关闭状态，必须显式配置为允许匿名请求。 无论存储帐户的设置如何，你的数据将永远不能用于公共访问，除非具有适当权限的用户执行此附加步骤以在容器上启用公共访问。

不允许对存储帐户进行公共访问，禁止对该帐户中的所有容器和 blob 进行匿名访问。 如果帐户不允许公共访问，则不能将容器的公共访问设置配置为允许匿名访问。 为了提高安全性，Microsoft 建议你禁止对存储帐户进行公共访问，除非你的方案要求用户匿名访问 blob 资源。

> [!IMPORTANT]
> 禁用存储帐户的公共访问权限会替代该存储帐户中所有容器的公共访问设置。 如果不允许对存储帐户进行公共访问，以后对该帐户的任何匿名请求都将失败。 在更改此设置之前，请务必了解可能会以匿名方式访问存储帐户中数据的客户端应用程序的影响。 有关详细信息，请参阅[阻止对容器和 blob 的匿名公共读取访问](anonymous-read-access-prevent.md)。

若要允许或禁止访问存储帐户的公共访问权限，请使用 Azure 门户或 Azure CLI 来配置帐户的**blobPublicAccess**属性。 此属性适用于使用 Azure 资源管理器部署模型创建的所有存储帐户。 有关详细信息，请参阅[存储帐户概述](../common/storage-account-overview.md)。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要允许或禁止 Azure 门户中存储帐户的公共访问权限，请执行以下步骤：

1. 导航到 Azure 门户中的存储帐户。
1. 找到 "**设置**" 下的**配置**设置。
1. 将**Blob 公共访问权限**设置为 "**已启用**" 或 "**已禁用**"。

    :::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="显示如何允许或禁止对帐户使用 blob 公共访问权限的屏幕截图":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要允许或禁止具有 Azure CLI 的存储帐户的公共访问权限，请先通过调用[az resource show](/cli/azure/resource#az-resource-show)命令获取存储帐户的资源 ID。 接下来，调用[az resource update](/cli/azure/resource#az-resource-update)命令设置存储帐户的**allowBlobPublicAccess**属性。 若要允许公共访问，请将**allowBlobPublicAccess**属性设置为 true;若要禁止，请将其设置为**false**。

以下示例不允许对存储帐户进行公共 blob 访问。 请记住，用自己的值替换括号中的占位符值：

```azurecli-interactive
storage_account_id=$(az resource show \
    --name anonpublicaccess \
    --resource-group storagesamples-rg \
    --resource-type Microsoft.Storage/storageAccounts \
    --query id \
    --output tsv)

az resource update \
    --ids $storage_account_id \
    --set properties.allowBlobPublicAccess=false
    ```
```

---

> [!NOTE]
> 禁止对存储帐户进行公共访问不会影响该存储帐户中托管的任何静态网站。 **$Web**容器始终可公开访问。

## <a name="check-whether-public-access-is-allowed-for-a-storage-account"></a>检查是否允许对存储帐户进行公共访问

若要检查是否允许对存储帐户进行公共访问，请获取**allowBlobPublicAccess**属性的值。 若要同时检查大量存储帐户的此属性，请使用 Azure 资源图资源管理器。

> [!IMPORTANT]
> 默认情况下， **allowBlobPublicAccess**属性不会设置，并且不会返回值，除非你显式设置该值。 当属性值为**null**或为**true**时，存储帐户允许公共访问。

### <a name="check-whether-public-access-is-allowed-for-a-single-storage-account"></a>检查是否允许对单个存储帐户进行公共访问

若要使用 Azure CLI 检查单个存储帐户是否允许公共访问，请调用[az resource show](/cli/azure/resource#az-resource-show)命令并查询**allowBlobPublicAccess**属性：

```azurecli-interactive
az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.allowBlobPublicAccess \
    --output tsv
```

### <a name="check-whether-public-access-is-allowed-for-a-set-of-storage-accounts"></a>检查是否允许对一组存储帐户进行公共访问

若要检查是否允许跨具有最佳性能的一组存储帐户进行公共访问，可以使用 Azure 门户中的 Azure 资源图资源管理器。 若要了解有关使用资源图资源管理器的详细信息，请参阅[快速入门：使用 Azure 资源关系图资源管理器运行第一个资源图形查询](/azure/governance/resource-graph/first-query-portal)。

在资源图资源管理器中运行以下查询将返回存储帐户的列表，并显示每个帐户的**allowBlobPublicAccess**属性值：

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowBlobPublicAccess = parse_json(properties).allowBlobPublicAccess
| project subscriptionId, resourceGroup, name, allowBlobPublicAccess
| order by subscriptionId, resourceGroup, name asc
```

## <a name="set-the-public-access-level-for-a-container"></a>设置容器的公共访问级别

若要授予匿名用户对容器及其 blob 的读取访问权限，请首先允许访问存储帐户，然后设置容器的公共访问级别。 如果拒绝了存储帐户的公共访问，你将无法配置容器的公共访问权限。

如果允许对存储帐户进行公共访问，则可以使用以下权限配置容器：

- **无公共读取访问：** 只能使用已授权的请求来访问容器及其 blob。 此选项是所有新容器的默认选项。
- **仅针对 blob 的公共读取访问：** 可以通过匿名请求读取容器中的 blob，但容器数据不能匿名访问。 匿名客户端无法枚举容器中的 Blob。
- **容器及其 blob 的公共读取访问权限：** 容器和 blob 数据可通过匿名请求读取，容器权限设置和容器元数据除外。 客户端可以通过匿名请求枚举容器中的 Blob，但无法枚举存储帐户中的容器。

无法更改单个 Blob 的公共访问级别。 只能在容器级别设置公共访问级别。

若要设置容器的公共访问级别，请使用 Azure 门户或 Azure CLI。 你可以在创建容器时设置容器的公共访问级别，或在现有容器上更新此设置。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

若要更新 Azure 门户中一个或多个现有容器的公共访问级别，请执行以下步骤：

1. 在 Azure 门户中导航到存储帐户概述。
1. 在菜单边栏选项卡上的 " **Blob 服务**" 下，选择 "**容器**"。
1. 选择要对其设置公共访问级别的容器。
1. 使用“更改访问级别”按钮显示公共访问权限设置。
1. 从“公共访问级别”下拉列表中选择所需的公共访问级别，然后单击“确定”按钮应用对选定容器所做的更改。

    ![显示如何在门户中设置公共访问级别的屏幕截图](./media/anonymous-read-access-configure/configure-public-access-container.png)

如果不允许对存储帐户进行公共访问，则不能设置容器的公共访问级别。 如果你尝试设置容器的公共访问级别，你会发现设置处于禁用状态，因为帐户不允许公共访问。

:::image type="content" source="media/anonymous-read-access-configure/container-public-access-blocked.png" alt-text="显示禁用公共访问时阻止设置容器公共访问级别的屏幕截图":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 更新一个或多个容器的公共访问级别，请调用[az storage container set 权限](/cli/azure/storage/container#az-storage-container-set-permission)命令。 通过传入帐户密钥、连接字符串或共享访问签名（SAS）来授权此操作。 设置容器的公共访问级别的 "[设置容器 ACL](/rest/api/storageservices/set-container-acl) " 操作不支持 Azure AD 的授权。 有关详细信息，请参阅[调用 blob 和队列数据操作的权限](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。

下面的示例设置容器的公共访问设置，以启用对容器及其 blob 的匿名访问。 请记住，用自己的值替换括号中的占位符值：

```azurecli-interactive
az storage container set-permission \
    --name <container-name> \
    --account-name <account-name> \
    --public-access container \
    --account-key <account-key> \
    --auth-mode key
```

如果不允许对存储帐户进行公共访问，则不能设置容器的公共访问级别。 如果尝试设置容器的公共访问级别，则会出现一个错误，指出存储帐户上不允许公共访问。

---

## <a name="check-the-container-public-access-setting"></a>检查容器公共访问设置

若要检查一个或多个容器的公共访问设置，可以使用 Azure 门户、PowerShell、Azure CLI、Azure 存储客户端库之一或 Azure 存储资源提供程序。 以下部分提供了一些示例。  

### <a name="check-the-public-access-setting-for-a-single-container"></a>检查单个容器的公共访问设置

若要使用 Azure CLI 获取一个或多个容器的公共访问级别，请调用[az storage container show 权限](/cli/azure/storage/container#az-storage-container-show-permission)命令。 通过传入帐户密钥、连接字符串或共享访问签名（SAS）来授权此操作。 返回容器的公共访问级别的[获取容器 ACL](/rest/api/storageservices/get-container-acl)操作不支持 Azure AD 的授权。 有关详细信息，请参阅[调用 blob 和队列数据操作的权限](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。

下面的示例读取容器的公共访问设置。 请记住，用自己的值替换括号中的占位符值：

```azurecli-interactive
az storage container show-permission \
    --name <container-name> \
    --account-name <account-name> \
    --account-key <account-key>
    --auth-mode key
```

### <a name="check-the-public-access-setting-for-a-set-of-containers"></a>检查一组容器的公共访问设置

可以通过列出容器并检查公共访问设置来检查一个或多个存储帐户中的哪些容器配置为公共访问。 当存储帐户不包含大量容器时，或者当你要检查少量存储帐户的设置时，此方法是一种可行的选择。 但是，如果尝试枚举大量的容器，性能可能会降低。

以下示例使用 PowerShell 获取存储帐户中所有容器的公共访问设置。 请记住，用自己的值替换括号中的占位符值：

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Get-AzStorageContainer -Context $ctx | Select Name, PublicAccess
```

## <a name="next-steps"></a>后续步骤

- [阻止对容器和 blob 的匿名公共读取访问](anonymous-read-access-prevent.md)
- [使用 .NET 以匿名方式访问公共容器和 blob](anonymous-read-access-client.md)
- [授权访问 Azure 存储](../common/storage-auth.md)
