---
title: 提供通知的托管应用
description: 使用 Webhook 终结点配置托管应用程序，以接收有关托管应用程序实例上的创建、更新、删除操作和错误的通知。
ms.topic: conceptual
ms.author: ilahat
author: ilahat
ms.date: 11/01/2019
ms.openlocfilehash: 3632a34678c7a0f0e6fa93e5ce8000b07bb413a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86054519"
---
# <a name="azure-managed-applications-with-notifications"></a>提供通知的 Azure 托管应用程序

Azure 托管应用程序通知可让发布者根据托管应用程序实例的生命周期事件自动执行操作。 发布者可以指定自定义通知 Webhook 终结点，以接收有关新的和现有的托管应用程序实例的事件通知。 发布者可以在预配、更新和删除应用程序时设置自定义工作流。

## <a name="getting-started"></a>入门
若要开始接收托管应用程序，请启动公共 HTTPS 终结点，并在发布服务目录应用程序定义或 Azure 市场套餐时指定该终结点。

下面是建议的快速入门步骤：
1. 启动一个公共 HTTPS 终结点，用于记录传入的 POST 请求并返回 `200 OK`。
2. 如本文稍后所述，将该终结点添加到服务目录应用程序定义或 Azure 市场套餐。
3. 创建一个引用应用程序定义或 Azure 市场套餐的托管应用程序实例。
4. 验证是否正在接收通知。
5. 根据本文的“终结点身份验证”部分所述启用授权。 
6. 按照本文的“通知架构”部分中的说明分析通知请求，并根据通知实现业务逻辑。 

## <a name="add-service-catalog-application-definition-notifications"></a>添加服务目录应用程序定义通知
#### <a name="azure-portal"></a>Azure 门户
若要开始，请参阅[通过 Azure 门户发布服务目录应用程序](./publish-portal.md)。

![Azure 门户中的服务目录应用程序定义通知](./media/publish-notifications/service-catalog-notifications.png)

#### <a name="rest-api"></a>REST API

> [!NOTE]
> 目前，只能在应用程序定义属性中的 `notificationEndpoints` 内提供一个终结点。

``` JSON
    {
      "properties": {
        "isEnabled": true,
        "lockLevel": "ReadOnly",
        "displayName": "Sample Application Definition",
        "description": "Notification-enabled application definition.",
        "notificationPolicy": {
            "notificationEndpoints": [
                {
                    "uri": "https://isv.azurewebsites.net:1214?sig=unique_token"
                }
            ]
        },
        "authorizations": [
          {
            "principalId": "d6b7fbd3-4d99-43fe-8a7a-f13aef11dc18",
            "roleDefinitionId": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635"
          },
        ...

```
## <a name="add-azure-marketplace-managed-application-notifications"></a>添加 Azure 市场托管应用程序通知
有关详细信息，请参阅[创建 Azure 应用程序套餐](../../marketplace/partner-center-portal/create-new-azure-apps-offer.md)。

![Azure 门户中的 Azure 市场托管应用程序通知](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>事件触发器
下表描述了 EventType 和 ProvisioningState 的所有可能组合及其触发器：

EventType | ProvisioningState | 通知的触发器
---|---|---
PUT | 已接受 | 在应用程序 PUT 之后（启动托管资源组中的部署之前）已成功创建并投影托管资源组。
PUT | 已成功 | PUT 之后完全预配托管应用程序成功。
PUT | 失败 | 在任意时间点 PUT 应用程序实例预配失败。
PATCH | 已成功 | 在托管应用程序实例上成功执行 PATCH 以更新标记、JIT 访问策略或托管标识之后发生。
DELETE | 正在删除 | 在用户启动托管应用程序实例的 DELETE 后立即发生。
DELETE | Deleted | 在完全成功删除托管应用程序之后发生。
DELETE | 失败 | 在取消预配过程中出现任何阻止删除的错误之后发生。
## <a name="notification-schema"></a>通知架构
在启动 Webhook 终结点来处理通知时，需要分析有效负载，以获取重要属性，然后再处理通知。 服务目录和 Azure 市场托管应用程序通知提供许多相同的属性。 示例后面的表格中概述了两个细微的差异。

#### <a name="service-catalog-application-notification-schema"></a>服务目录应用程序通知架构
下面是在成功预配托管应用程序实例后的服务目录通知示例：
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>"    
}

```

如果预配失败，会将包含错误详细信息的通知发送到指定的终结点。

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>",
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

#### <a name="azure-marketplace-application-notification-schema"></a>Azure 市场应用程序通知架构

下面是在成功预配托管应用程序实例后的服务目录通知示例：
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    }
}

```

如果预配失败，会将包含错误详细信息的通知发送到指定的终结点。

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    },
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

参数 | 说明
---|---
eventType | 触发通知的事件类型。 （例如 PUT、PATCH、DELETE。）
applicationId | 对其触发了通知的托管应用程序的完全限定资源标识符。
EventTime | 触发通知的事件的时间戳。 （UTC ISO 8601 格式的日期和时间。）
provisioningState | 托管应用程序实例的预配状态。 （例如 Succeeded、Failed、Deleting、Deleted。）
error | 仅当 provisioningState 为 Failed 时指定。  包含导致失败的问题的错误代码、消息和详细信息。
applicationDefinitionId | 仅为服务目录托管应用程序指定。  表示为其预配了托管应用程序实例的应用程序定义的完全限定资源标识符。
计划 | 仅为 Azure 市场托管应用程序指定。  表示托管应用程序实例的发布者、套餐、SKU 和版本。
billingDetails | 仅为 Azure 市场托管应用程序指定。  托管应用程序实例的计费详细信息。 包含可用于在 Azure 市场中查询使用情况详细信息的 resourceUsageId。

## <a name="endpoint-authentication"></a>终结点身份验证
若要保护 Webhook 终结点并确保通知的真实性：
1. 在 Webhook URI 的基础上提供一个查询参数，如下所示：https\://your-endpoint.com?sig=Guid。 对于每个通知，请检查查询参数 `sig` 是否包含预期值 `Guid`。
2. 使用 applicationId 对托管应用程序实例发出 GET。 验证 provisioningState 是否与通知的 provisioningState 相匹配，以确保一致性。

## <a name="notification-retries"></a>通知重试

托管应用程序通知服务预期 Webhook 终结点在通知中返回 `200 OK` 响应。 如果 Webhook 终结点返回大于或等于 500 的 HTTP 错误代码、返回错误代码 429，或暂时不可访问，则通知服务将会重试。 如果 Webhook 终结点在 10 小时内一直不可用，则会删除通知消息，并且重试将会停止。
