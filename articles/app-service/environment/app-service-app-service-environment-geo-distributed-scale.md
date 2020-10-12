---
title: 异地分布式缩放
description: 了解如何在流量管理器和应用服务环境中使用异地分布来水平缩放应用。
author: stefsch
ms.assetid: c1b05ca8-3703-4d87-a9ae-819d741787fb
ms.topic: article
ms.date: 09/07/2016
ms.author: stefsch
ms.custom: seodec18, references_regions
ms.openlocfilehash: 004b32118521f72c5b59ad7bab2d4e41244b85c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85833598"
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>应用服务环境的异地分布式缩放
## <a name="overview"></a>概述

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

需要很高缩放性的应用程序方案可能超过可用于单个应用部署的计算资源容量。  例如，投票应用程序、体育活动及电视娱乐活动都属于需要极高缩放性的方案。 通过横向扩展应用可以满足高缩放性要求。 若要处理非常高的负载需求，可在单个区域内以及跨区域进行多个应用部署。

应用服务环境是非常适合横向扩展的平台。选择可支持已知请求率的应用服务环境配置后，开发人员可通过千篇一律的方式部署其他应用服务环境，以获得所需的峰值负载容量。

例如，假设在应用服务环境配置上运行的应用已经过测试，每秒可处理 20K 个请求 (RPS)。  如果所需的峰值负载容量是 100K RPS，可以创建并配置五 (5) 个应用服务环境，以确保应用程序能够处理最大预计负载。

由于客户通常使用自定义（或虚构）域访问应用，因此开发人员必须要有将应用请求分散到所有应用服务环境实例的方法。  建议通过使用 [Azure 流量管理器配置文件][AzureTrafficManagerProfile]解析自定义域的方法来实现此目标。  流量管理器配置文件可配置为指向每个应用服务环境。  根据流量管理器配置文件中的负载均衡设置，流量管理器会自动将客户分散到所有的应用服务环境中。  无论所有的应用服务环境是部署在单个 Azure 区域还是跨多个 Azure 区域部署于世界各地，此方法都可正常进行。

此外，由于客户通过虚构域访问应用，因此无从得知运行应用的 应用服务环境数。  因此，开发人员可以根据观察到的流量负载，快速轻松地添加和删除应用服务环境。

下面的概念图描绘了在单个区域内以水平方式扩展到三个应用服务环境的应用。

![概念体系结构][ConceptualArchitecture] 

本主题的其余部分演练如何使用多个应用服务环境为示例应用设置分布式拓扑所需的步骤。

## <a name="planning-the-topology"></a>规划拓扑
在规划分布式应用的占用空间之前，最好先准备好几项信息。

* **应用的自定义域：** 客户访问应用时使用的自定义域名是什么？  对于示例应用，自定义域名为 `www.scalableasedemo.com`。
* **流量管理器域：** 创建 [Azure 流量管理器配置文件][AzureTrafficManagerProfile]时请选择域名。  此名称与 *trafficmanager.net* 后缀相结合，以注册流量管理器所管理的域条目。  就示例应用而言，选择的名称是 *scalable-ase-demo*。  因此，流量管理器所管理的完整域名是 *scalable-ase-demo.trafficmanager.net*。
* **缩放应用占用空间的策略：** 应用程序占用空间是否分布到单个区域中的多个应用服务环境？  是多个区域吗？  两种方法要混搭使用吗？  做决策时请依据对客户流量来源位置的预期，以及应用的支持性后端基础结构其余部分的可缩放程度。  例如，对于 100% 无状态的应用程序，应用可以通过使用每个 Azure 区域中多个应用服务环境的组合进行大规模缩放，通过多个 Azure 区域间部署的应用服务环境成倍扩展。  由于有 15 个以上的全球 Azure 区域可供选择，客户可真正构建全球性超高缩放性的应用程序范围。  对于本文使用的示例应用，在单个 Azure 区域中创建了三个应用服务环境 (美国中南部) 。
* **应用服务环境的命名约定：** 每个应用服务环境需要唯一的名称。  有两个或更多应用服务环境时，命名约定将有助于标识每个应用服务环境。  对于该示例应用，使用的是简单命名约定。  三个应用服务环境的名称分别是 *fe1ase*、*fe2ase* 和 *fe3ase*。
* **应用的命名约定：** 由于将部署多个应用实例，每个部署的应用实例都要有名称。  一项鲜为人知但非常方便的应用服务环境功能是，在多个应用服务环境中可以使用同一个应用名称。  由于每个应用服务环境都有唯一的域后缀，开发人员可以选择在每个环境中重复使用完全相同的应用名称。  例如，开发人员可能具有名为的应用程序，如下所示：  *myapp.foo1.p.azurewebsites.net*、 *myapp.foo2.p.azurewebsites.net*、 *myapp.foo3.p.azurewebsites.net*等。 但对于该示例应用程序，每个应用程序实例也具有唯一的名称。  所用的应用实例名称是 *webfrontend1*、*webfrontend2* 和 *webfrontend3*。

## <a name="setting-up-the-traffic-manager-profile"></a>设置流量管理器配置文件
将应用的多个实例部署在多个应用服务环境上之后，可以使用流量管理器来注册单个应用实例。  对于示例应用程序，需要对 *scalable-ase-demo.trafficmanager.net* 使用流量管理器配置文件，以便将客户路由到下列任何已部署的应用程序实例：

* **webfrontend1.fe1ase.p.azurewebsites.net：** 部署在第一个应用服务环境中的示例应用实例。
* **webfrontend2.fe2ase.p.azurewebsites.net：** 部署在第二个应用服务环境中的示例应用实例。
* **webfrontend3.fe3ase.p.azurewebsites.net：** 部署在第三个应用服务环境中的示例应用实例。

若要注册多个 Azure 应用服务终结点（全部运行于同一 Azure 区域中），最简单的方法是使用 PowerShell [Azure 资源管理器流量管理器支持][ARMTrafficManager]。  

第一个步骤是创建 Azure 流量管理器配置文件。  以下代码演示如何为示例应用创建配置文件：

```azurepowershell-interactive
$profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

请注意 *RelativeDnsName* 参数已设置为 *scalable-ase-demo*。  此参数会导致创建域名 *scalable-ase-demo.trafficmanager.net* ，并将其与流量管理器配置文件相关联。

*TrafficRoutingMethod* 参数定义负载均衡策略，流量管理器将使用该策略来确定如何将客户负载分散到所有可用的终结点。  在本示例中，选择的方法是 Weighted。  由于选择此方法，客户请求将会根据与每个终结点关联的相对权重被分散到所有已注册的应用程序终结点。 

在创建配置文件后，每个应用实例都添加到配置文件作为本机 Azure 终结点。  以下代码提取对每个前端 Web 应用的引用。 然后，它通过 TargetResourceId 参数将每个应用添加为流量管理器终结点。

```azurepowershell-interactive
$webapp1 = Get-AzWebApp -Name webfrontend1
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

$webapp2 = Get-AzWebApp -Name webfrontend2
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

$webapp3 = Get-AzWebApp -Name webfrontend3
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile
```

请注意，每个应用实例分别有一个 *Add-AzureTrafficManagerEndpointConfig* 调用。  每个 PowerShell 命令中的 TargetResourceId 参数都引用三个已部署的应用实例之一。  流量管理器配置文件将负载分布在配置文件中注册的所有三个终结点上。

所有三个终结点对 *Weight* 参数使用了相同的值 (10)。  这种情况会导致流量管理器将客户请求相对均匀地分散到所有三个应用实例。 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>将应用的自定义域指向流量管理器域
最后一个必要步骤是将应用的自定义域指向流量管理器域。  对于示例应用，应将 `www.scalableasedemo.com` 指向 `scalable-ase-demo.trafficmanager.net`。  请使用管理自定义域的域注册机构来完成这一步。  

如果使用注册机构的域管理工具，则需要创建一条将自定义域指向流量管理器域的 CNAME 记录。  下图显示了此 CNAME 配置的示例：

![自定义域的 CNAME][CNAMEforCustomDomain] 

尽管本主题并未说明，但请记住，每个应用实例也都需要注册其自定义域。  否则，如果一个请求到达应用实例，而应用程序尚未向该应用注册自定义域，则该请求将会失败。

在本示例中，自定义域是 `www.scalableasedemo.com`，并且每个应用程序实例都有与其关联的自定义域。

![自定义域][CustomDomain] 

有关在 Azure 应用服务应用中注册自定义域的汇总信息，请参阅[注册自定义域][RegisterCustomDomain]。

## <a name="trying-out-the-distributed-topology"></a>试用分布式拓扑
使用流量管理器及 DNS 配置的最终结果是 `www.scalableasedemo.com` 的请求经历以下顺序的流程：

1. 浏览器或设备对 `www.scalableasedemo.com` 发出 DNS 查找
2. 域注册机构上的 CNAME 条目使 DNS 查找重定向到 Azure 流量管理器。
3. 对某个 Azure 流量管理器 DNS 服务器执行 *scalable-ase-demo.trafficmanager.net* 的 DNS 查找。
4. 根据前面 *TrafficRoutingMethod* 参数中指定的负载平衡策略，流量管理器会选择已配置的终结点之一。 然后，它会将该终结点的 FQDN 返回到浏览器或设备。
5. 由于终结点的 FQDN 是在应用服务环境中运行的应用实例的 URL，因此浏览器或设备将会要求 Microsoft Azure DNS 服务器将 FQDN 解析为 IP 地址。 
6. 浏览器或设备将 HTTP/S 请求发送到此 IP 地址。  
7. 请求送达至某个应用服务环境中运行的应用实例之一。

下面的控制台图片显示了对示例应用的自定义域进行的 DNS 查找。 它成功解析为在三个示例应用服务环境之一（在这种情况下是这三个应用服务环境中的第二个）上运行的应用实例：

![DNS 查找][DNSLookup] 

## <a name="additional-links-and-information"></a>其他链接和信息
有关 PowerShell [Azure 资源管理器流量管理器支持][ARMTrafficManager]的文档。  

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[AzureTrafficManagerProfile]: ../../traffic-manager/traffic-manager-manage-profiles.md
[ARMTrafficManager]: ../../traffic-manager/traffic-manager-powershell-arm.md
[RegisterCustomDomain]: ../app-service-web-tutorial-custom-domain.md


<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-geo-distributed-scale/ConceptualArchitecture-1.png
[CNAMEforCustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CNAMECustomDomain-1.png
[DNSLookup]:  ./media/app-service-app-service-environment-geo-distributed-scale/DNSLookup-1.png
[CustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CustomDomain-1.png 
