---
title: "使用自动缩放操作发送电子邮件和 webhook 警报通知。 | Microsoft 文档"
description: "了解如何在 Azure 监视器中使用自动缩放操作来调用 Web URL 或发送电子邮件通知。 "
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: eb9a4c98-0894-488c-8ee8-5df0065d094f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: ancav
ms.openlocfilehash: 16caf14028494800e9259f0296c292b606d0210a
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>使用自动缩放操作在 Azure 监视器中发送电子邮件和 webhook 警报通知
本文演示如何设置触发器，以便可以在 Azure 中基于自动缩放操作调用特定 Web URL 或发送电子邮件。  

## <a name="webhooks"></a>Webhook
通过 webhook 可以将 Azure 警报通知路由到其他系统以便用于后处理或自定义通知。 例如，将警报路由到可以处理传入 web 请求的服务，以便使用聊天或消息服务等来发送 SMS、记录 bug、通知团队。Webhook URI 必须是有效 HTTP 或 HTTPS 终结点。

## <a name="email"></a>电子邮件
电子邮件可以发送到任何有效电子邮件地址。 还将通知运行规则的订阅的管理员和共同管理员。

## <a name="cloud-services-and-web-apps"></a>云服务和 Web 应用
可以从 Azure 门户选择加入云服务和服务器场 (Web 应用)。

* 选择“缩放依据”指标。

![缩放依据](./media/insights-autoscale-to-webhook-email/insights-autoscale-notify.png)

## <a name="virtual-machine-scale-sets"></a>虚拟机规模集
对于使用 Resource Manager（虚拟机规模集）创建的较新虚拟机，可以使用REST API、Resource Manager 模板、PowerShell 和 CLI 进行配置。 门户界面尚不可用。
使用 REST API 或 Resource Manager 模板时，包括具有以下选项的通知元素。

```
"notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": false,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]
```
| 字段 | 必需？ | 说明 |
| --- | --- | --- |
| operation |是 |值必须是“规模” |
| sendToSubscriptionAdministrator |是 |值必须是“true”或“false” |
| sendToSubscriptionCoAdministrators |是 |值必须是“true”或“false” |
| customEmails |是 |值可以为 null [] 或电子邮件的字符串数组 |
| webhooks |是 |该值可以为 null 或有效的 URI |
| serviceUri |是 |有效的 https URI |
| properties |是 |值必须是空的 {}，也可以包含键值对 |

## <a name="authentication-in-webhooks"></a>webhook 中的身份验证
webhook 可使用基于令牌的身份验证进行身份验证：将具有令牌 ID 的 webhook URI 保存为查询参数。 例如，https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue

## <a name="autoscale-notification-webhook-payload-schema"></a>自动缩放通知 webhook 负载架构
生成自动缩放通知时，以下元数据会包含在 webhook 负载中：

```
{
        "version": "1.0",
        "status": "Activated",
        "operation": "Scale In",
        "context": {
                "timestamp": "2016-03-11T07:31:04.5834118Z",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/myautoscaleSetting",
                "name": "myautoscaleSetting",
                "details": "Autoscale successfully started scale operation for resource 'MyCSRole' from capacity '3' to capacity '2'",
                "subscriptionId": "s1",
                "resourceGroupName": "rg1",
                "resourceName": "MyCSRole",
                "resourceType": "microsoft.classiccompute/domainnames/slots/roles",
                "resourceId": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService/slots/Production/roles/MyCSRole",
                "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService",
                "oldCapacity": "3",
                "newCapacity": "2"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```


| 字段 | 必需？ | 说明 |
| --- | --- | --- |
| status |是 |指示生成自动缩放操作的状态 |
| operation |是 |对于实例的增加，它会是“Scale Out”，对于实例的减少，它会是“Scale In” |
| 上下文 |是 |自动缩放操作上下文 |
| timestamp |是 |触发自动缩放操作时的时间戳 |
| id |是 |自动缩放设置的 Resource Manager ID |
| 名称 |是 |自动缩放设置的名称 |
| 详细信息 |是 |自动缩放服务执行的操作和实例计数的更改的说明 |
| subscriptionId |是 |所缩放的目标资源的订阅 ID |
| resourceGroupName |是 |所缩放的目标资源的资源组名 |
| resourceName |是 |所缩放的目标资源的名称 |
| resourceType |是 |三个支持的值是：“microsoft.classiccompute/domainnames/slots/roles” - 云服务角色、“microsoft.compute/virtualmachinescalesets” - 虚拟机规模集和“Microsoft.Web/serverfarms” - Web 应用 |
| resourceId |是 |所缩放的目标资源的 Resource Manager ID |
| portalLink |是 |指向目标资源摘要页的 Azure 门户链接 |
| oldCapacity |是 |自动缩放执行缩放操作时的当前（旧）实例计数 |
| newCapacity |是 |自动缩放将资源缩放到的新实例计数 |
| 属性 |否 |可选。 <键, 值> 对集（例如字典 <String, String>）。 properties 字段是可选的。 在自定义用户界面或基于逻辑应用的工作流中，可以输入可使用有效负载传递的键和值。 将自定义属性传递回传出 webhook 调用的替代方法是使用 webhook URI 本身（作为查询参数） |
