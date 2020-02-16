---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 667d017e8febcf1abcc1cfe21ecfaa43ae75ea6d
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170095"
---
|名称 |说明 |效果 |版本 |
|---|---|---|---|
|[允许的虚拟机 SKU](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMSkusAllowed_Deny.json) |使用此策略，你可以指定组织可部署的一组虚拟机 SKU。 |拒绝 |1.0.0 |
|[审核未配置灾难恢复的虚拟机](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |审核未配置灾难恢复的虚拟机。 若要详细了解灾难恢复，请访问 https://aka.ms/asr-doc 。 |auditIfNotExists |1.0.0 |
|[审核未使用托管磁盘的 VM](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |此策略审核未使用托管磁盘的 VM |audit |1.0.0 |
|[为 Windows Server 部署默认 Microsoft IaaSAntimalware 扩展](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMAntimalwareExtension_Deploy.json) |如果 VM 未配置反恶意软件扩展，则此策略部署使用默认配置的 Microsoft IaaSAntimalware 扩展。 |deployIfNotExists |1.0.0 |
|[应当启用虚拟机规模集中的诊断日志](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |建议启用日志，以便在出现某个事件或遭到入侵后需要进行调查时可以重新创建活动线索。 |AuditIfNotExists、Disabled |1.0.0 |
|[Microsoft Antimalware for Azure 应配置为自动更新保护签名](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VirtualMachines_AntiMalwareAutoUpdate_AuditIfNotExists.json) |此策略会审核所有未配置自动更新 Microsoft Antimalware 保护签名的 Windows 虚拟机。 |AuditIfNotExists、Disabled |1.0.0 |
|[应在 Windows Server 上部署 Microsoft IaaSAntimalware 扩展](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/WindowsServers_AntiMalware_AuditIfNotExists.json) |此策略会审核所有未部署 Microsoft IaaSAntimalware 扩展的 Windows Server VM。 |AuditIfNotExists、Disabled |1.0.0 |
|[应当仅安装已批准的 VM 扩展](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VirtualMachines_ApprovedExtensions_Audit.json) |此策略约束未获批准的虚拟机扩展。 |Audit、Deny、Disabled |1.0.0 |
|[要求自动在虚拟机规模集上执行 OS 映像修补](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/VMSSOSUpgradeHealthCheck_Deny.json) |该策略可强制启用虚拟机规模集上的自动 OS 映像修补程序，以便通过应用每月的最新安全修补程序始终确保虚拟机安全。 |deny |1.0.0 |
|[应当加密未附加的磁盘](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |此策略会审核未启用加密的所有未附加磁盘。 |Audit、Disabled |1.0.0 |
|[应将虚拟机迁移到新的 Azure 资源管理器资源](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Compute/ClassicCompute_Audit.json) |为虚拟机使用新的 Azure 资源管理器以提供安全增强功能，如: 更强的访问控制(RBAC)、更好地审核、基于 ARM 的部署和治理、托管标识访问权限、用于提供机密的密钥保管库的访问权限、基于 Azure AD 的身份验证以及可实现更轻松安全管理的标记和资源组支持 |Audit、Deny、Disabled |1.0.0 |
