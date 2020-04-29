---
title: Azure 安全控制-数据保护
description: Azure 安全控制数据保护
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 035894c80e619851264aae91daa2d7852d156964
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81408541"
---
# <a name="security-control-data-protection"></a>安全控制：数据保护

数据保护建议侧重于解决与加密、访问控制列表、基于标识的访问控制和审核日志记录相关的问题，以便进行数据访问。

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：维护敏感信息的清单

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 4.1 | 13.1 | 客户 |

使用标记帮助跟踪存储或处理敏感信息的 Azure 资源。

- [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔离存储或处理敏感信息的系统

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 4.2 | 13.2、2.10 | 客户 |

为单独的安全域（如环境类型和数据敏感度级别）使用单独的订阅和管理组实现隔离。 你可以限制对你的应用程序和企业环境所需的 Azure 资源的访问级别。 可以通过 Azure Active Directory 基于角色的访问控制来控制对 Azure 资源的访问权限。 

- [如何创建其他 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [如何创建管理组](https://docs.microsoft.com/azure/governance/management-groups/create)

- [如何创建和使用标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

## <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：监视和阻止敏感信息的未授权传输

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 4.3 | 13.3 | Shared |

利用来自网络外围的 Azure Marketplace 中的第三方解决方案，该解决方案监视敏感信息的未授权传输并在警报信息安全专业人员时阻止此类传输。

对于由 Microsoft 管理的底层平台，Microsoft 将所有客户内容视为敏感数据，并防范客户数据丢失和公开。 为了确保 Azure 中的客户数据保持安全，Microsoft 实现并维护一套可靠的数据保护控制和功能。

- [了解 Azure 中的客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密传输中的所有敏感信息

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 4.4 | 14.4 | Shared |

加密传输中的所有敏感信息。 确保连接到 Azure 资源的任何客户端都能够协商 TLS 1.2 或更高版本。

遵循 Azure 安全中心的静态加密和传输中的加密（如果适用）的建议。

- [了解 Azure 传输中的加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit)

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用活动发现工具识别敏感数据

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 4.5 | 14.5 | Shared |

如果 Azure 中没有适用于你的特定服务的功能，请使用第三方活动发现工具来确定组织的技术系统（包括位于现场的或远程服务提供商）存储、处理或传输的所有敏感信息，并更新组织的敏感信息清单。

使用 Azure 信息保护来识别 Office 365 文档中的敏感信息。

使用 Azure SQL 信息保护来帮助对 Azure SQL 数据库中存储的信息进行分类和标记。

- [如何实现 Azure SQL 数据发现](https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification)

- [如何实现 Azure 信息保护](https://docs.microsoft.com/azure/information-protection/deployment-roadmap)

- [了解 Azure 中的客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6：使用基于角色的访问控制来控制对资源的访问

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 4.6 | 14.6 | 客户 |

使用 Azure AD RBAC 来控制对数据和资源的访问，否则使用服务特定的访问控制方法。

- [如何在 Azure 中配置 RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

## <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用基于主机的数据丢失防护来强制实施访问控制

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 4.7 | 14.7 | Shared |

如果需要对计算资源进行符合性，请实现第三方工具（例如基于主机的自动数据丢失防护解决方案），以便即使在将数据复制到系统时也对数据强制实施访问控制。

对于由 Microsoft 管理的底层平台，Microsoft 将所有客户内容视为敏感内容，并在很大程度上防范客户数据丢失和公开。 为了确保 Azure 中的客户数据保持安全，Microsoft 实现并维护一套可靠的数据保护控制和功能。

- [了解 Azure 中的客户数据保护](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密静态敏感信息

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 4.8 | 14.8 | 客户 |

在所有 Azure 资源上使用静态加密。 Microsoft 建议允许 Azure 管理加密密钥，但在某些情况下，可以选择管理自己的密钥。 

- [了解 Azure 中的静态加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)

- [如何配置客户托管的加密密钥](https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal)

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：对关键 Azure 资源的更改进行记录和警报

| Azure ID | CIS Id | 职责 |
|--|--|--|
| 4.9 | 14.9 | 客户 |

将 Azure Monitor 与 Azure 活动日志结合使用，以创建针对关键 Azure 资源进行更改时发出的警报。

- [如何为 Azure 活动日志事件创建警报](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)


## <a name="next-steps"></a>后续步骤

- 请参阅下一个安全控制：[漏洞管理](security-control-vulnerability-management.md)