---
title: 使用 Azure 资源管理器模板配置 Azure Application Insights 智能检测规则设置 | Microsoft Docs
description: 使用 Azure 资源管理器模板自动完成 Azure Application Insights 智能检测规则的管理和配置
services: application-insights
documentationcenter: ''
author: harelbr
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/26/2019
ms.reviewer: mbullwin
ms.author: harelbr
ms.openlocfilehash: 6bb89eec0b4905e101bed87d3d3fc617dec589e0
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477864"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板管理 Application Insights 智能检测规则

可以使用 [Azure 资源管理器模板](../../azure-resource-manager/resource-group-authoring-templates.md)来管理和配置 Application Insights 中的智能检测规则。
使用 Azure 资源管理器自动化部署新的 Application Insights 资源或修改现有资源的设置时，可以使用此方法。

## <a name="smart-detection-rule-configuration"></a>智能检测规则配置

可以配置智能检测规则的以下设置：
- 是否已启用该规则（默认值为 **true**。）
- 电子邮件是否应发送到与订阅的关联用户[监视查阅者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader)并[监视参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)找到检测时，角色 (默认值是**true**.)
- 找到检测项时，应收到通知的其他任何电子邮件收件人。
    -  电子邮件配置的智能检测规则标记为不可用_预览版_。

为了让用户通过 Azure 资源管理器配置规则设置，智能检测规则配置现已在 Application Insights 资源中提供一个名为 **ProactiveDetectionConfigs** 的内部资源。
为了提供最大的灵活性，可为每个智能检测规则配置独特的通知设置。

## 

## <a name="examples"></a>示例

以下几个示例演示如何使用 Azure 资源管理器模板配置智能检测规则的设置。
在所有示例中，Application Insights 资源名为 _myApplication_，“‘依赖项持续时间长’智能检测规则”的内部名称为 _longdependencyduration_。
请务必替换 Application Insights 资源名称，并指定相关的智能检测规则内部名称。 在下表中查看每个智能检测规则的对应内部 Azure 资源管理器名称列表。

### <a name="disable-a-smart-detection-rule"></a>禁用智能检测规则

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": [],
            "enabled": false
          }
        }
      ]
    }
```

### <a name="disable-sending-email-notifications-for-a-smart-detection-rule"></a>禁用发送有关某个智能检测规则的电子邮件通知

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": false,
            "customEmails": [],
            "enabled": true
          }
        }
      ]
    }
```

### <a name="add-additional-email-recipients-for-a-smart-detection-rule"></a>为智能检测规则添加更多的电子邮件收件人

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": ['alice@contoso.com', 'bob@contoso.com'],
            "enabled": true
          }
        }
      ]
    }

```

### <a name="failure-anomalies-v2-non-classic-alert-rule"></a>失败异常 v2 （非经典） 的警报规则

此 Azure 资源管理器模板演示如何配置失败异常 v2 警报规则在严重级别为 2。 此新版本的失败异常警报规则是新的 Azure 警报平台的一部分，将替代被淘汰作为的一部分的经典版本[经典警报停用过程](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/)。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "microsoft.alertsmanagement/smartdetectoralertrules",
            "apiVersion": "2019-03-01",
            "name": "Failure Anomalies - my-app",
            "properties": {
                  "description": "Detects a spike in the failure rate of requests or dependencies",
                  "state": "Enabled",
                  "severity": "2",
                  "frequency": "PT1M",
                  "detector": {
                  "id": "FailureAnomaliesDetector"
                  },
                  "scope": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/MyResourceGroup/providers/microsoft.insights/components/my-app"],
                  "actionGroups": {
                        "groupIds": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourcegroups/MyResourceGroup/providers/microsoft.insights/actiongroups/MyActionGroup"]
                  }
            }
        }
    ]
}
```

> [!NOTE]
> 此 Azure 资源管理器模板是唯一的失败异常 v2 警报规则，并且是不同于其他经典智能检测规则本文中所述。   

## <a name="smart-detection-rule-names"></a>智能检测规则名称

下表列出了门户中显示的智能检测规则名称，以及应在 Azure 资源管理器模板中为这些规则使用的内部名称。

> [!NOTE]
> 智能检测规则标记为_预览版_不支持电子邮件通知。 因此，您可以只设置_启用_这些规则的属性。 

| Azure 门户规则名称 | 内部名称
|:---|:---|
| 页面加载慢 | slowpageloadtime |
| 服务器响应慢 | slowserverresponsetime |
| 依赖项持续时间长 | longdependencyduration |
| 服务器响应降级 | degradationinserverresponsetime |
| 依赖项持续时间减少 | degradationindependencyduration |
| 跟踪严重性比下降（预览） | extension_traceseveritydetector |
| 异常卷的异常增加（预览） | extension_exceptionchangeextension |
| 检测到潜在的内存泄漏（预览） | extension_memoryleakextension |
| 检测到潜在的安全问题（预览） | extension_securityextensionspackage |
| 中的每日数据量 （预览版） 异常升高 | extension_billingdatavolumedailyspikeextension |

## <a name="next-steps"></a>后续步骤

了解有关自动检测的详细信息：

- [失败异常](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [内存泄漏](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [性能异常](../../azure-monitor/app/proactive-performance-diagnostics.md)
