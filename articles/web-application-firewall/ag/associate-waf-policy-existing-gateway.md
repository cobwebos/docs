---
title: 将 Web 应用程序防火墙策略与现有的 Azure 应用程序网关相关联
description: 了解如何将 Web 应用程序防火墙策略与现有的 Azure 应用程序网关相关联。
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: 1ed2e0cf8cc8cd841d8779462d62ba4852774a3a
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083913"
---
# <a name="associate-a-waf-policy-with-an-existing-application-gateway"></a>将 WAF 策略与现有的应用程序网关关联

你可以使用 Azure PowerShell 来[创建 WAF 策略](create-waf-policy-ag.md)，但你可能已经有了应用程序网关，并且只想要将 WAF 策略关联到它。 在本文中，只需执行以下操作即可：创建 WAF 策略，并将其关联到现有的应用程序网关。 

1. 获取应用程序网关和防火墙策略。 如果没有现有的防火墙策略，请参阅步骤2。 

   ```azurepowershell-interactive
      Connect-AzAccount
      Select-AzSubscription -Subscription "<sub id>"`

      #Get Application Gateway and existing policy object or create new`
      $gw = Get-AzApplicationGateway -Name <Waf v2> -ResourceGroupName <RG name>`
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```

2. 可有可无创建防火墙策略。

   ```azurepowershell-interactive
      New-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>'
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```
   > [!NOTE]
   > 如果要创建此 WAF 策略以从 WAF Config 转换为 WAF 策略，则该策略需要是旧配置的精确副本。这意味着每个排除、自定义规则、禁用的规则组等都需要与 WAF Config 中的完全相同。
3. 可有可无你可以根据需要配置 WAF 策略。 这包括自定义规则、禁用规则/规则组、排除、设置文件上传限制等。如果跳过此步骤，将选择所有默认值。 
   
4. 保存策略，并将其附加到应用程序网关。 
   
   ```azurepowershell-interactive
      #Save the policy itself
      Set-AzApplicationGatewayFirewallPolicy -InputObject $policy`
   
      #Attach the policy to an Application Gateway
      $gw.FirewallPolicy = $policy`
   
      #Save the Application Gateway
      Set-AzApplicationGateway -ApplicationGateway $gw`
   ```

## <a name="next-steps"></a>后续步骤
[了解自定义规则。](configure-waf-custom-rules.md)
