---
title: Azure 监视器中的登录日志架构 |微软文档
description: 描述日志架构中的 Azure AD 标志，以便在 Azure 监视器中使用
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
ms.openlocfilehash: 5525f2f8ab4ef83ba9c3aeeff945bc9d875600d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75748662"
---
# <a name="interpret-the-azure-ad-sign-in-logs-schema-in-azure-monitor"></a>在 Azure 监视器中解释 Azure AD 登录日志架构

本文介绍 Azure Monitor 中的 Azure Active Directory (Azure AD) 登录日志架构。 与登录相关的大多数信息都在 `records` 对象的 Properties 属性下提供**。


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
| ResourceId | 此值未映射，可以放心地忽略此字段。  |
| OperationName | 对于登录，此值始终为“登录活动”**。 |
| OperationVersion | 客户端请求的 REST API 版本。 |
| 类别 | 对于登录，此值始终为“登录”**。 | 
| TenantId | 与日志关联的租户 GUID。 |
| ResultType | 登录操作的结果，可以是“成功”或“失败”****。 | 
| ResultSignature | 包含登录操作的错误代码（如果有）。 |
| ResultDescription | 提供登录操作的错误说明。 |
| 风险详情 | 风险详情 | 提供风险用户的特定状态、登录或风险检测背后的"原因"。 `none`可能的值是： 、 `adminGeneratedTemporaryPassword` `userPerformedSecuredPasswordChange`、 `userPerformedSecuredPasswordReset` `adminConfirmedSigninSafe`、 `aiConfirmedSigninSafe` `userPassedMFADrivenByRiskBasedPolicy`、 `adminDismissedAllRiskForUser` `adminConfirmedSigninCompromised`、 `unknownFutureValue`、 、 、 、 、 、 、 、 、 、 、 、 该值`none`表示到目前为止未对用户或登录执行任何操作。 <br>**注：** 此属性的详细信息需要 Azure AD 高级 P2 许可证。 其他许可证返回值`hidden`。 |
| 风险事件类型 | 风险事件类型 | 与登录关联的风险检测类型。 可能`unlikelyTravel`的值是： `anonymizedIPAddress`、 `maliciousIPAddress`、 `unfamiliarFeatures` `malwareInfectedIPAddress`、 `suspiciousIPAddress` `leakedCredentials`、 `investigationsThreatIntelligence` `generic`、 、 、 、 、 、 、 `unknownFutureValue`、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 |
| 风险级别聚合 | 风险级别 | 聚合风险级别。 可能的值是`none`： `low`、 `medium`、 `high` `hidden`、 `unknownFutureValue`、 、 和 。 该值`hidden`表示未为 Azure AD 标识保护启用用户或登录。 **注：** 此属性的详细信息仅适用于 Azure AD 高级 P2 客户。 所有其他客户将被退回`hidden`。 |
| 风险级别期间Signin | 风险级别 | 登录期间的风险级别。 可能的值是`none`： `low`、 `medium`、 `high` `hidden`、 `unknownFutureValue`、 、 和 。 该值`hidden`表示未为 Azure AD 标识保护启用用户或登录。 **注：** 此属性的详细信息仅适用于 Azure AD 高级 P2 客户。 所有其他客户将被退回`hidden`。 |
| 风险状态 | 风险状态 | 报告风险用户的状态、登录或风险检测。 可能的值`none`是： 、 `confirmedSafe` `remediated`、 `dismissed` `atRisk`、 `confirmedCompromised` `unknownFutureValue`、 、 、 、 、 、 、 |
| DurationMs |  此值未映射，可以放心地忽略此字段。 |
| CallerIpAddress | 发出请求的客户端的 IP 地址。 | 
| CorrelationId | 客户端所传递的可选 GUID。 此值可帮助将客户端操作与服务器端操作关联，并且在跟踪跨服务的日志时非常有用。 |
| 标识 | 发出请求时提供的令牌中的标识。 可以是用户帐户、系统帐户或服务主体。 |
| 级别 | 提供消息的类型。 对于审核，它始终是“信息”**。 |
| 位置 | 提供登录活动的位置。 |
| 属性 | 列出与登录关联的所有属性。有关详细信息，请参阅[Microsoft 图形 API 参考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)。 为提高可读性，此架构使用登录资源中的相同属性名。

## <a name="next-steps"></a>后续步骤

* [解释 Azure 监视器中的审核日志架构](reference-azure-monitor-audit-log-schema.md)
* [阅读有关 Azure 诊断日志的更多信息](../../azure-monitor/platform/platform-logs-overview.md)
