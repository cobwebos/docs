---
title: Azure 中支持的 FHIR 功能 - Azure API for FHIR
description: 本文介绍 Azure API for FHIR 中实现的 FHIR 规范功能
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 7f92395f19d84f904493af458d1334f8013fd263
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85808023"
---
# <a name="features"></a>功能

Azure API for FHIR 为适用于 Azure 的 Microsoft FHIR 服务器提供完全托管的部署。 该服务器是 [FHIR](https://hl7.org/fhir) 标准的实现。 本文档列出了 FHIR 服务器的主要功能。

## <a name="fhir-version"></a>FHIR 版本

支持的最新版本：`4.0.1`

目前还支持的旧版本包括：`3.0.2`

## <a name="rest-api"></a>REST API

| API                            | 支持 - PaaS | 支持 - OSS (SQL) | 支持 - OSS (Cosmos DB) | 注释                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| 读取                           | 是       | 是       | 是       |                                                     |
| vread                          | 是       | 是       | 是       |                                                     |
| update                         | 是       | 是       | 是       |                                                     |
| 使用乐观锁定的更新 | 是       | 是       | 是       |                                                     |
| 更新（条件性）           | 是       | 是       | 是       |                                                     |
| 修补                          | 否        | 否        | 否        |                                                     |
| delete                         | 是       | 是       | 是       |                                                     |
| 删除（条件性）           | 否        | 否        | 否        |                                                     |
| create                         | 是       | 是       | 是       | 支持 POST/PUT                               |
| 创建（条件性）           | 是       | 是       | 是       |                                                     |
| 搜索                         | 部分   | 部分   | 部分   | 请参阅下文                                           |
| 链式搜索                 | No        | 是       | No        |                                           |
| 反向链接搜索         | 否        | 否        | 否        |                                            |
| capabilities                   | 是       | 是       | 是       |                                                     |
| 批处理                          | 是       | 是       | 是       |                                                     |
| transaction                    | No        | 是       | No        |                                                     |
| history                        | 是       | 是       | 是       |                                                     |
| 分页                         | 部分   | 部分   | 部分   | `self``next`支持和                     |
| 中间人                 | 否        | 否        | 否        |                                                     |

## <a name="search"></a>搜索

支持所有搜索参数类型。 

| 搜索参数类型 | 支持-PaaS | 支持的操作系统（SQL） | 支持的操作系统（Cosmos DB） | 评论 |
|-----------------------|-----------|-----------|-----------|---------|
| Number                | 是       | 是       | 是       |         |
| Date/DateTime         | 是       | 是       | 是       |         |
| String                | 是       | 是       | 是       |         |
| 令牌                 | 是       | 是       | 是       |         |
| 参考             | 是       | 是       | 是       |         |
| 合成             | 是       | 是       | 是       |         |
| 数量              | 是       | 是       | 是       |         |
| URI                   | 是       | 是       | 是       |         |
| 特殊               | 否        | 否        | 否        |         |


| 修改键             | 支持-PaaS | 支持的操作系统（SQL） | 支持的操作系统（Cosmos DB） | 注释 |
|-----------------------|-----------|-----------|-----------|---------|
|`:missing`             | 是       | 是       | 是       |         |
|`:exact`               | 是       | 是       | 是       |         |
|`:contains`            | 是       | 是       | 是       |         |
|`:text`                | 是       | 是       | 是       |         |
|`:in`令牌          | 否        | 否        | 否        |         |
|`:below`令牌       | 否        | 否        | 否        |         |
|`:above`令牌       | 否        | 否        | 否        |         |
|`:not-in`令牌      | 否        | 否        | 否        |         |
|`:[type]`对  | 否        | 否        | 否        |         |
|`:below` (uri)         | 是       | 是       | 是       |         |
|`:not`                 | No        | 否        | 否        |         |
|`:above`oma-uri         | 否        | 否        | 否        | 问题[#158](https://github.com/Microsoft/fhir-server/issues/158) |

| 常用搜索参数 | 支持-PaaS | 支持的操作系统（SQL） | 支持的操作系统（Cosmos DB） | 注释 |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | 是       | 是       | 是       |         |
| `_lastUpdated`          | 是       | 是       | 是       |         |
| `_tag`                  | 是       | 是       | 是       |         |
| `_profile`              | 是       | 是       | 是       |         |
| `_security`             | 是       | 是       | 是       |         |
| `_text`                 | 否        | 否        | 否        |         |
| `_content`              | 否        | 否        | 否        |         |
| `_list`                 | 否        | 是       | 是       |         |
| `_has`                  | 否        | 否        | 否        |         |
| `_type`                 | 是       | 是       | 是       |         |
| `_query`                | No        | 否        | 否        |         |

| 搜索操作       | 支持-PaaS | 支持的操作系统（SQL） | 支持的操作系统（Cosmos DB） | 评论 |
|-------------------------|-----------|-----------|-----------|---------|
| `_filter`               | 否        | 否        | 否        |         |
| `_sort`                 | 否        | 否        | 否        |         |
| `_score`                | 否        | 否        | 否        |         |
| `_count`                | 是       | 是       | 是       |         |
| `_summary`              | 部分   | 部分   | 部分   | 支持 `_summary=count` |
| `_include`              | 否        | 是       | 否        |         |
| `_revinclude`           | 否        | 否        | 否        |         |
| `_contained`            | 否        | 否        | 否        |         |
| `_elements`             | 否        | 否        | 否        |         |

## <a name="persistence"></a>持久性

Microsoft FHIR 服务器具有可插接式持久性模块（请参阅 [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) ）。

目前，FHIR 服务器开源包括[Azure Cosmos DB](../cosmos-db/index-overview.md)和[SQL 数据库](https://azure.microsoft.com/services/sql-database/)的实现。

Cosmos DB 是一种全球分布的多模型（SQL API、MongoDB API 等）数据库。 它支持不同的[一致性级别](../cosmos-db/consistency-levels.md)。 默认部署模板配置 FHIR 服务器 `Strong` 一致性，但可以使用 `x-ms-consistency-level` 请求标头，通过请求对一致性策略进行修改（通常为宽松模式）。

## <a name="role-based-access-control"></a>基于角色的访问控制

FHIR 服务器使用[Azure Active Directory](https://azure.microsoft.com/services/active-directory/)进行访问控制。 具体来说，如果将配置参数设置为，则强制实施基于角色的访问控制（RBAC）， `FhirServer:Security:Enabled` `true` 并且 FHIR 服务器的所有请求（除外 `/metadata` ）必须 `Authorization` 将请求标头设置为 `Bearer <TOKEN>` 。 令牌必须包含声明中定义的一个或多个角色 `roles` 。 如果令牌包含允许指定资源上指定操作的角色，则将允许请求。

目前，对给定角色允许的操作在 API 上*全局*应用。

## <a name="next-steps"></a>后续步骤

本文介绍了 Azure API for FHIR 中支持的 FHIR 功能。 接下来，部署适用于 FHIR 的 Azure API。
 
>[!div class="nextstepaction"]
>[部署 Azure API for FHIR](fhir-paas-portal-quickstart.md)
