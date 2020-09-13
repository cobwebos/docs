---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: ab9ca05db0e6def896ec242df74ae531d33293bf
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2020
ms.locfileid: "90021937"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应为虚拟机启用 Azure 备份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |启用 Azure 备份，确保对 Azure 虚拟机进行保护。 Azure 备份是一种安全且经济高效的数据保护解决方案，适用于 Azure。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|[将某个位置的 VM 的备份配置到同一位置中的现有中央保管库](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |此策略将给定位置的虚拟机上的 Azure 备份保护配置到同一位置中的现有中央保管库。 它仅适用于尚未配置备份的那些 VM。 建议将此策略分配给不超过 200 个 VM。 如果将此策略分配给超过 200 个 VM，它可能会导致备份在定义的计划过去几小时后才被触发。 此策略将进行增强以支持更多 VM 映像。 |deployIfNotExists、auditIfNotExists、disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json) |
|[将恢复服务保管库的诊断设置部署到资源专有类别的 Log Analytics 工作区。](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc717fb0c-d118-4c43-ab3d-ece30ac81fb3) |部署恢复服务保管库的诊断设置，以便将数据流式传输到资源专有类别的 Log Analytics 工作区。 如果未启用任何资源专有类别，则会新建诊断设置。 |deployIfNotExists |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/EnableRecoveryServiceVaultDiagnosticSetting_Backup_DeployIfNotExist.json) |
