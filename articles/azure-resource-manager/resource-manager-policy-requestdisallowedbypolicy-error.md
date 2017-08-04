---
title: "Azure 资源策略的 RequestDisallowedByPolicy 错误 | Microsoft Docs"
description: "说明 RequestDisallowedByPolicy 错误的原因。"
services: azure-resource-manager,azure-portal
documentationcenter: 
author: genlin
manager: cshepard
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: 182a27e444c2f5db66d518a1a0c608d3e319d553
ms.contentlocale: zh-cn
ms.lasthandoff: 07/13/2017

---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Azure 资源策略的 RequestDisallowedByPolicy 错误

本文说明了 RequestDisallowedByPolicy 错误的原因，它还提供了此错误的解决方案。

## <a name="symptom"></a>症状

尝试在部署过程中执行操作时，可能会收到阻止执行操作的 **RequestDisallowedByPolicy** 错误。 以下是错误的示例：

```
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>故障排除

若要检索有关阻止部署的策略的详细信息，请使用以下方法之一：

### <a name="method-1"></a>方法 1

在 PowerShell 中，提供该策略标识符作为 **Id** 参数，检索有关阻止部署的策略的详细信息。

```PowerShell
(Get-AzureRmPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="method-2"></a>方法 2 

在 Azure CLI 2.0 中，提供策略定义的名称： 

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>解决方案

为了安全性或合规性，IT 部门可能会强制执行禁止创建公共 IP 地址、网络安全组、用户定义路由或路由表的资源策略。 在“症状”部分中说明的错误消息的示例中，策略名为 **regionPolicyDefinition**，但它可以不同。
若要解决此问题，请协助 IT 部门查看资源策略，并确定如何执行符合这些策略的请求操作。


有关详细信息，请参阅以下文章：

- [资源策略概述](resource-manager-policy.md)
- [常见部署错误 - RequestDisallowedByPolicy](resource-manager-common-deployment-errors.md#requestdisallowedbypolicy)

 



