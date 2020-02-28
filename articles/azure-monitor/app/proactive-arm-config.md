---
title: 智能检测规则设置-Azure 应用程序 Insights
description: 使用 Azure 资源管理器模板自动完成 Azure Application Insights 智能检测规则的管理和配置
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 06/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 3c028a97c2fb554b13035026025437d5331104c2
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669703"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板管理 Application Insights 智能检测规则

可以使用 [Azure 资源管理器模板](../../azure-resource-manager/templates/template-syntax.md)来管理和配置 Application Insights 中的智能检测规则。
使用 Azure 资源管理器自动化部署新的 Application Insights 资源或修改现有资源的设置时，可以使用此方法。

## <a name="smart-detection-rule-configuration"></a>智能检测规则配置

可以配置智能检测规则的以下设置：
- 是否已启用该规则（默认值为 **true**。）
- 如果在找到检测时应将电子邮件发送到与订阅的[监视读者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader)关联的用户，并[监视参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)角色（默认值为**true**）。
- 找到检测项时，应收到通知的其他任何电子邮件收件人。
    -  电子邮件配置不适用于标记为_预览版_的智能检测规则。

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

### <a name="failure-anomalies-alert-rule"></a>失败异常警报规则

此 Azure 资源管理器模板演示如何配置严重级别为2的故障异常警报规则。 这一新版本的故障异常警报规则是新的 Azure 警报平台的一部分，替换为[经典警报停用过程](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/)的一部分而停用的经典版本。

> [!NOTE]
> 故障异常是全局服务，因此在全局位置上创建规则位置。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "microsoft.alertsmanagement/smartdetectoralertrules",
            "apiVersion": "2019-03-01",
            "name": "Failure Anomalies - my-app",
            "location": "global", 
            "properties": {
                  "description": "Failure Anomalies notifies you of an unusual rise in the rate of failed HTTP requests or dependency calls.",
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
> 此 Azure 资源管理器模板是故障异常警报规则独有的，不同于本文中所述的其他经典智能检测规则。

## <a name="smart-detection-rule-names"></a>智能检测规则名称

下表列出了门户中显示的智能检测规则名称，以及应在 Azure 资源管理器模板中为这些规则使用的内部名称。

> [!NOTE]
> 标记为_预览版_的智能检测规则不支持电子邮件通知。 因此，只能为这些规则设置 "_已启用_" 属性。 

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
| 日常数据量（预览版）中的异常上升 | extension_billingdatavolumedailyspikeextension |

## <a name="next-steps"></a>后续步骤

了解有关自动检测的详细信息：

- [失败异常](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [内存泄漏](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [性能异常](../../azure-monitor/app/proactive-performance-diagnostics.md)
