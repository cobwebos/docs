---
title: Azure Monitor 中的 Azure Active Directory 登录日志架构 |Microsoft Docs
description: 描述在 Azure Monitor 中使用的 Azure AD 登录日志架构
services: active-directory
documentationcenter: ''
author: cawrites
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
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cc07ac2644ac9f97146e980a1961b9b84e7c561
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127054"
---
# <a name="interpret-the-azure-ad-sign-in-logs-schema-in-azure-monitor"></a>解释中的 Azure AD 登录日志架构 Azure Monitor

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

| 字段名称 | 描述 |
|------------|-------------|
| Time | 日期和时间 (UTC)。 |
| resourceId | 此值未映射，可以放心地忽略此字段。  |
| OperationName | 对于登录，此值始终为“登录活动”。 |
| OperationVersion | 客户端请求的 REST API 版本。 |
| 类别 | 对于登录，此值始终为“登录”。 | 
| TenantId | 与日志关联的租户 GUID。 |
| ResultType | 登录操作的结果，可以是“成功”或“失败”。 | 
| ResultSignature | 包含登录操作的错误代码（如果有）。 |
| ResultDescription | 提供登录操作的错误说明。 |
| riskDetail | riskDetail | 提供风险用户、登录或风险检测的特定状态背后的 "原因"。 可能的值包括: `none`、 `adminGeneratedTemporaryPassword` `userPerformedSecuredPasswordReset` `adminConfirmedSigninSafe` 、`adminDismissedAllRiskForUser`、、、 、、、和`aiConfirmedSigninSafe` 。`unknownFutureValue` `userPerformedSecuredPasswordChange` `userPassedMFADrivenByRiskBasedPolicy` `adminConfirmedSigninCompromised` 值`none`表示尚未对用户执行任何操作或目前未登录。 <br>**注意：** 此属性的详细信息需要 Azure AD Premium P2 许可证。 其他许可证返回值`hidden`。 |
| riskEventTypes | riskEventTypes | 与登录关联的风险检测类型。 可能的值包括: `unlikelyTravel`、 `anonymizedIPAddress` `unfamiliarFeatures` `malwareInfectedIPAddress` 、`generic`、、、 `suspiciousIPAddress` 、、`unknownFutureValue`、和。 `investigationsThreatIntelligence` `maliciousIPAddress` `leakedCredentials` |
| riskLevelAggregated | riskLevel | 聚合的风险级别。 可能的值包括: `none`、 `low`、 `medium`、 `high`、 `hidden`和。 `unknownFutureValue` 该值`hidden`表示没有为用户或登录启用 Azure AD Identity Protection。 **注意：** 此属性的详细信息仅适用于 Azure AD Premium P2 客户。 将返回`hidden`所有其他客户。 |
| riskLevelDuringSignIn | riskLevel | 登录过程中的风险级别。 可能的值包括: `none`、 `low`、 `medium`、 `high`、 `hidden`和。 `unknownFutureValue` 该值`hidden`表示没有为用户或登录启用 Azure AD Identity Protection。 **注意：** 此属性的详细信息仅适用于 Azure AD Premium P2 客户。 将返回`hidden`所有其他客户。 |
| riskState | riskState | 报告风险用户、登录或风险检测的状态。 可能的值包括: `none`、 `confirmedSafe`、 `remediated`、 `dismissed`、 `atRisk`、 `confirmedCompromised`和。`unknownFutureValue` |
| DurationMs |  此值未映射，可以放心地忽略此字段。 |
| CallerIpAddress | 发出请求的客户端的 IP 地址。 | 
| CorrelationId | 客户端所传递的可选 GUID。 此值可帮助将客户端操作与服务器端操作关联，并且在跟踪跨服务的日志时非常有用。 |
| 标识 | 发出请求时提供的令牌中的标识。 可以是用户帐户、系统帐户或服务主体。 |
| Level | 提供消息的类型。 对于审核，它始终是“信息”。 |
| Location | 提供登录活动的位置。 |
| 属性 | 列出与登录关联的所有属性。有关详细信息，请参阅 [Microsoft Graph API 参考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)。 为提高可读性，此架构使用登录资源中的相同属性名。

## <a name="next-steps"></a>后续步骤

* [解释 Azure Monitor 中的审核日志架构](reference-azure-monitor-audit-log-schema.md)
* [详细了解 Azure 诊断日志](../../azure-monitor/platform/diagnostic-logs-overview.md)
