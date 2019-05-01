---
title: 使用 web 应用程序防火墙规则的 Azure 第一道防线配置 IP 限制规则
description: 了解如何配置现有的第一道防线终结点的 IP 地址限制 WAF 规则。
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: kumud;tyao
ms.openlocfilehash: 514c034c23eed3a87111331724f3a33104651a43
ms.sourcegitcommit: e729629331ae10097a081a03029398525f4147a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2019
ms.locfileid: "64514899"
---
# <a name="configure-an-ip-restriction-rule-with-web-application-firewall-for-azure-front-door-preview"></a>使用 web 应用程序防火墙的 Azure 第一道防线 （预览版） 配置 IP 限制规则
 本文介绍如何使用 Azure CLI、 Azure PowerShell 或 Azure 资源管理器模板为第一道防线配置 IP 限制规则在 Azure web 应用程序防火墙 (WAF)。

IP 地址基于访问控制规则是自定义 WAF 规则，可用于控制对 web 应用程序的访问通过无类域间路由 (CIDR) 格式指定的 IP 地址或 IP 地址范围的列表。

默认情况下，web 应用程序是从 internet 访问。 如果你想要限制对 web 应用程序仅向客户端从列表中的已知的 IP 地址或 IP 地址范围访问，您需要创建两个 IP 匹配规则。 第一个 IP 匹配规则包含为匹配的值的 IP 地址的列表，并将操作设置为"允许"。 优先级较低，第二个是通过使用"All"运算符来阻止所有其他 IP 地址并将操作设置为"块"。 一旦应用 IP 限制规则，从该允许列表外的地址发出任何请求将收到 403 （禁止访问） 响应。  

> [!IMPORTANT]
> WAF IP 限制功能的 Azure 第一道防线当前处于公共预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="configure-waf-policy-with-azure-cli"></a>使用 Azure CLI 配置 WAF 策略

### <a name="prerequisites"></a>必备组件
在开始配置 IP 限制策略之前，在 CLI 环境设置和创建的第一道防线配置文件。

#### <a name="set-up-azure-cli-environment"></a>Azure CLI 环境设置
1. 安装[Azure CLI](/cli/azure/install-azure-cli)，或使用 Azure Cloud Shell。 Azure Cloud Shell 是可直接在 Azure 门户中运行的免费 Bash shell。 它具有 Azure CLI 预装并配置为使用与你的帐户。 选择**试试**CLI 中的按钮命令后面。 选择**试试**调用可以登录到 Azure 帐户的 Cloud Shell。 Cloud shell 会话开始后，输入`az extension add --name front-door`若要添加的第一道防线扩展。
 2. 如果在 Bash 中本地使用 CLI，登录到 Azure 中使用`az login`。

#### <a name="create-front-door-profile"></a>创建第一道防线配置文件
遵照以下文档中的说明创建 Front Door 配置文件：[快速入门：创建的第一道防线配置文件](quickstart-create-front-door.md)

### <a name="create-a-waf-policy"></a>创建 WAF 策略

创建采用 WAF 的策略[az 网络 waf 策略创建](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-create)命令。 在以下示例中，将策略名称*IPAllowPolicyExampleCLI*具有唯一策略名称。

```azurecli-interactive 
az network waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-custom-ip-access-control-rule"></a>添加自定义 IP 访问控制规则

将自定义的 IP 访问控制规则添加到在上一步中创建 WAF 策略[az 网络 waf 策略自定义规则创建](/cli/azure/ext/front-door/network/waf-policy/custom-rule?view=azure-cli-latest#ext-front-door-az-network-waf-policy-custom-rule-create)命令。 

在下面的示例：
-  替换*IPAllowPolicyExampleCLI*与您先前创建的唯一策略。
-  替换*ip 地址范围 1*， *ip 地址范围 2*与您自己的范围。

首先，创建 IP 允许指定的地址的规则。

```azurecli
az network waf-policy custom-rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --match-condition RemoteAddr IPMatch ["<ip-address-range-1>","<ip-address-range-2>"] \
  --action Allow \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
```
接下来，创建一个块所有 IP 规则不是上一 IP 允许规则的优先级较低。 替换*IPAllowPolicyExampleCLI*与您先前创建的唯一策略。

```azurecli
az network waf-policy custom-rule create \
  --name IPDenyAllRule\
  --priority 2 \
  --rule-type MatchRule \
  --match-condition RemoteAddr Any
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
 ```

### <a name="find-waf-policy-id"></a>查找 WAF 策略 ID
查找的 WAF 策略所具有的 ID [az 网络 waf 策略显示](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-show)命令。 替换*IPAllowPolicyExampleCLI*与您先前创建的唯一策略。

   ```azurecli
   az network waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-waf-policy-to-a-front-door-front-end-host"></a>链接 WAF 策略应用到的第一道防线前端主机

设置第一道防线*WebApplicationFirewallPolicyLink*使用的策略 ID 的 ID [az 网络第一道防线更新](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update)命令。 替换*IPAllowPolicyExampleCLI*与您先前创建的唯一策略。

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/<resource- name>/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
在此示例中，WAF 策略应用于 FrontendEndpoints [0]。 可能将 WAF 策略链接到任何应用的前端。
> [!Note]
> 只需设置**WebApplicationFirewallPolicyLink**属性一次将 WAF 策略链接到前端的第一道防线。 后续的策略更新自动应用于前端。

## <a name="configure-waf-policy-with-azure-powershell"></a>使用 Azure PowerShell 配置 WAF 策略

### <a name="prerequisites"></a>必备组件
若要配置 IP 限制策略在开始之前，设置 PowerShell 环境并创建的第一道防线配置文件。

#### <a name="set-up-your-powershell-environment"></a>设置 PowerShell 环境
Azure PowerShell 提供一组可以使用 [Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)模型管理 Azure 资源的 cmdlet。 

可以在本地计算机上安装 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 并在任何 PowerShell 会话中使用它。 在页上，若要使用 Azure 凭据，登录按照的说明，安装 Az PowerShell 模块。

##### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>使用交互式对话登录连接到 Azure
```
Connect-AzAccount

```
在安装第一道防线模块之前，请确保您具有安装 PowerShellGet 的当前版本。 运行以下命令，并重新打开 PowerShell。

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

##### <a name="install-azfrontdoor-module"></a>安装 Az.FrontDoor 模块 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>创建 Front Door 配置文件
遵照以下文档中的说明创建 Front Door 配置文件：[快速入门：创建的第一道防线配置文件](quickstart-create-front-door.md)

### <a name="define-ip-match-condition"></a>定义 IP 匹配条件
使用[新建 AzFrontDoorMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoormatchconditionobject)命令，以便定义 IP 匹配条件。 在以下示例中，替换*ip 地址范围 1*， *ip 地址范围 2*与您自己的范围。

```powershell
  $IPMatchCondition = New-AzFrontDoorMatchConditionObject `
    -MatchVariable  RemoteAddr `
    -OperatorProperty IPMatch `
    -MatchValue ["ip-address-range-1", "ip-address-range-2"]
```
创建 IP 匹配所有条件规则
```powershell
  $IPMatchALlCondition = New-AzFrontDoorMatchConditionObject `
    -MatchVariable  RemoteAddr `
    -OperatorProperty Any
    
```

### <a name="create-a-custom-ip-allow-rule"></a>创建自定义 IP 允许规则
   使用[新建 AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-AzFrontDoorCustomRuleObject)命令定义的操作，并设置优先级。 在以下示例中，将允许从客户端 Ip 与列表匹配的请求。 

```powershell
  $IPAllowRule = New-AzFrontDoorCustomRuleObject `
    -Name "IPAllowRule" `
    -RuleType MatchRule `
    -MatchCondition $IPMatchCondition `
    -Action Allow -Priority 1
```
创建具有较低的优先级高于允许规则对上一 IP 所有 IP 规则的块。

```powershell
  $IPBlockAll = New-AzFrontDoorCustomRuleObject `
    -Name "IPDenyAll" `
    -RuleType MatchRule `
    -MatchCondition $IPMatchALlCondition `
    -Action Block `
    -Priority 2
   ```

### <a name="configure-waf-policy"></a>配置 WAF 策略
使用 `Get-AzResourceGroup` 找到包含该 Front Door 配置文件的资源组的名称。 接下来，使用配置的 WAF 的 IP 块规则 using[新建 AzFrontDoorFireWallPolicy](/powershell/module/Az.FrontDoor/New-AzFrontDoorFireWallPolicy)。

```powershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorFireWallPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule $IPBlockAll `
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-waf-policy-to-a-front-door-front-end-host"></a>链接 WAF 策略应用到的第一道防线前端主机

WAF 策略对象链接到现有的第一道防线前端主机并更新第一道防线属性。 首先检索第一道防线对象使用[Get AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)。 接下来，设置前端*WebApplicationFirewallPolicyLink*属性设置为的 resourceId *$IPAllowPolicyExamplePS*在上一步中创建[集 AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)命令。

```powershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> 在此示例中，WAF 策略应用于 FrontendEndpoints [0]。 可能将 WAF 策略链接到任何应用的前端。只需设置*WebApplicationFirewallPolicyLink*属性一次将 WAF 策略链接到前端的第一道防线。 后续的策略更新自动应用于前端。


## <a name="configure-waf-policy-with-resource-manager-template"></a>使用资源管理器模板配置 WAF 策略
查看用于创建包含自定义 IP 限制规则的第一道防线和 WAF 策略模板[此处](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip)。


## <a name="next-steps"></a>后续步骤

- 了解如何[创建的第一道防线配置文件](quickstart-create-front-door.md)。
