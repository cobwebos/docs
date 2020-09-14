---
title: Azure 安全基准 V2-资产管理
description: Azure 安全基准 V2 资产管理
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: ba010c0dd3384af895c68d980fcae788c2d6d45a
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059143"
---
# <a name="security-control-asset-management"></a>安全控制：资产管理

资产管理涵盖控制以确保对 Azure 资源进行安全可见性和管理。 这包括对安全人员权限的建议、对资产清单的安全访问，以及管理服务和资源的审批 (库存、跟踪和更正) 。

## <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>上午-1：确保安全团队了解资产风险

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| 上午-1 | 1.1、1.2、1.3、1.4、9.1、12.1 | CM-7、CM-8、CM-11、PM-5 |

确保安全团队在 Azure 租户和订阅中获得安全读者权限，以便他们可以使用 Azure 安全中心监视安全风险。 

根据安全团队责任的构建方式，监视安全风险可能是中心安全团队或本地团队的责任。 也就是说，必须始终在组织内集中聚合安全见解和风险。 

安全读者权限可广泛应用于整个租户 (根管理组) 或作用域限制为管理组或特定订阅。 

注意：若要了解工作负荷和服务，可能需要其他权限。 

- [安全读者角色概述](../../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 管理组概述](../../governance/management-groups/overview.md)

**责任**：客户

**客户安全利益干系人**：

- [基础结构和终结点安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [安全合规性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>上午-2：确保安全团队有权访问资产清单和元数据

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| 上午-2 | 1.5 | CM-8，下午5 |

确保安全团队有权访问 Azure 上不断更新的资产清单。 安全团队通常需要此清单来评估组织对新兴风险的潜在风险，并作为持续的安全改进的输入。 

Azure 安全中心清单功能和 Azure 资源图可查询和发现订阅中的所有资源，包括 Azure 服务、应用程序和网络资源。  

使用标记以及 Azure 中的其他元数据、Azure (名称、说明和类别) ，以逻辑方式组织资产。  

- [如何通过 Azure 资源关系图资源管理器创建查询](../../governance/resource-graph/first-query-portal.md)

- [Azure 安全中心资产清单管理](../../security-center/asset-inventory.md)

- [有关标记资产的详细信息，请参阅资源命名和标记决策指南](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**责任**：客户

**客户安全利益干系人**：

- [基础结构和终结点安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [安全合规性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-3-use-only-approved-azure-services"></a>上午-3：仅使用批准的 Azure 服务

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| 上午-3 | 2.3、2.4 | CM-7，CM-8 |

使用 Azure 策略审核和限制用户可在你的环境中预配的服务。 使用 Azure Resource Graph 查询和发现订阅中的资源。  你还可以使用 Azure Monitor 来创建规则，以便在检测到未批准的服务时触发警报。

- [配置和管理 Azure 策略](../../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Policy 拒绝特定的资源类型](/azure/governance/policy/samples/not-allowed-resource-types)

- [如何通过 Azure 资源关系图资源管理器创建查询](../../governance/resource-graph/first-query-portal.md)

**责任**：客户

**客户安全利益干系人**：

- [安全合规性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

## <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>上午-4：确保资产生命周期管理的安全性

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| 上午-4 | 2.3、2.4、2。5 | CM-7、CM-8、CM-10、CM-11 |

建立或更新安全策略，这些策略用于满足潜在的高影响修改的资产生命周期管理过程。 这些修改包括对的更改：标识提供者和访问、数据敏感度、网络配置和管理特权分配。

如果不再需要 Azure 资源，请将其删除。

- [删除 Azure 资源组和资源](../../azure-resource-manager/management/delete-resource-group.md)

**责任**：客户

**客户安全利益干系人**：

- [基础结构和终结点安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [安全合规性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>上午-5：限制用户与 Azure 资源管理器的交互能力

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| 上午-5 | 2.9 | AC-3 |

使用 Azure AD 条件性访问，通过为 "Microsoft Azure 管理" 应用配置 "阻止访问"，限制用户与 Azure 资源管理器的交互能力。

- [如何配置条件访问来阻止对 Azure 资源管理器的访问](../../role-based-access-control/conditional-access-azure-management.md)

**责任**：客户

**客户安全利益干系人**：

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [基础结构和终结点安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="am-6-use-only-approved-applications-in-compute-resources"></a>上午-6：仅在计算资源中使用批准的应用程序

| Azure ID | CIS 控制 v2.0 ID (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| 上午-6 | 2.6 | AC-3、CM-7、CM-8、CM-10、CM-11 |

确保仅执行授权软件，并且阻止所有未经授权的软件在 Azure 虚拟机上执行。

使用 Azure 安全中心 (ASC) 自适应应用程序控件发现并生成应用程序允许列表。 你还可以使用 ASC 自适应应用程序控件，以确保仅执行授权的软件，并且阻止所有未经授权的软件在 Azure 虚拟机上执行。

使用 Azure 自动化更改跟踪和清单自动收集 Windows 和 Linux Vm 中的清单信息。 可从 Azure 门户获取软件名称、版本、发布者和刷新时间。 若要获取软件安装日期和其他信息，请启用来宾级别诊断，并将 Windows 事件日志定向到 Log Analytics 工作区。

根据脚本的类型，可以使用特定于操作系统的配置或第三方资源来限制用户在 Azure 计算资源中执行脚本的能力。 

你还可以使用第三方解决方案来发现和识别未批准的软件。

- [如何使用 Azure 安全中心自适应应用程序控件](../../security-center/security-center-adaptive-application.md)

- [了解 Azure 自动化更改跟踪和清单](../../automation/change-tracking.md)

- [如何在 Windows 环境中控制 PowerShell 脚本执行](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

**责任**：客户

**客户安全利益干系人**：

- [基础结构和终结点安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [状况管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)  

- [安全合规性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

