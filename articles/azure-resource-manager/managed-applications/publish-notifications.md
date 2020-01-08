---
title: 托管应用和通知
description: 将托管应用程序配置为 webhook 终结点，以接收有关在托管应用程序实例上创建、更新、删除和错误的通知。
ms.topic: conceptual
ms.author: ilahat
author: ilahat
ms.date: 11/01/2019
ms.openlocfilehash: b33366b65fed0042eb3024c2264bce1c4a1c4c1d
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75651626"
---
# <a name="azure-managed-applications-with-notifications"></a>带有通知的 Azure 托管应用程序

Azure 托管应用程序通知允许发布者根据托管应用程序实例的生命周期事件自动执行操作。 发布者可以指定自定义通知 webhook 终结点，以接收有关新的和现有的托管应用程序实例的事件通知。 发布者可以在应用程序预配、更新和删除时设置自定义工作流。

## <a name="getting-started"></a>入门
若要开始接收托管应用程序，请启动一个公共 HTTPS 终结点，并在发布服务目录应用程序定义或 Azure Marketplace 产品/服务时指定它。

下面是快速入门的建议步骤：
1. 加速公共 HTTPS 终结点，该终结点记录传入的 POST 请求并返回 `200 OK`。
2. 如本文后面所述，将终结点添加到服务目录应用程序定义或 Azure Marketplace 产品/服务。
3. 创建引用应用程序定义或 Azure Marketplace 产品/服务的托管应用程序实例。
4. 验证是否正在接收通知。
5. 启用授权，如本文的 "**终结点身份验证**" 一节中所述。
6. 按照本文的**通知架构**部分中的说明进行操作，以分析通知请求并根据通知实现业务逻辑。

## <a name="add-service-catalog-application-definition-notifications"></a>添加服务目录应用程序定义通知
#### <a name="azure-portal"></a>Azure 门户
若要开始使用，请参阅[通过 Azure 门户发布服务目录应用程序](./publish-portal.md)。

![Azure 门户中的服务目录应用程序定义通知](./media/publish-notifications/service-catalog-notifications.png)

#### <a name="rest-api"></a>REST API

> [!NOTE]
> 目前，只能在应用程序定义属性的 `notificationEndpoints` 中提供一个终结点。

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
## <a name="add-azure-marketplace-managed-application-notifications"></a>添加 Azure Marketplace 托管应用程序通知
有关详细信息，请参阅[创建 Azure 应用程序产品/服务](../../marketplace/cloud-partner-portal/azure-applications/cpp-create-offer.md)。

![Azure 门户中的 Azure Marketplace 托管应用程序通知](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>事件触发器
下表描述了事件可能与 ProvisioningState 及其触发器的所有可能组合：

EventType | ProvisioningState | 通知触发器
---|---|---
PUT | 已接受 | 在应用程序 PUT 之后（在启动托管资源组中的部署之前），已成功创建并投影了托管资源组。
PUT | 已成功 | 完成托管应用程序的完全预配后成功完成。
PUT | 已失败 | 在任何时候都不能设置应用程序实例。
PATCH | 已成功 | 在托管应用程序实例上成功修补后，以更新标记、JIT 访问策略或托管标识。
DELETE | 正在删除 | 一旦用户启动了托管应用程序实例的删除。
DELETE | 已删除 | 在完全和成功删除托管应用程序之后。
DELETE | 已失败 | 在取消预配过程中的任何错误之后阻止删除。
## <a name="notification-schema"></a>通知架构
当您加速 webhook 终结点以处理通知时，您需要分析有效负载以获取重要属性，然后对通知进行操作。 服务目录和 Azure Marketplace 托管应用程序通知提供很多相同的属性。 示例的下表中概述了两个小差异。

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

如果预配失败，将向指定的终结点发送一条包含错误详细信息的通知。

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

#### <a name="azure-marketplace-application-notification-schema"></a>Azure Marketplace 应用程序通知架构

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

如果预配失败，将向指定的终结点发送一条包含错误详细信息的通知。

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

参数 | Description
---|---
eventType | 触发通知的事件类型。 （例如 PUT、PATCH、DELETE。）
applicationId | 触发通知的托管应用程序的完全限定的资源标识符。
EventTime | 触发通知的事件的时间戳。 （UTC ISO 8601 格式的日期和时间。）
provisioningState | 托管应用程序实例的设置状态。 （例如，成功、失败、删除、删除。）
error | *仅在 ProvisioningState 失败时指定*。 包含错误代码、消息以及导致失败的问题的详细信息。
applicationDefinitionId | *仅为服务目录托管应用程序指定*。 表示为其预配了托管应用程序实例的应用程序定义的完全限定的资源标识符。
计划 | *仅适用于 Azure Marketplace 托管应用程序*。 表示托管应用程序实例的发布者、产品/服务、SKU 和版本。
billingDetails | *仅适用于 Azure Marketplace 托管应用程序。* 托管应用程序实例的计费详细信息。 包含可用于在 Azure Marketplace 中查询使用情况详细信息的 resourceUsageId。

## <a name="endpoint-authentication"></a>终结点身份验证
保护 webhook 终结点并确保通知的真实性：
1. 在 webhook URI 的顶层提供一个查询参数，如下所示： https://your-endpoint.com?sig=Guid 。 对于每个通知，请检查查询参数 `sig` 是否 `Guid`了预期的值。
2. 使用 applicationId 在托管应用程序实例上发出 GET。 验证 provisioningState 是否符合通知的 provisioningState，以确保一致性。

## <a name="notification-retries"></a>通知重试次数

托管应用程序通知服务需要从 webhook 终结点到通知的 `200 OK` 响应。 如果 webhook 终结点返回的 HTTP 错误代码大于或等于500，则通知服务将重试; 如果返回的错误代码为429，则为; 如果该终结点暂时无法访问，则为。 如果在10小时内不能使用 webhook 终结点，则会删除通知消息，并重试将停止。
