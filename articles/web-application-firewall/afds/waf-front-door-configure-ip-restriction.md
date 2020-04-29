---
title: 为 Azure 前门配置 IP 限制 WAF 规则
description: 了解如何配置 Web 应用程序防火墙规则，以限制现有 Azure 前端终结点的 IP 地址。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 03/26/2020
ms.author: tyao
ms.openlocfilehash: 077f127648688b25d45b433fa2bc94ee011b3f2d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80336077"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door"></a>使用 Azure 前门的 Web 应用程序防火墙配置 IP 限制规则

本文介绍如何使用 Azure 门户、Azure CLI、Azure PowerShell 或 Azure 资源管理器模板在 Azure 前门的 Web 应用程序防火墙（WAF）中配置 IP 限制规则。

基于 IP 地址的访问控制规则是一个自定义的 WAF 规则，可用于控制对 web 应用程序的访问。 它通过以无类别的域间路由（CIDR）格式指定 IP 地址或 IP 地址范围的列表来实现此功能。

默认情况下，可从 Internet 访问你的 web 应用程序。 如果要从已知 IP 地址或 IP 地址范围列表中限制对客户端的访问，则可以创建一个 IP 匹配规则，其中包含作为匹配值的 IP 地址的列表，并将 operator 设置为 "Not" （求反为 true）以及要**阻止**的操作。 应用 IP 限制规则后，源自此允许列表外部地址的请求将收到403禁止的响应。

## <a name="configure-a-waf-policy-with-the-azure-portal"></a>使用 Azure 门户配置 WAF 策略

### <a name="prerequisites"></a>必备条件

按照[快速入门：为高度可用的全局 web 应用程序创建前门](../../frontdoor/quickstart-create-front-door.md)中所述的说明创建 Azure 前门配置文件。

### <a name="create-a-waf-policy"></a>创建 WAF 策略

1. 在 Azure 门户上，选择 "**创建资源**"，在搜索框中键入 " **web 应用程序防火墙**"，然后选择 " **WEB 应用程序防火墙（WAF）**"。
2. 选择“创建”。 
3. 在 "**创建 WAF 策略**" 页上，使用以下值完成 "**基本**信息" 选项卡：
   
   |设置  |值  |
   |---------|---------|
   |策略     |全局 WAF （前门）|
   |订阅     |选择订阅|
   |资源组     |选择前门所在的资源组。|
   |策略名称     |键入策略的名称|
   |策略状态     |已启用|

   选择**下一步：策略设置**

1. 在 "**策略设置**" 选项卡上，选择 "**阻止**"。 对于 "**阻止响应正文**"，请键入*已被阻止！* 您可以看到您的自定义规则已生效。
2. 选择 "**下一步：托管规则**"。
3. 选择 "**下一步：自定义规则**"。
4. 选择 "**添加自定义规则**"。
5. 在 "**添加自定义规则**" 页上，使用以下测试值创建自定义规则：

   |设置  |值  |
   |---------|---------|
   |自定义规则名称     |FdWafCustRule|
   |状态     |已启用|
   |规则类型     |匹配|
   |Priority    |100|
   |匹配类型     |IP 地址|
   |Match 变量|RemoteAddr|
   |Operation|不包含|
   |IP 地址或范围|10.10.10.0/24|
   |则|拒绝流量|

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/custom-rule.png" alt-text="自定义规则":::

   选择 **添加** 。
6. 选择**下一步：关联**。
7. 选择 "**添加前端主机**"。
8. 对于 "**前端主机**"，请选择前端主机，然后选择 "**添加**"。
9. 选择“查看 + 创建”  。
10. 策略验证通过后，选择 "**创建**"。

### <a name="test-your-waf-policy"></a>测试 WAF 策略

1. WAF 策略部署完成后，浏览到前门前端主机名。
2. 应会看到自定义的阻止消息。

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/waf-rule-test.png" alt-text="WAF 规则测试":::

   > [!NOTE]
   > 专用 IP 地址是特意在自定义规则中使用的，以确保规则将触发。 在实际部署中，为特定情况使用 IP 地址创建*允许*和*拒绝*规则。

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>使用 Azure CLI 配置 WAF 策略

### <a name="prerequisites"></a>必备条件
在开始配置 IP 限制策略之前，请设置 CLI 环境并创建 Azure 前门配置文件。

#### <a name="set-up-the-azure-cli-environment"></a>设置 Azure CLI 环境
1. 安装[Azure CLI](/cli/azure/install-azure-cli)，或使用 Azure Cloud Shell。 Azure Cloud Shell 是可直接在 Azure 门户中运行的免费 Bash shell。 它预安装有 Azure CLI 并将其配置为与帐户一起使用。 选择以下 CLI 命令中的 "**试用**" 按钮，然后在打开的 Cloud Shell 会话中登录到 Azure 帐户。 会话启动后，输入`az extension add --name front-door`以添加 Azure 前门扩展。
 2. 如果在 Bash 本地使用 CLI，请使用`az login`登录到 Azure。

#### <a name="create-an-azure-front-door-profile"></a>创建 Azure 前门配置文件
按照[快速入门：为高度可用的全局 web 应用程序创建前门](../../frontdoor/quickstart-create-front-door.md)中所述的说明创建 Azure 前门配置文件。

### <a name="create-a-waf-policy"></a>创建 WAF 策略

使用[az network 前门 WAF create](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-create)命令创建 WAF 策略。 在下面的示例中，使用唯一策略名称替换策略名称*IPAllowPolicyExampleCLI* 。

```azurecli-interactive 
az network front-door waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>添加自定义 IP 访问控制规则

使用[az network 前门 waf create](/cli/azure/ext/front-door/network/front-door/waf-policy/rule?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-rule-create)命令为刚创建的 waf 策略添加自定义 IP 访问控制规则。

在以下示例中：
-  将*IPAllowPolicyExampleCLI*替换为前面创建的唯一策略。
-  将*ip**地址范围-1 替换为你*自己的范围。

首先，为在上一步中创建的策略创建 IP 允许规则。 
> [!NOTE]
> **--延迟**是必需的，因为规则必须具有匹配条件才能在下一步中添加。

```azurecli
az network front-door waf-policy rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI --defer
```
接下来，将 "匹配条件" 添加到规则：

```azurecli
az network front-door waf-policy rule match-condition add \
--match-variable RemoteAddr \
--operator IPMatch \
--values "ip-address-range-1" "ip-address-range-2" \
--negate true \
--name IPAllowListRule \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI 
  ```
                                                   
### <a name="find-the-id-of-a-waf-policy"></a>查找 WAF 策略的 ID 
使用[az network 前门 WAF show](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-show)命令查找 WAF 策略的 ID。 将以下示例中的*IPAllowPolicyExampleCLI*替换为之前创建的唯一策略。

   ```azurecli
   az network front-door  waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>将 WAF 策略链接到 Azure 前门前端主机

使用[az network 前门 update](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update)命令将 Azure 前门*WebApplicationFirewallPolicyLink* ID 设置为策略 ID。 将*IPAllowPolicyExampleCLI*替换为之前创建的唯一策略。

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/resource-group-name/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
在此示例中，WAF 策略将应用于**FrontendEndpoints [0]**。 可以将 WAF 策略链接到任何前端。
> [!Note]
> 只需将**WebApplicationFirewallPolicyLink**属性设置一次，即可将 WAF 策略链接到 Azure 前门前端。 后续策略更新会自动应用到前端。

## <a name="configure-a-waf-policy-with-azure-powershell"></a>使用 Azure PowerShell 配置 WAF 策略

### <a name="prerequisites"></a>必备条件
在开始配置 IP 限制策略之前，请设置 PowerShell 环境，并创建 Azure 前门配置文件。

#### <a name="set-up-your-powershell-environment"></a>设置 PowerShell 环境
Azure PowerShell 提供了一组 cmdlet，这些 cmdlet 使用[azure 资源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)模型来管理 azure 资源。

可以在本地计算机上安装 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 并在任何 PowerShell 会话中使用它。 按照页面上的说明使用 Azure 凭据登录到 PowerShell，然后安装 Az 模块。

1. 使用以下命令连接到 Azure，然后使用交互式对话框进行登录。
    ```
    Connect-AzAccount
    ```
 2. 安装 Azure 前门模块之前，请确保已安装 PowerShellGet 模块的最新版本。 运行以下命令，然后重新打开 PowerShell。

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. 使用以下命令安装 FrontDoor 模块。 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-profile"></a>创建 Azure 前门配置文件
按照[快速入门：为高度可用的全局 web 应用程序创建前门](../../frontdoor/quickstart-create-front-door.md)中所述的说明创建 Azure 前门配置文件。

### <a name="define-an-ip-match-condition"></a>定义 IP 匹配条件
使用[AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject)命令定义 IP 匹配条件。
在下面的示例中，将 ip 地址范围 *-1**替换为你*自己的范围。    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```
     
### <a name="create-a-custom-ip-allow-rule"></a>创建自定义 IP 允许规则

使用[AzFrontDoorWafCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject)命令定义操作并设置优先级。 在下面的示例中，将阻止不是来自客户端 Ip 且与列表匹配的请求。

```azurepowershell
$IPAllowRule = New-AzFrontDoorWafCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Block -Priority 1
```

### <a name="configure-a-waf-policy"></a>配置 WAF 策略
使用`Get-AzResourceGroup`查找包含 Azure 前门配置文件的资源组的名称。 接下来，使用[AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)将 WAF 策略配置为 IP 规则。

```azurepowershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule`
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>将 WAF 策略链接到 Azure 前门前端主机

将 WAF 策略对象链接到现有前端主机并更新 Azure 前门属性。 首先，使用[AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)检索 Azure 前门对象。 接下来，使用[AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)命令将**WebApplicationFirewallPolicyLink**属性设置为在上一步中创建的 *$IPAllowPolicyExamplePS*的资源 ID。

```azurepowershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> 在此示例中，WAF 策略将应用于**FrontendEndpoints [0]**。 可以将 WAF 策略链接到任何前端。 只需将**WebApplicationFirewallPolicyLink**属性设置一次，即可将 WAF 策略链接到 Azure 前门前端。 后续策略更新会自动应用到前端。


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>使用资源管理器模板配置 WAF 策略
若要查看使用自定义 IP 限制规则创建 Azure 前门策略和 WAF 策略的模板，请参阅[GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip)。


## <a name="next-steps"></a>后续步骤

- 了解如何[创建 Azure 前门配置文件](../../frontdoor/quickstart-create-front-door.md)。
