---
title: 使用 ServiceNow 配置 Azure 服务运行状况警报 | Microsoft Docs
description: 获取有关发送到 ServiceNow 实例的服务运行状况事件的个性化通知。
author: shawntabrizi
services: service-health
documentationcenter: service-health
ms.assetid: ''
ms.service: service-health
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: shtabriz
ms.openlocfilehash: 867a8c0b478df9d2b7690b8b914ded7c42558583
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
ms.locfileid: "30178862"
---
# <a name="configure-service-health-alerts-with-servicenow"></a>使用 ServiceNow 配置服务运行状况警报

本文介绍如何使用 Webhook 将 Azure 服务运行状况警报与 ServiceNow 集成。 设置与 ServiceNow 实例的 Webhook 集成以后，当 Azure 服务问题影响你时，你会通过现有的通知基础结构获得警报。 Azure 服务运行状况警报在每次触发时，会通过 ServiceNow 的脚本 REST API 调用 Webhook。

## <a name="creating-a-scripted-rest-api-in-servicenow"></a>在 ServiceNow 中创建脚本 REST API
1.  请确保已注册并登录到 [ServiceNow](https://www.servicenow.com/) 帐户。

2.  在 ServiceNow 中导航到“系统 Web 服务”部分，然后选择“脚本 REST API”。

    ![ServiceNow 中的“脚本 Web 服务”部分](./media/webhook-alerts/servicenow-sws-section.png)

3.  选择“新建”，创建新的脚本 REST 服务。
 
    ![ServiceNow 中的“新建脚本 REST API”按钮](./media/webhook-alerts/servicenow-new-button.png)

4.  为 REST API 添加“名称”，然后将“API ID”设置为 `azureservicehealth`。

5.  选择“提交”。

    ![ServiceNow 中的“REST API 设置”](./media/webhook-alerts/servicenow-restapi-settings.png)

6.  选择已创建的 REST API，然后在“资源”选项卡下选择“新建”。

    ![ServiceNow 中的“资源选项卡”](./media/webhook-alerts/servicenow-resources-tab.png)

7.  为新资源 `event` **命名**，然后将“HTTP 方法”更改为 `POST`。

8.  在“脚本”部分，添加以下 JavaScript 代码：

    >[!NOTE]
    >需要在下面的脚本中更新 `<secret>`、`<group>` 和 `<email>` 值。
    >* `<secret>` 应为随机字符串，例如 GUID
    >* `<group>` 应为要向其分配事件的 ServiceNow 组
    >* `<email>` 应为要向其分配事件的特定人员（可选）
    >

    ```javascript
    (function process( /*RESTAPIRequest*/ request, /*RESTAPIResponse*/ response) {
        var apiKey = request.queryParams['apiKey'];
        var secret = '<secret>';
        if (apiKey == secret) {
            var event = request.body.data;
            var responseBody = {};
            if (event.data.context.activityLog.operationName == 'Microsoft.ServiceHealth/incident/action') {
                var inc = new GlideRecord('incident');
                var incidentExists = false;
                inc.addQuery('number', event.data.context.activityLog.properties.trackingId);
                inc.query();
                if (inc.hasNext()) {
                    incidentExists = true;
                    inc.next();
                } else {
                    inc.initialize();
                }
                var short_description = "Azure Service Health";
                if (event.data.context.activityLog.properties.incidentType == "Incident") {
                    short_description += " - Service Issue - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Maintenance") {
                    short_description += " - Planned Maintenance - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Information" || event.data.context.activityLog.properties.incidentType == "ActionRequired") {
                    short_description += " - Health Advisory - ";
                }
                short_description += event.data.context.activityLog.properties.title;
                inc.short_description = short_description;
                inc.description = event.data.context.activityLog.properties.communication;
                inc.work_notes = "Impacted subscription: " + event.data.context.activityLog.subscriptionId;
                if (incidentExists) {
                    if (event.data.context.activityLog.properties.stage == 'Active') {
                        inc.state = 2;
                    } else if (event.data.context.activityLog.properties.stage == 'Resolved') {
                        inc.state = 6;
                    } else if (event.data.context.activityLog.properties.stage == 'Closed') {
                        inc.state = 7;
                    }
                    inc.update();
                    responseBody.message = "Incident updated.";
                } else {
                    inc.number = event.data.context.activityLog.properties.trackingId;
                    inc.state = 1;
                    inc.impact = 2;
                    inc.urgency = 2;
                    inc.priority = 2;
                    inc.assigned_to = '<email>';
                    inc.assignment_group.setDisplayValue('<group>');
                    var subscriptionId = event.data.context.activityLog.subscriptionId;
                    var comments = "Azure portal Link: https://app.azure.com/h";
                    comments += "/" + event.data.context.activityLog.properties.trackingId;
                    comments += "/" + subscriptionId.substring(0, 3) + subscriptionId.slice(-3);
                    var impactedServices = JSON.parse(event.data.context.activityLog.properties.impactedServices);
                    var impactedServicesFormatted = "";
                    for (var i = 0; i < impactedServices.length; i++) {
                        impactedServicesFormatted += impactedServices[i].ServiceName + ": ";
                        for (var j = 0; j < impactedServices[i].ImpactedRegions.length; j++) {
                            if (j != 0) {
                                impactedServicesFormatted += ", ";
                            }
                            impactedServicesFormatted += impactedServices[i].ImpactedRegions[j].RegionName;
                        }

                        impactedServicesFormatted += "\n";

                    }
                    comments += "\n\nImpacted Services:\n" + impactedServicesFormatted;
                    inc.comments = comments;
                    inc.insert();
                    responseBody.message = "Incident created.";
                }
            } else {
                responseBody.message = "Hello from the other side!";
            }
            response.setBody(responseBody);
        } else {
            var unauthorized = new sn_ws_err.ServiceError();
            unauthorized.setStatus(401);
            unauthorized.setMessage('Invalid apiKey');
            response.setError(unauthorized);
        }
    })(request, response);
    ```

9.  在安全性选项卡中，取消选择“要求身份验证”，然后选择“提交”。 所设置的 `<secret>` 改为保护此 API。

    ![ServiceNow 中的“要求身份验证”复选框](./media/webhook-alerts/servicenow-resource-settings.png)

10.  回到“脚本 REST API”部分，此时会找到新 REST API 的“基础 API 路径”：

     ![ServiceNow 中的“基础 API 路径”](./media/webhook-alerts/servicenow-base-api-path.png)

11.  完整的集成 URL 如下所示：
        
         https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>


## <a name="create-an-alert-using-servicenow-in-the-azure-portal"></a>在 Azure 门户中使用 ServiceNow 创建警报
### <a name="for-a-new-action-group"></a>对于新操作组：
1. 按[此文](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md)中的步骤 1-8 操作，使用新操作组创建警报。

2. 在“操作”列表中定义：

    a. **操作类型：***Webhook*

    b. **详细信息：** 前面保存的 ServiceNow **集成 URL**。

    c. **名称：** Webhook 的名称、别名或标识符。

3. 警报创建完成后，选择“保存”。

### <a name="for-an-existing-action-group"></a>对于现有操作组：
1. 在 [Azure 门户](https://portal.azure.com/)中，选择“监视”。

2. 在“设置”部分中，选择“操作组”。

3. 找到要编辑的操作组并选择它。

4. 添加到“操作”列表：

    a. **操作类型：***Webhook*

    b. **详细信息：** 前面保存的 ServiceNow **集成 URL**。

    c. **名称：** Webhook 的名称、别名或标识符。

5. 操作组更新完成后，选择“保存”。

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>通过 HTTP POST 请求测试 Webhook 集成
1. 创建要发送的服务运行状况有效负载。 可以在 [Azure 活动日志警报的 Webhook](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md) 中找到示例服务运行状况 Webhook 有效负载。

2. 按如下所示创建 HTTP POST 请求：

    ```
    POST        https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
3. 此时会收到 `200 OK` 响应，其中包含消息“事件已创建”。

4. 转到 [ServiceNow](https://www.servicenow.com/)，确认集成已设置成功。

## <a name="next-steps"></a>后续步骤
- 了解如何[为现有问题管理系统配置 Webhook 通知](service-health-alert-webhook-guide.md)。
- 查看[活动日志警报 webhook 架构](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md)。 
- 了解[服务运行状况通知](../monitoring-and-diagnostics/monitoring-service-notifications.md)。
- 详细了解[操作组](../monitoring-and-diagnostics/monitoring-action-groups.md)。