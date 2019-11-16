---
title: Azure Lighthouse 示例和模板
description: 这些示例和 Azure 资源管理器模板说明了如何载入客户以进行 Azure 委托资源管理，以及如何支持 Azure Lighthouse 方案。
ms.topic: sample
ms.date: 10/17/2019
ms.openlocfilehash: 20c1fc62c83202f97ed9cff68fd285280d94a321
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132162"
---
# <a name="azure-lighthouse-samples"></a>Azure Lighthouse 示例

下表包含 Azure Lighthouse 的关键 Azure 资源管理器模板的链接。 这些文件以及其他内容还可在 [Azure Lighthouse 示例存储库](https://github.com/Azure/Azure-Lighthouse-samples/)中找到。

## <a name="onboarding-customers-to-azure-delegated-resource-management"></a>将客户载入到 Azure 委派资源管理

我们提供了不同的模板来处理特定载入方案。 选择最适合的选项，并确保修改参数文件以反映环境。 有关如何在部署中使用这些文件的详细信息，请参阅[将客户载入到 Azure 委派资源管理](../how-to/onboard-customer.md)。

| **模板** | **说明** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management) | 将客户的订阅载入到 Azure 委派资源管理。 必须为每个订阅执行单独的部署。 |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) | 将客户的一个或多个资源组载入到 Azure 委派资源管理。 将 **rgDelegatedResourceManagement** 用于单个资源组，或使用 **multipleRgDelegatedResourceManagement**  载入同一订阅中的多个资源组。 |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management) | 如果已[向 Azure 市场发布托管服务产品](../how-to/publish-managed-services-offers.md)，则可以选择使用此模板为已接受该产品的客户载入资源。 参数文件中的 marketplace 值必须与发布产品时使用的值匹配。 |

通常，每个载入的订阅都需要单独部署，但你也可以跨多个订阅部署模板。

| **模板** | **说明** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-subscription-deployment) | 跨多个订阅部署 Azure 资源管理器模板。 |

## <a name="azure-policy"></a>Azure Policy

这些示例演示如何将 Azure Policy 用于已为 Azure 委派资源管理而载入的订阅。

| **模板** | **说明** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-add-or-replace-tag) | 分配一个策略，该策略为委派的订阅添加或删除标记（使用 modify 效果）。 有关详细信息，请参阅[部署可以在委派的订阅中修正的策略](../how-to/deploy-policy-remediation.md)。 |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-audit-delegation) | 分配一个将用于审核委派分配的策略。 |
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring) | 分配一个策略，该策略对委托订阅中的 Azure Key Vault 资源启用诊断（使用 deployIfNotExists 效果）。 有关详细信息，请参阅[部署可以在委派的订阅中修正的策略](../how-to/deploy-policy-remediation.md)。 |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-sub-monitoring) | 分配几个策略，以便对委派的订阅启用诊断，并将所有 Windows VM 和 Linux VM 连接到按策略创建的 Log Analytics 工作区。 有关详细信息，请参阅[部署可以在委派的订阅中修正的策略](../how-to/deploy-policy-remediation.md)。 |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-initiative) | 将计划（多个相关的策略定义）应用于委派的订阅。 |

## <a name="azure-monitor"></a>Azure Monitor

这些示例演示如何使用 Azure Monitor 为已针对 Azure 委派资源管理而载入的订阅创建警报。

| **模板** | **说明** |
|---------|---------|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/alert-using-actiongroup) | 此模板创建一个 Azure 警报并连接到现有操作组。|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/multiple-loganalytics-alerts) | 基于 Kusto 查询创建多个日志警报。|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegation-alert-for-customer) | 当用户将订阅委派给管理租户时在租户中部署警报。|

## <a name="additional-cross-tenant-scenarios"></a>其他跨租户方案

这些示例说明了可在跨租户管理方案中执行的各种任务。

| **模板** | **说明** |
|---------|---------|
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-rg-deployment) | 将存储帐户部署到两个不同的资源组中。|
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-mgmt-services) | 创建 Azure 管理服务，将它们链接在一起并部署其他解决方案。 对于端到端部署，请使用 **rgWithAzureMgmt.json** 模板。 |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-security-center) | 在目标 Azure 订阅内启用并配置 Azure 安全中心。 |
| [deploy-azure-sentinel](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-sentinel) | 在委托订阅中的现有 Log Analytics 工作区上部署并启用 Azure Sentinel。 |
| [deploy-log-analytics-vm-extensions](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-log-analytics-vm-extensions) | 使用这些模板可以将 Log Analytics VM 扩展部署到 Windows VM 和 Linux VM，并将其连接到指定的 Log Analytics 工作区 |

## <a name="next-steps"></a>后续步骤

- 了解 [Azure 委派资源管理](../concepts/azure-delegated-resource-management.md)。
- 探索 [Azure Lighthouse 示例存储库](https://github.com/Azure/Azure-Lighthouse-samples/)。
