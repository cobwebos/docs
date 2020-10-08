---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/07/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: dba47b730454817d8cb1e0a90cee8297e2ebd8de
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91821748"
---
## <a name="azure-security-benchmark"></a>Azure 安全基准

[Azure 安全基准](../../../../articles/security/benchmarks/overview.md)提供有关如何在 Azure 上保护云解决方案的建议。 若要查看此服务如何完全映射到 Azure 安全基准，请参阅 [Azure 安全基准映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

若要查看所有 Azure 服务的可用 Azure Policy 内置项如何映射到此合规性标准，请参阅 [Azure Policy 法规遵从性 - Azure 安全基准](../../../../articles/governance/policy/samples/azure-security-benchmark.md)。

|域 |控制 ID |控制标题 |策略<br /><sub>（Azure 门户）</sub> |策略版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|网络安全 |1.1 |在虚拟网络上使用网络安全组或 Azure 防火墙来保护资源 |[应在 Kubernetes 服务上定义经授权的 IP 范围](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e246bcf-5f6f-4f87-bc6f-775d4712c7ea) |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableIpRanges_KubernetesService_Audit.json) |
|数据保护 |4.6 |使用 Azure RBAC 控制对资源的访问 |[应在 Kubernetes 服务中使用基于角色的访问控制 (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |
|漏洞管理 |5.3 |部署第三方软件修补程序自动化管理解决方案 |[Kubernetes 服务应升级到不易受攻击的 Kubernetes 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb893a29-21bb-418c-a157-e99480ec364c) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UpgradeVersion_KubernetesService_Audit.json) |

## <a name="cis-microsoft-azure-foundations-benchmark"></a>CIS Microsoft Azure 基础基准

若要查看所有 Azure 服务的可用 Azure Policy 内置项如何映射到此合规性标准，请参阅 [Azure Policy 法规遵从性 - CIS Microsoft Azure 基础基准 1.1.0](../../../../articles/governance/policy/samples/cis-azure-1-1-0.md)。
有关此符合性标准的详细信息，请参阅 [CIS Microsoft Azure 基础基准](https://www.cisecurity.org/benchmark/azure/)。

|域 |控制 ID |控制标题 |策略<br /><sub>（Azure 门户）</sub> |策略版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|其他安全注意事项 |8.5 |在 Azure Kubernetes 服务中启用基于角色的访问控制 (RBAC) |[应在 Kubernetes 服务中使用基于角色的访问控制 (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

若要查看所有 Azure 服务的可用 Azure Policy 内置项如何映射到此合规性标准，请参阅 [Azure Policy 法规合规性 - HIPAA HITRUST 9.2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md)。
有关此合规性标准的详细信息，请参阅 [HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html)。

|域 |控制 ID |控制标题 |策略<br /><sub>（Azure 门户）</sub> |策略版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|特权管理 |1149.01c2System.9 - 01.c |组织可使获得授权的用户能够在组织规定允许自行决定的情况下确定业务合作伙伴的访问权限，并采用手动流程或自动化机制来帮助用户进行信息共享/协作决策，从而促进信息共享。 |[应在 Kubernetes 服务中使用基于角色的访问控制 (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |
|特权管理 |1153.01c3System.35 - 01.c |所有未明确要求的文件系统访问权限都被禁用，只有获得授权的用户才能访问，且他们只能访问履行用户工作职责所明确需要的文件系统访问权限。 |[应在 Kubernetes 服务中使用基于角色的访问控制 (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |
|职责分离 |1229.09c1Organizational.1 - 09.c |职责分离用于限制未经授权或无意中修改信息和系统的风险。 |[应在 Kubernetes 服务中使用基于角色的访问控制 (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

## <a name="nist-sp-800-171-r2"></a>NIST SP 800-171 R2

若要查看所有 Azure 服务的可用 Azure Policy 内置项如何映射到此合规性标准，请参阅 [Azure Policy 法规遵从性 - NIST SP 800-171 R2](../../../../articles/governance/policy/samples/nist-sp-800-171-r2.md)。
有关此符合性标准的详细信息，请参阅 [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final)。

|域 |控制 ID |控制标题 |策略<br /><sub>（Azure 门户）</sub> |策略版本<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|系统和信息完整性 |3.14.1 |及时识别、报告和更正系统缺陷。 |[Kubernetes 服务应升级到不易受攻击的 Kubernetes 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffb893a29-21bb-418c-a157-e99480ec364c) |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UpgradeVersion_KubernetesService_Audit.json) |

