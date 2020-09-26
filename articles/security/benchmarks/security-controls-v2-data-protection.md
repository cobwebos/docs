---
title: Azure 安全基准 V2-数据保护
description: Azure 安全基准 V2 数据保护
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: ab42acbc07072f48d28b3adb5bc4c65672f257bc
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91314311"
---
# <a name="security-control-v2-data-protection"></a>安全控制 V2：数据保护

数据保护涉及到对静态数据保护、传输中的数据保护，以及通过授权访问机制的控制。 这包括使用 Azure 中的访问控制、加密和日志记录发现、分类、保护和监视敏感数据资产。

## <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1：发现，对敏感数据进行分类和标记

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| DP-1 | 13.1、14.5、14。7 | SC-28 |

发现、分类和标记您的敏感数据，以便您可以设计适当的控件，以确保组织的技术系统安全存储、处理和传输敏感信息。 

使用 Azure 信息保护 (及其关联的扫描工具) 了解 Azure、本地、Office 365 和其他位置中的 Office 文档中的敏感信息。 

可以使用 Azure SQL 信息保护来帮助对 Azure SQL 数据库中存储的信息进行分类和标记。

- [使用 Azure 信息保护标记敏感信息](/azure/information-protection/what-is-information-protection) 

- [如何实现 Azure SQL 数据发现](/azure/sql-database/sql-database-data-discovery-and-classification)

**责任**：共享

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [应用程序安全性和 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)  

- [数据安全性](/azure/cloud-adoption-framework/organize/cloud-security-data-security) 

- [基础结构和终结点安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-2-protect-sensitive-data"></a>DP-2：保护敏感数据

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| DP-2 | 13.2、2.10 | SC-7、AC-4 |

通过使用 azure 基于角色的访问控制来限制对敏感数据的访问控制 (Azure RBAC) 、基于网络的访问控制和 Azure 服务中的特定控件 (例如，SQL 中的加密和) 中的其他数据库。 

为了确保一致的访问控制，所有类型的访问控制都应该与企业分段策略一致。 企业分段策略还应由敏感或业务关键数据和系统的位置来了解。

对于 Microsoft 管理的基础平台，Microsoft 会将所有客户内容视为敏感数据，全方位防范客户数据丢失和泄露。 为了确保 Azure 中的客户数据保持安全，Microsoft 实现了一些默认的数据保护控制和功能。

- [Azure 基于角色的访问控制 (RBAC)](../../role-based-access-control/overview.md)

- [了解 Azure 中的客户数据保护](../fundamentals/protection-customer-data.md)

**责任**：共享

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [应用程序安全性和 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [数据安全性](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

- [基础结构和终结点安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3：监视敏感数据的未授权传输

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| DP-3 | 13.3 | AC-4，SI-4 |

监视是否向企业可见性和控制以外的位置进行未经授权的数据传输。 这通常涉及监视异常活动 (大或异常传输) 可能指示未经授权的数据渗透。 

Azure 存储高级威胁防护 (ATP) 和 Azure SQL ATP 可能会对可能指示未经授权传输敏感信息的信息的异常传输发出警报。 

Azure 信息保护 (AIP) 为已分类并标记的信息提供监视功能。 

如果需要针对数据丢失防护 (DLP) 的符合性，则可以使用基于主机的 DLP 解决方案来强制执行策略，以防止数据渗透。

- [启用 Azure SQL ATP](../../azure-sql/database/threat-detection-overview.md)

- [启用 Azure 存储 ATP](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-security-center)

**责任**：共享

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全操作](/azure/cloud-adoption-framework/organize/cloud-security) 

- [应用程序安全性和 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [基础结构和终结点安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4：加密传输中的敏感信息

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| DP-4 | 14.4 | SC-8 |

为了补充访问控制，应保护传输中的数据免受 "带外" 攻击 (例如，流量捕获) 使用加密确保攻击者无法轻松读取或修改数据。 

虽然这对于专用网络上的流量是可选的，但这对于外部网络和公用网络上的流量是至关重要的。 对于 HTTP 流量，请确保连接到 Azure 资源的任何客户端都可以协商 TLS 1.2 或更高版本。 对于远程管理，请使用适用于 Linux 的 SSH () 或适用于) Windows 的 RDP/TLS (，而不是使用未加密的协议。 已过时的 SSL、TLS 和 SSH 版本和协议，应禁用弱密码。  

默认情况下，Azure 为 Azure 数据中心之间传输中的数据提供加密。 

- [了解 Azure 传输中的加密](../fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [TLS 安全信息](/security/engineering/solving-tls1-problem)

- [传输中的 Azure 数据双加密](../fundamentals/double-encryption.md#data-in-transit)

**责任**：共享

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全体系结构](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [基础结构和终结点安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [应用程序安全性和 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [数据安全性](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

## <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5：对静态敏感数据进行加密

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| DP-5 | 14.8 | SC-28，SC-12 |

若要补充访问控制，应防止静态数据受到 "带外" 的攻击， (例如使用加密访问基础存储) 。 这有助于确保攻击者无法轻松读取或修改数据。 

默认情况下，Azure 为静态数据提供加密。 对于高度敏感的数据，您可以选择在所有 Azure 资源上实现额外的静态加密。 默认情况下，azure 会管理你的加密密钥，但 Azure 提供选项来管理你自己的密钥 (适用于某些 Azure 服务的客户托管密钥) 。

- [了解 Azure 中的静态加密](../fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services)

- [如何配置客户管理的加密密钥](../../storage/common/storage-encryption-keys-portal.md)

- [加密模型和密钥管理表](../fundamentals/encryption-models.md)

- [Azure 中的静态数据加密](../fundamentals/double-encryption.md#data-at-rest)

**责任**：共享

**客户安全利益干系人** ([详细](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全体系结构](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [基础结构和终结点安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [应用程序安全性和 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [数据安全性](/azure/cloud-adoption-framework/organize/cloud-security-data-security)

