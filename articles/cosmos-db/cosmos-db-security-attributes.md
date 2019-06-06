---
title: Azure Cosmos DB 的安全属性
description: 评估 Azure Cosmos DB 的安全属性进行的检查表
services: cosmos-db
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: ffeb60f5476a540e3da46a82c240b0dda9aa6be2
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480462"
---
# <a name="security-attributes-for-azure-cosmos-db"></a>Azure Cosmos DB 的安全属性

本文介绍内置于 Azure Cosmos DB 的常见安全属性。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>预防

| 安全特性 | 是/否 | 说明 |
|---|---|--|
| （例如服务器端加密、 使用客户托管密钥的服务器端加密和其他加密功能） 的静态加密 | 是 | 所有 Cosmos DB 数据库和备份都加密默认设置。请参阅[Azure Cosmos DB 中的数据加密](database-encryption-at-rest.md)。 不支持使用客户托管密钥的服务器端加密。 |
| 传输中加密 （如 Vnet 加密和 VNet 加密中的 ExpressRoute 加密）| 是 | Azure Cosmos DB 的所有数据传输进行都加密。 |
| 加密密钥处理（CMK、BYOK 等）| 否 |  |
| 列级加密（Azure 数据服务）| 是 | 仅在表 API 高级。 并非所有 Api 都支持此功能。 请参阅[Azure Cosmos DB 简介：将表 API](table-introduction.md)。 |
| 加密的 API 调用| 是 | 所有连接到 Azure Cosmos DB 都支持 HTTPS。 Azure Cosmos DB 还支持 TLS 1.2 连接，但这还不强制执行。 如果客户关闭较低级别 TLS 他们一端，他们可以确保连接到 Cosmos DB。  |

## <a name="network-segmentation"></a>网络分段

| 安全特性 | 是/否 | 说明 |
|---|---|--|
| 服务终结点支持| 是 |  |
| vNET 注入支持| 是 | 使用 VNet 服务终结点，可以配置一个 Azure Cosmos DB 帐户以允许仅来自特定子网的虚拟网络 (VNet) 的访问。 也可以使用防火墙规则组合 VNet 访问。  请参阅[从虚拟网络访问 Azure Cosmos DB](vnet-service-endpoint.md)。 |
| 网络隔离和 Firewalling 支持| 是 | 使用防火墙的支持，可以配置你的 Azure Cosmos 帐户以允许仅从一组已批准的 IP 地址，一系列 IP 地址进行访问和/或云服务。 请参阅[Azure Cosmos DB 中的配置 IP 防火墙](how-to-configure-firewall.md)。|
| 支持强制隧道 | 是 | 可以在虚拟机的位置在 VNET 上的客户端配置。   |

## <a name="detection"></a>检测

| 安全特性 | 是/否 | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | 记录发送到 Azure Cosmos DB 的所有请求。 [Azure 监视](../azure-monitor/overview.md)，支持 Azure 指标、 Azure 审核日志记录。  可以记录对应的数据平面请求、 查询运行时统计信息、 查询文本信息，MongoDB 请求。 你还可以设置警报。 |

## <a name="identity-and-access-management"></a>标识和访问管理

| 安全特性 | 是/否 | 说明|
|---|---|--|
| Authentication| 是 | 是数据库级别的帐户;在数据平面级别，Cosmos DB 使用资源令牌和密钥访问权限。 |
| 授权| 是 | 支持在 Azure Cosmos 帐户主密钥 （主要和辅助） 和资源令牌。 可以获取读/写或只读数据的主密钥的密钥的访问。 资源令牌允许有限的时间内访问资源，例如文档和容器。 |


## <a name="audit-trail"></a>审核线索

| 安全特性 | 是/否 | 说明|
|---|---|--|
| 控制/管理计划日志记录和审核| 是 | Azure 活动日志中的是否有帐户操作，例如防火墙、 Vnet，密钥访问权限和 IAM。 |
| 数据平面日志记录和审核 | 是 | 诊断监视，如容器级别操作的日志记录创建容器，预配吞吐量，索引策略，以及对文档的 CRUD 操作。 |

## <a name="configuration-management"></a>配置管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 否  | | 

## <a name="additional-security-attributes-for-cosmos-db"></a>Cosmos DB 的其他安全属性

| 安全特性 | 是/否 | 说明|
|---|---|--|
| 跨域资源共享 (CORS) | 是 | 请参阅[配置跨域资源共享 (CORS)](how-to-configure-cross-origin-resource-sharing.md)。 |
