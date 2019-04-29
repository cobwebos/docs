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
ms.date: 02/07/2019
ms.reviewer: mbullwin
ms.author: harelbr
ms.openlocfilehash: 3ab50c92543615488d9ced599df433bf7e1e4061
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61461555"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板管理 Application Insights 智能检测规则

可以使用 [Azure 资源管理器模板](../../azure-resource-manager/resource-group-authoring-templates.md)来管理和配置 Application Insights 中的智能检测规则。
使用 Azure 资源管理器自动化部署新的 Application Insights 资源或修改现有资源的设置时，可以使用此方法。

## <a name="smart-detection-rule-configuration"></a>智能检测规则配置

可以配置智能检测规则的以下设置：
- 是否已启用该规则（默认值为 **true**。）
- 找到检测项时，是否将电子邮件发送到订阅所有者、参与者或读取者（默认值为 **true**。）
- 找到检测项时，应收到通知的其他任何电子邮件收件人。
- * 电子邮件配置不适用于标记为“预览”的智能检测规则。

为了让用户通过 Azure 资源管理器配置规则设置，智能检测规则配置现已在 Application Insights 资源中提供一个名为 **ProactiveDetectionConfigs** 的内部资源。
为了提供最大的灵活性，可为每个智能检测规则配置独特的通知设置。

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

## <a name="smart-detection-rule-names"></a>智能检测规则名称

下表列出了门户中显示的智能检测规则名称，以及应在 Azure 资源管理器模板中为这些规则使用的内部名称。

> [!NOTE]
> 标记为“预览”的智能检测规则不支持电子邮件通知。 因此，只能为这些规则设置已启用的属性。 

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
| 检测到资源利用率问题（预览） | extension_resourceutilizationextensionspackage |

## <a name="who-receives-the-classic-alert-notifications"></a>谁会收到（经典）警报通知？

本节仅适用于智能检测经典警报，并将帮助优化警报通知以确保只有预期的接收人能收到通知。 若要详细了解[经典警报](../platform/alerts-classic.overview.md)与新的警报体验之间的区别，请参阅[警报概述文章](../platform/alerts-overview.md)。 当前智能检测警报仅支持经典警报体验。 有一个例外情况，是 [Azure 云服务上的智能检测警报](./proactive-cloud-services.md)。 若要控制 Azure 云服务上智能检测警报的警报通知，可使用[操作组](../platform/action-groups.md)。

* 建议将智能检测/经典警报通知应用于特定收件人。

* 对于智能检测警报，“批/组”复选框选项（如果已启用）将发送给订阅中具有所有者、参与者或读者角色的用户。 实际上，可以访问订阅 Application Insights 资源的_所有_用户均会接收到通知。 

> [!NOTE]
> 如果当前使用“批/组”复选框选项并禁用它，则无法还原更改。

## <a name="next-steps"></a>后续步骤

了解有关自动检测的详细信息：

- [失败异常](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [内存泄漏](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [性能异常](../../azure-monitor/app/proactive-performance-diagnostics.md)