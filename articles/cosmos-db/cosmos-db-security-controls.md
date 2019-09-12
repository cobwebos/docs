---
title: Azure Cosmos DB 的安全控制
description: 用于评估 Azure Cosmos DB 安全控制的清单
services: cosmos-db
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 20cdfc61a4cdfe5263e48d049aab14cad2458b06
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70886333"
---
# <a name="security-controls-for-azure-cosmos-db"></a>Azure Cosmos DB 的安全控制

本文介绍 Azure Cosmos DB 中内置的安全控件。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>网络

| 安全控制 | 是/否 | 说明 |
|---|---|--|
| 服务终结点支持| 是 |  |
| VNet 注入支持| 是 | 使用 VNet 服务终结点可将 Azure Cosmos DB 帐户配置为仅允许从虚拟网络 (VNet) 的特定子网进行访问。 还可以将 VNet 访问与防火墙规则相结合。  请参阅[从虚拟网络访问 Azure Cosmos DB](VNet-service-endpoint.md)。 |
| 网络隔离和防火墙支持| 是 | 借助防火墙支持，可将 Azure Cosmos 帐户配置为仅允许从一组已批准的 IP 地址、某个 IP 地址范围和/或云服务进行访问。 请参阅[在 Azure Cosmos DB 中配置 IP 防火墙](how-to-configure-firewall.md)。|
| 强制隧道支持| 是 | 可以在虚拟机所在的 VNet 中的客户端上配置。   |

## <a name="monitoring--logging"></a>监视 & 日志记录

| 安全控制 | 是/否 | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | 发送到 Azure Cosmos DB 的所有请求将被记录。 支持 [Azure 监视](../azure-monitor/overview.md)、Azure 指标和 Azure 审核日志记录。  可以记录对应于数据平面请求、查询运行时统计、查询文本和 MongoDB 请求的信息。 你还可以设置警报。 |
| 控制和管理平面日志记录和审核| 是 | 帐户级操作（例如防火墙、VNet、密钥访问和 IAM）的 Azure 活动日志。 |
| 数据平面日志记录和审核 | 是 | 容器级操作（例如创建容器、预配吞吐量、为策略编制索引，以及对文档执行 CRUD 操作）的诊断监视日志记录。 |

## <a name="identity"></a>标识

| 安全控制 | 是/否 | 说明|
|---|---|--|
| 身份验证| 是 | 在数据库帐户级别为“是”；在数据平面级别，Cosmos DB 将使用资源令牌和密钥访问。 |
| Authorization| 是 | 在使用主密钥（主要和辅助密钥）与资源令牌的 Azure Cosmos 帐户级别受支持。 可以使用主密钥获取对数据的读/写或只读访问权限。 使用资源令牌可以在有限的时间内访问文档和容器等资源。 |

## <a name="data-protection"></a>数据保护

| 安全控制 | 是/否 | 说明 |
|---|---|--|
| 服务器端静态加密：Microsoft 托管的密钥 | 是 | 所有 Cosmos 数据库和备份默认已加密。请参阅 [Azure Cosmos DB 中的数据加密](database-encryption-at-rest.md)。 不支持使用客户管理的密钥进行服务器端加密。 |
| 服务器端加密：客户托管的密钥（BYOK） | 否 |  |
| 列级加密（Azure 数据服务）| 是 | 只能在表 API 高级版中使用。 并非所有 API 都支持此功能。 请参阅 [Azure Cosmos DB 简介：表 API](table-introduction.md)。 |
| 传输中加密（例如 ExpressRoute 加密、VNet 中加密，以及 VNet-VNet 加密）| 是 | 所有 Azure Cosmos DB 数据在传输中都会经过加密。 |
| 加密的 API 调用| 是 | 与 Azure Cosmos DB 建立的所有连接都支持 HTTPS。 Azure Cosmos DB 还支持 TLS 1.2 连接，但目前不强制要求支持。 如果客户在其一端禁用了较低级别的 TLS，则可以确保连接到 Cosmos DB。  |

## <a name="configuration-management"></a>配置管理

| 安全控制 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 否  | | 

## <a name="additional-security-controls-for-cosmos-db"></a>Cosmos DB 的其他安全控件

| 安全控制 | 是/否 | 说明|
|---|---|--|
| 跨域资源共享 (CORS) | 是 | 请参阅[配置跨域资源共享 (CORS)](how-to-configure-cross-origin-resource-sharing.md)。 |

## <a name="next-steps"></a>后续步骤

- 详细了解[Azure 服务中的内置安全控件](../security/fundamentals/security-controls.md)。