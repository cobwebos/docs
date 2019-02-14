---
author: msmbaldwin
ms.service: backup
ms.topic: include
ms.date: 01/31/2019
ms.author: mbaldwin
ms.openlocfilehash: 5a4cc3ea822c5613fc7a50b8e370d6846b683721
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55513292"
---
## <a name="preventative"></a>预防

| 安全特性 | Yes/No | 说明 |
|---|---|--|
| 静态加密：<ul><li>服务器端加密</li><li>使用客户托管密钥的服务器端加密</li><li>其他加密功能（如客户端、始终加密等）</ul>| 是 | 对存储帐户使用存储服务加密。 |
| 传输中加密：<ul><li>快速路由加密</li><li>Vnet 中加密</li><li>VNet-VNet 加密</ul>| 否 | 使用 HTTPS。 |
| 加密密钥处理（CMK、BYOK 等）| 否 |  |
| 列级加密（Azure 数据服务）| 否 |  |
| 加密的 API 调用| 是 |  |

## <a name="network-segmentation"></a>网络分段

| 安全特性 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 否 |  |
| vNET 注入支持| 否 |  |
| 网络隔离/防火墙支持| 是 | 对 VM 备份支持强制隧道。 对 VM 内运行的工作负荷不支持强制隧道。 |
| 支持强制隧道 | 否 |  |

## <a name="detection"></a>检测

| 安全特性 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持（日志分析、App Insights 等）| 是 | 通过诊断日志支持 Log Analytics。 有关详细信息，请参阅“使用 Log Analytics 监视 Azure 备份保护的工作负荷”(https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/)。 |

## <a name="iam-support"></a>IAM 支持

| 安全特性 | Yes/No | 说明|
|---|---|--|
| 访问管理 - 身份验证| 是 | 身份验证通过 Azure Active Directory 来进行。 |
| 访问管理 - 授权| 是 | 使用客户创建和内置的 RBAC 角色。 有关详细信息，请参阅“使用基于角色的访问控制管理 Azure 备份恢复点”(https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault) |


## <a name="audit-trail"></a>审核线索

| 安全特性 | Yes/No | 说明|
|---|---|--|
| 控制/管理计划日志记录和审核| 是 | 来自 Azure 门户的所有客户触发操作都会记录到活动日志中。 |
| 数据平面日志记录和审核| 否 | 无法直接访问 Azure 备份数据平面。  |

## <a name="configuration-management"></a>配置管理

| 安全特性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是|  |