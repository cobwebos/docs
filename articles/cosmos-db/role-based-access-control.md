---
title: 中基于角色的访问控制 Azure Cosmos DB
description: 了解 Azure Cosmos DB 如何向 Active directory 集成（RBAC）提供数据库保护。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: 0c7332a42751b35b6ad8ec3f88afb7bc78cc85e3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445104"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>中基于角色的访问控制 Azure Cosmos DB

Azure Cosmos DB 为 Azure Cosmos DB 中的常见管理方案提供内置的基于角色的访问控制（RBAC）。 在 Azure Active Directory 中有配置文件的个人可以将这些 RBAC 角色分配给用户、组、服务主体或托管标识，以授予或拒绝对 Azure Cosmos DB 资源的资源和操作的访问权限。 角色分配的作用域仅限于控制平面访问，其中包括对 Azure Cosmos 帐户、数据库、容器和产品/服务（吞吐量）的访问权限。

## <a name="built-in-roles"></a>内置角色

下面是 Azure Cosmos DB 支持的内置角色：

|**内置角色**  |**说明**  |
|---------|---------|
|[DocumentDB 帐户参与者](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|可管理 Azure Cosmos DB 帐户。|
|[Cosmos DB 帐户读者](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|可以读取 Azure Cosmos DB 帐户数据。|
|[Cosmos 备份操作员](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)|可以提交 Azure Cosmos 数据库或容器的还原请求。|
|[Cosmos DB 运算符](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|可以预配 Azure Cosmos 帐户、数据库和容器，但不能访问访问数据所需的密钥。|

> [!IMPORTANT]
> Azure Cosmos DB 中的 RBAC 支持仅适用于控制平面操作。 使用主密钥或资源令牌保护数据平面操作。 若要了解详细信息，请参阅[安全访问 Azure Cosmos DB 中的数据](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>标识和访问管理 (IAM)

Azure 门户中的 "**访问控制（IAM）** " 窗格用于在 Azure Cosmos 资源上配置基于角色的访问控制。 这些角色将应用于 Active Directory 中的用户、组、服务主体和管理标识。 你可以对个人和组使用内置角色或自定义角色。 以下屏幕截图显示了在 Azure 门户中使用访问控制（IAM） Active Directory 集成（RBAC）：

![Azure 门户中的访问控制 (IAM) - 演示数据库安全性](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>自定义角色

除了内置角色外，用户还可以在 Azure 中创建[自定义角色](../role-based-access-control/custom-roles.md)，并将这些角色应用到其 Active Directory 租户中所有订阅的服务主体。 自定义角色为用户提供了一种使用一组自定义资源提供程序操作创建 RBAC 角色定义的方法。 若要了解哪些操作可用于构建的自定义角色 Azure Cosmos DB 参阅[Azure Cosmos DB 资源提供程序操作](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

## <a name="preventing-changes-from-cosmos-sdk"></a>阻止 Cosmos SDK 的更改

可以锁定 Cosmos 资源提供程序，以防止任何资源的更改，包括通过帐户密钥连接的任何客户端（即通过 Cosmos SDK 连接的应用程序）的任何客户端。 设置后，对任何资源所做的更改必须来自具有适当 RBAC 角色和凭据的用户。 此功能设置为 Cosmos 资源提供程序中的 `disableKeyBasedMetadataWriteAccess` 属性值。 下面是使用此属性设置的 Azure 资源管理器模板的示例。

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

- [什么是适用于 Azure 资源的基于角色的访问控制（RBAC）](../role-based-access-control/overview.md)
- [Azure 资源的自定义角色](../role-based-access-control/custom-roles.md)
- [Azure Cosmos DB 资源提供程序操作](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
