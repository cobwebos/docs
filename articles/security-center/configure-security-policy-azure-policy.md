---
title: 使用 REST API 创建和编辑 Azure 策略安全策略 |Microsoft Docs
description: 通过 REST API 了解 Azure 策略策略管理。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 56c5ca8c4e1d70e002a338c753f9ab1f0b1aa411
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73522016"
---
# <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>使用 REST API 在 Azure 策略中配置安全策略

Azure 安全中心与 Azure Policy 实现了本机集成，借助它，可以利用 Azure Policy 的 REST API 来创建策略分配。 以下说明演示如何创建策略分配以及如何自定义现有的分配。 

Azure Policy 中的重要概念： 

- **策略定义**是一种规则 

- **计划**是策略定义（规则）的集合 

- **分配**是将计划或策略应用于特定的范围（管理组、订阅等） 

安全中心有一项内置计划，它包括中心内的所有安全策略。 若要评估 Azure 资源上的安全中心策略，你应在要评估的管理组或订阅上创建分配。

内置计划默认启用所有安全中心策略。 你可以选择禁用内置计划中的某些策略。 例如，若要应用除**web 应用程序防火墙**外的所有安全中心策略，请将策略的 effect 参数的值更改为 "**已禁用**"。 

## <a name="api-examples"></a>API 示例

在下面的示例中，替换以下三个变量：

- **{scope}** 输入要对其应用策略的管理组或订阅的名称。
- **{policyAssignmentName}** ，用于输入[相关策略分配的名称](#policy-names)。
- **{name}** ，用于输入你的名字或批准策略更改的管理员的名字。

本示例演示如何对订阅或管理组分配内置的安全中心计划
 
 ```
    PUT  
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 

    Request Body (JSON) 

    { 

      "properties":{ 

    "displayName":"Enable Monitoring in Azure Security Center", 

    "metadata":{ 

    "assignedBy":"{Name}" 

    }, 

    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 

    "parameters":{}, 

    } 

    } 
 ```

本示例演示如何对订阅分配内置的安全中心计划，且禁用以下策略： 

- 系统更新 ("systemUpdatesMonitoringEffect") 

- 安全配置 ("systemConfigurationsMonitoringEffect") 

- 终结点保护 ("endpointProtectionMonitoringEffect") 

 ```
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
    
    Request Body (JSON) 
    
    { 
    
      "properties":{ 
    
    "displayName":"Enable Monitoring in Azure Security Center", 
    
    "metadata":{ 
    
    "assignedBy":"{Name}" 
    
    }, 
    
    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 
    
    "parameters":{ 
    
    "systemUpdatesMonitoringEffect":{"value":"Disabled"}, 
    
    "systemConfigurationsMonitoringEffect":{"value":"Disabled"}, 
    
    "endpointProtectionMonitoringEffect":{"value":"Disabled"}, 
    
    }, 
    
     } 
    
    } 
 ```
此示例演示如何删除分配：
 ```
    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
 ```

## 策略名引用 <a name="policy-names"></a>

|安全中心内的策略名|Azure Policy 中显示的策略名 |策略效果参数名|
|----|----|----|
|SQL 加密 |监视 Azure 安全中心内未加密的 SQL 数据库 |sqlEncryptionMonitoringEffect| 
|SQL 审核 |监视 Azure 安全中心内未审核的 SQL 数据库 |sqlAuditingMonitoringEffect|
|系统更新 |监视 Azure 安全中心内系统更新的缺失情况 |systemUpdatesMonitoringEffect|
|存储加密 |审核存储帐户是否缺少 blob 加密 |storageEncryptionMonitoringEffect|
|JIT 网络访问 |在 Azure 安全中心监视可能的网络实时（JIT）访问 |jitNetworkAccessMonitoringEffect |
|自适应应用程序控制 |监视 Azure 安全中心内列入允许列表的可能的应用 |adaptiveApplicationControlsMonitoringEffect|
|网络安全组 |监视 Azure 安全中心内规则较宽松的网络访问 |networkSecurityGroupsMonitoringEffect| 
|安全配置 |监视 Azure 安全中心的 OS 漏洞 |systemConfigurationsMonitoringEffect| 
|终结点保护 |监视 Azure 安全中心 Endpoint Protection 的缺失情况 |endpointProtectionMonitoringEffect |
|磁盘加密 |监视 Azure 安全中心内未加密的 VM 磁盘 |diskEncryptionMonitoringEffect|
|漏洞评估 |监视 Azure 安全中心的 VM 漏洞 |vulnerabilityAssessmentMonitoringEffect|
|Web 应用程序防火墙 |监视 Azure 安全中心内未受保护的 Web 应用程序 |webApplicationFirewallMonitoringEffect |
|下一代防火墙 |监视 Azure 安全中心内未受保护的网络终结点| |


## <a name="next-steps"></a>后续步骤

有关其他相关材料，请参阅以下文章： 

- [自定义安全策略](custom-security-policies.md)
- [安全策略概述](tutorial-security-policy.md)