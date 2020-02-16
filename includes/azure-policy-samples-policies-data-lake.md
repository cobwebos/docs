---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 645494688ed1fb2a29efa083d50e9456f6d2b1b4
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170065"
---
|名称 |说明 |作用 |版本 |
|---|---|---|---|
|[应启用 Azure Data Lake Store 的诊断日志](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |审核是否已启用诊断日志。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists, 已禁用 |2.0.0 |
|[应启用 Data Lake Analytics 的诊断日志](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |审核是否已启用诊断日志。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists, 已禁用 |2.0.0 |
|[要求对 Data Lake Store 帐户进行加密](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Data%20Lake/DataLakeStoreEncryption_Deny.json) |此策略可确保对所有 Data Lake Store 帐户启用了加密 |deny |1.0.0 |
