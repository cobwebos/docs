---
title: 在 Azure 数字孪生中创建和管理角色分配 | Microsoft Docs
description: 在 Azure 数字孪生中创建和管理角色分配。
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/26/2018
ms.author: lyrana
ms.custom: seodec18
ms.openlocfilehash: 72155799971760e9ddc93746dceafb1ea554d88b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "66162107"
---
# <a name="create-and-manage-role-assignments-in-azure-digital-twins"></a>在 Azure 数字孪生中创建和管理角色分配

Azure 数字孪生使用基于角色的访问控制 ([RBAC](./security-role-based-access-control.md)) 来管理对资源的访问权限。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="role-assignments-overview"></a>角色分配概述

每个角色分配符合以下定义：

```JSON
{
  "roleId": "00e00ad7-00d4-4007-853b-b9968ad000d1",
  "objectId": "be2c6daa-a3a0-0c0a-b0da-c000000fbc5f",
  "objectIdType": "ServicePrincipalId",
  "path": "/",
  "tenantId": "00f000bf-86f1-00aa-91ab-2d7cd000db47"
}
```

下表描述了每个属性：

| 特性 | 名称 | 需要 | Type | 描述 |
| --- | --- | --- | --- | --- |
| roleId | 角色定义标识符 | “是” | String | 所需角色分配的唯一 ID。 通过查询系统 API 或查看下表查找角色定义及其标识符。 |
| objectId | 对象标识符 | “是” | String | Azure Active Directory ID、服务主体对象 ID 或域名。 该角色分配要分配到哪个对象。 必须根据其关联类型设置角色分配的格式。 对于 `DomainName` objectIdType，objectId 必须以 `“@”` 字符开头。 |
| objectIdType | 对象标识符类型 | “是” | String | 使用的对象标识符类型。 请参阅下面的**支持的 ObjectIdType**。 |
| path | 空间路径 | “是” | String | `Space` 对象的完整访问路径。 例如 `/{Guid}/{Guid}`。 如果标识符需要整个图形的角色分配，请指定 `"/"`。 此字符指定根目录，但不建议使用。 请始终遵循最低权限原则。 |
| tenantId | 租户标识符 | 多种多样 | String | 在大多数情况下，为 Azure Active Directory 租户 ID。 不能用于 `DeviceId` 和 `TenantId` ObjectIdType。 对于 `UserId` 和 `ServicePrincipalId` ObjectIdType 必需。 对于 DomainName ObjectIdType 可选。 |

### <a name="supported-role-definition-identifiers"></a>支持的角色定义标识符

每个角色分配将某个角色定义关联到 Azure 数字孪生环境中的某个实体。

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

### <a name="supported-object-identifier-types"></a>支持的对象标识符类型

前面已经介绍了 **objectIdType** 属性。

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

## <a name="role-assignment-operations"></a>角色分配操作

Azure 数字孪生支持对角色分配执行完整的 *CREATE*、*READ* 和 *DELETE* 操作。 *UPDATE* 操作的处理方式是添加角色分配、删除角色分配，或者修改角色分配授权访问的[空间智能图形](./concepts-objectmodel-spatialgraph.md)节点。

![角色分配终结点][1]

提供的 Swagger 参考文档包含了有关所有可用 API 终结点、请求操作和定义的更多信息。

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

<div id="grant"></div>

### <a name="grant-permissions-to-your-service-principal"></a>向服务主体授予权限

向服务主体授予权限通常是使用 Azure 数字孪生时要执行的第一个步骤。 此操作需要：

1. 通过 PowerShell 登录到 Azure 实例。
1. 获取服务主体信息。
1. 将所需的角色分配给服务主体。

应用程序 ID 在 Azure Active Directory 中提供。 若要详细了解如何在 Active Directory 中配置和预配 Azure 数字孪生，请阅读[快速入门](./quickstart-view-occupancy-dotnet.md)。

获取应用程序 ID 后，执行以下 PowerShell 命令：

```shell
Login-AzAccount
Get-AzADServicePrincipal -ApplicationId  <ApplicationId>
```

然后，具有“管理员”角色的用户可以通过向以下 URL 发出经过身份验证的 HTTP POST 请求，将“空间管理员”角色分配给用户：

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments
```

使用以下 JSON 正文：

```JSON
{
  "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
  "objectId": "YOUR_SERVICE_PRINCIPLE_OBJECT_ID",
  "objectIdType": "ServicePrincipalId",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

<div id="all"></div>

### <a name="retrieve-all-roles"></a>检索所有角色

![系统角色][2]

若要列出所有可用的角色（角色定义），请向以下对象发出经过身份验证的 HTTP GET 请求：

```plaintext
YOUR_MANAGEMENT_API_URL/system/roles
```

成功的请求将返回一个 JSON 数组，其中包含可分配的每个角色的条目：

```JSON
[
    {
        "id": "3cdfde07-bc16-40d9-bed3-66d49a8f52ae",
        "name": "DeviceAdministrator",
        "permissions": [
            {
                "notActions": [],
                "actions": [
                    "Read",
                    "Create",
                    "Update",
                    "Delete"
                ],
                "condition": "@Resource.Type Any_of {'Device', 'DeviceBlobMetadata', 'DeviceExtendedProperty', 'Sensor', 'SensorBlobMetadata', 'SensorExtendedProperty'} || ( @Resource.Type == 'ExtendedType' && (!Exists @Resource.Category || @Resource.Category Any_of { 'DeviceSubtype', 'DeviceType', 'DeviceBlobType', 'DeviceBlobSubtype', 'SensorBlobSubtype', 'SensorBlobType', 'SensorDataSubtype', 'SensorDataType', 'SensorDataUnitType', 'SensorPortType', 'SensorType' } ) )"
            },
            {
                "notActions": [],
                "actions": [
                    "Read"
                ],
                "condition": "@Resource.Type == 'Space' && @Resource.Category == 'WithoutSpecifiedRbacResourceTypes' || @Resource.Type Any_of {'ExtendedPropertyKey', 'SpaceExtendedProperty', 'SpaceBlobMetadata', 'SpaceResource', 'Matcher'}"
            }
        ],
        "accessControlPath": "/system",
        "friendlyPath": "/system",
        "accessControlType": "System"
    }
]
```

<div id="check"></div>

### <a name="check-a-specific-role-assignment"></a>检查特定的角色分配

若要检查特定的角色分配，请向以下对象发出经过身份验证的 HTTP GET 请求：

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments/check?userId=YOUR_USER_ID&path=YOUR_PATH&accessType=YOUR_ACCESS_TYPE&resourceType=YOUR_RESOURCE_TYPE
```

| **参数值** | **必需** |  类型 |  **说明** |
| --- | --- | --- | --- |
| YOUR_USER_ID |  True | String |   UserId objectIdType 的 objectId。 |
| YOUR_PATH | True | String |   要检查访问权限的所选路径。 |
| YOUR_ACCESS_TYPE |  True | String |   要检查的访问类型。 |
| YOUR_RESOURCE_TYPE | True | String |  要检查的资源。 |

成功的请求将返回布尔值 `true` 或 `false`，指示是否已将该访问类型分配到给定路径和资源的用户。

### <a name="get-role-assignments-by-path"></a>按路径获取角色分配

若要检查某个路径的所有角色分配，请向以下对象发出经过身份验证的 HTTP GET 请求：

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments?path=YOUR_PATH
```

| 值 | 替换为 |
| --- | --- |
| YOUR_PATH | 空间的完整路径 |

成功的请求将返回一个 JSON 数组，其中包含与所选 **path** 参数关联的每个角色分配：

```JSON
[
    {
        "id": "0000c484-698e-46fd-a3fd-c12aa11e53a1",
        "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
        "objectId": "0de38846-1aa5-000c-a46d-ea3d8ca8ee5e",
        "objectIdType": "UserId",
        "path": "/"
    }
]
```

### <a name="revoke-a-permission"></a>撤销权限

若要撤销某个接收者的权限，请通过发出经过身份验证的 HTTP DELETE 请求来删除角色分配：

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments/YOUR_ROLE_ASSIGNMENT_ID
```

| 参数 | 替换为 |
| --- | --- |
| *YOUR_ROLE_ASSIGNMENT_ID* | 要删除的角色分配的 **ID** |

成功的 DELETE 请求将返回 204 响应状态。 通过[检查](#check)角色分配是否仍然存在来验证是否删除了该角色分配。

### <a name="create-a-role-assignment"></a>创建角色分配

若要创建角色分配，请向以下 URL 发出经过身份验证的 HTTP POST 请求：

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments
```

验证 JSON 正文是否符合以下架构：

```JSON
{
  "roleId": "YOUR_ROLE_ID",
  "objectId": "YOUR_OBJECT_ID",
  "objectIdType": "YOUR_OBJECT_ID_TYPE",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

成功的请求将返回 201 响应状态，以及新建的角色分配的 **ID**：

```JSON
"d92c7823-6e65-41d4-aaaa-f5b32e3f01b9"
```

## <a name="configuration-examples"></a>配置示例

以下示例演示如何在多种常见的角色分配方案中配置 JSON 正文。

* **示例**：用户需要对租户空间的某一层具有管理访问权限。

   ```JSON
   {
    "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
    "objectId" : " 0fc863aa-eb51-4704-a312-7d635d70e000",
    "objectIdType" : "UserId",
    "tenantId": " a0c20ae6-e830-4c60-993d-a00ce6032724",
    "path": "/ 000e349c-c0ea-43d4-93cf-6b00abd23a44/ d84e82e6-84d5-45a4-bd9d-006a000e3bab"
   }
   ```

* **示例**：运行模拟设备和传感器的测试方案的应用程序。

   ```JSON
   {
    "roleId": "98e44ad7-28d4-0007-853b-b9968ad132d1",
    "objectId" : "cabf7aaa-af0b-41c5-000a-ce2f4c20000b",
    "objectIdType" : "ServicePrincipalId",
    "tenantId": " a0c20ae6-e000-4c60-993d-a91ce6000724",
    "path": "/"
   }
    ```

* **示例**：属于某个域的一部分的所有用户都将收到对空间、传感器和用户的读取访问权限。 此访问权限包括其对应的相关对象。

   ```JSON
   {
    "roleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
    "objectId" : "@microsoft.com",
    "objectIdType" : "DomainName",
    "path": "/000e349c-c0ea-43d4-93cf-6b00abd23a00"
   }
   ```

## <a name="next-steps"></a>后续步骤

- 若要查看 Azure 数字孪生的基于角色的访问控制，请参阅[基于角色的访问控制](./security-authenticating-apis.md)。

- 若要了解 Azure 数字孪生 API 身份验证，请参阅 [API 身份验证](./security-authenticating-apis.md)。

<!-- Images -->
[1]: media/security-roles/roleassignments.png
[2]: media/security-roles/system.png
