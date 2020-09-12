---
title: Azure 服务的推荐策略
description: 描述如何查找和应用 azure 虚拟机等 Azure 服务的推荐策略。
ms.date: 09/02/2020
ms.topic: conceptual
ms.openlocfilehash: 919d09a569fd950ab2061ba3452a4a940d25cb5c
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89447770"
---
# <a name="recommended-policies-for-azure-services"></a>Azure 服务的推荐策略

不熟悉 Azure 策略的客户通常会寻找常见的策略定义来管理和控制其资源。 Azure 策略的 **推荐策略** 提供了一系列专门的常见策略定义。 为支持的资源 **推荐的策略** 体验嵌入到该资源的门户体验中。

有关其他 Azure 策略内置的详细，请参阅 [Azure 策略内置定义](../samples/built-in-policies.md)。

## <a name="azure-virtual-machines"></a>Azure 虚拟机

[Azure 虚拟机](../../../virtual-machines/index.yml)的**建议策略**位于虚拟机的 "**概述**" 页和 "**功能**" 选项卡下。在_Azure 策略_卡中，选择 "未配置" 或 "# 分配" 文本以打开带有建议策略的侧窗格。 已分配给虚拟机所属作用域的任何策略定义均为灰显。选择要应用于此虚拟机的建议策略，并选择 " **分配策略** "，为每个虚拟机创建分配。

随着组织组织 [其资源和资源层次结构](/azure/cloud-adoption-framework/ready/azure-best-practices/organize-subscriptions)的成熟，我们建议将这些策略分配从每个资源一个资源转换为订阅或 [管理组](../../management-groups/index.yml) 级别。

### <a name="azure-virtual-machines-recommended-policies"></a>Azure 虚拟机建议策略

|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[审核未配置灾难恢复的虚拟机](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |审核未配置灾难恢复的虚拟机。 若要详细了解灾难恢复，请访问 [https://aka.ms/asr-doc](https://aka.ms/asr-doc)。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |
|[审核未使用托管磁盘的 VM](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F06a78e20-9358-41c9-923c-fb736d382a4d) |此策略审核未使用托管磁盘的 VM |审核 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |
|[应为虚拟机启用 Azure 备份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |此策略可帮助审核是否为所有虚拟机启用了 Azure 备份服务。 Azure 备份是一个经济高效的一键式备份解决方案，可简化数据恢复，并且比其他云备份服务更易于启用。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="next-steps"></a>后续步骤

- 在 [Azure Policy 示例](../samples/index.md)中查看示例。
- 查看[了解策略效果](./effects.md)。
- 了解如何[修正不符合的资源](../how-to/remediate-resources.md)。