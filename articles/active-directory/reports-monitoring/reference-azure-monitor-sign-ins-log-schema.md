---
title: Azure Monitor 中的 azure Active Directory 登录日志架构 |Microsoft Docs
description: 介绍日志架构中使用 Azure Monitor 中为 Azure AD 登录
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8ac6c56dca100ea9836158f46881c4eb12213e1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60285194"
---
# <a name="interpret-the-azure-ad-sign-in-logs-schema-in-azure-monitor"></a>解释 Azure Monitor 中的 Azure AD 登录日志架构

本文介绍 Azure Monitor 中的 Azure Active Directory (Azure AD) 登录日志架构。 与登录相关的大多数信息都在 `records` 对象的 Properties 属性下提供。


```json
{ 
    "time": "2019-03-12T16:02:15.5522137Z", 
    "resourceId": "/tenants/<TENANT ID>/providers/Microsoft.aadiam",
    "operationName": "Sign-in activity", 
    "operationVersion": "1.0", 
    "category": "SignInLogs", 
    "tenantId": "<TENANT ID>", 
    "resultType": "50140", 
    "resultSignature": "None", 
    "resultDescription": "This error occurred due to 'Keep me signed in' interrupt when the user was signing-in.", 
    "durationMs": 0, 
    "callerIpAddress": "<CALLER IP ADDRESS>", 
    "correlationId": "a75a10bd-c126-486b-9742-c03110d36262", 
    "identity": "Timothy Perkins", 
    "Level": 4, 
    "location": "US", 
    "properties": 
        {
            "id":"0231f922-93fa-4005-bb11-b344eca03c01",
            "createdDateTime":"2019-03-12T16:02:15.5522137+00:00",
            "userDisplayName":"Timothy Perkins",
            "userPrincipalName":"<USER PRINCIPAL NAME>",
            "userId":"<USER ID>",
            "appId":"<APPLICATION ID>",
            "appDisplayName":"Azure Portal",
            "ipAddress":"<IP ADDRESS>",
            "status":
            {
                "errorCode":50140,
                "failureReason":"This error occurred due to 'Keep me signed in' interrupt when the user was signing-in."
            },
            "clientAppUsed":"Browser",
            "deviceDetail":
            {
                "operatingSystem":"Windows 10",
                "browser":"Chrome 72.0.3626"
            },
            "location":
                {
                    "city":"Bellevue",
                    "state":"Washington",
                    "countryOrRegion":"US",
                    "geoCoordinates":
                    {
                        "latitude":45,
                        "longitude":122
                    }
                },
            "correlationId":"a75a10bd-c126-486b-9742-c03110d36262",
            "conditionalAccessStatus":"notApplied",
            "appliedConditionalAccessPolicies":
            [
                {
                    "id":"ae11ffaa-9879-44e0-972c-7538fd5c4d1a",
                    "displayName":"Hr app access policy",
                    "enforcedGrantControls":
                    [
                        "Mfa"
                    ],
                    "enforcedSessionControls":
                    [
                    ],
                    "result":"notApplied"
                },
                {
                    "id":"b915a70b-2eee-47b6-85b6-ff4f4a66256d",
                    "displayName":"MFA for all but global support access",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"830f27fa-67a8-461f-8791-635b7225caf1",
                    "displayName":"Header Based Application Control",
                    "enforcedGrantControls":["Mfa"],
                    "enforcedSessionControls":[],
                    "result":"notApplied"
                },
                {
                    "id":"8ed8d7f7-0a2e-437b-b512-9e47bed562e6",
                    "displayName":"MFA for everyones",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"52924e0f-798b-4afd-8c42-49055c7d6395",
                    "displayName":"Device compliant",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
             ],
            "isInteractive":true,
            "tokenIssuerType":"AzureAD",
            "authenticationProcessingDetails":[],
            "networkLocationDetails":[],
            "processingTimeInMilliseconds":0,
            "riskDetail":"hidden",
            "riskLevelAggregated":"hidden",
            "riskLevelDuringSignIn":"hidden",
            "riskState":"none",
            "riskEventTypes":[],
            "resourceDisplayName":"windows azure service management api",
            "resourceId":"797f4846-ba00-4fd7-ba43-dac1f8f63013",
            "authenticationMethodsUsed":[]
        }
}
```


## <a name="field-descriptions"></a>字段说明

| 字段名 | 描述 |
|------------|-------------|
| 时间 | 日期和时间 (UTC)。 |
| resourceId | 此值未映射，可以放心地忽略此字段。  |
| OperationName | 对于登录，此值始终为“登录活动”。 |
| OperationVersion | 客户端请求的 REST API 版本。 |
| 类别 | 对于登录，此值始终为“登录”。 | 
| TenantId | 与日志关联的租户 GUID。 |
| ResultType | 登录操作的结果，可以是“成功”或“失败”。 | 
| ResultSignature | 包含登录操作的错误代码（如果有）。 |
| ResultDescription | 提供登录操作的错误说明。 |
| DurationMs |  此值未映射，可以放心地忽略此字段。|
| CallerIpAddress | 发出请求的客户端的 IP 地址。 | 
| CorrelationId | 客户端所传递的可选 GUID。 此值可帮助将客户端操作与服务器端操作关联，并且在跟踪跨服务的日志时非常有用。 |
| 标识 | 发出请求时提供的令牌中的标识。 可以是用户帐户、系统帐户或服务主体。 |
| 级别 | 提供消息的类型。 对于审核，它始终是“信息”。 |
| Location | 提供登录活动的位置。 |
| 属性 | 列出与登录关联的所有属性。有关详细信息，请参阅 [Microsoft Graph API 参考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)。 为提高可读性，此架构使用登录资源中的相同属性名。

## <a name="next-steps"></a>后续步骤

* [解释 Azure Monitor 中的审核日志架构](reference-azure-monitor-audit-log-schema.md)
* [详细了解 Azure 诊断日志](../../azure-monitor/platform/diagnostic-logs-overview.md)