---
title: 使用 Azure Front Door 和 Azure Web 应用程序防火墙 (WAF) 快速缩放和保护 Web 应用程序 | Microsoft Docs
description: 本文将帮助你了解如何结合使用 Web 应用程序防火墙和 Azure Front Door 服务
services: frontdoor
documentationcenter: ''
author: tremansdoerfer
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/06/2020
ms.author: rimansdo
ms.openlocfilehash: 3c0b23bc7c25c56953d267e5193a63fe08e35f64
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83663515"
---
# <a name="quickly-scale-and-protect-a-web-application-using-azure-front-door-and-azure-web-application-firewall-waf"></a>使用 Azure Front Door 和 Azure Web 应用程序防火墙 (WAF) 快速缩放和保护 Web 应用程序

在最近几周内，许多 Web 应用程序上与新冠肺炎相关的流量快速增加。 此外，这些 Web 应用程序也观察到恶意流量激增，包括拒绝服务攻击。 处理这两个需求、横向扩展以应对流量激增并防范攻击的有效方式是，设置 Azure Front Door 和 Azure WAF 作为 Web 应用程序前面的加速、缓存和安全层。 本文介绍了如何为在 Azure 内部或外部运行的任何 Web 应用程序快速设置 Azure Front Door 和 Azure WAF。 

在本教程中，我们将使用 Azure CLI 来设置 WAF，但在 Azure 门户、Azure PowerShell、Azure ARM 和 Azure REST API 中也完全支持所有这些步骤。 

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

此博客中的说明使用 Azure 命令行接口 (CLI)。 查看本指南，[开始使用 Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)。

*提示：在 Azure CLI 上轻松且快速开始的方法是使用 [Azure Cloud Shell 中的 Bash](https://docs.microsoft.com/azure/cloud-shell/quickstart)*

确保将 front-door 扩展添加到 Azure CLI

```azurecli-interactive 
az extension add --name front-door
```

注意：有关下面列出的命令的详细信息，请参阅 [Front Door 的 Azure CLI 参考](https://docs.microsoft.com/cli/azure/ext/front-door/?view=azure-cli-latest)。

## <a name="step-1-create-an-azure-front-door-afd-resource"></a>步骤 1：创建 Azure Front Door (AFD) 资源


```azurecli-interactive 
az network front-door create --backend-address <>  --accepted-protocols <> --name <> --resource-group <>
```

**--backend-address**：后端地址是要保护的应用程序的完全限定域名 (FQDN)。 例如 myapplication.contoso.com

**--accepted-protocols**：接受的协议指定你希望 AFD 为你的 Web 应用程序提供支持的所有协议。 例如 --accepted-protocols Http Https。

**--name**：指定 AFD 资源的名称

**--resource-group**：要在其中放置此 AFD 资源的资源组。  要详细了解资源组，请访问“在 Azure 中管理资源组”

在成功执行此命令所获得的响应中，查找“hostName”项，并记下其值，以便在后面的步骤中使用。 hostName 是已创建的 AFD 资源的 DNS 名称

## <a name="step-2-create-an-azure-waf-profile-to-use-with-azure-front-door-resources"></a>步骤 2：创建要与 Azure Front Door 资源一起使用的 Azure WAF 配置文件

```azurecli-interactive 
az network front-door waf-policy create --name <>  --resource-group <>  --disabled false --mode Prevention
```

--name：指定 Azure WAF 策略的名称

--resource-group：要在其中放置此 WAF 资源的资源组。 

以上 CLI 代码会创建一个已启用并处于防护模式的 WAF 策略。 

注意：另外建议在检测模式下创建 WAF，并观察它如何检测和记录恶意请求（不阻止），然后再决定更改为防护模式。

在成功执行此命令所获得的响应中，查找“ID”项，并记下其值，以便在后面的步骤中使用。 ID 字段应采用以下格式

/subscriptions/subscription id/resourcegroups/resource group name/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/WAF policy name  

## <a name="step-3-add-managed-rulesets-to-this-waf-policy"></a>步骤 3：将托管规则集添加到此 WAF 策略

在 WAF 策略中，你可以添加托管规则集，托管规则集指的是由 Microsoft 构建并管理的一组规则，可针对整个威胁类提供现成的防护。 在此示例中，我们将添加两个此类规则集：(1) 默认规则集，用于防范常见 Web 威胁；(2) 机器人防护规则集，用于防止恶意机器人

(1) 添加默认规则集

```azurecli-interactive 
az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type DefaultRuleSet --version 1.0
```

(2) 添加机器人管理器规则集

```azurecli-interactive 
az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type Microsoft_BotManagerRuleSet --version 1.0
```

--policy name：为 Azure WAF 资源提供的名称

--resource-group：已在其中放置此 WAF 资源的资源组。

## <a name="step-4-associate-the-waf-policy-with-the-afd-resource"></a>步骤 4：将 WAF 策略与 AFD 资源关联

在此步骤中，我们将已构建的 WAF 策略与位于 Web 应用程序前面的 AFD 资源关联。

```azurecli-interactive 
az network front-door update --name <> --resource-group <> --set frontendEndpoints[0].webApplicationFirewallPolicyLink='{"id":"<>"}'
```

--name：已为 AFD 资源指定的名称

--resource-group：已在其中放置 Azure Front Door 资源的资源组。

--set：在这里，你可以使用新构建的 WAF 策略为与 AFD 资源关联的 frontendEndpoint 更新属性 WebApplicationFirewallPolicyLink。 可以从上面步骤 2 获取的响应中找到 WAF 策略的 ID

注意：上面的示例适用于你未使用自定义域的情况

如果你未使用任何自定义域来访问 Web 应用程序，则可以跳过步骤 5。 在这种情况下，你将向最终用户提供你在步骤 1 中获得的主机名，以便导航到你的 Web 应用程序

## <a name="step-5-configure-custom-domain-for-your-web-application"></a>步骤 5：为 Web 应用程序配置自定义域

最初，Web 应用程序的自定义域名（客户用于引用应用程序的域名，例如 www.contoso.com）指向在引入 AFD 之前运行该应用程序的位置。 对体系结构进行更改（即在应用程序前面添加 AFD+WAF）后，与该自定义域相对应的 DNS 条目现在应指向此 AFD 资源。 为此，可以将 DNS 服务器中的此条目重新映射到步骤 1 中记下的 AFD 主机名。

更新 DNS 记录的具体步骤将取决于你的 DNS 服务提供商，但如果使用 Azure DNS 来托管 DNS 名称，则可以参阅[更新 DNS 记录的步骤](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-cli)文档，然后指向 AFD 主机名。 

这里需要注意的一点是，如果需要用户使用区域顶点导航到你的网站（例如 contoso.com），则必须使用 Azure DNS 及其[别名记录类型](https://docs.microsoft.com/azure/dns/dns-alias)来托管你的 DNS 名称。 

此外，还需要更新 AFD 配置，[向其添加此自定义域](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain)，以便 AFD 知道此映射。

最后，如果你使用自定义域来访问 Web 应用程序并想要启用 HTTPS 协议，则需要[在 AFD 中为你的自定义域设置证书](https://docs.microsoft.com/azure/frontdoor/front-door-custom-domain-https)。 

## <a name="step-6-lock-down-your-web-application"></a>步骤 6：锁定 Web 应用程序

要遵循的一个可选最佳做法是确保只有 AFD 边缘可以与 Web 应用程序进行通信。 此操作可确保没有人可以绕过 AFD 保护直接访问应用程序。 可以通过访问 [AFD 的常见问题解答部分](https://docs.microsoft.com/azure/frontdoor/front-door-faq)，并参考有关锁定后端以仅通过 AFD 进行访问的问题，来完成此锁定。
