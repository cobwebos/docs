---
title: 在门户中针对 Azure 搜索管理访问权限设置 RBAC 角色 | Microsoft Docs
description: Azure 门户中基于角色的管理控制。
services: search
documentationcenter: ''
author: HeidiSteen
manager: cgronlun
editor: ''
tags: azure-portal
ms.assetid: ''
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 03/20/2018
ms.author: heidist
ms.openlocfilehash: a5f6395a0160402b3b0dfe95dc12b866854e70d9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="set-rbac-roles-for-administrative-access"></a>针对管理访问权限设置 RBAC 角色

对于通过门户或 Resource Manager API 管理的所有服务，Azure 提供了[基于全局角色的授权模型](../active-directory/role-based-access-control-configure.md)。 所有者、参与者和读者角色根据分配给每个角色的 Active Directory 用户、组和安全主体的服务管理，确定服务管理的级别。 

> [!Note]
> 不存在用于保护文档索引或文档子集且基于角色的访问控制。 如果要实现针对搜索结果的、基于标识的访问，可创建安全筛选器按标识来细化结果，由此去除请求者不应具有访问权限的那些文档。 有关详细信息，请参阅[安全筛选器](search-security-trimming-for-azure-search.md)和[使用 Active Directory 进行保护](search-security-trimming-for-azure-search-with-aad.md)。

## <a name="management-tasks-by-role"></a>按角色划分的管理任务

对于 Azure 搜索，角色与支持以下管理任务的权限级别相关联：

| 角色 | 任务 |
| --- | --- |
| 所有者 |创建或删除服务或者服务上的任何对象，包括 API 密钥、索引、索引器、索引器数据源和索引器计划。<p>查看服务状态，包括计数和存储大小。<p>添加或删除角色成员身份（仅所有者才能管理角色成员身份）。<p>订阅管理员和服务所有者拥有所有者角色的自动成员身份。 |
| 参与者 |访问级别与所有者的访问级别相同，不包括 RBAC 角色管理。 例如，参与者可创建或删除对象，或查看和重新生成 [API 密钥](search-security-api-keys.md)，但不能修改角色成员身份。 |
| [搜索服务参与者内置角色](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles#search-service-contributor) | 等效于参与者角色。 |
| 读取器 |查看服务概要和指标。 此角色的成员无法查看索引、索引器、数据源或密钥信息。  |

角色不授予对服务终结点的访问权限。 搜索服务操作（例如索引管理、索引填充和搜索数据的查询）可通过 API 密钥而非角色进行控制。 有关详细信息，请参阅[管理 API 密钥](search-security-api-keys.md)。

## <a name="see-also"></a>另请参阅

+ [使用 PowerShell 进行管理](search-manage-powershell.md) 
+ [Azure 搜索中的性能和优化](search-performance-optimization.md)
+ [Azure 门户中基于角色的访问控制入门](../active-directory/role-based-access-control-what-is.md)。