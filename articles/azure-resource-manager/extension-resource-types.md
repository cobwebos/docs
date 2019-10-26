---
title: Azure 扩展资源类型
description: 列出了用于扩展其他资源类型功能的 Azure 资源类型。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: tomfitz
ms.openlocfilehash: 00e8efaaa81ec0ce189d8cfe8a78285ded445c68
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901919"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>扩展其他资源功能的资源类型

扩展资源是添加到另一资源的功能的资源。 例如，资源锁是扩展资源。 将资源锁应用到另一个资源，以防止其被删除或修改。 创建资源锁本身并无意义。 扩展资源始终应用于另一资源。

## <a name="extension-resource-types"></a>扩展资源类型

- Microsoft Advisor/配置
- Microsoft Advisor/建议
- Microsoft Advisor/禁止显示
- AlertsManagement/警报
- AlertsManagement/alertsSummary
- Microsoft. Authorization/checkAccess
- Microsoft. Authorization/denyAssignments
- Microsoft. 授权/锁
- Microsoft. 授权/权限
- Microsoft. Authorization/policyAssignments
- Microsoft. Authorization/policyDefinitions
- Microsoft. Authorization/policySetDefinitions
- Microsoft. Authorization/roleAssignments
- Microsoft. Authorization/roleDefinitions
- Microsoft. 计费/billingPeriods
- Microsoft. 计费/billingPermissions
- Microsoft. 计费/billingRoleAssignments
- Microsoft. 计费/billingRoleDefinitions
- Microsoft. 计费/createBillingRoleAssignment
- Microsoft. 蓝图/blueprintAssignments
- Microsoft. 蓝图/蓝图
- AggregatedCost/
- Microsoft. 消耗/余额
- Microsoft. 消耗/预算
- Microsoft. 消耗/费用
- CostTags/
- Microsoft. 消耗/预测
- Microsoft. 消费/市场
- OperationResults/
- OperationStatus/
- Pricesheets/
- ReservationDetails/
- ReservationRecommendations/
- ReservationSummaries/
- ReservationTransactions/
- Microsoft. 消耗/标记
- Microsoft. 消耗/条款
- UsageDetails/
- Microsoft. 消耗/信用额度
- Microsoft. 消耗/事件
- Microsoft. 消耗/批次
- Microsoft. 消费/产品
- Microsoft. 消耗/租户
- ContainerInstance/serviceAssociationLinks
- CostManagement/警报
- CostManagement/预算
- CostManagement/维度
- CostManagement/出口
- CostManagement/ExternalSubscriptions
- CostManagement/预测
- CostManagement/查询
- CostManagement/Reportconfigs
- CostManagement/报表
- CostManagement/Views
- CostManagement/showbackRules
- CustomProviders/关联
- Microsoft.EventGrid/eventSubscriptions
- Microsoft.EventGrid/extensionTopics
- GuestConfiguration/configurationProfileAssignments
- GuestConfiguration/guestConfigurationAssignments
- GuestConfiguration/software
- GuestConfiguration/softwareUpdateProfile
- GuestConfiguration/softwareUpdates
- microsoft insights/automatedExportSettings
- microsoft insights/基线
- microsoft insights/calculatebaseline
- microsoft insights/diagnosticSettings
- microsoft insights/diagnosticSettingsCategories
- microsoft insights/eventtypes
- microsoft insights/extendedDiagnosticSettings
- microsoft insights/guestDiagnosticSettingsAssociation
- microsoft insights/logDefinitions
- microsoft insights/日志
- microsoft insights/metricDefinitions
- microsoft insights/metricNamespaces
- microsoft insights/metricbaselines
- microsoft insights/指标
- microsoft insights/myWorkbooks
- microsoft insights/vmInsightsOnboardingStatuses
- KubernetesConfiguration/sourceControlConfigurations
- ApplyUpdates/维护/维护
- ConfigurationAssignments/维护/维护
- Microsoft. 维护/更新
- 对 microsoft.managedidentity/标识
- ManagedServices/registrationAssignments
- ManagedServices/registrationDefinitions
- Microsoft.operationalinsights/storageInsightConfigs
- 为 microsoft.operationsmanagement/managementassociations
- PolicyInsights/policyEvents
- PolicyInsights/policyStates
- PolicyInsights/policyTrackedResources
- PolicyInsights/修正
- Microsoft.recoveryservices/backupProtectedItems
- ResourceHealth/availabilityStatuses
- ResourceHealth/childAvailabilityStatuses
- ResourceHealth/childResources
- ResourceHealth/事件
- ResourceHealth/impactedResources
- ResourceHealth/通知
- Microsoft. 资源/链接
- Microsoft .Resources/标记
- Microsoft. Security/符合性要求
- Microsoft. Security/InformationProtectionPolicies
- Microsoft. Security/adaptiveNetworkHardenings
- Microsoft. Security/advancedThreatProtectionSettings
- Microsoft. Security/assessmentMetadata
- Microsoft 安全/评估
- Microsoft. Security/complianceResults
- Microsoft. Security/dataCollectionAgents
- Microsoft. Security/dataCollectionResults
- Microsoft. Security/deviceSecurityGroups
- Microsoft. Security/Networkdata.xml
- Microsoft. Security/serverVulnerabilityAssessments
- SecurityInsights/聚合
- SecurityInsights/alertRuleTemplates
- SecurityInsights/alertRules
- SecurityInsights/书签
- SecurityInsights/事例
- SecurityInsights/dataConnectors
- SecurityInsights/实体
- SecurityInsights/entityQueries
- SecurityInsights/officeConsents
- SecurityInsights/settings
- SoftwarePlan/hybridUseBenefits
- Microsoft. 订阅/CreateSubscription
- microsoft. support/createsupportticket
- microsoft. support/supporttickets
- WorkloadMonitor/组件
- WorkloadMonitor/monitorInstances
- WorkloadMonitor/监视器
- WorkloadMonitor/notificationSettings

## <a name="next-steps"></a>后续步骤

- 若要获取 Azure 资源管理器模板中扩展资源的资源 ID，请使用[extensionResourceId](resource-group-template-functions-resource.md#extensionresourceid)。
- 有关在模板中创建扩展资源的示例，请参阅[事件网格事件订阅](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions)。
