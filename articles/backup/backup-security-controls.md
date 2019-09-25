---
title: Azure 备份的安全控制
description: 用于评估 Azure 备份的安全控制清单
ms.reviewer: mbaldwin
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: dacurwin
ms.openlocfilehash: f44be7556b6d741df93faeeab1dbdfc15bc8ebfd
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211801"
---
# <a name="security-controls-for-azure-backup"></a>Azure 备份的安全控制

本文阐述了 Azure 备份中内置的安全控制。 

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>网络

| 安全控制 | Yes/No | 说明 | 文档
|---|---|--|
| 服务终结点支持| 否 |  |  |
| VNet 注入支持| 否 |  |  |
| 网络隔离和防火墙支持| 是 | 对 VM 备份支持强制隧道。 对 VM 内运行的工作负荷不支持强制隧道。 |  |
| 强制隧道支持| 否 |  |  |

## <a name="monitoring--logging"></a>监视和日志记录

| 安全控制 | Yes/No | 说明| | 文档
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | 通过诊断日志支持 Log Analytics。 有关详细信息，请参阅[使用 Log Analytics 监视 Azure 备份保护的工作负荷](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/)。 |  |
| 控制和管理平面日志记录和审核| 是 | 来自 Azure 门户的所有客户触发操作都会记录到活动日志中。 |  |
| 数据平面日志记录和审核| 否 | 无法直接访问 Azure 备份数据平面。  |  |

## <a name="identity"></a>标识

| 安全控制 | Yes/No | 说明| | 文档
|---|---|--|
| 身份验证| 是 | 身份验证通过 Azure Active Directory 来进行。 |  |
| Authorization| 是 | 使用客户创建和内置的 RBAC 角色。 有关详细信息，请参阅[使用基于角色的访问控制管理 Azure 备份恢复点](/azure/backup/backup-rbac-rs-vault)。 |  |

## <a name="data-protection"></a>数据保护

| 安全控制 | Yes/No | 说明 | | 文档
|---|---|--|
| 服务器端静态加密：Microsoft 管理的密钥 | 是 | 对存储帐户使用存储服务加密。 |  |
| 服务器端静态加密：客户管理的密钥 (BYOK) | 否 |  |  |
| 列级加密（Azure 数据服务）| 否 |  |  |
| 传输中加密（例如 ExpressRoute 加密、VNet 中加密，以及 VNet-VNet 加密）| 否 | 使用 HTTPS。 |  |
| 加密的 API 调用| 是 |  |  |

## <a name="configuration-management"></a>配置管理

| 安全控制 | Yes/No | 说明| | 文档
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是|  |  |

## <a name="next-steps"></a>后续步骤

- 详细了解[Azure 服务中的内置安全控件](../security/fundamentals/security-controls.md)。