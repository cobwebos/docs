---
title: 快速入门：使用 Azure Front Door 设置高可用性 - Azure PowerShell
description: 本快速入门介绍如何通过 Azure PowerShell 来使用 Azure Front Door 创建具有高可用性和高性能的全局 Web 应用程序。
services: front-door
documentationcenter: na
author: duongau
manager: KumudD
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/21/2020
ms.author: duau
ms.openlocfilehash: 8ccc50dfc4c53f1ee207a04d7d7775775f13cb65
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91347810"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application-using-azure-powershell"></a>快速入门：使用 Azure PowerShell 创建 Front Door，以实现高度可用的全局 Web 应用程序

通过使用 Azure PowerShell 创建高度可用且高性能的全局 Web 应用程序，开始使用 Azure Front Door。

Front Door 会将 Web 流量定向到后端池中的特定资源。 你已定义前端域，请将资源添加到后端池，并创建路由规则。 本文使用一个后端池的简单配置，其中包含两个 Web 应用资源和一个使用默认路径匹配“/*”的路由规则。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 本地安装的 Azure PowerShell 或 Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-resource-group"></a>创建资源组

在 Azure 中，可将相关的资源分配到资源组。 可以使用现有资源组，也可以创建新组。

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 创建资源组：

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupFD -Location centralus
```

## <a name="create-two-instances-of-a-web-app"></a>创建 Web 应用的两个实例

本快速入门要求 Web 应用程序的两个实例在不同的 Azure 区域中运行。 这两个 Web 应用程序实例在“主动/主动”模式下运行，因此其中的任何一个实例都可以接收流量。 此配置不同于“主动/备用”配置，在后一种配置中，只有一个实例充当故障转移节点。

如果还没有 Web 应用，请使用以下脚本设置两个示例 Web 应用。

```azurepowershell-interactive
# Create first web app in Central US region.
$webapp1 = New-AzWebApp `
-Name "WebAppContoso-$(Get-Random)" `
-Location centralus `
-ResourceGroupName myResourceGroupFD `
-AppServicePlan myAppServicePlanCentralUS

# Create second web app in South Central US region.
$webapp2 = New-AzWebApp `
-Name "WebAppContoso-$(Get-Random)" `
-Location southcentralus `
-ResourceGroupName myResourceGroupFD `
-AppServicePlan myAppServicePlanSouthCentralUS
```

## <a name="create-a-front-door"></a>创建 Front Door

本部分详细介绍如何创建和配置 Front Door 的以下组件：
    
* 前端对象包含 Front Door 默认域。
* 后端池是一组等效后端，Front Door 会将客户端请求负载均衡到这些后端。
* 路由规则会将前端主机和匹配的 URL 路径模式映射到特定的后端池。

### <a name="create-a-frontend-object"></a>创建前端对象

前端对象为 Front Door 配置主机名。 默认情况下，主机名的后缀为“*.azurefd.net”。

```azurepowershell-interactive
# Create a unique name
$fdname = "contoso-frontend-$(Get-Random)"

#Create the frontend object
$FrontendEndObject = New-AzFrontDoorFrontendEndpointObject `
-Name "frontendEndpoint1" `
-HostName $fdname".azurefd.net"
```

### <a name="create-the-backend-pool"></a>创建后端池

后端池包含在本快速入门教程开头创建的两个 Web 应用。 此步骤中定义的运行状况探测和负载均衡设置使用默认值。

```azurepowershell-interactive
# Create backend objects that points to the hostname of the web apps
$backendObject1 = New-AzFrontDoorBackendObject `
-Address $webapp1.DefaultHostName
$backendObject2 = New-AzFrontDoorBackendObject `
-Address $webapp2.DefaultHostName

# Create a health probe object
$HealthProbeObject = New-AzFrontDoorHealthProbeSettingObject `
-Name "HealthProbeSetting"

# Create the load balancing setting object
$LoadBalancingSettingObject = New-AzFrontDoorLoadBalancingSettingObject `
-Name "Loadbalancingsetting" `
-SampleSize "4" `
-SuccessfulSamplesRequired "2" `
-AdditionalLatencyInMilliseconds "0"

# Create a backend pool using the backend objects, health probe, and load balancing settings
$BackendPoolObject = New-AzFrontDoorBackendPoolObject `
-Name "myBackendPool" `
-FrontDoorName $fdname `
-ResourceGroupName myResourceGroupFD `
-Backend $backendObject1,$backendObject2 `
-HealthProbeSettingsName "HealthProbeSetting" `
-LoadBalancingSettingsName "Loadbalancingsetting"
```

### <a name="create-a-routing-rule"></a>创建路由规则

路由规则会将后端池映射到前端域，并将默认路径匹配值设置为“/*”。

```azurepowershell-interactive
# Create a routing rule mapping the frontend host to the backend pool
$RoutingRuleObject = New-AzFrontDoorRoutingRuleObject `
-Name LocationRule `
-FrontDoorName $fdname `
-ResourceGroupName myResourceGroupFD `
-FrontendEndpointName "frontendEndpoint1" `
-BackendPoolName "myBackendPool" `
-PatternToMatch "/*"
```
### <a name="create-the-front-door"></a>创建 Front Door

现在，你已创建了必需的对象，接下来创建 Front Door：

```azurepowershell-interactive
# Creates the Front Door
New-AzFrontDoor `
-Name $fdname `
-ResourceGroupName myResourceGroupFD `
-RoutingRule $RoutingRuleObject `
-BackendPool $BackendPoolObject `
-FrontendEndpoint $FrontendEndObject `
-LoadBalancingSetting $LoadBalancingSettingObject `
-HealthProbeSetting $HealthProbeObject
```

部署成功后，可以按照下一部分中的步骤对其进行测试。

## <a name="test-the-front-door"></a>测试 Front Door

运行以下命令，获取 Front Door 的主机名。

```azurepowershell-interactive
# Gets Front Door in resource group and output the hostname of the frontend domain.
$fd = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD
$fd.FrontendEndpoints[0].Hostname
```

打开 Web 浏览器并输入从命令获取的主机名。 Front Door 会将你的请求定向到后端资源之一。 

:::image type="content" source="./media/quickstart-create-front-door-powershell/front-door-test-page.png" alt-text="Front Door 测试页":::

## <a name="clean-up-resources"></a>清理资源

如果不再需要使用 Front Door 创建的资源，请删除资源组。 删除资源组时，也会删除 Front Door 及其所有相关资源。 

若要删除资源组，请调用 `Remove-AzResourceGroup` cmdlet：

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupFD
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，我们创建了：
* Front Door
* 两个 Web 应用

若要了解如何将自定义域添加到 Front Door，请继续学习 Front Door 教程。

> [!div class="nextstepaction"]
> [添加自定义域](front-door-custom-domain.md)
