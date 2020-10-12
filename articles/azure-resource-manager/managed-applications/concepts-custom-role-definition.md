---
title: 自定义角色定义概述
description: 介绍为托管应用程序创建自定义角色定义的概念。
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 09/16/2019
ms.openlocfilehash: 7b7fff913c177703f959bfa103c8e310d01059e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "81391829"
---
# <a name="custom-role-definition-artifact-in-azure-managed-applications"></a>Azure 托管应用程序中的自定义角色定义项目

自定义角色定义是托管应用程序中的可选项目。 它用于确定托管应用程序执行其功能所需的权限。

本文概述了自定义角色定义项目及其功能。

## <a name="custom-role-definition-artifact"></a>自定义角色定义项目

需要将自定义角色定义项目命名为 customRoleDefinition.json。 请将其放置在特定级别中，该级别与创建托管应用程序定义的 .zip 包中的 createUiDefinition.json 和 mainTemplate.json 属于同一级别。 若要了解如何创建 .zip 包并发布托管应用程序定义，请参阅[发布托管应用程序定义。](publish-service-catalog-app.md)

## <a name="custom-role-definition-schema"></a>自定义角色定义架构

customRoleDefinition.json 文件有一个顶级 `roles` 属性，该属性是角色的数组。 这些角色是托管应用程序正常运行所需的权限。 目前只允许使用内置角色，但你可以指定多个角色。 角色定义 ID 或角色名称可以引用角色。

自定义角色定义的示例 JSON：

```json
{
    "contentVersion": "0.0.0.1",
    "roles": [
        {
            "properties": {
                "roleName": "Contributor"
            }
        },
        {
            "id": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
        },
        {
            "id": "/providers/Microsoft.Authorization/roledefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
        }
    ]
}
```

## <a name="roles"></a>角色

角色由 `$.properties.roleName` 或 `id` 组成：

```json
{
    "id": null,
    "properties": {
        "roleName": "Contributor"
    }
}
```

> [!NOTE]
> 可以使用 `id` 或 `roleName` 字段。 只需要一个。 这些字段用于查找应该应用的角色定义。 如果同时提供两者，则会使用 `id` 字段。

|properties|必需？|说明|
|---------|---------|---------|
|id|是|内置角色的 ID。 可以使用完整 ID 或只使用 GUID。|
|roleName|是|内置角色的名称。|