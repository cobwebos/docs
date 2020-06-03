---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/29/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 3dabcb0b27a19b275556135f73a0c504a8ad1648
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/31/2020
ms.locfileid: "84234854"
---
|名称 |说明 |效果 |版本 |GitHub |
|---|---|---|---|---|
|[将搜索服务的诊断设置部署到事件中心](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3d5da587-71bd-41f5-ac95-dd3330c2d58d) |在创建或更新缺少搜索服务的诊断设置的任何搜索服务时，将此诊断设置流式部署到区域事件中心。 |DeployIfNotExists、Disabled |2.0.0 |[链接](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Search_DeployDiagnosticLog_Deploy_EventHub.json)。 |
|[将搜索服务的诊断设置部署到 Log Analytics 工作区](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08ba64b8-738f-4918-9686-730d2ed79c7d) |在创建或更新缺少搜索服务的诊断设置的任何搜索服务时，将此诊断设置流式部署到区域 Log Analytics 工作区。 |DeployIfNotExists、Disabled |1.0.0 |[链接](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Search_DeployDiagnosticLog_Deploy_LogAnalytics.json)。 |
|[应启用搜索服务的诊断日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |审核是否已启用诊断日志。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists, 已禁用 |2.0.0 |[链接](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json)。 |
