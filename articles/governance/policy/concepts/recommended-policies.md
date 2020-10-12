---
title: Azure 服务的推荐策略
description: 介绍如何查找和应用 Azure 服务（例如 Azure 虚拟机）的推荐策略。
ms.date: 09/02/2020
ms.topic: conceptual
ms.openlocfilehash: 919d09a569fd950ab2061ba3452a4a940d25cb5c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89447770"
---
# <a name="recommended-policies-for-azure-services"></a>Azure 服务的推荐策略

不熟悉 Azure Policy 的客户通常会寻找常见的策略定义来管理和治理其资源。 Azure Policy 的推荐策略提供了重要的常见策略定义列表以帮助你开始使用。 支持资源的门户体验中嵌入了该资源的推荐策略体验。

有关其他 Azure Policy 内置定义，请参阅 [Azure Policy 内置定义](../samples/built-in-policies.md)。

## <a name="azure-virtual-machines"></a>Azure 虚拟机

[Azure 虚拟机](../../../virtual-machines/index.yml)的推荐策略位于虚拟机“概述”页面的“功能”选项卡下方  。在 Azure Policy 卡中，选择“未配置”或“分配的编号”文本，以打开具有推荐策略的侧窗格。 已分配到虚拟机所属作用域的任何策略定义均呈灰显。选择推荐策略以应用于该虚拟机，然后选择“分配策略”以分配每一个策略。

当组织可以成熟地[组织其资源和资源层次结构](/azure/cloud-adoption-framework/ready/azure-best-practices/organize-subscriptions)时，建议将这些策略分配从每个资源分配一个策略转换为订阅或[管理组](../../management-groups/index.yml)级别。

### <a name="azure-virtual-machines-recommended-policies"></a>Azure 虚拟机推荐策略

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[审核未配置灾难恢复的虚拟机](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |审核未配置灾难恢复的虚拟机。 若要详细了解灾难恢复，请访问 [https://aka.ms/asr-doc](https://aka.ms/asr-doc)。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |
|[审核未使用托管磁盘的 VM](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F06a78e20-9358-41c9-923c-fb736d382a4d) |此策略审核未使用托管磁盘的 VM |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |
|[应为虚拟机启用 Azure 备份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |此策略可帮助审核是否为所有虚拟机启用了 Azure 备份服务。 Azure 备份是一个经济高效的一键式备份解决方案，可简化数据恢复，并且比其他云备份服务更易于启用。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="next-steps"></a>后续步骤

- 在 [Azure Policy 示例](../samples/index.md)中查看示例。
- 查看[了解策略效果](./effects.md)。
- 了解如何[修正不符合的资源](../how-to/remediate-resources.md)。