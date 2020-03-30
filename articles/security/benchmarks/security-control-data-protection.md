---
title: Azure 安全控制 - 数据保护
description: 安全控制数据保护
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 5482495f87e87e5d05d8adca6b053810a62dcb4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934522"
---
# <a name="security-control-data-protection"></a>安全控制：数据保护

数据保护建议侧重于解决与加密、访问控制列表、基于身份的访问控制和数据访问的审核日志记录相关的问题。

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1： 维护敏感信息的清单

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 4.1 | 13.1 | 客户 |

使用标记可帮助跟踪存储或处理敏感信息的 Azure 资源。

如何创建和使用标记：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2： 隔离存储或处理敏感信息的系统

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 4.2 | 13.2 | 客户 |

为开发、测试和生产实施单独的订阅和/或管理组。 资源应由 VNet/子网分隔，标记得当，并由 NSG 或 Azure 防火墙保护。 存储或处理敏感数据的资源应足够隔离。 对于存储或处理敏感数据的虚拟机，请实施策略和程序，在不使用时将其关闭。

如何创建其他 Azure 订阅：

https://docs.microsoft.com/azure/billing/billing-create-subscription

如何创建管理组：

https://docs.microsoft.com/azure/governance/management-groups/create

如何创建和使用标记：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

如何创建虚拟网络：

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

如何创建具有安全配置的 NSG：

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

如何部署 Azure 防火墙：

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

如何使用 Azure 防火墙配置警报或警报和拒绝：

https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3： 监控和阻止未经授权传输敏感信息

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 4.3 | 13.3 | 客户 |

在网络外围部署自动工具，用于监视敏感信息的未授权传输，并阻止此类传输，同时提醒信息安全专业人员。

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 4.4 | 14.4 | Shared |

加密传输中的所有敏感信息。 确保连接到 Azure 资源的任何客户端能够协商 TLS 1.2 或更高。

请按照 Azure 安全中心的建议，了解静态加密和传输中的加密（如果适用）。

了解使用 Azure 传输中的加密：

https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5： 使用活动发现工具识别敏感数据

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 4.5 | 14.5 | 客户 |

在 Azure 中没有可用于特定服务的功能时，请使用第三方活动发现工具来标识组织技术系统存储、处理或传输的所有敏感信息，包括位于现场或远程服务提供商，并更新组织的敏感信息清单。

使用 Azure 信息保护标识 Office 365 文档中的敏感信息。

使用 Azure SQL 信息保护可帮助分类和标记存储在 Azure SQL 数据库中的信息。

如何实现 Azure SQL 数据发现：

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

如何实现 Azure 信息保护：

https://docs.microsoft.com/azure/information-protection/deployment-roadmap

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6： 使用 Azure RBAC 控制对资源的访问

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 4.6 | 14.6 | 客户 |

使用 Azure AD RBAC 控制对数据和资源的访问，否则请使用特定于服务的访问控制方法。

了解 Azure RBAC：

https://docs.microsoft.com/azure/role-based-access-control/overview

如何在 Azure 中配置 RBAC：

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7： 使用基于主机的数据丢失防护来实施访问控制

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 4.7 | 14.7 | 客户 |

实施第三方工具（如基于主机的自动数据丢失防护解决方案），即使将数据从系统复制，也能强制实施对数据的访问控制。

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8： 静态加密敏感信息

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 4.8 | 14.8 | 客户 |

对所有 Azure 资源使用静态加密。 Microsoft 建议允许 Azure 管理加密密钥，但在某些情况下，您可以选择管理自己的密钥。 

了解 Azure 中静态加密：

https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

如何配置客户托管加密密钥：

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9： 对关键 Azure 资源的更改进行日志和警报

| Azure ID | 独联体的代用品 | 职责 |
|--|--|--|
| 4.9 | 14.9 | 客户 |

将 Azure 监视器与 Azure 活动日志一起创建对关键 Azure 资源进行更改时的警报。

如何为 Azure 活动日志事件创建警报：

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

## <a name="next-steps"></a>后续步骤

请参阅下一个安全控制：[漏洞管理](security-control-vulnerability-management.md)
