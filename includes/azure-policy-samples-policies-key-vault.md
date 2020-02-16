---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 53494bc9642a3df91492d2353a6aa3c6875c1fff
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170125"
---
|名称 |说明 |作用 |版本 |
|---|---|---|---|
|[将 Key Vault 的诊断设置部署到事件中心](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Key%20Vault/KeyVault_DiagnosticLog_Deploy.json) |创建或更新缺少此诊断设置的任何 Key Vault 时，部署 Key Vault 的诊断设置，以便流式传输到区域事件中心。 |deployIfNotExists |1.0.0 |
|[应启用 Key Vault 的诊断日志](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |审核是否已启用诊断日志。 这样便可以在发生安全事件或网络受到威胁时重新创建活动线索以用于调查目的 |AuditIfNotExists, 已禁用 |1.0.0 |
|[密钥保管库对象应可恢复](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |此策略审核密钥保管库对象是否不可恢复。 软删除功能有助于在给定的保留期（90 天）内有效地保留资源，即使在 DELETE 操作之后也是如此，同时提供对象已被删除的外观。 启用“清除保护”后，在长达 90 天的保留期到期之前，不能清除处于已删除状态的保管库或对象。 这些保管库和对象仍然可以恢复，从而向客户保证将遵循保留策略。 |Audit, 已禁用 |1.0.0 |
