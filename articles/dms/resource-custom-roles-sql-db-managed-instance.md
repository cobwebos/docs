---
title: 自定义角色： Online SQL Server SQL 托管实例迁移
titleSuffix: Azure Database Migration Service
description: 了解如何使用自定义角色 SQL Server 到 Azure SQL 数据库托管实例在线迁移。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 10/25/2019
ms.openlocfilehash: 85c70968eb0f8a5752bbfa338918a8bba2fe7794
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437693"
---
# <a name="custom-roles-for-sql-server-to-sql-database-managed-instance-online-migrations"></a>适用于 SQL 数据库托管实例联机迁移的自定义角色 SQL Server

Azure 数据库迁移服务使用应用 ID 与 Azure 服务进行交互。 应用 ID 要求订阅级别的参与者角色（许多公司安全部门不允许）或创建自定义角色，以授予 Azure 数据库迁移服务所需的特定权限。 由于 Azure Active Directory 中存在2000个自定义角色限制，因此你可能需要将应用 ID 特别需要的所有权限合并为一个或两个自定义角色，然后向应用 ID 授予对特定对象或资源组（与 sub）上的自定义角色。描述级别）。 如果不考虑自定义角色的数量，可以按资源类型拆分自定义角色，以创建三个自定义角色，如下所述。

角色定义 json 字符串的 AssignableScopes 部分允许您控制权限在门户的 "**添加角色分配**" UI 中显示的位置。 你可能想要在资源组或资源级别定义角色，以避免让 UI 具有额外角色。 请注意，这不会执行实际的角色分配。

## <a name="minimum-number-of-roles"></a>最小角色数量

我们目前建议为应用 ID 至少创建两个自定义角色，一个在资源级别，另一个在订阅级别。

> [!NOTE]
> 最后一个自定义角色要求可能最终会被删除，因为新的 SQL 数据库托管实例代码部署到 Azure。

**应用 ID 的自定义角色**。 此角色是在*资源*或*资源组*级别的 Azure 数据库迁移服务迁移所必需的（有关应用 ID 的详细信息，请参阅[使用门户创建可访问资源 Azure AD 应用程序和服务主体](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)）。

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

**应用 ID-订阅的自定义角色**。 在*订阅*级别迁移 Azure 数据库迁移服务需要此角色。

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

以上 json 必须存储在三个文本文件中，你可以使用 AzureRM、AZ PowerShell cmdlet 或 Azure CLI 来使用**get-azurermroledefinition （AzureRM）** 或**AzRoleDefinition （AZ）** 创建角色。

有关详细信息，请参阅[Azure 资源的自定义角色](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)一文。

创建这些自定义角色后，必须将角色分配添加到用户，并将应用 ID 添加到相应的资源或资源组：

* 必须将 "DMS 角色-应用 ID" 角色授予用于迁移的应用 ID，还必须授予存储帐户、Azure 数据库迁移服务实例和 SQL 数据库托管实例资源级别。
* 必须在订阅级别向应用 ID 授予 "DMS 角色-应用 ID-Sub" 角色（在资源或资源组中授予权限将失败）。 此要求是临时的，直到部署了代码更新。

## <a name="expanded-number-of-roles"></a>扩展的角色数量

如果你的 Azure Active Directory 中的自定义角色数量不是问题，我们建议你总共创建三个角色。 你仍需要 "DMS 角色-应用 ID-Sub" 角色，但上面的 "DMS 角色-应用 ID" 角色按资源类型拆分为两个不同的角色。

**适用于 SQL 数据库托管实例的应用 ID 的自定义角色**

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

**用于存储的应用 ID 的自定义角色**

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

若要将角色分配给用户/应用 ID，请打开 Azure 门户，执行以下步骤：

1. 导航到资源组或资源（需要对订阅授予的角色除外），转到 "**访问控制**"，然后滚动查找刚刚创建的自定义角色。

2. 选择适当的角色，选择 "应用 ID"，并保存所做的更改。

  你的应用程序 ID 现在显示在 "**角色分配**" 选项卡上。

## <a name="next-steps"></a>后续步骤

* 查看 Microsoft[数据库迁移指南](https://datamigration.microsoft.com/)中有关方案的迁移指南。
