---
title: Azure 服务的安全属性
description: 用于评估 Azure Service Fabric 的常见安全属性的清单
services: security
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: security
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 64accb70561d4c0282b3ee45935d955dba1c67c4
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66474535"
---
# <a name="security-attributes-for-azure-services"></a>Azure 服务的安全属性

这篇文章收集所选的 Azure 服务的常见安全属性。 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="azure-api-managementapi-managementapi-management-security-attributesmd"></a>[Azure API 管理](../api-management/api-management-security-attributes.md)

### <a name="preventative"></a>预防

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 静态加密：<ul><li>服务器端加密</li><li>使用客户托管密钥的服务器端加密</li><li>其他加密功能（例如客户端、始终加密等）</ul>| 是（仅服务端加密） | 敏感数据（例如证书、密钥以及使用机密命名的值）使用服务托管的、基于服务实例的密钥进行加密。 |
| 传输中加密：<ul><li>快速路由加密</li><li>VNet 中加密</li><li>VNet-VNet 加密</ul>| 是 | [快速路由](../expressroute/index.yml)和 VNet 加密由 [Azure 网络](../virtual-network/index.yml)提供。 |
| 加密密钥处理（CMK、BYOK 等）| 否 | 所有加密密钥都是基于服务实例的，也是通过服务托管的。 |
| 列级加密（Azure 数据服务）| 不适用 | |
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
| Authentication| 是 | |
| 授权| 是 | |


### <a name="audit-trail"></a>审核线索

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 控制和管理平面日志记录和审核| 是 | [Azure Monitor 活动日志](../azure-monitor/platform/activity-logs-overview.md) |
| 数据平面日志记录和审核| 是 | [Azure Monitor 诊断日志](../azure-monitor/platform/diagnostic-logs-overview.md)和 （可选） [Azure Application Insights](../azure-monitor/app/app-insights-overview.md)。  |

### <a name="configuration-management"></a>配置管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是 | 使用 [Azure API 管理 DevOps 资源工具包](https://aka.ms/apimdevops) |


## <a name="azure-app-serviceapp-serviceapp-service-security-attributesmd"></a>[Azure 应用服务](../app-service/app-service-security-attributes.md)

### <a name="preventative"></a>预防

| 安全特性 | Yes/No | 说明 |
|---|---|--|
| （例如服务器端加密、 使用客户托管密钥的服务器端加密和其他加密功能） 的静态加密 | 是 | 网站文件内容存储在 Azure 存储空间，它会自动加密静态内容。 请参阅[静态数据的 Azure 存储加密](../storage/common/storage-service-encryption.md)。<br><br>提供的客户机密进行静态加密。 静态存储在应用服务配置数据库中时加密机密。<br><br>（可选），本地附加的磁盘可以用作临时存储的网站 （D:\local 和 %TMP%)。 本地附加的磁盘不进行静态加密。 |
| （例如 VNet 加密和 VNet 加密中的 ExpressRoute 加密） 的传输中加密| 是 | 客户可以配置为要求和为入站流量使用 HTTPS 的网站。 请参阅博客文章[如何使 Azure 应用服务仅 HTTPS](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/)。 |
| 加密密钥处理（CMK、BYOK 等）| 是 | 客户可以选择在 Key Vault 中存储应用程序机密，并在运行时检索它们。 请参阅[使用密钥保管库引用应用服务和 Azure Functions （预览版）](../app-service/app-service-key-vault-references.md)。|
| 列级加密（Azure 数据服务）| 不适用 | |
| 加密的 API 调用| 是 | 通过进行管理调用来配置应用服务[Azure 资源管理器](../azure-resource-manager/index.yml)通过 HTTPS 调用。 |

### <a name="network-segmentation"></a>网络分段

| 安全特性 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 是 | 当前提供预览版，应用服务。 请参阅[Azure 应用服务访问限制](../app-service/app-service-ip-restrictions.md)。 |
| VNet 注入支持| 是 | 应用服务环境是应用服务专用于单个客户注入到客户的虚拟网络的专用实现。 请参阅[应用服务环境简介](../app-service/environment/intro.md)。 |
| 网络隔离和 Firewalling 支持| 是 | 对于应用服务的公共多租户变体，客户可以配置网络 Acl （IP 限制），以允许入站流量锁定。  请参阅[Azure 应用服务访问限制](../app-service/app-service-ip-restrictions.md)。  应用服务环境部署直接到虚拟网络，并因此可以使用 Nsg 保护。 |
| 强制隧道支持| 是 | 可以将应用服务环境部署到客户的虚拟网络，配置强制隧道。 客户需要按照中的说明[使用强制隧道配置应用服务环境](../app-service/environment/forced-tunnel-support.md)。 |

### <a name="detection"></a>检测

| 安全特性 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | 应用服务与 Application Insights 集成支持 Application Insights （完整的.NET Framework、.NET Core、 Java 和 Node.JS） 的语言。  请参阅[监视 Azure 应用服务性能](../azure-monitor/app/azure-web-apps.md)。 应用服务还会应用程序指标发送到 Azure Monitor。 请参阅[在 Azure 应用服务中监视应用](../app-service/web-sites-monitor.md)。 |

### <a name="identity-and-access-management"></a>标识和访问管理

| 安全特性 | Yes/No | 说明|
|---|---|--|
| Authentication| 是 | 客户可以生成自动将与集成的应用服务上的应用程序[Azure Active Directory (Azure AD)](../active-directory/index.yml)以及其他 OAuth 兼容的标识提供程序; 请参阅[身份验证和授权Azure 应用服务](../app-service/overview-authentication-authorization.md)。 管理应用服务资产的访问，所有访问都受 Azure AD 进行身份验证主体和 Azure 资源管理器 RBAC 角色的组合。 |
| 授权| 是 | 管理应用服务资产的访问，所有访问都受 Azure AD 进行身份验证主体和 Azure 资源管理器 RBAC 角色的组合。  |


### <a name="audit-trail"></a>审核线索

| 安全特性 | Yes/No | 说明|
|---|---|--|
| 控制和管理平面日志记录和审核| 是 | 通过应用服务的对象上执行的所有管理操作都发生[Azure 资源管理器](../azure-resource-manager/index.yml)。 这些操作的历史日志可在门户中，通过 CLI;请参阅[Azure 资源管理器资源提供程序操作](../role-based-access-control/resource-provider-operations.md#microsoftweb)并[az monitor 活动日志](/cli/azure/monitor/activity-log)。 |
| 数据平面日志记录和审核 | 否 | 应用服务的数据平面是包含客户部署 web 站点内容的远程文件共享。  没有远程文件共享的任何审核。 |

### <a name="configuration-management"></a>配置管理

| 安全特性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是 | 对于管理操作，应用服务配置的状态可以导出为 Azure 资源管理器模板和版本控制随着时间的推移。 对于运行时操作，客户可以维护应用程序使用应用服务部署槽功能的多个不同的实时版本。 | 


## <a name="azure-resource-managerazure-resource-managerazure-resource-manager-security-attributesmd"></a>[Azure 资源管理器](../azure-resource-manager/azure-resource-manager-security-attributes.md)

### <a name="preventative"></a>预防

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 静态加密：<ul><li>服务器端加密</li><li>使用客户托管密钥的服务器端加密</li><li>其他加密功能（例如客户端、始终加密等）</ul>| 是 |  |
| 传输中加密：<ul><li>快速路由加密</li><li>VNet 中加密</li><li>VNet-VNet 加密</ul>| 是 | HTTPS/TLS。 |
| 加密密钥处理（CMK、BYOK 等）| 不适用 | Azure 资源管理器不存储客户内容，仅存储控制数据。 |
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
| Authentication| 是 | 基于 [Azure Active Directory](/azure/active-directory)。|
| 授权| 是 | |


### <a name="audit-trail"></a>审核线索

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 控制和管理平面日志记录和审核| 是 | 活动日志公开对资源执行的所有写入操作（PUT、POST、DELETE）；请参阅[查看活动日志来审核对资源的操作](../azure-resource-manager/resource-group-audit.md)。 |
| 数据平面日志记录和审核| 不适用 | |

### <a name="configuration-management"></a>配置管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是 |  |


## <a name="azure-backupbackupbackup-security-attributesmd"></a>[Azure 备份](../backup/backup-security-attributes.md)

### <a name="preventative"></a>预防

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 静态加密：<ul><li>服务器端加密</li><li>使用客户托管密钥的服务器端加密</li><li>其他加密功能（例如客户端、始终加密等）</ul>| 是 | 对存储帐户使用存储服务加密。 |
| 传输中加密：<ul><li>快速路由加密</li><li>VNet 中加密</li><li>VNet-VNet 加密</ul>| 否 | 使用 HTTPS。 |
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
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | 通过诊断日志支持 Log Analytics。 有关详细信息，请参阅“使用 Log Analytics 监视 Azure 备份保护的工作负荷”(https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/)。 |

### <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Authentication| 是 | 身份验证通过 Azure Active Directory 来进行。 |
| 授权| 是 | 使用客户创建和内置的 RBAC 角色。 请参阅使用基于角色的访问控制管理 Azure 备份恢复点 （/azure/backup/备份-rbac-rs-保管库） 有关详细信息。 |


### <a name="audit-trail"></a>审核线索

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 控制和管理平面日志记录和审核| 是 | 来自 Azure 门户的所有客户触发操作都会记录到活动日志中。 |
| 数据平面日志记录和审核| 否 | 无法直接访问 Azure 备份数据平面。  |

### <a name="configuration-management"></a>配置管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是|  |


## <a name="azure-key-vaultkey-vaultkey-vault-security-attributesmd"></a>[Azure Key Vault](../key-vault/key-vault-security-attributes.md)

### <a name="preventative"></a>预防

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 静态加密：<ul><li>服务器端加密</li><li>使用客户托管密钥的服务器端加密</li><li>其他加密功能（例如客户端、始终加密等）</ul>| 是 | 加密所有对象。 |
| 传输中加密：<ul><li>快速路由加密</li><li>VNet 中加密</li><li>VNet-VNet 加密</ul>| 是 | 所有通信都通过加密的 API 调用进行 |
| 加密密钥处理（CMK、BYOK 等）| 是 | 客户控制其密钥保管库中的所有密钥。 如果指定了硬件安全模块 (HSM) 支持的密钥，FIPS 2 级 HSM 会保护密钥、证书或机密。 |
| 列级加密（Azure 数据服务）| 不适用 |  |
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
| Authentication| 是 | 身份验证通过 Azure Active Directory 来进行。 |
| 授权| 是 | 使用密钥保管库访问策略。 |


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

## <a name="azure-service-bus-messagingservice-bus-messagingservice-bus-messaging-security-attributesmd"></a>[Azure 服务总线消息传送](../service-bus-messaging/service-bus-messaging-security-attributes.md)

### <a name="preventative"></a>预防

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 静态加密：<ul><li>服务器端加密</li><li>使用客户托管密钥的服务器端加密</li><li>其他加密功能（例如客户端、始终加密等）</ul>|  默认情况下，是用于服务器端静态加密。 | 尚不支持客户托管的密钥和 BYOK。 客户端加密是客户端的责任 |
| 传输中加密：<ul><li>快速路由加密</li><li>VNet 中加密</li><li>VNet-VNet 加密</ul>| 是 | 支持标准的 HTTPS/TLS 机制。 |
| 加密密钥处理（CMK、BYOK 等）| 否 |   |
| 列级加密（Azure 数据服务）| 不适用 | |
| 加密的 API 调用| 是 | API 调用都是通过[Azure 资源管理器](../azure-resource-manager/index.yml)和 HTTPS。 |

### <a name="network-segmentation"></a>网络分段

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 是 （仅适用于高级层） | 支持 VNet 服务终结点[服务总线高级层](../service-bus-messaging/service-bus-premium-messaging.md)仅。 |
| VNet 注入支持| 否 | |
| 网络隔离和防火墙支持| 是 （仅适用于高级层） |  |
| 强制隧道支持| 否 |  |

### <a name="detection"></a>检测

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | 支持通过[Azure Monitor 和警报](../service-bus-messaging/service-bus-metrics-azure-monitor.md)。 |

### <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Authentication| 是 | 通过管理[Azure Active Directory 托管服务标识](../service-bus-messaging/service-bus-managed-service-identity.md); 请参阅[服务总线身份验证和授权](../service-bus-messaging/service-bus-authentication-and-authorization.md)。|
| 授权| 是 | 支持通过授权[RBAC](../service-bus-messaging/service-bus-role-based-access-control.md) （预览版） 和 SAS 令牌; 请参阅[服务总线身份验证和授权](../service-bus-messaging/service-bus-authentication-and-authorization.md)。 |

### <a name="audit-trail"></a>审核线索

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 控制和管理平面日志记录和审核| 是 | 操作日志才会有效。请参阅[服务总线诊断日志](../service-bus-messaging/service-bus-diagnostic-logs.md)。  |
| 数据平面日志记录和审核| 否 |  |

### <a name="configuration-management"></a>配置管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是 | 支持通过资源提供程序版本控制[Azure 资源管理器 API](/rest/api/resources/)。|


## <a name="azure-service-bus-relayservice-bus-relayservice-bus-relay-security-attributesmd"></a>[Azure 服务总线中继](../service-bus-relay/service-bus-relay-security-attributes.md)

### <a name="preventative"></a>预防

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 静态加密：<ul><li>服务器端加密</li><li>使用客户托管密钥的服务器端加密</li><li>其他加密功能（例如客户端、始终加密等）</ul>|  不适用 | 中继是一个 Web 套接字，不保存数据。 |
| 传输中加密：<ul><li>快速路由加密</li><li>VNet 中加密</li><li>VNet-VNet 加密</ul>| 是 | 服务需要 TLS。 |
| 加密密钥处理（CMK、BYOK 等）| 否 | 仅使用 Microsoft TLS 证书。  |
| 列级加密（Azure 数据服务）| 不适用 | |
| 加密的 API 调用| 是 | HTTPS。 |

### <a name="network-segmentation"></a>网络分段

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 否 |  |
| 网络隔离和防火墙支持| 否 |  |
| 强制隧道支持| 不适用 | 中继是 TLS 隧道  |

### <a name="detection"></a>检测

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | |

### <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Authentication| 是 | 通过 SAS。 |
| 授权|  是 | 通过 SAS。 |


### <a name="audit-trail"></a>审核线索

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 控制和管理平面日志记录和审核| 是 | 通过 [Azure 资源管理器](../azure-resource-manager/index.yml)。 |
| 数据平面日志记录和审核| 是 | 连接成功/失败、错误和记录的内容。  |

### <a name="configuration-management"></a>配置管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是 | 通过 [Azure 资源管理器](../azure-resource-manager/index.yml)。|


## <a name="azure-service-fabricservice-fabricservice-fabric-security-attributesmd"></a>[Azure Service Fabric](../service-fabric/service-fabric-security-attributes.md)

### <a name="preventative"></a>预防

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 静态加密：<ul><li>服务器端加密</li><li>使用客户托管密钥的服务器端加密</li><li>其他加密功能（例如客户端、始终加密等）</ul>| 是 | 客户拥有群集以及作为群集构建基础的虚拟机 (VM) 规模集。 可以在虚拟机规模集上启用 Azure 磁盘加密。 |
| 传输中加密：<ul><li>快速路由加密</li><li>VNet 中加密</li><li>VNet-VNet 加密</ul>| 是 |  |
| 加密密钥处理（CMK、BYOK 等）| 是 | 客户拥有群集以及作为群集构建基础的虚拟机 (VM) 规模集。 可以在虚拟机规模集上启用 Azure 磁盘加密。 |
| 列级加密（Azure 数据服务）| 不适用 |  |
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
| Authentication| 是 | 身份验证通过 Azure Active Directory 来进行。 |
| 授权| 是 | 通过 SFRP 进行针对调用的标识和访问管理 (IAM)。 直接对群集终结点进行的调用支持两个角色：用户和管理员。客户可以将 API 映射到任一角色。 |

### <a name="audit-trail"></a>审核线索

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 控制和管理平面日志记录和审核| 是 | 所有控制平面操作都需经过审核和审批流程。 |
| 数据平面日志记录和审核| 不适用 | 客户拥有群集。  |

### <a name="configuration-management"></a>配置管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是 | 服务配置使用 Azure 部署进行版本控制和部署。 代码（应用程序和运行时）使用 Azure 生成进行版本控制。
 |

## <a name="azure-sql-databasesql-databasesql-database-security-attributesmd"></a>[Azure SQL 数据库](../sql-database/sql-database-security-attributes.md)

### <a name="preventative"></a>预防

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 静态加密：<ul><li>服务器端加密</li><li>使用客户托管密钥的服务器端加密</li><li>其他加密功能（例如客户端、始终加密等）</ul>| 是 | 称为“使用中加密”，详见 [Always Encrypted](../sql-database/sql-database-always-encrypted.md) 一文中的说明。 服务端加密使用[透明数据加密](../sql-database/transparent-data-encryption-azure-sql.md) (TDE)。|
| 传输中加密：<ul><li>ExpressRoute 加密</li><li>VNet 中加密</li><li>VNet-VNet 加密</ul>| 是 | 使用 HTTPS。 |
| 加密密钥处理（CMK、BYOK 等）| 是 | 提供服务托管和客户托管密钥处理（后者通过 [Azure Key Vault](../key-vault/index.yml) 进行）。 |
| 列级加密（Azure 数据服务）| 是 | 通过 [Always Encrypted](../sql-database/sql-database-always-encrypted.md) 进行。 |
| 加密的 API 调用| 是 | 使用 HTTPS/SSL。 |

### <a name="network-segmentation"></a>网络分段

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 是 | 仅适用于[单一数据库](../sql-database/sql-database-single-index.yml)。 |
| VNet 注入支持| 是 | 适用于[托管的实例](../sql-database/sql-database-managed-instance.md)仅。 |
| 网络隔离和防火墙支持| 是 | 防火墙在这两个数据库和服务器级别;网络隔离，适用[托管的实例](../sql-database/sql-database-managed-instance.md)仅 |
| 强制隧道支持| 是 | [托管的实例](../sql-database/sql-database-managed-instance.md)通过[Azure ExpressRoute](../expressroute/index.yml) VPN |

### <a name="detection"></a>检测

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | 来自 Imperva (SecureSphere) 的第三方 SIEM 解决方案还支持，则通过[Azure 事件中心](../event-hubs/index.yml)通过集成[SQL 审核](../sql-database/sql-database-auditing.md)。 |

### <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Authentication| 是 | Azure Active Directory。 |
| 授权| 是 |  |


### <a name="audit-trail"></a>审核线索

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 控制和管理平面日志记录和审核| 是 | 仅对某些事件为是。 |
| 数据平面日志记录和审核 | 是 | 通过 [SQL 审核](../sql-database/sql-database-auditing.md)。 |

### <a name="configuration-management"></a>配置管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 否  | | 

### <a name="additional-security-attributes-for-sql-database"></a>SQL 数据库的其他安全属性

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 预防：漏洞评估 | 是 | 请参阅 [SQL 漏洞评估服务可帮助你识别数据库漏洞](../sql-database/sql-vulnerability-assessment.md)。 |
| 预防：数据发现和分类  | 是 | 请参阅 [Azure SQL 数据库和 SQL 数据仓库数据发现和分类](../sql-database/sql-database-data-discovery-and-classification.md)。 |
| 检测：威胁检测 | 是 | 请参阅 [Azure SQL 数据库的高级威胁防护](../sql-database/sql-database-threat-detection-overview.md)。 |

## <a name="azure-storagestoragecommonstorage-security-attributesmd"></a>[Azure 存储](../storage/common/storage-security-attributes.md)

### <a name="preventative"></a>预防

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 静态加密：<ul><li>服务器端加密</li><li>使用客户托管密钥的服务器端加密</li><li>其他加密功能（例如客户端、始终加密等）</ul>| 是 |  |
| 传输中加密：<ul><li>快速路由加密</li><li>VNet 中加密</li><li>VNet-VNet 加密</ul>| 是 | 支持标准的 HTTPS/TLS 机制。  用户也可以先加密数据，然后再将其传输到服务。 |
| 加密密钥处理（CMK、BYOK 等）| 是 | 请参阅[在 Azure Key Vault 中使用客户托管密钥进行存储服务加密](../storage/common/storage-service-encryption-customer-managed-keys.md)。|
| 列级加密（Azure 数据服务）| 不适用 |  |
| 加密的 API 调用| 是 |  |

### <a name="network-segmentation"></a>网络分段

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 是 |  |
| VNet 注入支持| 不适用 |  |
| 网络隔离和防火墙支持| 是 | |
| 强制隧道支持| 不适用 |  |

### <a name="detection"></a>检测

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | Azure Monitor 指标现已发布，日志开始推出预览版 |

### <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Authentication| 是 | Azure Active Directory、共享密钥、共享访问令牌。 |
| 授权| 是 | 支持通过 RBAC、POSIX ACL 和 SAS 令牌进行授权 |


### <a name="audit-trail"></a>审核线索

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 控制和管理平面日志记录和审核 | 是 | Azure 资源管理器活动日志 |
| 数据平面日志记录和审核| 是 | 服务诊断日志，Azure Monitor 日志记录开始推出预览版  |

### <a name="configuration-management"></a>配置管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是 | 支持通过 Azure 资源管理器 API 进行资源提供程序版本控制 |