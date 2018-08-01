---
title: 解释 Azure Monitor（预览版）中的 Azure Active Directory 审核日志架构 | Microsoft Docs
description: 介绍在 Azure Monitor（预览版）中使用的 Azure AD 审核日志架构
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: e1ae8e2a4dc9ef9c21300ebfc4df8c0f1c5819f2
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240343"
---
# <a name="interpret-the-azure-active-directory-audit-logs-schema-in-azure-monitor-preview"></a>解释 Azure Monitor（预览版）中的 Azure Active Directory 审核日志架构

本文介绍 Azure Monitor 中的 Azure AD 审核日志架构。 每个单独的日志条目都存储为文本，格式为 JSON blob，如以下两个示例所示。 

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-17T00:14:31.2585575Z", 
        "operationName": "Change password (self-service)",
        "operationVersion": "1.0",
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "resultDescription": "None", 
        "durationMs": "-1", 
        "correlationId": "60d5e89a-b890-413f-9e25-a047734afe9f", 
        "identity": "sreens@wingtiptoysonline.com", 
        "Level": "Informational", 
        "location": "WUS", 
        "properties": { 
            "identityType": "UPN", 
            "operationType": "Update", 
            "additionalDetails": "None", 
            "additionalTargets": "", 
            "targetUpdatedProperties": "", 
            "targetResourceType": "UPN__TenantContextID__PUID__ObjectID__ObjectClass", 
            "targetResourceName": "sreens@wingtiptoysonline.com__bf85dc9d-cb43-44a4-80c4-469e8c58249e__1003BFFD9FEB17DB__7a408bdd-7d97-4574-8511-dd747b56465d__User", 
            "auditEventCategory": "UserManagement" 
        } 
    } 
    ] 
} 
```

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-18T19:47:43.0368859Z", 
        "operationName": "Update service principal.", 
        "operationVersion": "1.0", 
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "durationMs": "-1", 
        "callerIpAddress": "<null>", 
        "correlationId": "14916c7a-5a7d-44e8-9b06-74b49efb08ee", 
        "identity": "NA", 
        "Level": "Informational", 
        "properties": { 
            "identityType": "NA", 
            "operationType": "Update", 
            "additionalDetails": {}, 
            "additionalTargets": "", 
            "targetUpdatedProperties": [ 
            { 
                "Name": "Included Updated Properties", 
                "OldValue": null, 
                "NewValue": "" 
            }, 
            { 
                "Name": "TargetId.ServicePrincipalNames", 
                "OldValue": null, 
                "NewValue": "http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29" 
            } 
            ], 
        "targetResourceType": "Other__ObjectID__ObjectClass__Name__AppId__SPN", 
        "targetResourceName": "ServicePrincipal_ea70a262-4da3-440a-b396-9734ddfd9df2__ea70a262-4da3-440a-b396-9734ddfd9df2__ServicePrincipal__Salesforce__cd3ed3de-93ee-400b-8b19-b61ef44a0f29__http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29", 
        "auditEventCategory": "ApplicationManagement" 
      } 
    } 
    ] 
} 
```

| 字段名称 | Description |
|------------|-------------|
| time       | 日期和时间 (UTC) |
| operationName | 操作的名称 |
| operationVersion | 客户端请求的 REST API 版本 |
| category | 目前，“审核”是唯一支持的值 |
| tenantId | 与日志关联的租户 Guid |
| resultType | 操作的结果，可以是“成功”或“失败” |
| resultSignature |  此项未映射，可以放心地忽略此字段。 | 
| resultDescription | 结果的附加说明（如果有） | 
| durationMs |  此项未映射，可以放心地忽略此字段。 |
| callerIpAddress | 发出请求的客户端的 IP 地址 | 
| correlationId | 客户端所传递的可选 Guid。 此项可以帮助将客户端操作与服务器端操作相关联，并且在跟踪跨服务的日志时非常有用。 |
| identity | 发出请求时提供的令牌中的标识。 可以是用户帐户、系统帐户或服务主体。 |
| 级别 | 消息类型。 对于审核日志，此项始终为“信息” |
| location | 数据中心的位置 |
| 属性 | 列出与审核日志相关的受支持属性。 有关详细信息，请参阅下表。 | 


| 属性名称 | Description |
|---------------|-------------|
| AuditEventCategory | 审核事件的类型。 可以是“用户管理”、“应用程序管理”等。|
| 标识类型 | “应用程序”或“用户” |
| 操作类型 | 可以是“添加”、“更新”、“删除”或“其他” |
| 目标资源类型 | 指定已对其执行操作的目标资源类型。 可以是“应用程序”、“用户”、“角色”、“策略” | 
| 目标资源名称 | 目标资源的名称。 例如，此项可以是应用程序名称、角色名称、用户主体名称或服务主体名称 |
| additionalTargets | 列出特定操作的任何其他属性。 例如，对于更新操作，旧值和新值在 *targetUpdatedProperties* 下列出 | 

## <a name="next-steps"></a>后续步骤

* [解释 Azure Monitor 中的登录日志架构](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [详细了解 Azure 诊断日志](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [常见问题解答和已知的问题](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)