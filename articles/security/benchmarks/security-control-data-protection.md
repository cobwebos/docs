---
title: Azure 安全控制-数据保护
description: 安全控制数据保护
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 5482495f87e87e5d05d8adca6b053810a62dcb4e
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934522"
---
# <a name="security-control-data-protection"></a>安全控制：数据保护

数据保护建议侧重于解决与加密、访问控制列表、基于标识的访问控制和审核日志记录相关的问题，以便进行数据访问。

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 4.1 | 13.1 | 客户 |

使用标记帮助跟踪存储或处理敏感信息的 Azure 资源。

如何创建和使用标记：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 4.2 | 13.2 | 客户 |

为开发、测试和生产实施单独的订阅和/或管理组。 资源应由 VNet/子网进行分隔，并适当标记，并由 NSG 或 Azure 防火墙保护。 存储或处理敏感数据的资源应该足够隔离。 对于存储或处理敏感数据的虚拟机，请在不使用策略和过程时将其关闭。

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

如何配置警报或警报，以及如何拒绝 Azure 防火墙：

https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止敏感信息的未授权传输

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 4.3 | 13.3 | 客户 |

将自动化工具部署在网络外围上，用于监视敏感信息的未授权传输并在通知信息安全专业人员时阻止此类传输。

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 4.4 | 14.4 | 共享 |

加密传输中的所有敏感信息。 确保连接到 Azure 资源的任何客户端都能够协商 TLS 1.2 或更高版本。

遵循 Azure 安全中心的静态加密和传输中的加密（如果适用）的建议。

了解 Azure 传输中的加密：

https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用活动发现工具识别敏感数据

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 4.5 | 14.5 | 客户 |

如果 Azure 中没有适用于你的特定服务的任何功能，请使用第三方活动发现工具来确定由组织的技术系统（包括位于现场的或远程服务提供商，并更新组织的敏感信息清单。

使用 Azure 信息保护来识别 Office 365 文档中的敏感信息。

使用 Azure SQL 信息保护来帮助对 Azure SQL 数据库中存储的信息进行分类和标记。

如何实现 Azure SQL 数据发现：

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

如何实现 Azure 信息保护：

https://docs.microsoft.com/azure/information-protection/deployment-roadmap

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 控制对资源的访问

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 4.6 | 14.6 | 客户 |

使用 Azure AD RBAC 来控制对数据和资源的访问，否则使用服务特定的访问控制方法。

了解 Azure RBAC：

https://docs.microsoft.com/azure/role-based-access-control/overview

如何在 Azure 中配置 RBAC：

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 4.7 | 14.7 | 客户 |

实现第三方工具，例如自动基于主机的数据丢失防护解决方案，即使在将数据复制到系统时也要对数据强制实施访问控制。

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密静态敏感信息

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 4.8 | 14.8 | 客户 |

在所有 Azure 资源上使用静态加密。 Microsoft 建议允许 Azure 管理加密密钥，但在某些情况下，可以选择管理自己的密钥。 

了解 Azure 中的静态加密：

https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

如何配置客户托管的加密密钥：

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：对关键 Azure 资源的更改进行记录和警报

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 4.9 | 14.9 | 客户 |

将 Azure Monitor 与 Azure 活动日志结合使用，以创建针对关键 Azure 资源进行更改时发出的警报。

如何为 Azure 活动日志事件创建警报：

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

## <a name="next-steps"></a>后续步骤

请参阅下一个安全控制：[漏洞管理](security-control-vulnerability-management.md)
