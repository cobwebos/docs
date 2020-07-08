---
title: Azure 安全基准的 azure 策略安全性基准
description: Azure 策略安全基线为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: azure-policy
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 7871099e3e05613bec29a4bfc0824dd9647431e7
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85937486"
---
# <a name="azure-policy-security-baseline-for-azure-security-benchmark"></a>Azure 安全基准的 azure 策略安全性基准

此安全基线将[Azure 安全性基准](../../../security/benchmarks/overview.md)中的指南应用于 azure 策略。 Azure 安全基准提供了有关如何在 Azure 上保护云解决方案的建议。 内容按照 Azure 安全基准定义的**符合性域**和**安全控件**进行分组，以及适用于 azure 策略的相关指南。 排除了不适用于 Azure 策略的**控件**。 若要查看 Azure 策略如何完全映射到 Azure 安全基准，请参阅[完整的 Azure 策略安全基线映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

有关通过内置计划将 Azure 安全基准控制映射到内置策略定义的内容，请参阅合规[性： Azure 安全基准](../samples/azure-security-benchmark.md)。

Azure 策略使用术语 "_所有权_" 来代替_责任_。 有关_所有权_的详细信息，请参阅[Azure 策略策略定义](./definition-structure.md#type)和[云中的共享责任](../../../security/fundamentals/shared-responsibility.md)。


## <a name="logging-and-monitoring"></a>日志记录和监视

有关详细信息，请参阅[安全控制：日志记录和监视](../../../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指南**： Azure 策略使用自动启用的活动日志，以包含事件源、日期、用户、时间戳、源地址、目标地址和其他有用元素。

* [如何使用 Azure Monitor 收集平台日志和指标](../../../azure-monitor/platform/diagnostic-settings.md)

* [了解 Azure 中的日志记录和不同的日志类型](../../../azure-monitor/platform/platform-logs-overview.md)


**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="identity-and-access-control"></a>标识和访问控制

有关详细信息，请参阅[安全控制：标识和访问控制](../../../security/benchmarks/security-control-identity-access-control.md)。

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指导**：围绕专用管理帐户的使用创建标准操作程序。 使用 Azure 安全中心标识和访问管理来监视管理帐户的数量。 

还可以通过使用[Azure AD Privileged Identity Management](../../../active-directory/privileged-identity-management/pim-configure.md)特权角色或[Azure 资源管理器](../../../azure-resource-manager/management/overview.md)来启用实时/刚好足够的访问解决方案。


**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：对所有管理任务使用专用计算机（特权访问工作站）

**指南**：将 PAW（特权访问工作站）与为登录和配置 Azure 资源而配置的 MFA 结合使用。

* [了解特权访问工作站](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [如何在 Azure 中启用 MFA](../../../active-directory/authentication/howto-mfa-getstarted.md)


**Azure 安全中心监视**：不适用

责任：客户

## <a name="data-protection"></a>数据保护

有关详细信息，请参阅[安全控制：数据保护](../../../security/benchmarks/security-control-data-protection.md)。

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6：使用基于角色的访问控制来控制对资源的访问

**指南**：使用 Azure Active Directory 基于角色的访问控制（RBAC）来控制对 Azure 策略的访问。

* [Azure Policy 中的 RBAC 权限](../overview.md#rbac-permissions-in-azure-policy)

* [如何在 Azure 中配置 RBAC](../../../role-based-access-control/role-assignments-portal.md)


**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指南**：将 Azure Monitor 与活动日志结合使用，以便为 Azure 策略中发生的更改创建警报。

* [如何为 Azure 活动日志事件创建警报](../../../azure-monitor/platform/alerts-activity-log.md)


**Azure 安全中心监视**：目前不可用

**责任**：客户

## <a name="inventory-and-asset-management"></a>库存和资产管理

有关详细信息，请参阅[安全控制：清单和资产管理](../../../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指导**：将标记应用到 Azure资源，以便有条理地将元数据组织成某种分类。 使用 Azure 策略_修改_效果来报告和强制实施符合性和一致性标记管理。

* [教程：创建和管理策略](../tutorials/create-and-manage.md)

* [教程：管理标记治理](../tutorials/govern-tags.md)


**Azure 安全中心监视**：目前不可用

**责任**：客户

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4：定义并维护已获批 Azure 资源的清单

**指南**：根据组织需求创建已批准的策略定义和策略分配的清单。

**Azure 安全中心监视**：不适用

责任：客户

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指南**：使用 Azure 策略对可在订阅中创建的资源类型施加限制。

* [如何配置和管理 Azure Policy](../tutorials/create-and-manage.md)


**Azure 安全中心监视**：不适用

责任：客户

## <a name="next-steps"></a>后续步骤

- 请参阅 [Azure 安全基准](../../../security/benchmarks/overview.md)
- 详细了解 [Azure 安全基线](../../../security/benchmarks/security-baselines-overview.md)
