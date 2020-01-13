---
title: Azure Cosmos DB 的安全控制
description: 获取安全控制的清单，如网络、监视、标识和数据保护，以评估 Azure Cosmos DB
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.openlocfilehash: 1ac4d12f58977497642cdb0706ab7e85e9a4db64
ms.sourcegitcommit: e9776e6574c0819296f28b43c9647aa749d1f5a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/13/2020
ms.locfileid: "75913078"
---
# <a name="security-controls-for-azure-cosmos-db"></a>Azure Cosmos DB 的安全控制

本文介绍 Azure Cosmos DB 中内置的安全控件。

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>网络

| 安全控制 | 是/否 | 说明 |
|---|---|--|
| 服务终结点支持| 是 |  |
| VNet 注入支持| 是 | 使用 VNet 服务终结点，你可以将 Azure Cosmos DB 帐户配置为仅允许从虚拟网络（VNet）的特定子网访问。 你还可以将 VNet 访问与防火墙规则结合使用。 若要了解详细信息，请参阅[从虚拟网络访问 Azure Cosmos DB](VNet-service-endpoint.md)。 |
| 网络隔离和防火墙支持| 是 | 通过防火墙支持，你可以将 Azure Cosmos 帐户配置为仅允许从一组已批准的 IP 地址、一系列 IP 地址和/或云服务进行访问。 若要了解详细信息，请参阅[在 Azure Cosmos DB 中配置 IP 防火墙](how-to-configure-firewall.md)。|
| 强制隧道支持| 是 | 可在虚拟机所在的 VNet 的客户端进行配置。   |

## <a name="monitoring--logging"></a>监视 & 日志记录

| 安全控制 | 是/否 | 说明|
|---|---|--|
| Azure 监视支持（Log analytics、App insights 等）| 是 | 记录发送到 Azure Cosmos DB 的所有请求。 支持[Azure 监视](../azure-monitor/overview.md)、azure 指标、Azure 审核日志记录。  您可以记录与数据平面请求、查询运行时统计信息、查询文本、MongoDB 请求对应的信息。 你还可以设置警报。 |
| 控制和管理平面日志记录和审核| 是 | 适用于帐户级别操作（例如防火墙、Vnet、密钥访问和 IAM）的 Azure 活动日志。 |
| 数据平面日志记录和审核 | 是 | 针对容器级别操作的诊断监视日志记录，如创建容器、预配吞吐量、索引策略以及对文档的 CRUD 操作。 |

## <a name="identity"></a>身份标识

| 安全控制 | 是/否 | 说明|
|---|---|--|
| 身份验证| 是 | 数据库帐户级别为 Yes;在数据平面级别，Cosmos DB 使用资源令牌和密钥访问。 |
| 授权| 是 | Azure Cosmos 帐户支持主密钥（主密钥和辅助密钥）和资源令牌。 您可以使用主密钥来获取对数据的读/写或只读访问权限。 资源令牌允许对资源（如文档和容器）进行有限的时间访问。 |

## <a name="data-protection"></a>数据保护

| 安全控制 | 是/否 | 说明 |
|---|---|--|
| 服务器端加密： Microsoft 托管密钥 | 是 | 默认情况下，所有 Azure Cosmos 数据库和备份都已加密;请参阅[Azure Cosmos DB 中的数据加密](database-encryption-at-rest.md)。 |
| 服务器端加密：客户托管的密钥（BYOK） | 是 | 请参阅[为你的 Azure Cosmos DB 帐户配置客户托管的密钥](how-to-setup-cmk.md)  |
| 列级加密（Azure 数据服务）| 是 | 仅适用于表 API 高级版。 并非所有 Api 都支持此功能。 请参阅[Azure Cosmos DB 简介：表 API](table-introduction.md)。 |
| 传输中的加密（如 ExpressRoute 加密、VNet 加密和 VNet-VNet 加密）| 是 | 传输时加密所有 Azure Cosmos DB 数据。 |
| 加密的 API 调用| 是 | 与 Azure Cosmos DB 的所有连接都支持 HTTPS。 Azure Cosmos DB 还支持 TLS 1.2。<br>可以强制服务器端使用最低 TLS 版本。 为此，请联系[cosmosdbpm@microsoft.com](maito:cosmosdbpm@microsoft.com])。 |

## <a name="configuration-management"></a>配置管理

| 安全控制 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 否  | | 

## <a name="additional-security-controls-for-cosmos-db"></a>Cosmos DB 的其他安全控件

| 安全控制 | 是/否 | 说明|
|---|---|--|
| 跨域资源共享（CORS） | 是 | 请参阅[配置跨域资源共享（CORS）](how-to-configure-cross-origin-resource-sharing.md)。 |

## <a name="next-steps"></a>后续步骤

- 详细了解[Azure 服务中的内置安全控件](../security/fundamentals/security-controls.md)。