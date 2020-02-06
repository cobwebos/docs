---
title: 快速入门：创建配置文件以实现应用程序的高可用性 - Azure PowerShell - Azure 流量管理器
description: 本快速入门文章介绍如何创建流量管理器配置文件，以生成高度可用的 Web 应用程序。
services: traffic-manager
author: rohinkoul
mnager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2019
ms.author: rohink
ms.openlocfilehash: 0ab7392b4fa6e248d51392706fedaed156344a99
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934818"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-powershell"></a>快速入门：使用 Azure PowerShell 创建流量管理器配置文件以实现 Web 应用程序的高可用性

本快速入门介绍如何创建流量管理器配置文件，以便实现 Web 应用程序的高度可用性。

在本快速入门中，我们将创建 Web 应用程序的两个实例。 每个实例在不同的 Azure 区域运行。 需根据[终结点优先级](traffic-manager-routing-methods.md#priority-traffic-routing-method)创建流量管理器配置文件。 此配置文件将用户流量定向到运行 Web 应用程序的主站点。 流量管理器持续监视 Web 应用程序。 如果主站点不可用，它会提供目标为备份站点的自动故障转移。

如果还没有 Azure 订阅，请现在就创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 PowerShell，则本文需要 Azure PowerShell 模块 5.4.1 或更高版本。 运行 `Get-Module -ListAvailable Az` 查找已安装的版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-Az-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 来创建与 Azure 的连接。

## <a name="create-a-resource-group"></a>创建资源组。
使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 创建资源组。

```azurepowershell-interactive


# Variables
$Location1="WestUS"

# Create a Resource Group
New-AzResourceGroup -Name MyResourceGroup -Location $Location1
```

## <a name="create-a-traffic-manager-profile"></a>创建流量管理器配置文件

使用 [New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) 创建流量管理器配置文件，以根据终结点优先级定向用户流量。

```azurepowershell-interactive

# Generates a random value
$Random=(New-Guid).ToString().Substring(0,8)
$mytrafficmanagerprofile="mytrafficmanagerprofile$Random"

New-AzTrafficManagerProfile `
-Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup `
-TrafficRoutingMethod Priority `
-MonitorPath '/' `
-MonitorProtocol "HTTP" `
-RelativeDnsName $mytrafficmanagerprofile `
-Ttl 30 `
-MonitorPort 80
```

## <a name="create-web-apps"></a>创建 Web 应用

本快速入门需要两个部署在两个不同的 Azure 区域（“美国西部”和“美国东部”）的 Web 应用程序实例。   每个都可以充当流量管理器的主终结点和故障转移终结点。

### <a name="create-web-app-service-plans"></a>创建 Web 应用服务计划
使用 [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) 为要部署在两个不同 Azure 区域中的两个 Web 应用程序实例创建 Web 应用服务计划。

```azurepowershell-interactive

# Variables
$App1Name="AppServiceTM1$Random"
$App2Name="AppServiceTM2$Random"
$Location1="WestUS"
$Location2="EastUS"

# Create an App service plan
New-AzAppservicePlan -Name "$App1Name-Plan" -ResourceGroupName MyResourceGroup -Location $Location1 -Tier Standard
New-AzAppservicePlan -Name "$App2Name-Plan" -ResourceGroupName MyResourceGroup -Location $Location2 -Tier Standard

```
### <a name="create-a-web-app-in-the-app-service-plan"></a>在应用服务计划中创建 Web 应用
在应用服务计划中使用 [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) 在“美国西部”和“美国东部”Azure 区域中创建 Web 应用程序的两个实例。  

```azurepowershell-interactive
$App1ResourceId=(New-AzWebApp -Name $App1Name -ResourceGroupName MyResourceGroup -Location $Location1 -AppServicePlan "$App1Name-Plan").Id
$App2ResourceId=(New-AzWebApp -Name $App2Name -ResourceGroupName MyResourceGroup -Location $Location2 -AppServicePlan "$App2Name-Plan").Id

```

## <a name="add-traffic-manager-endpoints"></a>添加流量管理器终结点
使用 [New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) 将两个 Web 应用作为流量管理器终结点添加到流量管理器配置文件，如下所示：
- 将“美国西部”Azure 区域中的 Web 应用添加为主要终结点，以路由所有用户流量。  
- 将“美国东部”Azure 区域中的 Web 应用添加为故障转移终结点。  当主终结点不可用时，流量自动路由到故障转移终结点。

```azurepowershell-interactive
New-AzTrafficManagerEndpoint -Name "$App1Name-$Location1" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App1ResourceId `
-EndpointStatus "Enabled"

New-AzTrafficManagerEndpoint -Name "$App2Name-$Location2" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App2ResourceId `
-EndpointStatus "Enabled"
```

## <a name="test-traffic-manager-profile"></a>测试流量管理器配置文件

在此部分，需检查流量管理器配置文件的域名。 此外还需将主终结点配置为不可用。 最后可以看到该 Web 应用仍然可用。 这是因为流量管理器将流量发送到故障转移终结点。

### <a name="determine-the-dns-name"></a>确定 DNS 名称

使用 [Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile) 确定流量管理器配置文件的 DNS 名称。

```azurepowershell-interactive
Get-AzTrafficManagerProfile -Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup
```

复制 **RelativeDnsName** 值。 流量管理器配置文件的 DNS 名称为 *http://<* relativednsname *>.trafficmanager.net*。 

### <a name="view-traffic-manager-in-action"></a>查看正在运行的流量管理器
1. 在 Web 浏览器中输入流量管理器配置文件的 DNS 名称 (*http://<* relativednsname *>.trafficmanager.net*)，以查看 Web 应用的默认网站。

    > [!NOTE]
    > 在本快速入门方案中，所有请求都路由到主终结点。 它设置为“优先级 1”。 
2. 若要查看流量管理器故障转移如何进行，请使用 [Disable-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/disable-aztrafficmanagerendpoint) 禁用主要站点。

   ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name $App1Name-$Location1 `
    -Type AzureEndpoints `
    -ProfileName $mytrafficmanagerprofile `
    -ResourceGroupName MyResourceGroup `
    -Force
   ```
3. 复制流量管理器配置文件的 DNS 名称 (*http://<* relativednsname *>.trafficmanager.net*)，以在新的 Web 浏览器会话中查看该网站。
4. 验证 Web 应用是否仍然可用。

## <a name="clean-up-resources"></a>清理资源

完成后，请使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 删除资源组、Web 应用程序和所有相关资源。

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，我们创建了一个可为 Web 应用程序提供高可用性的流量管理器配置文件。 若要详细了解如何路由流量，请继续学习流量管理器教程。

> [!div class="nextstepaction"]
> [流量管理器教程](tutorial-traffic-manager-improve-website-response.md)
