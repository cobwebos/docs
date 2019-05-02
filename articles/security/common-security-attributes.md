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
ms.openlocfilehash: 94681351758f34cc53c425f3207660bdb09f6494
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717798"
---
# <a name="common-security-attributes-for-azure-services"></a>常见的 Azure 服务的安全属性

安全性已集成到 Azure 服务的各个方面。 这篇文章收集所选的 Azure 服务的常见安全属性。 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]


## <a name="azure-backupbackupbackup-security-attributesmd"></a>[Azure 备份](../backup/backup-security-attributes.md)

### <a name="preventative"></a>预防

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 静态加密：<ul><li>服务器端加密</li><li>使用客户托管密钥的服务器端加密</li><li>其他加密功能（例如客户端、始终加密等）</ul>| 是 | 对存储帐户使用存储服务加密。 |
| 传输中加密：<ul><li>快速路由加密</li><li>在 VNet 加密</li><li>VNet-VNet 加密</ul>| 否 | 使用 HTTPS。 |
| 加密密钥处理 （CMK，BYOK，等等。）| 否 |  |
| 列级加密 （Azure 数据服务）| 否 |  |
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
| Azure 监视支持 （日志分析、 应用程序见解等）| 是 | 通过诊断日志支持 Log Analytics。 有关详细信息，请参阅“使用 Log Analytics 监视 Azure 备份保护的工作负荷”(https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/)。 |

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

| 安全特性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持 （版本控制的配置，等等）。| 是|  |

## <a name="azure-key-vaultkey-vaultkey-vault-security-attributesmd"></a>[Azure Key Vault](../key-vault/key-vault-security-attributes.md)

### <a name="preventative"></a>预防

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 静态加密：<ul><li>服务器端加密</li><li>使用客户托管密钥的服务器端加密</li><li>其他加密功能（例如客户端、始终加密等）</ul>| 是 | 加密所有对象。 |
| 传输中加密：<ul><li>快速路由加密</li><li>在 VNet 加密</li><li>VNet-VNet 加密</ul>| 是 | 所有通信都通过加密的 API 调用进行 |
| 加密密钥处理 （CMK，BYOK，等等。）| 是 | 客户控制其密钥保管库中的所有密钥。 如果指定了硬件安全模块 (HSM) 支持的密钥，FIPS 级别 2 HSM 保护密钥、 证书或密钥。 |
| 列级加密 （Azure 数据服务）| 不适用 |  |
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
| Azure 监视支持 （日志分析、 应用程序见解等）| 是 | 使用 Log Analytics。 |

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

## <a name="azure-service-fabricservice-fabricservice-fabric-security-attributesmd"></a>[Azure Service Fabric](../service-fabric/service-fabric-security-attributes.md)

### <a name="preventative"></a>预防

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 静态加密：<ul><li>服务器端加密</li><li>使用客户托管密钥的服务器端加密</li><li>其他加密功能（例如客户端、始终加密等）</ul>| 是 | 客户拥有群集以及作为群集构建基础的虚拟机 (VM) 规模集。 可以在虚拟机规模集上启用 azure 磁盘加密。 |
| 传输中加密：<ul><li>快速路由加密</li><li>在 VNet 加密</li><li>VNet-VNet 加密</ul>| 是 |  |
| 加密密钥处理 （CMK，BYOK，等等。）| 是 | 客户拥有群集以及作为群集构建基础的虚拟机 (VM) 规模集。 可以在虚拟机规模集上启用 azure 磁盘加密。 |
| 列级加密 （Azure 数据服务）| 不适用 |  |
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
| Azure 监视支持 （日志分析、 应用程序见解等）| 是 | 使用 Azure 监视支持和第三方支持。 |

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

| 安全特性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持 （版本控制的配置，等等）。| 是 | 服务配置使用 Azure 部署进行版本控制和部署。 代码（应用程序和运行时）使用 Azure 生成进行版本控制。
 |

## <a name="azure-storage"></a>Azure 存储

### <a name="preventative"></a>预防

| 安全属性 | Yes/No | 说明 |
|---|---|--|
| 静态加密：<ul><li>服务器端加密</li><li>使用客户托管密钥的服务器端加密</li><li>其他加密功能（例如客户端、始终加密等）</ul>| 是 |  |
| 传输中加密：<ul><li>快速路由加密</li><li>在 VNet 加密</li><li>VNet-VNet 加密</ul>| 是 | 支持标准的 HTTPS/TLS 机制。  传输到服务之前，用户还可以加密数据。 |
| 加密密钥处理 （CMK，BYOK，等等。）| 是 | 请参阅[使用 Azure Key Vault 中客户托管密钥的存储服务加密](../storage/common/storage-service-encryption-customer-managed-keys.md)。|
| 列级加密 （Azure 数据服务）| 不适用 |  |
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
| Azure 监视支持 （日志分析、 应用程序见解等）| 是 | 可用的 azure Monitor 指标现在，可以记录开始预览 |

### <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | Yes/No | 说明|
|---|---|--|
| Authentication| 是 | Azure Active Directory、 共享密钥、 共享访问令牌。 |
| 授权| 是 | 支持通过 RBAC、 POSIX Acl 和 SAS 令牌的授权 |


### <a name="audit-trail"></a>审核线索

| 安全属性 | Yes/No | 说明|
|---|---|--|
| 控制和管理平面日志记录和审核 | 是 | Azure 资源管理器活动日志 |
| 数据平面日志记录和审核| 是 | 服务诊断日志和 Azure Monitor 日志记录开始预览  |

### <a name="configuration-management"></a>配置管理

| 安全特性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持 （版本控制的配置，等等）。| 是 | 支持通过 Azure 资源管理器 Api 的资源提供程序版本控制 |