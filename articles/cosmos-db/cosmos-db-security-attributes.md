---
title: Azure Cosmos DB 的安全属性
description: 用于评估 Azure Cosmos DB 的安全属性的清单
services: cosmos-db
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: 50711f8675e1b8aca6b9f90925ea921d22020ddd
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442556"
---
# <a name="security-attributes-for-azure-cosmos-db"></a>Azure Cosmos DB 的安全属性

本文介绍 Azure Cosmos DB 中内置的安全属性。

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>预防

| 安全属性 | 是/否 | 说明 |
|---|---|--|
| 静态加密（例如服务器端加密、带客户托管密钥的服务器端加密，以及其他加密功能） | 是 | 所有 Cosmos DB 数据库和备份默认已加密。请参阅 [Azure Cosmos DB 中的数据加密](database-encryption-at-rest.md)。 不支持使用客户管理的密钥进行服务器端加密。 |
| 传输中加密（例如 ExpressRoute 加密、VNet 中加密，以及 VNet-VNet 加密）| 是 | 所有 Azure Cosmos DB 数据在传输中都会经过加密。 |
| 加密密钥处理（CMK、BYOK 等）| 否 |  |
| 列级加密（Azure 数据服务）| 是 | 只能在表 API 高级版中使用。 并非所有 API 都支持此功能。 请参阅 [Azure Cosmos DB 简介：表 API](table-introduction.md)。 |
| 加密的 API 调用| 是 | 与 Azure Cosmos DB 建立的所有连接都支持 HTTPS。 Azure Cosmos DB 还支持 TLS 1.2 连接，但目前不强制要求支持。 如果客户在其一端禁用了较低级别的 TLS，则可以确保连接到 Cosmos DB。  |

## <a name="network-segmentation"></a>网络分段

| 安全属性 | 是/否 | 说明 |
|---|---|--|
| 服务终结点支持| 是 |  |
| VNet 注入支持| 是 | 使用 VNet 服务终结点可将 Azure Cosmos DB 帐户配置为仅允许从虚拟网络 (VNet) 的特定子网进行访问。 还可以将 VNet 访问与防火墙规则相结合。  请参阅[从虚拟网络访问 Azure Cosmos DB](VNet-service-endpoint.md)。 |
| 网络隔离和防火墙支持| 是 | 借助防火墙支持，可将 Azure Cosmos 帐户配置为仅允许从一组已批准的 IP 地址、某个 IP 地址范围和/或云服务进行访问。 请参阅[在 Azure Cosmos DB 中配置 IP 防火墙](how-to-configure-firewall.md)。|
| 强制隧道支持| 是 | 可在虚拟机所在的 VNet 的客户端进行配置。   |

## <a name="detection"></a>检测

| 安全属性 | 是/否 | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | 发送到 Azure Cosmos DB 的所有请求将被记录。 支持 [Azure 监视](../azure-monitor/overview.md)、Azure 指标和 Azure 审核日志记录。  可以记录对应于数据平面请求、查询运行时统计、查询文本和 MongoDB 请求的信息。 你还可以设置警报。 |

## <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | 是/否 | 说明|
|---|---|--|
| 身份验证| 是 | 在数据库帐户级别为“是”；在数据平面级别，Cosmos DB 将使用资源令牌和密钥访问。 |
| Authorization| 是 | 在使用主密钥（主要和辅助密钥）与资源令牌的 Azure Cosmos 帐户级别受支持。 可以使用主密钥获取对数据的读/写或只读访问权限。 使用资源令牌可以在有限的时间内访问文档和容器等资源。 |


## <a name="audit-trail"></a>审核线索

| 安全属性 | 是/否 | 说明|
|---|---|--|
| 控制和管理平面日志记录和审核| 是 | 帐户级操作（例如防火墙、VNet、密钥访问和 IAM）的 Azure 活动日志。 |
| 数据平面日志记录和审核 | 是 | 容器级操作（例如创建容器、预配吞吐量、为策略编制索引，以及对文档执行 CRUD 操作）的诊断监视日志记录。 |

## <a name="configuration-management"></a>配置管理

| 安全属性 | 是/否 | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 否  | | 

## <a name="additional-security-attributes-for-cosmos-db"></a>Cosmos DB 的其他安全属性

| 安全属性 | 是/否 | 说明|
|---|---|--|
| 跨域资源共享 (CORS) | 是 | 请参阅[配置跨域资源共享 (CORS)](how-to-configure-cross-origin-resource-sharing.md)。 |
