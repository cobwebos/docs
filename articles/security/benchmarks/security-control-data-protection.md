---
title: Azure 安全控制 - 数据保护
description: Azure 安全控制 - 数据保护
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: f2162ee6df551e1bc64741229aec99d5e697fd29
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91395985"
---
# <a name="security-control-data-protection"></a>安全控制：数据保护

数据保护建议侧重于解决与加密、访问控制列表、基于标识的访问控制以及数据访问审核日志记录相关的问题。

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 4.1 | 13.1 | 客户 |

使用标记可以帮助跟踪存储或处理敏感信息的 Azure 资源。

- [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 4.2 | 13.2、2.10 | 客户 |

使用单独的订阅和管理组对各个安全域（如环境类型和数据敏感度级别）实现隔离。 你可以限制对应用程序和企业环境所需 Azure 资源的访问级别。 可以通过 Azure 基于角色的访问控制 (Azure RBAC) 来控制对 Azure 资源的访问。 

- [如何创建其他 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [如何创建管理组](https://docs.microsoft.com/azure/governance/management-groups/create)

- [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止未经授权的敏感信息传输

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 4.3 | 13.3 | 共享 |

利用 Azure 市场中有关网络外围的第三方解决方案，该解决方案可监视并阻止敏感信息的未授权传输，同时提醒信息安全专业人员。

对于 Microsoft 管理的基础平台，Microsoft 会将所有客户内容视为敏感数据，并全方位防范客户数据丢失和泄露。 为了确保 Azure 中的客户数据保持安全，Microsoft 已实施并维护一套可靠的数据保护控制机制和功能。

- [了解 Azure 中的客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 4.4 | 14.4 | 共享 |

加密传输中的所有敏感信息。 确保连接到 Azure 资源的任何客户端能够协商 TLS 1.2 或更高版本。

请按照 Azure 安全中心的建议，了解静态加密和传输中的加密（如果适用）。

- [了解 Azure 传输中的加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用有效的发现工具识别敏感数据

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 4.5 | 14.5 | 共享 |

如果 Azure 中的功能均不适用于你的特定服务，请使用可用的第三方发现工具来标识组织技术系统（包括现场或远程服务提供商处的技术系统）存储、处理或传输的所有敏感信息，并更新组织的敏感信息清单。

使用 Azure 信息保护来识别 Microsoft 365 文档中的敏感信息。

使用 Azure SQL 信息保护有助于对 Azure SQL 数据库中存储的信息进行分类和标记。

- [如何实现 Azure SQL 数据发现](https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification)

- [如何实现 Azure 信息保护](https://docs.microsoft.com/azure/information-protection/deployment-roadmap)

- [了解 Azure 中的客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 控制对资源的访问

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 4.6 | 14.6 | 客户 |

使用 azure RBAC)  (基于角色的访问控制来控制对数据和资源的访问，否则使用服务特定的访问控制方法。

- [如何配置 Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 4.7 | 14.7 | 共享 |

如果需要在计算资源上确保合规性，则实施第三方工具（如基于主机的自动数据丢失防护解决方案），以便对数据强制实施访问控制，即使数据从系统复制也是如此。

对于 Microsoft 管理的底层平台，Microsoft 会将所有客户内容视为敏感数据，并会全方位地防范客户数据丢失和遭到透露。 为了确保 Azure 中的客户数据保持安全，Microsoft 实施并维护了一套可靠的数据保护控制措施和功能。

- [了解 Azure 中的客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：静态加密敏感信息

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 4.8 | 14.8 | 客户 |

在所有 Azure 资源上使用静态加密。 Microsoft 建议允许 Azure 管理加密密钥，但在某些情况下，你可以选择管理自己的密钥。 

- [了解 Azure 中的静态加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)

- [如何配置客户管理的加密密钥](https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal)

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

| Azure ID | CIS ID | 责任方 |
|--|--|--|
| 4.9 | 14.9 | 客户 |

将 Azure Monitor 与 Azure 活动日志结合使用，以创建在关键 Azure 资源发生更改时发出的警报。

- [如何针对 Azure 活动日志事件创建警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)


## <a name="next-steps"></a>后续步骤

- 请参阅下一个安全控制：[漏洞管理](security-control-vulnerability-management.md)