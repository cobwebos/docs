---
title: Azure Cosmos DB 中基于角色的访问控制
description: 了解 Azure Cosmos DB 如何使用 Active Directory 集成 (RBAC) 来提供数据库保护。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: 0c7332a42751b35b6ad8ec3f88afb7bc78cc85e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445104"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Azure Cosmos DB 中基于角色的访问控制

Azure Cosmos DB 为 Azure Cosmos DB 中的常见管理方案提供内置的基于角色的访问控制 (RBAC)。 在 Azure Active Directory 中创建了配置文件的个人可将这些 RBAC 角色分配给用户、组、服务主体或托管标识，以授予或拒绝对 Azure Cosmos DB 中的资源和操作的访问权限。 角色分配范围仅限控制平面访问，包括对 Azure Cosmos 帐户、数据库、容器和套餐（吞吐量）的访问。

## <a name="built-in-roles"></a>内置角色

下面是 Azure Cosmos DB 支持的内置角色：

|**内置角色**  |**说明**  |
|---------|---------|
|[DocumentDB 帐户参与者](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|可管理 Azure Cosmos DB 帐户。|
|[Cosmos DB 帐户读取者](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|可以读取 Azure Cosmos DB 帐户数据。|
|[Cosmos 备份操作员](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)|可以提交对 Azure Cosmos 数据库或容器的还原请求|
|[Cosmos DB 操作员](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|可以预配 Azure Cosmos 帐户、数据库和容器，但无法访问用于访问数据的密钥。|

> [!IMPORTANT]
> Azure Cosmos DB 中的 RBAC 仅支持适用于控制平面操作。 使用主密钥或资源令牌保护数据平面操作。 要了解详细信息，请参阅[Azure Cosmos DB 中对数据的安全访问](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>标识和访问管理 (IAM)

Azure 门户中的“访问控制(IAM)”窗格用于针对 Azure Cosmos 资源配置基于角色的访问控制。**** 角色将应用到 Active Directory 中的用户、组、服务主体和托管标识。 对于个人和组，可使用内置角色或自定义角色。 以下屏幕截图显示在 Azure 门户中使用访问控制 (IAM) 的 Active Directory 集成 (RBAC)：

![Azure 门户中的访问控制 (IAM) - 演示数据库安全性](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>自定义角色

除内置角色以外，用户还可以在 Azure 中创建[自定义角色](../role-based-access-control/custom-roles.md)，并将这些角色应用到其 Active Directory 租户内的所有订阅中的服务主体。 自定义角色可让用户使用一组自定义的资源提供程序操作来创建 RBAC 角色定义。 若要了解可以使用哪些操作来为 Azure Cosmos DB 生成自定义角色，请参阅 [Azure Cosmos DB 资源提供程序操作](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

## <a name="preventing-changes-from-cosmos-sdk"></a>阻止来自 Cosmos SDK 的更改

可以锁定 Cosmos 资源提供程序，防止通过帐户密钥进行连接的任何客户端（即通过 Cosmos SDK 连接的应用程序）对资源（包括 Cosmos 帐户、数据库、容器和吞吐量）进行任何更改。 设置以后，对任何资源进行的更改必须由具有适当 RBAC 角色和凭据的用户进行。 此功能在 Cosmos 资源提供程序中使用 `disableKeyBasedMetadataWriteAccess` 属性值进行设置。 下面是包含此属性设置的 Azure 资源管理模板的示例。

```json
{
    {
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "name": "[variables('accountName')]",
      "apiVersion": "2019-08-01",
      "location": "[parameters('location')]",
      "kind": "GlobalDocumentDB",
      "properties": {
        "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
        "locations": "[variables('locations')]",
        "databaseAccountOfferType": "Standard",
        "disableKeyBasedMetadataWriteAccess": true
        }
    }
}
```

## <a name="next-steps"></a>后续步骤

- [Azure 资源的基于角色的访问控制 （RBAC） 是什么](../role-based-access-control/overview.md)
- [Azure 资源的自定义角色](../role-based-access-control/custom-roles.md)
- [Azure 宇宙数据库资源提供程序操作](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
