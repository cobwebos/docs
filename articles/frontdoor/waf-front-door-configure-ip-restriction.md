---
title: 为 Azure 第一道防线服务与 web 应用程序防火墙规则配置 IP 限制规则
description: 了解如何配置 web 应用程序防火墙规则以限制现有 Azure 第一道防线服务终结点的 IP 地址。
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud;tyao
ms.openlocfilehash: 88c5c284f26203ff3d6c39810a7b2810c1ebbc5a
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743167"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door-service"></a>为 Azure 第一道防线服务 web 应用程序防火墙配置 IP 限制规则
本文介绍如何使用 Azure CLI、 Azure PowerShell 或 Azure 资源管理器模板，为 Azure 第一道防线服务配置 IP 限制规则的 web 应用程序防火墙 (WAF)。

IP 地址-基于访问控制规则是自定义 WAF 规则，可用于控制对 web 应用程序的访问。 做到这一点通过无类域间路由 (CIDR) 格式指定的 IP 地址或 IP 地址范围的列表。

默认情况下，web 应用程序是从 internet 访问。 如果你想要限制从一系列已知的 IP 地址或 IP 地址范围访问客户端，必须创建两个 IP 匹配规则。 第一个 IP 匹配规则包含为匹配的值的 IP 地址的列表，并将操作设置为**允许**。 第二个，具有低优先级，通过阻止所有其他 IP 地址**所有**运算符，并将操作设置**块**。 应用 IP 限制规则后，从该允许列表外的地址发出的请求将收到 403 Forbidden 响应。  

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>使用 Azure CLI 配置的 WAF

### <a name="prerequisites"></a>必备组件
在开始配置 IP 限制策略之前，在 CLI 环境设置和创建 Azure 第一道防线服务配置文件。

#### <a name="set-up-the-azure-cli-environment"></a>Azure CLI 环境设置
1. 安装[Azure CLI](/cli/azure/install-azure-cli)，或使用 Azure Cloud Shell。 Azure Cloud Shell 是可直接在 Azure 门户中运行的免费 Bash shell。 它预安装有 Azure CLI 并将其配置为与帐户一起使用。 选择**试试**中执行，并登录到 Azure 帐户在打开 Cloud Shell 会话中的 CLI 命令按钮。 在会话启动后，输入`az extension add --name front-door`若要添加 Azure 第一道防线服务扩展。
 2. 如果要在 Bash 中的本地使用 CLI，登录到 Azure 使用`az login`。

#### <a name="create-an-azure-front-door-service-profile"></a>创建 Azure 第一道防线服务配置文件
创建 Azure 第一道防线服务配置文件中所述的说明[快速入门：创建高度可用的全球 web 应用程序第一道防线](quickstart-create-front-door.md)。

### <a name="create-a-waf-policy"></a>创建 WAF 策略

使用创建 WAF 策略[az 网络 waf 策略创建](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-create)命令。 在示例中，如下所示，替换策略名称*IPAllowPolicyExampleCLI*具有唯一策略名称。

```azurecli-interactive 
az network waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>添加自定义的 IP 访问控制规则

使用[az 网络 waf 策略自定义规则创建](/cli/azure/ext/front-door/network/waf-policy/custom-rule?view=azure-cli-latest#ext-front-door-az-network-waf-policy-custom-rule-create)若要添加的自定义 IP 访问控制规则 WAF 策略刚创建的命令。

在下面的示例：
-  替换*IPAllowPolicyExampleCLI*与您先前创建的唯一策略。
-  替换*ip 地址范围 1*， *ip 地址范围 2*与您自己的范围。

首先，创建 IP 允许指定的地址的规则。

```azurecli
az network waf-policy custom-rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --match-condition RemoteAddr IPMatch "<ip-address-range-1>","<ip-address-range-2>" \
  --action Allow \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
```
接下来，创建**阻止所有**比之前的低优先级的规则**允许**规则。 同样，替换*IPAllowPolicyExampleCLI*在下面的示例与您先前创建的唯一策略。

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
    
### <a name="find-the-id-of-a-waf-policy"></a>查找 WAF 策略的 ID 
使用查找 WAF 策略的 ID [az 网络 waf 策略显示](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-show)命令。 替换*IPAllowPolicyExampleCLI*在下面的示例与您先前创建的唯一策略。

   ```azurecli
   az network waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>将 WAF 策略链接到 Azure 的第一道防线服务前端主机

设置 Azure 第一道防线服务*WebApplicationFirewallPolicyLink*使用的策略 ID 的 ID [az 网络第一道防线更新](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update)命令。 替换*IPAllowPolicyExampleCLI*与您先前创建的唯一策略。

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/<resource- name>/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
在此示例中，WAF 策略应用于**FrontendEndpoints [0]** 。 您可以链接到任何你前端 WAF 策略。
> [!Note]
> 您需要设置**WebApplicationFirewallPolicyLink**属性一次只能将 WAF 策略链接到 Azure 的第一道防线服务前端。 后续的策略更新自动应用于的前端。

## <a name="configure-a-waf-policy-with-azure-powershell"></a>使用 Azure PowerShell 配置的 WAF

### <a name="prerequisites"></a>必备组件
在开始配置 IP 限制策略之前，设置 PowerShell 环境并创建一个 Azure 第一道防线服务配置文件。

#### <a name="set-up-your-powershell-environment"></a>设置 PowerShell 环境
Azure PowerShell 提供了一组使用的 cmdlet [Azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)模型管理 Azure 资源。

可以在本地计算机上安装 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 并在任何 PowerShell 会话中使用它。 按页后，可以使用 Azure 凭据，登录到 PowerShell 的说明，然后安装 Az 模块。

1. 使用以下命令，连接到 Azure，然后使用交互式对话框登录。
    ```
    Connect-AzAccount
    ```
 2. 在安装 Azure 第一道防线服务模块之前，请确保已安装的 PowerShellGet 模块的当前版本。 运行以下命令，并重新打开 PowerShell。

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. 使用以下命令安装 Az.FrontDoor 模块。 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-service-profile"></a>创建 Azure 第一道防线服务配置文件
创建 Azure 第一道防线服务配置文件中所述的说明[快速入门：创建高度可用的全球 web 应用程序第一道防线](quickstart-create-front-door.md)。

### <a name="define-an-ip-match-condition"></a>定义 IP 匹配条件
使用[新建 AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject)命令，以便定义 IP 匹配条件。
在以下示例中，替换*ip 地址范围 1*， *ip 地址范围 2*与您自己的范围。    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue ["ip-address-range-1", "ip-address-range-2"]
```
创建 IP*匹配所有条件*规则通过使用以下命令：
```powershell
$IPMatchALlCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty Any        
  ```
    
### <a name="create-a-custom-ip-allow-rule"></a>创建自定义 IP 允许规则

使用[新建 AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject)命令定义的操作，并设置优先级。 在以下示例中，将允许从客户端 Ip 与列表匹配的请求。

```powershell
$IPAllowRule = New-AzFrontDoorCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Allow -Priority 1
```
创建**阻止所有**具有较低的优先级高于为上一 IP 规则**允许**规则。
```powershell
$IPBlockAll = New-AzFrontDoorCustomRuleObject `
-Name "IPDenyAll" `
-RuleType MatchRule `
-MatchCondition $IPMatchALlCondition `
-Action Block `
-Priority 2
```

### <a name="configure-a-waf-policy"></a>配置 WAF 策略
查找包含 Azure 第一道防线服务配置文件使用的资源组的名称`Get-AzResourceGroup`。 接下来，配置 WAF 策略使用的 IP**阻止所有**通过使用规则[新建 AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)。

```powershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule $IPBlockAll `
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>将 WAF 策略链接到 Azure 的第一道防线服务前端主机

将 WAF 策略对象链接到现有前端主机和更新 Azure 第一道防线服务属性。 首先，使用检索 Azure 第一道防线服务对象[Get AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)。 接下来，设置**WebApplicationFirewallPolicyLink**的资源 ID 的属性 *$IPAllowPolicyExamplePS*，创建在上一步骤中，通过使用[集 AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)命令。

```powershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> 在此示例中，WAF 策略应用于**FrontendEndpoints [0]** 。 您可以链接到任何你前端 WAF 策略。 您需要设置**WebApplicationFirewallPolicyLink**属性一次只能将 WAF 策略链接到 Azure 的第一道防线服务前端。 后续的策略更新自动应用于的前端。


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>使用资源管理器模板配置的 WAF
若要查看用于创建包含自定义 IP 限制规则的 Azure 第一道防线服务策略和 WAF 策略模板，请转到[GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip)。


## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Azure 第一道防线服务配置文件](quickstart-create-front-door.md)。
