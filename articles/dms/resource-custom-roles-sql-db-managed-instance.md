---
title: 自定义角色：在线将 SQL Server 迁移到 SQL 托管实例
titleSuffix: Azure Database Migration Service
description: 了解如何使用自定义角色从 SQL Server 在线迁移到 Azure SQL 托管实例。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: e950e34a1965e309d226c42bf4b3128cbe3bf3c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91291497"
---
# <a name="custom-roles-for-sql-server-to-azure-sql-managed-instance-online-migrations"></a>用于从 SQL Server 在线迁移到 Azure SQL 托管实例的自定义角色

Azure 数据库迁移服务使用应用 ID 来与 Azure 服务交互。 应用 ID 需要订阅级别的“参与者”角色（许多企业安全部门不允许这种角色），或创建自定义角色用于授予 Azure 数据库迁移服务所需的特定权限。 由于 Azure Active Directory 将自定义角色数目限制为 2,000 个，因此你可能希望将应用 ID 专门需要的所有权限合并成一个或两个自定义角色，然后为该应用 ID 授予对特定对象或资源组的自定义角色（而不是在订阅级别授予这种角色）。 如果自定义角色数目没有限制，则可按资源类型拆分自定义角色，以根据下面所述总共创建三个自定义角色。

使用角色定义 JSON 字符串的 AssignableScopes 节，可以控制权限在门户上“添加角色分配”UI 中的显示位置。**** 你可能想要在资源组甚至资源级别定义角色，以免额外的角色使 UI 变得杂乱无章。 请注意，这并不会执行实际的角色分配。

## <a name="minimum-number-of-roles"></a>最小角色数

目前，我们建议至少为应用 ID 创建两个自定义角色，一个在资源级别创建，另一个在订阅级别创建。

> [!NOTE]
> 最后一项自定义角色要求最终可能会取消，因为新的 SQL 托管实例代码将部署到 Azure 中。

**应用 ID 的自定义角色**。 在资源或资源组级别进行 Azure 数据库迁移服务迁移时需要此角色（有关应用 ID 的详细信息，请参阅[使用门户创建可访问资源的 Azure AD 应用程序和服务主体](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)一文）。** **

```json
{
  "Name": "DMS Role - App ID",
  "IsCustom": true,
  "Description": "DMS App ID access to complete MI migrations",
  "Actions": [
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageaccounts/blobservices/read",
        "Microsoft.Storage/storageaccounts/blobservices/write",
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/write",
        "Microsoft.Sql/managedInstances/databases/read",
        "Microsoft.Sql/managedInstances/databases/write",
        "Microsoft.Sql/managedInstances/databases/delete",
        "Microsoft.Sql/managedInstances/metrics/read",
        "Microsoft.DataMigration/locations/*",
        "Microsoft.DataMigration/services/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/ResourceGroups/<StorageAccount_rg_name>",
    "/subscriptions/<subscription_id>/ResourceGroups/<ManagedInstance_rg_name>",
    "/subscriptions/<subscription_id>/ResourceGroups/<DMS_rg_name>",
  ]
}
```

**应用 ID 的自定义角色 - 订阅**。 在订阅级别进行 Azure 数据库迁移服务迁移时需要此角色。**

```json
{
  "Name": "DMS Role - App ID - Sub",
  "IsCustom": true,
  "Description": "DMS App ID access at subscription level to complete MI migrations",
  "Actions": [
        "Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>"
  ]
}
```

以上 JSON 必须存储在三个文本文件中，可以在 AzureRM、AZ PowerShell cmdlet 或 Azure CLI 中使用 **New-AzureRmRoleDefinition (AzureRM)** 或 **New-AzRoleDefinition (AZ)** 创建角色。

有关详细信息，请参阅 [Azure 自定义角色](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)一文。

创建这些自定义角色后，必须将角色分配添加到相应资源或资源组中的用户和应用 ID：

* 必须为应用 ID 授予用于迁移的“DMS 角色 - 应用 ID”角色，并在存储帐户、Azure 数据库迁移服务实例和 SQL 托管实例资源级别授予该角色。
* 必须在订阅级别为应用 ID 授予“DMS 角色 - 应用 ID - 订阅”角色（无法在资源或资源组级别授予）。 此项要求是暂时性的，部署代码更新后将会取消。

## <a name="expanded-number-of-roles"></a>扩展的角色数

如果 Azure Active Directory 中的自定义角色数目没有限制，我们建议总共创建三个角色。 仍需要“DMS 角色 - 应用 ID - 订阅”角色，但上述“DMS 角色 - 应用 ID”角色将按资源类型拆分成两个不同的角色。

**SQL 托管实例的应用 ID 的自定义角色**

```json
{
  "Name": "DMS Role - App ID - SQL MI",
  "IsCustom": true,
  "Description": "DMS App ID access to complete MI migrations",
  "Actions": [
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/write",
        "Microsoft.Sql/managedInstances/databases/read",
        "Microsoft.Sql/managedInstances/databases/write",
        "Microsoft.Sql/managedInstances/databases/delete",
        "Microsoft.Sql/managedInstances/metrics/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/resourceGroups/<ManagedInstance_rg_name>"
  ]
}
```

**存储的应用 ID 的自定义角色**

```json
{
  "Name": "DMS Role - App ID - Storage",
  "IsCustom": true,
  "Description": "DMS App ID storage access to complete MI migrations",
  "Actions": [
"Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageaccounts/blobservices/read",
        "Microsoft.Storage/storageaccounts/blobservices/write"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/resourceGroups/<StorageAccount_rg_name>"
  ]
}
```

## <a name="role-assignment"></a>角色分配

若要将角色分配给用户/应用 ID，请打开 Azure 门户并执行以下步骤：

1. 导航到相应的资源组或资源（需要在订阅级别授予的角色除外），转到“访问控制”，然后滚动页面找到刚刚创建的自定义角色。****

2. 选择相应的角色，选择应用 ID，然后保存更改。

  该应用 ID 随即会显示在“角色分配”选项卡上。****

## <a name="next-steps"></a>后续步骤

* 查看 Microsoft [数据库迁移指南](https://datamigration.microsoft.com/)中有关方案的迁移指南。
