---
title: 教程：使用 Azure Front Door 和 Azure Web 应用程序防火墙 (WAF) 缩放和保护 Web 应用
description: 本教程将演示如何将 Azure Web 应用程序防火墙与 Azure Front Door 服务结合使用。
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/01/2020
ms.author: duau
ms.openlocfilehash: 7c5e938f985296e0534ca6e2438cf3acedb0fb65
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91626473"
---
# <a name="tutorial-quickly-scale-and-protect-a-web-application-by-using-azure-front-door-and-azure-web-application-firewall-waf"></a>教程：使用 Azure Front Door 和 Azure Web 应用程序防火墙 (WAF) 快速缩放和保护 Web 应用程序

在最近几周内，由于新冠肺炎疫情 (COVID-19) 影响，许多 Web 应用的流量迅速增加。 这些 Web 应用也同样遭遇了恶意流量激增，包括拒绝服务攻击。 有一种有效的方法既可以横向扩展应用程序以应对流量激增，又可以防范攻击，即：将带有 Azure WAF 的 Azure Front Door 配置为 Web 应用前面的加速、缓存和安全层。 本文介绍如何为在 Azure 内部或外部运行的任何 Web 应用配置带有 Azure WAF 的 Azure Front Door。 

在本教程中，我们将使用 Azure CLI 来配置 WAF。 可以通过使用 Azure 门户、Azure PowerShell、Azure 资源管理器或 Azure REST API 来完成相同的任务。 

在本教程中，你将了解：
> [!div class="checklist"]
> - 创建 Front Door。
> - 创建 Azure WAF 策略。
> - 为 WAF 策略配置规则集。
> - 将 WAF 策略与 Front Door 关联。
> - 配置自定义域。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

- 本教程中的说明使用 Azure CLI。 [查看本指南](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest&preserve-view=true)以开始使用 Azure CLI。

  > [!TIP] 
  > 快速且轻松地开始使用 Azure CLI 的方法是利用 [Azure Cloud Shell 中的 Bash](https://docs.microsoft.com/azure/cloud-shell/quickstart)。

- 确保将 `front-door` 扩展添加到 Azure CLI：

   ```azurecli-interactive 
   az extension add --name front-door
   ```

> [!NOTE] 
> 有关本教程中使用的命令的详细信息，请参阅 [适用于 Azure Front Door 的 Azure CLI 参考](https://docs.microsoft.com/cli/azure/ext/front-door/?view=azure-cli-latest&preserve-view=true)。

## <a name="create-an-azure-front-door-resource"></a>创建 Azure Front Door 资源

```azurecli-interactive 
az network front-door create --backend-address <>  --accepted-protocols <> --name <> --resource-group <>
```

`--backend-address`：要保护的应用程序的完全限定域名 (FQDN)。 例如 `myapplication.contoso.com`。

`--accepted-protocols`：指定你希望 Azure Front Door 为 Web 应用提供支持的协议。 例如 `--accepted-protocols Http Https`。

`--name`：Azure Front Door 资源的名称。

`--resource-group`：要在其中放置此 Azure Front Door 资源的资源组。 若要详细了解资源组，请参阅[ Azure 中管理资源组](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal)。

在运行此命令时得到的响应中，查找密钥 `hostName`。 稍后的步骤中需要用到此值。 `hostName` 是所创建的 Azure Front Door 资源的 DNS 名称。

## <a name="create-an-azure-waf-profile-to-use-with-azure-front-door-resources"></a>创建要与 Azure Front Door 资源一起使用的 Azure WAF 配置文件

```azurecli-interactive 
az network front-door waf-policy create --name <>  --resource-group <>  --disabled false --mode Prevention
```

`--name`：新的 Azure WAF 策略的名称。

`--resource-group`：要在其中放置此 WAF 资源的资源组。 

前面的 CLI 代码将创建一个已启用且处于预防模式的 WAF 策略。 

> [!NOTE] 
> 你可能想要在检测模式下创建 WAF 策略，并在决定使用保护模式之前观察它如何检测和记录恶意请求（而不阻止这些请求）。

在运行此命令时得到的响应中，查找密钥 `ID`。 稍后的步骤中需要用到此值。 

`ID` 字段的格式应为：

/subscriptions/subscription id/resourcegroups/resource group name/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/WAF policy name  

## <a name="add-managed-rule-sets-to-the-waf-policy"></a>将托管规则集添加到 WAF 策略

你可以将托管规则集添加到 WAF 策略。 托管规则集是由 Microsoft 构建和管理的一组规则，可帮助你抵御一类威胁。 在此示例中，我们将添加两个规则集：
- 默认规则集，有助于保护你免受常见的 Web 威胁。 
- 机器人防护规则集，有助于免受恶意机器人的影响。

添加默认规则集：

   ```azurecli-interactive 
   az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type DefaultRuleSet --version 1.0
   ```

添加机器人防护规则集：

   ```azurecli-interactive 
   az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type Microsoft_BotManagerRuleSet --version 1.0
   ```

`--policy-name`：为 Azure WAF 资源指定的名称。

`--resource-group`：放入 WAF 资源的资源组。

## <a name="associate-the-waf-policy-with-the-azure-front-door-resource"></a>将 WAF 策略与 Azure Front Door 资源关联

在此步骤中，我们将已创建的 WAF 策略与位于 Web 应用前面的 Azure Front Door 资源关联：

```azurecli-interactive 
az network front-door update --name <> --resource-group <> --set frontendEndpoints[0].webApplicationFirewallPolicyLink='{"id":"<>"}'
```

`--name`：为 Azure Front Door 资源指定的名称。

`--resource-group`：已在其中放置 Azure Front Door 资源的资源组。

`--set`：在此项中，你可以使用新的 WAF 策略更新与 Azure Front Door 资源关联的 `frontendEndpoint` 的 `WebApplicationFirewallPolicyLink` 属性。 你应在本教程前面创建 WAF 配置文件时得到的响应中获得 WAF 策略的 ID。

 > [!NOTE] 
> 以上示例适用于不使用自定义域的情况。 如果你未使用任何自定义域来访问 Web 应用，则可以跳过下一部分。 在这种情况下，你将向客户提供在创建 Azure Front Door 资源时获得的 `hostName`。 他们将使用此 `hostName` 转到 Web 应用。

## <a name="configure-the-custom-domain-for-your-web-application"></a>为 Web 应用配置自定义域

Web 应用的自定义域名是客户用来引用应用程序的域名。 例如 www.contoso.com。 最初，此自定义域名指向引入 Azure Front Door 之前它运行的位置。 将 Azure Front Door 和 WAF 添加到应用程序前面之后，与该自定义域对应的 DNS 条目应该指向 Azure Front Door 资源。 你可以通过将 DNS 服务器中的条目重新映射到在创建 Azure Front Door 资源时记下的 Azure Front Door `hostName` 来进行此更改。

更新 DNS 记录的具体步骤将取决于 DNS 服务提供商。 如果你使用 Azure DNS 来托管 DNS 名称，则可以参考文档以了解[更新 DNS 记录的步骤](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-cli)并指向 Azure Front Door `hostName`。 

如果需要客户使用区域顶点（例如 contoso.com）访问网站，则需要注意一件重要的事情。 在这种情况下，必须使用 Azure DNS 及其[别名记录类型](https://docs.microsoft.com/azure/dns/dns-alias)来托管 DNS 名称。 

你还需要更新 Azure Front Door 配置，[向其添加此自定义域](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain)，以便它知道此映射。

最后，如果你使用自定义域来访问 Web 应用程序，并且想要启用 HTTPS 协议， 则需要[在 Azure Front Door 中为自定义域设置证书](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain-https)。 

## <a name="lock-down-your-web-application"></a>锁定 Web 应用程序

建议你确保只有 Azure Front Door 边缘可以与 Web 应用程序通信。 这样做将确保没有人可以绕过 Azure Front Door 保护直接访问应用程序。 若要实现此锁定，请参阅[如何实现将后端访问锁定为仅限于 Azure Front Door？](https://docs.microsoft.com/azure/frontdoor/front-door-faq#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door)。

## <a name="clean-up-resources"></a>清理资源

如果不再需要本教程中使用的资源，请使用 [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete&preserve-view=true) 命令删除资源组、Front Door 和 WAF 策略：

```azurecli-interactive
  az group delete \
    --name <>
```
`--name`：本教程中使用的所有资源的资源组的名称。

## <a name="next-steps"></a>后续步骤

若要了解如何排查 Front Door 问题，请参阅故障排除指南：

> [!div class="nextstepaction"]
> [排查常见的路由问题](front-door-troubleshoot-routing.md)
