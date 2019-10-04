---
title: include 文件
description: include 文件
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 09/30/2019
ms.author: v-adgera
ms.custom: include file
ms.openlocfilehash: 1f4cdf45fb643499f7d6ab717c6f36395ad292c8
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827701"
---
`objectIdType`（或**对象标识符类型**）是指赋予某个角色的标识类型。 除 `DeviceId` 和 `UserDefinedFunctionId` 类型外，对象标识符类型与 Azure Active Directory 对象的属性相对应。

下表包含 Azure 数字孪生中支持的对象标识符类型：

| 类型 | 描述 |
| --- | --- |
| UserId | 向用户分配角色。 |
| DeviceId | 向设备分配角色。 |
| DomainName | 向域名分配角色。 具有指定域名的每位用户都将拥有相应角色的访问权限。 |
| TenantId | 向租户分配角色。 属于指定 Azure AD 租户 ID 的每位用户都将拥有相应角色的访问权限。 |
| ServicePrincipalId | 向服务主体对象 ID 分配角色。 |
| UserDefinedFunctionId | 向用户定义的函数 (UDF) 分配角色。 |