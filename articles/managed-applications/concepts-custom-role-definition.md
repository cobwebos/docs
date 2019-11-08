---
title: Azure 托管应用程序中的自定义角色定义概述 |Microsoft Docs
description: 介绍为托管应用程序创建自定义角色定义的概念。
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 09/16/2019
ms.openlocfilehash: 7f4371bea467d6d4c99a776e03cdf13070d77ac6
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818396"
---
# <a name="custom-role-definition-artifact-in-azure-managed-applications"></a>Azure 托管应用程序中的自定义角色定义项目

自定义角色定义是托管应用程序中的可选项目。 它用于确定托管应用程序执行其功能所需的权限。

本文提供自定义角色定义项目及其功能的概述。

## <a name="custom-role-definition-artifact"></a>自定义角色定义项目

需要将自定义角色定义项目命名为 customRoleDefinition。 将其放置在创建托管应用程序定义的 .zip 包中的 createUiDefinition 和 Maintemplate.json 所在的同一级别。 若要了解如何创建 .zip 包并发布托管应用程序定义，请参阅[发布托管应用程序定义。](publish-managed-app-definition-quickstart.md)

## <a name="custom-role-definition-schema"></a>自定义角色定义架构

CustomRoleDefinition 文件具有一个顶级 `roles` 属性，它是一组角色。 这些角色是托管应用程序运行所需的权限。 目前只允许使用内置角色，但你可以指定多个角色。 角色定义的 ID 或角色名称可以引用角色。

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

角色由 `$.properties.roleName` 或 `id`组成：

```json
{
    "id": null,
    "properties": {
        "roleName": "Contributor"
    }
}
```

> [!NOTE]
> 您可以使用 `id` 或 `roleName` 字段。 仅需要一个。 这些字段用于查找应应用的角色定义。 如果同时提供两者，将使用 `id` 字段。

|属性|必需？|说明|
|---------|---------|---------|
|id|是|内置角色的 ID。 您可以使用完整 ID 或只使用 GUID。|
|RoleName|是|内置角色的名称。|