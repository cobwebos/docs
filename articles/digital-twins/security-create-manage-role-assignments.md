---
title: 了解 Azure 数字孪生设备连接和身份验证 | Microsoft Docs
description: 使用 Azure 数字孪生连接设备并对其进行身份验证
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: lyrana
ms.openlocfilehash: 42c1b0fbb6d87e9ed35d4ecce3971d8512eed4d4
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2018
ms.locfileid: "51012456"
---
# <a name="create-and-manage-role-assignments"></a>创建和管理角色分配

Azure 数字孪生使用基于角色的访问控制 ([RBAC](./security-role-based-access-control.md)) 来管理对资源的访问权限。

每个角色分配均包括：

* **对象标识符**：Azure Active Directory ID、服务主体对象 ID 或域名
* **对象标识符类型**
* **角色定义 ID**
* **空间路径**
* **租户 ID**：大多数情况下是 Azure Active Directory 租户 ID

## <a name="role-definition-identifiers"></a>角色定义标识符

下表显示了可以通过查询系统/角色 API 获得的内容。

| **角色** | **标识符** |
| --- | --- |
| 空间管理员 | 98e44ad7-28d4-4007-853b-b9968ad132d1 |
| 用户管理员| dfaac54c-f583-4dd2-b45d-8d4bbc0aa1ac |
| 设备管理员 | 3cdfde07-bc16-40d9-bed3-66d49a8f52ae |
| 密钥管理员 | 5a0b1afc-e118-4068-969f-b50efb8e5da6 |
| 令牌管理员 | 38a3bb21-5424-43b4-b0bf-78ee228840c3 |
| 用户 | b1ffdb77-c635-4e7e-ad25-948237d85b30 |
| 支持专家 | 6e46958b-dc62-4e7c-990c-c3da2e030969 |
| 设备安装员 | b16dd9fe-4efe-467b-8c8c-720e2ff8817c |
| 网关设备 | d4c69766-e9bd-4e61-bfc1-d8b6e686c7a8 |

## <a name="supported-objectidtypes"></a>支持的 ObjectIdTypes

支持的 `ObjectIdTypes`：

* `UserId`
* `DeviceId`
* `DomainName`
* `TenantId`
* `ServicePrincipalId`
* `UserDefinedFunctionId`

## <a name="create-a-role-assignment"></a>创建角色分配

```plaintext
HTTP POST /api/v1.0/roleassignments
```

| **名称** | **必需** | 类型 | **说明** |
| --- | --- | --- | --- |
| roleId| 是 |String | 角色定义标识符。 通过查询系统 API 查找角色定义及其标识符。 |
| objectId | 是 |String | 角色分配的对象 ID，必须根据其关联类型进行格式化。 对于 `DomainName` ObjectIdType，ObjectId 必须以 `“@”` 字符开头。 |
| objectIdType | 是 |String | 角色分配的类型。 必须是此表中的以下行之一。 |
| tenantId | 多种多样 | String |租户标识符。 不能用于 `DeviceId` 和 `TenantId` ObjectIdType。 对于 `UserId` 和 `ServicePrincipalId` ObjectIdType 必需。 对于 DomainName ObjectIdType 可选。 |
| path* | 是 | String |`Space` 对象的完整访问路径。 例如 `/{Guid}/{Guid}`。 如果标识符需要整个图形的角色分配，请指定 `"/"`。 此字符指定根目录，但不建议使用。 请始终遵循最低权限原则。 |

## <a name="sample-configuration"></a>示例配置

在此示例中，用户需要对租户空间的某一层具有管理访问权限。

  ```JSON
    {
      "RoleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
      "ObjectId" : " 0fc863bb-eb51-4704-a312-7d635d70e599",
      "ObjectIdType" : "UserId",
      "TenantId": " a0c20ae6-e830-4c60-993d-a91ce6032724",
      "Path": "/ 091e349c-c0ea-43d4-93cf-6b57abd23a44/ d84e82e6-84d5-45a4-bd9d-006a118e3bab"
    }
  ```

在此示例中，一个应用程序运行对设备和传感器进行模拟的测试方案。

  ```JSON
    {
      "RoleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
      "ObjectId" : "cabf7acd-af0b-41c5-959a-ce2f4c26565b",
      "ObjectIdType" : "ServicePrincipalId",
      "TenantId": " a0c20ae6-e830-4c60-993d-a91ce6032724",
      "Path": "/"
    }
  ```

属于某个域的一部分的所有用户都将收到对空间、传感器和用户的读取访问权限。 此访问权限包括其对应的相关对象。

  ```JSON
    {
      "RoleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
      "ObjectId" : "@microsoft.com",
      "ObjectIdType" : "DomainName",
      "Path": "/091e349c-c0ea-43d4-93cf-6b57abd23a44"
    }
  ```

使用 GET 获取角色分配。

```plaintext
HTTP GET /api/v1/roleassignments?path={path}
```

| **名称** | **位于** | **必需** |    类型 |  **说明** |
| --- | --- | --- | --- | --- |
| 路径 | 路径 | True | String | 空间的完整路径 |

使用 DELETE 删除角色分配。

```plaintext
HTTP DELETE /api/v1/roleassignments/{id}
```

| **名称** | **位于** | **必需** | 类型 | **说明** |
| --- | --- | --- | --- | --- |
| ID | 路径 | True | String |   角色分配 ID |

## <a name="next-steps"></a>后续步骤

若要了解 Azure 数字孪生的安全性，请阅读 [API 身份验证](./security-authenticating-apis.md)。
