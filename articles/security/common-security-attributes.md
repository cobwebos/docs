---
title: Azure 服务的安全属性
description: 用于评估 Azure Service Fabric 的常见安全属性的清单
services: security
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: security
ms.topic: conceptual
ms.date: 07/11/2019
ms.author: mbaldwin
ms.openlocfilehash: d45e28175412b574432adb59cf700568c9a7fb39
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304255"
---
# <a name="security-attributes-for-azure-services"></a>Azure 服务的安全属性

本文收集所选 Azure 服务的常见安全属性。 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="api-managementapi-managementapi-management-security-attributesmd"></a>[API 管理](../api-management/api-management-security-attributes.md)

### <a name="preventative"></a>预防

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 静态加密（例如服务器端加密、带客户托管密钥的服务器端加密，以及其他加密功能） | 敏感数据（例如证书、密钥以及使用机密命名的值）使用服务托管的、基于服务实例的密钥进行加密。 |
| 传输中加密（例如 ExpressRoute 加密、VNet 中加密，以及 VNet-VNet 加密） | 是 | [快速路由](../expressroute/index.yml)和 VNet 加密由 [Azure 网络](../virtual-network/index.yml)提供。 |
| 加密密钥处理（CMK、BYOK 等）| 否 | 所有加密密钥都是基于服务实例的，也是通过服务托管的。 |
| 列级加密（Azure 数据服务）| 不可用 | |
| 加密的 API 调用| 是 | 通过 [Azure 资源管理器](../azure-resource-manager/index.yml)经 TLS 进行管理平面调用。 需要有效 JSON web 令牌 (JWT)。  可以通过 TLS 以及某个受支持的身份验证机制（例如，客户端证书或 JWT）对数据平面调用进行保护。
 |

### <a name="network-segmentation"></a>网络分段

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 否 | |
| VNet 注入支持| 是 | |
| 网络隔离和防火墙支持| 是 | 分别使用网络安全组 (NSG) 和 Azure 应用程序网关（或其他软件设备）。 |
| 强制隧道支持| 是 | Azure 网络支持强制隧道。 |

### <a name="detection"></a>检测

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | |

### <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 身份验证| 是 | |
| Authorization| 是 | |


### <a name="audit-trail"></a>审核线索

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 控制和管理平面日志记录和审核| 是 | [Azure Monitor 活动日志](../azure-monitor/platform/activity-logs-overview.md) |
| 数据平面日志记录和审核| 是 | [Azure Monitor 诊断日志](../azure-monitor/platform/diagnostic-logs-overview.md)和 (可选) [Azure 应用程序见解](../azure-monitor/app/app-insights-overview.md)。  |

### <a name="configuration-management"></a>配置管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是 | 使用 [Azure API 管理 DevOps 资源工具包](https://aka.ms/apimdevops) |

### <a name="vulnerability-scans-false-positives"></a>漏洞扫描误报

此部分记录不影响 Azure API 管理的常见漏洞。

| 漏洞               | 描述                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed 是在实现 TLS SessionTicket 扩展过程中出现的漏洞，在某些 F5 产品中发现。 该漏洞会导致从取消初始化的内存中泄露（“溢出”）最多 31 字节的数据。 这是由 TLS 堆栈填充通过客户端传递的会话 ID 导致的, 其数据用于使其长度为32位。 |


## <a name="app-serviceapp-serviceapp-service-security-attributesmd"></a>[应用服务](../app-service/app-service-security-attributes.md)

### <a name="preventative"></a>预防

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 静态加密（例如服务器端加密、带客户托管密钥的服务器端加密，以及其他加密功能） | 是 | 网站文件内容存储在 Azure 存储中，后者自动对内容进行静态加密。 请参阅[静态数据的 Azure 存储加密](../storage/common/storage-service-encryption.md)。<br><br>客户提供的机密会进行静态加密。 机密在存储到应用服务配置数据库中时会进行静态加密。<br><br>本地附加的磁盘可以由网站选择性地用作临时存储 (D:\local and %TMP%)。 本地附加的磁盘不进行静态加密。 |
| 传输中加密（例如 ExpressRoute 加密、VNet 中加密，以及 VNet-VNet 加密） | 是 | 客户可以将网站配置为要求将 HTTPS 用于入站流量。 请参阅博客文章[如何仅创建 Azure App Service 的 HTTPS](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/)。 |
| 加密密钥处理（CMK、BYOK 等）| 是 | 客户可以选择将应用程序机密存储在 Key Vault 中，在运行时检索它们。 请参阅[使用应用服务和 Azure Functions 的 Key Vault 引用（预览版）](../app-service/app-service-key-vault-references.md)。|
| 列级加密（Azure 数据服务）| 不可用 | |
| 加密的 API 调用| 是 | 可以通过基于 HTTPS 的 [Azure 资源管理器](../azure-resource-manager/index.yml)调用进行管理调用，以便配置应用服务。 |

### <a name="network-segmentation"></a>网络分段

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 是 | 目前提供适用于应用服务的预览版。 请参阅 [Azure 应用服务访问限制](../app-service/app-service-ip-restrictions.md)。 |
| VNet 注入支持| 是 | 应用服务环境是专用于注入到客户虚拟网络的单个客户的应用服务的专用实现。 请参阅[应用服务环境简介](../app-service/environment/intro.md)。 |
| 网络隔离和防火墙支持| 是 | 对于应用服务的公共多租户变体，客户可以配置网络 ACL（IP 限制），锁定允许的入站流量。  请参阅 [Azure 应用服务访问限制](../app-service/app-service-ip-restrictions.md)。  应用服务环境直接部署到虚拟网络中，因此可以通过 NSG 来确保安全。 |
| 强制隧道支持| 是 | 应用服务环境可部署到客户的虚拟网络中, 其中配置了强制隧道。 客户需要按照[将应用服务环境配置为强制隧道](../app-service/environment/forced-tunnel-support.md)中的说明进行操作。 |

### <a name="detection"></a>检测

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | 应用服务与支持 Application Insights (完整 .NET Framework、.NET Core、Java 和 NODE.JS) 的语言的 Application Insights 集成。  请参阅[监视 Azure App Service 性能](../azure-monitor/app/azure-web-apps.md)。 应用服务还会将应用程序指标发送到 Azure Monitor。 请参阅[在 Azure App Service 中监视应用](../app-service/web-sites-monitor.md)。 |

### <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 身份验证| 是 | 客户可以构建基于应用服务的应用程序，这些应用程序自动集成 [Azure Active Directory (Azure AD)](../active-directory/index.yml) 以及其他与 OAuth 兼容的标识提供者；请参阅 [Azure 应用服务中的身份验证和授权](../app-service/overview-authentication-authorization.md)。 对应用服务资产进行管理访问时，所有访问都可以通过组合使用经 Azure AD 验证的主体和 Azure 资源管理器 RBAC 角色进行控制。 |
| Authorization| 是 | 对应用服务资产进行管理访问时，所有访问都可以通过组合使用经 Azure AD 验证的主体和 Azure 资源管理器 RBAC 角色进行控制。  |


### <a name="audit-trail"></a>审核线索

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 控制和管理平面日志记录和审核| 是 | 在应用服务对象上执行的所有管理操作都通过 [Azure 资源管理器](../azure-resource-manager/index.yml)来完成。 可以通过门户和 CLI 获取这些操作的历史日志；请参阅 [Azure 资源管理器资源提供程序操作](../role-based-access-control/resource-provider-operations.md#microsoftweb)和 [az monitor activity-log](/cli/azure/monitor/activity-log)。 |
| 数据平面日志记录和审核 | 否 | 应用服务的数据平面是一个远程文件共享，其中包含客户的已部署网站内容。  不对远程文件共享进行审核。 |

### <a name="configuration-management"></a>配置管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是 | 进行管理操作时，可以将应用服务配置的状态导出为 Azure 资源管理器模板，并在一段时间内进行版本控制。 进行运行时操作时，客户可以使用应用服务的部署槽功能保留某个应用程序的多个不同的实时版本。 | 



## <a name="azure-resource-managerazure-resource-managerazure-resource-manager-security-attributesmd"></a>[Azure 资源管理器](../azure-resource-manager/azure-resource-manager-security-attributes.md)

### <a name="preventative"></a>预防

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 静态加密（例如服务器端加密、带客户托管密钥的服务器端加密，以及其他加密功能） | 是 |  |
| 传输中加密（例如 ExpressRoute 加密、VNet 中加密，以及 VNet-VNet 加密） | 是 | HTTPS/TLS。 |
| 加密密钥处理（CMK、BYOK 等）| 不可用 | Azure 资源管理器不存储客户内容，仅存储控制数据。 |
| 列级加密（Azure 数据服务）| 是 | |
| 加密的 API 调用| 是 | |

### <a name="network-segmentation"></a>网络分段

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 否 | |
| VNet 注入支持| 是 | |
| 网络隔离和防火墙支持| 否 |  |
| 强制隧道支持| 否 |  |

### <a name="detection"></a>检测

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 否 | |

### <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 身份验证| 是 | 基于 [Azure Active Directory](/azure/active-directory)。|
| Authorization| 是 | |


### <a name="audit-trail"></a>审核线索

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 控制和管理平面日志记录和审核| 是 | 活动日志公开对资源执行的所有写入操作（PUT、POST、DELETE）；请参阅[查看活动日志来审核对资源的操作](../azure-resource-manager/resource-group-audit.md)。 |
| 数据平面日志记录和审核| 不可用 | |

### <a name="configuration-management"></a>配置管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是 |  |


## <a name="azure-backupbackupbackup-security-attributesmd"></a>[Azure 备份](../backup/backup-security-attributes.md)

### <a name="preventative"></a>预防

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 静态加密（例如服务器端加密、带客户托管密钥的服务器端加密，以及其他加密功能）| 是 | 对存储帐户使用存储服务加密。 |
| 传输中加密（例如 ExpressRoute 加密、VNet 中加密，以及 VNet-VNet 加密） | 否 | 使用 HTTPS。 |
| 加密密钥处理（CMK、BYOK 等）| 否 |  |
| 列级加密（Azure 数据服务）| 否 |  |
| 加密的 API 调用| 是 |  |

### <a name="network-segmentation"></a>网络分段

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 否 |  |
| VNet 注入支持| 否 |  |
| 网络隔离和防火墙支持| 是 | 对 VM 备份支持强制隧道。 对 VM 内运行的工作负荷不支持强制隧道。 |
| 强制隧道支持| 否 |  |

### <a name="detection"></a>检测

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | 通过诊断日志支持 Log Analytics。 有关详细信息，请参阅[使用 Log Analytics 监视 Azure 备份保护的工作负荷](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/)。 |

### <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 身份验证| 是 | 身份验证通过 Azure Active Directory 来进行。 |
| Authorization| 是 | 使用客户创建和内置的 RBAC 角色。 有关详细信息，请参阅[使用基于角色的访问控制管理 Azure 备份恢复点](/azure/backup/backup-rbac-rs-vault)。 |


### <a name="audit-trail"></a>审核线索

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 控制和管理平面日志记录和审核| 是 | 来自 Azure 门户的所有客户触发操作都会记录到活动日志中。 |
| 数据平面日志记录和审核| 否 | 无法直接访问 Azure 备份数据平面。  |

### <a name="configuration-management"></a>配置管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是|  |

## <a name="cosmos-dbcosmos-dbcosmos-db-security-attributesmd"></a>[Cosmos DB](../cosmos-db/cosmos-db-security-attributes.md)

### <a name="preventative"></a>预防

| 安全属性 | 是/否 | 说明 |
|---|---|--|
| 静态加密（例如服务器端加密、带客户托管密钥的服务器端加密，以及其他加密功能） | 是 | 所有 Cosmos DB 数据库和备份默认已加密。请参阅 [Azure Cosmos DB 中的数据加密](../cosmos-db/database-encryption-at-rest.md)。 不支持使用客户管理的密钥进行服务器端加密。 |
| 传输中加密（例如 ExpressRoute 加密、VNet 中加密，以及 VNet-VNet 加密）| 是 | 所有 Azure Cosmos DB 数据在传输中都会经过加密。 |
| 加密密钥处理（CMK、BYOK 等）| 否 |  |
| 列级加密（Azure 数据服务）| 是 | 只能在表 API 高级版中使用。 并非所有 API 都支持此功能。 请参阅 [Azure Cosmos DB 简介：表 API](../cosmos-db/table-introduction.md)。 |
| 加密的 API 调用| 是 | 与 Azure Cosmos DB 建立的所有连接都支持 HTTPS。 Azure Cosmos DB 还支持 TLS 1.2 连接，但目前不强制要求支持。 如果客户在其一端禁用了较低级别的 TLS，则可以确保连接到 Cosmos DB。  |

### <a name="network-segmentation"></a>网络分段

| 安全属性 | 是/否 | 说明 |
|---|---|--|
| 服务终结点支持| 是 |  |
| vNET 注入支持| 是 | 使用 VNet 服务终结点可将 Azure Cosmos DB 帐户配置为仅允许从虚拟网络 (VNet) 的特定子网进行访问。 还可以将 VNet 访问与防火墙规则相结合。  请参阅[从虚拟网络访问 Azure Cosmos DB](../cosmos-db/vnet-service-endpoint.md)。 |
| 网络隔离和防火墙支持| 是 | 借助防火墙支持，可将 Azure Cosmos 帐户配置为仅允许从一组已批准的 IP 地址、某个 IP 地址范围和/或云服务进行访问。 请参阅[在 Azure Cosmos DB 中配置 IP 防火墙](../cosmos-db/how-to-configure-firewall.md)。|
| 支持强制隧道 | 是 | 可以在虚拟机所在的 VNET 中的客户端上配置。   |

### <a name="detection"></a>检测

| 安全属性 | 是/否 | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | 发送到 Azure Cosmos DB 的所有请求将被记录。 支持 [Azure 监视](../azure-monitor/overview.md)、Azure 指标和 Azure 审核日志记录。  可以记录对应于数据平面请求、查询运行时统计、查询文本和 MongoDB 请求的信息。 还可以设置警报。 |

### <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | 是/否 | 说明|
|---|---|--|
| 身份验证| 是 | 在数据库帐户级别为“是”；在数据平面级别，Cosmos DB 将使用资源令牌和密钥访问。 |
| Authorization| 是 | 在使用主密钥（主要和辅助密钥）与资源令牌的 Azure Cosmos 帐户级别受支持。 可以使用主密钥获取对数据的读/写或只读访问权限。 使用资源令牌可以在有限的时间内访问文档和容器等资源。 |

### <a name="audit-trail"></a>审核线索

| 安全属性 | 是/否 | 说明|
|---|---|--|
| 控制/管理计划日志记录和审核| 是 | 帐户级操作（例如防火墙、VNet、密钥访问和 IAM）的 Azure 活动日志。 |
| 数据平面日志记录和审核 | 是 | 容器级操作（例如创建容器、预配吞吐量、为策略编制索引，以及对文档执行 CRUD 操作）的诊断监视日志记录。 |

### <a name="configuration-management"></a>配置管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 否  | | 

### <a name="additional-security-attributes-for-cosmos-db"></a>Cosmos DB 的其他安全属性

| 安全属性 | 是/否 | 说明|
|---|---|--|
| 跨域资源共享 (CORS) | 是 | 请参阅[配置跨域资源共享 (CORS)](../cosmos-db/how-to-configure-cross-origin-resource-sharing.md)。 |


## <a name="event-hubsevent-hubsevent-hubs-security-attributesmd"></a>[事件中心](../event-hubs/event-hubs-security-attributes.md)

### <a name="preventative"></a>预防

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 静态加密（例如服务器端加密、带客户托管密钥的服务器端加密，以及其他加密功能） |  是 | |
| 传输中加密（例如 ExpressRoute 加密、VNet 中加密，以及 VNet-VNet 加密） | 是 | |
| 加密密钥处理（CMK、BYOK 等）| 否 |  |
| 列级加密（Azure 数据服务）| 不可用 | |
| 加密的 API 调用| 是 |  |

### <a name="network-segmentation"></a>网络分段

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 是 |  |
| vNET 注入支持| 否 | |
| 网络隔离和防火墙支持| 是 |  |
| 强制隧道支持| 否 |  |

### <a name="detection"></a>检测

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | |

### <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 身份验证| 是 | |
| Authorization|  是 | |


### <a name="audit-trail"></a>审核线索

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 控制和管理平面日志记录和审核| 是 |  |
| 数据平面日志记录和审核| 是 |   |

### <a name="configuration-management"></a>配置管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是 | |


## <a name="expressrouteexpressrouteexpressroute-security-attributesmd"></a>[ExpressRoute](../expressroute/expressroute-security-attributes.md)

### <a name="preventative"></a>预防

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 静态加密（例如服务器端加密、带客户托管密钥的服务器端加密，以及其他加密功能） |  不可用 | ExpressRoute 不存储客户数据。 |
| 传输中加密（例如 ExpressRoute 加密、VNet 中加密，以及 VNet-VNet 加密） | 否 | |
| 加密密钥处理（CMK、BYOK 等）| 不可用 |  |
| 列级加密（Azure 数据服务）| 不可用 | |
| 加密的 API 调用| 是 | 通过[Azure 资源管理器](../azure-resource-manager/index.yml)和 HTTPS。 |

### <a name="network-segmentation"></a>网络分段

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 不可用 |  |
| vNET 注入支持| 不可用 | |
| 网络隔离和防火墙支持| 是 | 每个客户都包含在其自己的路由域中并隧道到其自己的 VNet |
| 强制隧道支持| 不可用 | 通过边界网关协议 (BGP)。 |

### <a name="detection"></a>检测

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | 请参阅[ExpressRoute 监视、指标和警报](../expressroute/expressroute-monitoring-metrics-alerts.md)。|

### <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 身份验证| 是 | 用于 Microsoft 的网关 (GWM) (控制器) 的服务帐户;用于开发和操作的实时 (JIT) 访问。 |
| Authorization|  是 |用于 Microsoft 的网关 (GWM) (控制器) 的服务帐户;用于开发和操作的实时 (JIT) 访问。 |


### <a name="audit-trail"></a>审核线索

| 安全属性 | Yes/No | 说明| 
|---|---|--|
| 控制和管理平面日志记录和审核| 是 |  |
| 数据平面日志记录和审核| 否 |   |

### <a name="configuration-management"></a>配置管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是 | 通过网络资源提供程序 (NRP)。 |


## <a name="key-vaultkey-vaultkey-vault-security-attributesmd"></a>[Key Vault](../key-vault/key-vault-security-attributes.md)

### <a name="preventative"></a>预防

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 静态加密（例如服务器端加密、带客户托管密钥的服务器端加密，以及其他加密功能） | 是 | 加密所有对象。 |
| 传输中加密（例如 ExpressRoute 加密、VNet 中加密，以及 VNet-VNet 加密） | 是 | 所有通信都通过加密的 API 调用进行 |
| 加密密钥处理（CMK、BYOK 等）| 是 | 客户控制其密钥保管库中的所有密钥。 如果指定了硬件安全模块 (HSM) 支持的密钥，FIPS 2 级 HSM 会保护密钥、证书或机密。 |
| 列级加密（Azure 数据服务）| 不可用 |  |
| 加密的 API 调用| 是 | 使用 HTTPS。 |

### <a name="network-segmentation"></a>网络分段

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 是 | 使用虚拟网络 (VNet) 服务终结点。 |
| VNet 注入支持| 否 |  |
| 网络隔离和防火墙支持| 是 | 使用 VNet 防火墙规则。 |
| 强制隧道支持| 否 |  |

### <a name="detection"></a>检测

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | 使用 Log Analytics。 |

### <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 身份验证| 是 | 身份验证通过 Azure Active Directory 来进行。 |
| Authorization| 是 | 使用密钥保管库访问策略。 |

### <a name="audit-trail"></a>审核线索

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 控制/管理平面日志记录和审核| 是 | 使用 Log Analytics。 |
| 数据平面日志记录和审核| 是 | 使用 Log Analytics。 |

### <a name="access-controls"></a>访问控制

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 控制/管理平面访问控制 | 是 | Azure Resource Manager 基于角色的访问控制 (RBAC) |
| 数据平面访问控制（在每个服务级别） | 是 | 密钥保管库访问策略 |

## <a name="load-balancerload-balancerload-balancer-security-attributesmd"></a>[负载均衡器](../load-balancer/load-balancer-security-attributes.md)

### <a name="preventative"></a>预防

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 静态加密（例如服务器端加密、带客户托管密钥的服务器端加密，以及其他加密功能） | 不可用 | |
| 传输中加密（例如 ExpressRoute 加密、VNet 中加密，以及 VNet-VNet 加密）| 不可用 | |
| 加密密钥处理（CMK、BYOK 等）| 不可用 | |
| 列级加密（Azure 数据服务）| 不可用 | |
| 加密的 API 调用| 是 | 通过[Azure 资源管理器](../azure-resource-manager/index.yml)。 |

### <a name="network-segmentation"></a>网络分段

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 不可用 | |
| VNet 注入支持| 不可用 | . |
| 网络隔离和防火墙支持| 不可用 |  |
| 强制隧道支持| 不可用 | |

### <a name="detection"></a>检测

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | [有关公共基本负载均衡器](../load-balancer/load-balancer-monitor-log.md), 请参阅 Azure Monitor 日志。 |

### <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 身份验证| 不可用 |  |
| Authorization| 不可用 |  |

### <a name="audit-trail"></a>审核线索

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 控制和管理平面日志记录和审核| 是 | [有关公共基本负载均衡器](../load-balancer/load-balancer-monitor-log.md), 请参阅 Azure Monitor 日志。 |
| 数据平面日志记录和审核 | 不可用 |  |

### <a name="configuration-management"></a>配置管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 不可用 |  | 

## <a name="service-bus-messagingservice-bus-messagingservice-bus-messaging-security-attributesmd"></a>[服务总线消息传送](../service-bus-messaging/service-bus-messaging-security-attributes.md)

### <a name="preventative"></a>预防

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 静态加密（例如服务器端加密、带客户托管密钥的服务器端加密，以及其他加密功能） |  是, 默认情况下, 服务器端加密为静态。 | 目前尚不支持客户托管密钥和 BYOK。 客户端加密是客户端的责任 |
| 传输中加密（例如 ExpressRoute 加密、VNet 中加密，以及 VNet-VNet 加密） | 是 | 支持标准 HTTPS/TLS 机制。 |
| 加密密钥处理（CMK、BYOK 等）| 否 |   |
| 列级加密（Azure 数据服务）| 不可用 | |
| 加密的 API 调用| 是 | API 调用通过[Azure 资源管理器](../azure-resource-manager/index.yml)和 HTTPS 进行。 |

### <a name="network-segmentation"></a>网络分段

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 是 (仅限高级层) | 仅[服务总线高级层](../service-bus-messaging/service-bus-premium-messaging.md)支持 VNet 服务终结点。 |
| VNet 注入支持| 否 | |
| 网络隔离和防火墙支持| 是 (仅限高级层) |  |
| 强制隧道支持| 否 |  |

### <a name="detection"></a>检测

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | 通过[Azure Monitor 和警报](../service-bus-messaging/service-bus-metrics-azure-monitor.md)支持。 |

### <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 身份验证| 是 | 通过[Azure Active Directory 托管服务标识](../service-bus-messaging/service-bus-managed-service-identity.md)进行管理;请参阅[服务总线身份验证和授权](../service-bus-messaging/service-bus-authentication-and-authorization.md)。|
| Authorization| 是 | 支持通过[RBAC](../service-bus-messaging/service-bus-role-based-access-control.md) (预览版) 和 SAS 令牌进行授权;请参阅[服务总线身份验证和授权](../service-bus-messaging/service-bus-authentication-and-authorization.md)。 |


### <a name="audit-trail"></a>审核线索

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 控制和管理平面日志记录和审核| 是 | 可用的操作日志;请参阅[服务总线诊断日志](../service-bus-messaging/service-bus-diagnostic-logs.md)。  |
| 数据平面日志记录和审核| 否 |  |

### <a name="configuration-management"></a>配置管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是 | 支持通过[Azure 资源管理器 API](/rest/api/resources/)进行资源提供程序版本控制。|


## <a name="service-bus-relayservice-bus-relayservice-bus-relay-security-attributesmd"></a>[服务总线中继](../service-bus-relay/service-bus-relay-security-attributes.md)

### <a name="preventative"></a>预防

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 静态加密（例如服务器端加密、带客户托管密钥的服务器端加密，以及其他加密功能） |  不可用 | 中继是一个 Web 套接字，不保存数据。 |
| 传输中加密（例如 ExpressRoute 加密、VNet 中加密，以及 VNet-VNet 加密） | 是 | 服务需要 TLS。 |
| 加密密钥处理（CMK、BYOK 等）| 否 | 仅使用 Microsoft TLS 证书。  |
| 列级加密（Azure 数据服务）| 不可用 | |
| 加密的 API 调用| 是 | HTTPS。 |

### <a name="network-segmentation"></a>网络分段

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 否 |  |
| 网络隔离和防火墙支持| 否 |  |
| 强制隧道支持| 不可用 | 中继是 TLS 隧道  |

### <a name="detection"></a>检测

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | |

### <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 身份验证| 是 | 通过 SAS。 |
| Authorization|  是 | 通过 SAS。 |

### <a name="audit-trail"></a>审核线索

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 控制和管理平面日志记录和审核| 是 | 通过 [Azure 资源管理器](../azure-resource-manager/index.yml)。 |
| 数据平面日志记录和审核| 是 | 连接成功/失败、错误和记录的内容。  |

### <a name="configuration-management"></a>配置管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是 | 通过 [Azure 资源管理器](../azure-resource-manager/index.yml)。|

## <a name="service-fabricservice-fabricservice-fabric-security-attributesmd"></a>[Service Fabric](../service-fabric/service-fabric-security-attributes.md)

### <a name="preventative"></a>预防

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 静态加密（例如服务器端加密、带客户托管密钥的服务器端加密，以及其他加密功能） | 是 | 客户拥有群集以及作为群集构建基础的虚拟机规模集。 可以在虚拟机规模集上启用 Azure 磁盘加密。 |
| 传输中加密（例如 ExpressRoute 加密、VNet 中加密，以及 VNet-VNet 加密） | 是 | 客户拥有群集以及作为群集构建基础的虚拟机规模集。 可以在虚拟机规模集上启用 Azure 磁盘加密。 |
| 列级加密（Azure 数据服务）| 不可用 |  |
| 加密的 API 调用| 是 | Service Fabric API 调用通过 Azure 资源管理器进行。 需要有效 JSON web 令牌 (JWT)。 |

### <a name="network-segmentation"></a>网络分段

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 是 |  |
| VNet 注入支持| 是 |  |
| 网络隔离和防火墙支持| 是 | 使用网络安全组 (NSG)。 |
| 强制隧道支持| 是 | Azure 网络支持强制隧道。 |

### <a name="detection"></a>检测

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | 使用 Azure 监视支持和第三方支持。 |

### <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 身份验证| 是 | 身份验证通过 Azure Active Directory 来进行。 |
| Authorization| 是 | 通过 SFRP 进行针对调用的标识和访问管理 (IAM)。 直接对群集终结点进行的调用支持两个角色：用户和管理员。客户可以将 API 映射到任一角色。 |

### <a name="audit-trail"></a>审核线索

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 控制和管理平面日志记录和审核| 是 | 所有控制平面操作都需经过审核和审批流程。 |
| 数据平面日志记录和审核| 不可用 | 客户拥有群集。  |

### <a name="configuration-management"></a>配置管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是 | |

## <a name="sql-databasesql-databasesql-database-security-attributesmd"></a>[SQL 数据库](../sql-database/sql-database-security-attributes.md)

SQL 数据库同时包含[单一数据库](../sql-database/sql-database-single-index.yml)和[托管实例](../sql-database/sql-database-managed-instance.md)。 以下条目适用于这两个产品/服务, 但在其他情况下除外。

### <a name="preventative"></a>预防

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 静态加密（例如服务器端加密、带客户托管密钥的服务器端加密，以及其他加密功能） | 是 | 称为 "正在使用的加密", 如[Always Encrypted](../sql-database/sql-database-always-encrypted.md)中所述。 服务器端加密使用[透明数据加密](../sql-database/transparent-data-encryption-azure-sql.md)。|
| 传输中加密（例如 ExpressRoute 加密、VNet 中加密，以及 VNet-VNet 加密） | 是 | 使用 HTTPS。 |
| 加密密钥处理, 如 CMK 或 BYOK| 是 | 提供服务管理的和客户管理的密钥处理。 后者通过[Azure Key Vault](../key-vault/index.yml)提供。 |
| Azure 数据服务提供的列级加密| 是 | 通过 [Always Encrypted](../sql-database/sql-database-always-encrypted.md) 进行。 |
| 加密的 API 调用| 是 | 使用 HTTPS/SSL。 |

### <a name="network-segmentation"></a>网络分段

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 是 | 仅适用于[单一数据库](../sql-database/sql-database-single-index.yml)。 |
| Azure 虚拟网络注入支持| 是 | 仅适用于[托管实例](../sql-database/sql-database-managed-instance.md)。 |
| 网络隔离和防火墙支持| 是 | 数据库级别和服务器级别的防火墙。 网络隔离仅适用于[托管实例](../sql-database/sql-database-managed-instance.md)。 |
| 强制隧道支持| 是 | [托管实例](../sql-database/sql-database-managed-instance.md)通过[ExpressRoute](../expressroute/index.yml) VPN。 |

### <a name="detection"></a>检测

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持, 如 Log Analytics 或 Application Insights| 是 | SecureSphere 是 Imperva 的 SIEM 解决方案, 也通过[SQL 审核](../sql-database/sql-database-auditing.md)通过[Azure 事件中心](../event-hubs/index.yml)集成来支持。 |

### <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 身份验证| 是 | Azure Active Directory (Azure AD) |
| Authorization| 是 | 无 |

### <a name="audit-trail"></a>审核线索

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 控制平面和管理平面日志记录和审核| 是 | 对于某些事件是唯一的 |
| 数据平面日志记录和审核 | 是 | 通过[SQL 审核](../sql-database/sql-database-auditing.md) |

### <a name="configuration-management"></a>配置管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持, 例如配置的版本控制| 否  | 无 |

### <a name="additional-security-attributes-for-sql-database"></a>SQL 数据库的其他安全属性

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 预防：漏洞评估 | 是 | 请参阅 [SQL 漏洞评估服务可帮助你识别数据库漏洞](../sql-database/sql-vulnerability-assessment.md)。 |
| 预防：数据发现和分类  | 是 | 请参阅 [Azure SQL 数据库和 SQL 数据仓库数据发现和分类](../sql-database/sql-database-data-discovery-and-classification.md)。 |
| 检测：威胁检测 | 是 | 请参阅 [Azure SQL 数据库的高级威胁防护](../sql-database/sql-database-threat-detection-overview.md)。 |


## <a name="storagestoragecommonstorage-security-attributesmd"></a>[存储](../storage/common/storage-security-attributes.md)

### <a name="preventative"></a>预防

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 静态加密（例如服务器端加密、带客户托管密钥的服务器端加密，以及其他加密功能） | 是 |  |
| 传输中加密（例如 ExpressRoute 加密、VNet 中加密，以及 VNet-VNet 加密） | 是 | 支持标准的 HTTPS/TLS 机制。  用户也可以先加密数据，然后再将其传输到服务。 |
| 加密密钥处理（CMK、BYOK 等）| 是 | 请参阅[在 Azure Key Vault 中使用客户托管密钥进行存储服务加密](../storage/common/storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。|
| 列级加密（Azure 数据服务）| 不可用 |  |
| 加密的 API 调用| 是 |  |

### <a name="network-segmentation"></a>网络分段

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 是 |  |
| VNet 注入支持| 不可用 |  |
| 网络隔离和防火墙支持| 是 | |
| 强制隧道支持| 不可用 |  |

### <a name="detection"></a>检测

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | Azure Monitor 指标现已发布，日志开始推出预览版 |

### <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 身份验证| 是 | Azure Active Directory、共享密钥、共享访问令牌。 |
| Authorization| 是 | 支持通过 RBAC、POSIX ACL 和 SAS 令牌进行授权 |

### <a name="audit-trail"></a>审核线索

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 控制和管理平面日志记录和审核 | 是 | Azure 资源管理器活动日志 |
| 数据平面日志记录和审核| 是 | 服务诊断日志，Azure Monitor 日志记录开始推出预览版  |

### <a name="configuration-management"></a>配置管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是 | 支持通过 Azure 资源管理器 API 进行资源提供程序版本控制 |

## <a name="virtual-machines-and-virtual-machine-scale-sets"></a>虚拟机和虚拟机规模集

[Linux vm](../virtual-machines/windows/virtual-machines-windows-security-attributes.md) | [Windows vm](../virtual-machines/windows/virtual-machines-windows-security-attributes.md) | [虚拟机规模集](../virtual-machine-scale-sets/virtual-machine-scale-sets-security-attributes.md)


### <a name="preventative"></a>预防

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 静态加密（例如服务器端加密、带客户托管密钥的服务器端加密，以及其他加密功能） | 是 | 请参阅[如何在 Azure 中加密 Linux 虚拟机](/azure/virtual-machines/linux/encrypt-disks)和[加密 Windows VM 上的虚拟磁盘](/azure/virtual-machines/windows/encrypt-disks)。 |
| 传输中加密（例如 ExpressRoute 加密、VNet 中加密，以及 VNet-VNet 加密）| 是 | Azure 虚拟机支持[ExpressRoute](/azure/expressroute)和 VNET 加密。 请参阅[Vm 中的传输中加密](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)。 |
| 加密密钥处理（CMK、BYOK 等）| 是 | 客户托管的密钥是受支持的 Azure 加密方案;请参阅[Azure 加密概述](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)。|
| 列级加密（Azure 数据服务）| 不可用 | |
| 加密的 API 调用| 是 | 通过 HTTPS 和 SSL。 |

### <a name="network-segmentation"></a>网络分段

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 是 | |
| VNet 注入支持| 是 | . |
| 网络隔离和防火墙支持| 是 |  |
| 强制隧道支持| 是 | 请参阅[使用 Azure 资源管理器部署模型配置强制隧道](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm)。 |

### <a name="detection"></a>检测

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | 请参阅[在 azure 中监视和更新 Linux 虚拟机](/azure/virtual-machines/linux/tutorial-monitoring)和[在 azure 中监视和更新 Windows 虚拟机](/azure/virtual-machines/windows/tutorial-monitoring)。 |

### <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 身份验证| 是 |  |
| Authorization| 是 |  |


### <a name="audit-trail"></a>审核线索

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 控制和管理平面日志记录和审核| 是 |  |
| 数据平面日志记录和审核 | 否 |  |

### <a name="configuration-management"></a>配置管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是 |  | 


## <a name="vpn-gatewayvpn-gatewayvpn-gateway-security-attributesmd"></a>[VPN 网关](../vpn-gateway/vpn-gateway-security-attributes.md)

### <a name="preventative"></a>预防

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 静态加密（例如服务器端加密、带客户托管密钥的服务器端加密，以及其他加密功能） | 不可用 | VPN 网关传输客户数据, 不存储客户数据 |
| 传输中加密（例如 ExpressRoute 加密、VNet 中加密，以及 VNet-VNet 加密）| 是 | VPN 网关对 Azure VPN 网关与客户本地 VPN 设备 (S2S) 或 VPN 客户端 (P2S) 之间的客户数据包进行加密。 VPN 网关还支持 VNet 到 VNet 加密。 |
| 加密密钥处理（CMK、BYOK 等）| 否 | 静态加密客户指定的预共享密钥;但尚未与 CMK 集成。 |
| 列级加密（Azure 数据服务）| 不可用 | |
| 加密的 API 调用| 是 | 通过[Azure 资源管理器](../azure-resource-manager/index.yml)和 HTTPS  |

### <a name="network-segmentation"></a>网络分段

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 不可用 | |
| VNet 注入支持| 不可用 | . |
| 网络隔离和防火墙支持| 是 | VPN 网关是每个客户虚拟网络的专用 VM 实例  |
| 强制隧道支持| 是 |  |

### <a name="detection"></a>检测

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | 请参阅[Azure Monitor 诊断日志/警报](../vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Azure Monitor 指标/警报](../vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)。  |

### <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 身份验证| 是 | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) , 用于管理服务和配置 Azure VPN 网关。 |
| Authorization| 是 | 支持通过[RBAC](../role-based-access-control/overview.md)的授权。 |

### <a name="audit-trail"></a>审核线索

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 控制和管理平面日志记录和审核| 是 | Azure 资源管理器活动日志。 |
| 数据平面日志记录和审核 | 是 | Azure Monitor VPN 连接日志记录和审核的[诊断日志](../azure-resource-manager/resource-group-audit.md)。 |

### <a name="configuration-management"></a>配置管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是 | 对于管理操作, 可以将 Azure VPN 网关配置的状态导出为 Azure 资源管理器模板, 并随时间进行版本控制。 | 

