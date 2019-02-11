---
title: include 文件
description: include 文件
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 12/20/2018
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: 40ab53c941a7ac619ebb09d381a4ae0450f26e8b
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/27/2018
ms.locfileid: "53795870"
---
`objectIdType`（或**对象标识符类型**）是指赋予某个角色的标识类型。 除 `DeviceId` 和 `UserDefinedFunctionId` 类型外，对象标识符类型与 Azure Active Directory 对象的属性相对应。

下表包含 Azure 数字孪生中支持的对象标识符类型：

| 类型 | Description |
| --- | --- |
| UserId | 向用户分配角色。 |
| DeviceId | 向设备分配角色。 |
| DomainName | 向域名分配角色。 具有指定域名的每位用户都将拥有相应角色的访问权限。 |
| TenantId | 向租户分配角色。 属于指定 Azure AD 租户 ID 的每位用户都将拥有相应角色的访问权限。 |
| ServicePrincipalId | 向服务主体对象 ID 分配角色。 |
| UserDefinedFunctionId | 向用户定义的函数 (UDF) 分配角色。 |