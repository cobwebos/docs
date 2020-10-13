---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/07/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: aa824de268d45d6420db499444b543aaac599524
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91820640"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure 机器学习工作区应使用客户托管密钥 (CMK) 进行加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fba769a63-b8cc-4b2d-abf6-ac33c7204be8) |评估未使用客户托管密钥 (CMK) 启用加密的 Azure 机器学习工作区。 客户托管密钥为工作区添加了额外一层安全。 有关详细信息，请访问 [https://aka.ms/azureml-workspaces-cmk](https://aka.ms/azureml-workspaces-cmk)。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Machine%20Learning/Workspace_CMKEnabled_Audit.json) |
|[ Azure 机器学习工作区应使用专用链接](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F40cec1dd-a100-4920-b15b-3024fe8901ab) |评估没有达到“至少有一个已批准的专用终结点连接”标准的 Azure 机器学习工作区。 虚拟网络中的客户端可以安全地访问通过专用链接获得专用终结点连接的资源。 有关详细信息，请访问：[https://aka.ms/azureml-workspaces-privatelink](https://aka.ms/azureml-workspaces-privatelink)。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Machine%20Learning/Workspace_PrivateLinkEnabled_Audit.json) |
|[为指定的 Azure 机器学习计算配置允许的模块作者](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F53c70b02-63dd-11ea-bc55-0242ac130003) |此策略有助于提供在指定的 Azure 机器学习计算中允许的模块作者，并可以在工作区中分配这些模块作者。 有关详细信息，请访问 [https://aka.ms/amlpolicydoc](https://aka.ms/amlpolicydoc)。 |enforceSetting，已禁用 |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Machine%20Learning/AllowedModuleAuthors_EnforceSetting.json) |
|[为指定的 Azure 机器学习计算配置允许的 Python 包](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F77eeea86-7e81-4a7d-9067-de844d096752) | 此策略有助于提供在指定的 Azure 机器学习计算中允许的 Python 包，并可以在工作区中分配这些包。 有关详细信息，请访问 [https://aka.ms/amlpolicydoc](https://aka.ms/amlpolicydoc)。 |enforceSetting，已禁用 |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Machine%20Learning/AllowedPythonPackageChannels_EnforceSetting.json) |
|[为指定的 Azure 机器学习计算配置允许的注册表](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5853517a-63de-11ea-bc55-0242ac130003) |此策略有助于提供在指定的 Azure 机器学习计算中允许的注册表，并可以在工作区中分配这些注册表。 有关详细信息，请访问 [https://aka.ms/amlpolicydoc](https://aka.ms/amlpolicydoc)。 |enforceSetting，已禁用 |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Machine%20Learning/AllowedACRs_EnforceSetting.json) |
|[为指定的 Azure 机器学习计算配置在运行作业之前调用的审批终结点](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3948394e-63de-11ea-bc55-0242ac130003) |此策略有助于为指定的 Azure 机器学习计算配置在运行作业之前调用的审批终结点，并能将其分配到工作区。 有关详细信息， 有关详细信息，请访问 [https://aka.ms/amlpolicydoc](https://aka.ms/amlpolicydoc)。 |enforceSetting，已禁用 |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Machine%20Learning/ApprovalEndpoint_EnforceSetting.json) |
|[为指定的 Azure 机器学习计算配置用于定型代码的代码签名](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6a6f7384-63de-11ea-bc55-0242ac130003) |此策略有助于在指定的 Azure 机器学习计算中为定型代码提供代码签名，可在工作区中分配此策略。 有关详细信息，请访问 [https://aka.ms/amlpolicydoc](https://aka.ms/amlpolicydoc)。 |enforceSetting，已禁用 |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Machine%20Learning/AllowedSigningKey_EnforceSetting.json) |
|[为指定的 Azure 机器学习计算配置用于完整日志的日志筛选表达式以及数据存储](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d413020-63de-11ea-bc55-0242ac130003) |此策略有助于在指定的 Azure 机器学习计算中提供用于完整日志的日志筛选表达式和数据存储，并能将其分配到工作区。 有关详细信息，请访问 [https://aka.ms/amlpolicydoc](https://aka.ms/amlpolicydoc)。 |enforceSetting，已禁用 |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Machine%20Learning/AllowedLogFilter_EnforceSetting.json) |
