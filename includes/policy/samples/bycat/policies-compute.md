---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/05/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: ff2c937414da4496ec23fbb22988a4c5c320a5f3
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82837922"
---
|名称 |说明 |效果 |版本 |GitHub |
|---|---|---|---|---|
|[允许的虚拟机 SKU](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcccc23c7-8427-4f53-ad12-b6a63eb452b3) |使用此策略，你可以指定组织可部署的一组虚拟机 SKU。 |拒绝 |1.0.0 |[链接](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMSkusAllowed_Deny.json)。 |
|[审核未配置灾难恢复的虚拟机](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |审核未配置灾难恢复的虚拟机。 若要详细了解灾难恢复，请访问 [https://aka.ms/asr-doc](https://aka.ms/asr-doc)。 |auditIfNotExists |1.0.0 |[链接](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json)。 |
|[审核未使用托管磁盘的 VM](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F06a78e20-9358-41c9-923c-fb736d382a4d) |此策略审核未使用托管磁盘的 VM |审核 |1.0.0 |[链接](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json)。 |
|[为 Windows Server 部署默认 Microsoft IaaSAntimalware 扩展](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2835b622-407b-4114-9198-6f7064cbe0dc) |如果 VM 未配置反恶意软件扩展，则此策略部署使用默认配置的 Microsoft IaaSAntimalware 扩展。 |deployIfNotExists |1.0.0 |[链接](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMAntimalwareExtension_Deploy.json)。 |
|[应当启用虚拟机规模集中的诊断日志](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |建议启用日志，以便在出现某个事件或遭到入侵后需要进行调查时可以重新创建活动线索。 |AuditIfNotExists、Disabled |1.0.0 |[链接](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json)。 |
|[Microsoft Antimalware for Azure 应配置为自动更新保护签名](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc43e4a30-77cb-48ab-a4dd-93f175c63b57) |此策略会审核所有未配置自动更新 Microsoft Antimalware 保护签名的 Windows 虚拟机。 |AuditIfNotExists、Disabled |1.0.0 |[链接](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_AntiMalwareAutoUpdate_AuditIfNotExists.json)。 |
|[应在 Windows Server 上部署 Microsoft IaaSAntimalware 扩展](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9b597639-28e4-48eb-b506-56b05d366257) |此策略会审核所有未部署 Microsoft IaaSAntimalware 扩展的 Windows Server VM。 |AuditIfNotExists、Disabled |1.0.0 |[链接](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/WindowsServers_AntiMalware_AuditIfNotExists.json)。 |
|[应当仅安装已批准的 VM 扩展](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc0e996f8-39cf-4af9-9f45-83fbde810432) |此策略约束未获批准的虚拟机扩展。 |Audit、Deny、Disabled |1.0.0 |[链接](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_ApprovedExtensions_Audit.json)。 |
|[要求自动在虚拟机规模集上执行 OS 映像修补](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F465f0161-0087-490a-9ad9-ad6217f4f43a) |该策略可强制启用虚拟机规模集上的自动 OS 映像修补程序，以便通过应用每月的最新安全修补程序始终确保虚拟机安全。 |deny |1.0.0 |[链接](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMSSOSUpgradeHealthCheck_Deny.json)。 |
|[应当加密未附加的磁盘](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |此策略会审核未启用加密的所有未附加磁盘。 |Audit、Disabled |1.0.0 |[链接](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json)。 |
|[应将虚拟机迁移到新的 Azure 资源管理器资源](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d84d5fb-01f6-4d12-ba4f-4a26081d403d) |对虚拟机使用新的 Azure 资源管理器以提供安全增强功能，例如：更强的访问控制 (RBAC)、更佳审核功能、基于 Azure 资源管理器的部署和治理、对托管标识的访问、访问密钥保管库以获取机密、基于 Azure AD 的身份验证以及支持使用标记和资源组简化安全管理 |Audit、Deny、Disabled |1.0.0 |[链接](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ClassicCompute_Audit.json)。 |
