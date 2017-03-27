---
title: "应用服务环境的异地分布式缩放"
description: "了解如何在流量管理器和应用服务环境中使用异地分布来水平缩放应用。"
services: app-service
documentationcenter: 
author: stefsch
manager: erikre
editor: 
ms.assetid: c1b05ca8-3703-4d87-a9ae-819d741787fb
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2016
ms.author: stefsch
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: e7f816713cc74ca27dd7718e8071f48dd653e213
ms.lasthandoff: 02/16/2017


---
# <a name="geo-distributed-scale-with-app-service-environments"></a>应用服务环境的异地分布式缩放
## <a name="overview"></a>概述
需要极高缩放性的应用程序方案可能超过单个应用部署可用的计算资源容量。  例如，投票应用程序、体育活动及电视娱乐活动都属于需要极高缩放性的方案。 只要对单个区域内和跨区域的多个应用部署进行应用的水平扩展，即可达到高缩放性要求以处理极高的负载要求。

应用服务环境是水平扩展的理想平台。  在选择可支持已知请求率的应用服务环境配置后，开发人员即可通过以千篇一律的方式部署其他应用服务环境，以获得所需的峰值负载容量。

例如，假设在应用服务环境配置上运行的应用已经过测试，每秒可处理 20K 个请求 (RPS)。  如果所需的峰值负载容量是 100K RPS，则可以创建并配置五 (5) 个应用服务环境，以确保应用程序能够处理最大预计负载。

由于客户通常使用自定义（或虚构）域访问应用，因此开发人员必须要有将应用请求分散到所有应用服务环境实例的方法。  使用 [Azure 流量管理器配置文件][AzureTrafficManagerProfile]解析自定义域是实现此目的的好方法。  流量管理器配置文件可配置为指向每个应用服务环境。  流量管理器根据流量管理器配置文件中的负载均衡设置，自动将客户分散到所有应用服务环境。  无论所有的应用服务环境是部署在单个 Azure 区域还是跨多个 Azure 区域部署于世界各地，此方法都可正常进行。

此外，由于客户通过虚构域访问应用，因此无从得知运行应用的 应用服务环境数。  因此，开发人员可以根据观察到的流量负载，快速轻松地添加和删除应用服务环境。

下面的概念图描绘了在单个区域内以水平方式扩展到三个应用服务环境的应用。

![概念体系结构][ConceptualArchitecture] 

本主题的其余部分将演练如何使用多个应用服务环境为示例应用设置分布式拓扑所需的步骤。

## <a name="planning-the-topology"></a>规划拓扑
在规划分布式应用的占用空间之前，最好先准备好几项信息。

* **应用的自定义域：**客户访问应用时使用的自定义域名是什么？  示例应用的自定义域名是 *www.scalableasedemo.com*
* **流量管理器域：**创建 [Azure 流量管理器配置文件][AzureTrafficManagerProfile]时需要选择域名。  此名称与 *trafficmanager.net* 后缀相结合，以注册流量管理器所管理的域条目。  就示例应用而言，选择的名称是 *scalable-ase-demo*。  因此，流量管理器所管理的完整域名是 *scalable-ase-demo.trafficmanager.net*。
* **缩放应用占用空间的策略：**应用程序占用空间是否分布到单个区域中的多个应用服务环境？  是多个区域吗？  两种方法要混搭使用吗？  决策依据应来自于客户流量的来源位置，以及其余应用的支持后端基础结构的可缩放性。  例如，对于 100% 无状态的应用程序，可以使用每一 Azure 区域多个应用服务环境的组合，乘以跨多个 Azure 区域部署的应用服务环境数，来大幅缩放应用。  由于有 15 个以上的公用 Azure 区域可供选择，客户将可真正构建全球性超高缩放性的应用程序占用空间。  在本文所使用的示例应用中，有三个应用服务环境创建在单个 Azure 区域（美国中南部）。
* **应用服务环境的命名约定：**每个应用服务环境都需要唯一名称。  有两个或更多应用服务环境时，命名约定将有助于标识每个应用服务环境。  示例应用中使用了简单的命名约定。  三个应用服务环境的名称分别是 *fe1ase*、*fe2ase* 和 *fe3ase*。
* **应用的命名约定：**由于将部署多个应用实例，每个部署的应用实例都要有名称。  有一项鲜为人知、但非常方便的应用服务环境功能，是多个应用服务环境可以使用相同的应用名称。  由于每个应用服务环境都有唯一的域后缀，开发人员可以选择在每个环境中重复使用完全相同的应用名称。  例如，开发人员可以将应用命名如下：*myapp.foo1.p.azurewebsites.net*、*myapp.foo2.p.azurewebsites.net*、*myapp.foo3.p.azurewebsites.net* 等。但示例应用的每个应用实例也都有唯一名称。  所用的应用实例名称是 *webfrontend1*、*webfrontend2* 和 *webfrontend3*。

## <a name="setting-up-the-traffic-manager-profile"></a>设置流量管理器配置文件
将应用的多个实例部署在多个应用服务环境上之后，可以使用流量管理器来注册单个应用实例。  就示例应用而言，*scalable-ase-demo.trafficmanager.net* 必须要有流量管理器配置文件，将客户路由到任何以下已部署的应用实例：

* **webfrontend1.fe1ase.p.azurewebsites.net：**部署在第一个应用服务环境中的示例应用实例。
* **webfrontend2.fe2ase.p.azurewebsites.net：**部署在第二个应用服务环境中的示例应用实例。
* **webfrontend3.fe3ase.p.azurewebsites.net：**部署在第三个应用服务环境中的示例应用实例。

要注册多个 Azure 应用服务终结点（全部在**相同**的 Azure 区域中运行），最简单的方法是使用 Powershell [Azure Resource Manager 流量管理器支持][ARMTrafficManager]。  

第一个步骤是创建 Azure 流量管理器配置文件。  以下代码演示如何为示例应用创建配置文件：

    $profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

请注意 *RelativeDnsName* 参数已设置为 *scalable-ase-demo*。  这说明域名 *scalable-ase-demo.trafficmanager.net* 是如何创建并与流量管理器配置文件关联的。

*TrafficRoutingMethod* 参数定义负载均衡策略，供流量管理器用于判断如何将客户负载分散到所有可用的终结点。  本示例选择了 *Weighted* 方法。  这使客户请求根据与每个终结点关联的相对加权分散到所有已注册的应用程序终结点。 

在创建配置文件后，每个应用实例都添加到配置文件作为本机 Azure 终结点。  下面的代码获取对每个前端 Web 应用的引用，然后通过 *TargetResourceId* 参数将每个应用添加为流量管理器终结点。

    $webapp1 = Get-AzureRMWebApp -Name webfrontend1
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

    $webapp2 = Get-AzureRMWebApp -Name webfrontend2
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

    $webapp3 = Get-AzureRMWebApp -Name webfrontend3
    Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

    Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

请注意，每个应用实例分别有一个 *Add-AzureTrafficManagerEndpointConfig* 调用。  每个 Powershell 命令中的 *TargetResourceId* 参数引用三个已部署应用实例之一。  流量管理器配置文件将负载分布在配置文件中注册的所有三个终结点上。

所有三个终结点对 *Weight* 参数使用了相同的值 (10)。  这使流量管理器将客户请求较平均地分散到所有三个应用实例。 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>将应用的自定义域指向流量管理器域
最后一个必要步骤是将应用的自定义域指向流量管理器域。  就示例应用而言，这意味着将 *www.scalableasedemo.com* 指向 *scalable-ase-demo.trafficmanager.net*。  此步骤必须以管理自定义域的域注册机构来完成。  

如果使用注册机构的域管理工具，则需要创建一条将自定义域指向流量管理器域的 CNAME 记录。  下图显示了此 CNAME 配置的示例：

![自定义域的 CNAME][CNAMEforCustomDomain] 

尽管本主题并未说明，但请记住，每个应用实例也都需要注册其自定义域。  否则，在对应用实例发出请求时，如果应用程序并未注册应用的自定义域，请求将失败。  

在本示例中，自定义域是 *www.scalableasedemo.com*，且每个应用程序实例都有其关联的自定义域。

![自定义域][CustomDomain] 

有关在 Azure 应用服务应用中注册自定义域的汇总信息，请参阅以下有关[注册自定义域][RegisterCustomDomain]的文章。

## <a name="trying-out-the-distributed-topology"></a>试用分布式拓扑
使用流量管理器及 DNS 配置的最终结果是 *www.scalableasedemo.com* 的请求经历以下顺序的流程：

1. 浏览器或设备执行 *www.scalableasedemo.com* 的 DNS 查找
2. 域注册机构上的 CNAME 条目使 DNS 查找重定向到 Azure 流量管理器。
3. 对某个 Azure 流量管理器 DNS 服务器执行 *scalable-ase-demo.trafficmanager.net* 的 DNS 查找。
4. 根据负载均衡策略（前面创建流量管理器配置文件时所用的 *TrafficRoutingMethod* 参数），流量管理器选择其中一个已配置的终结点，并将该终结点的 FQDN 返回到浏览器或设备。
5. 由于终结点的 FQDN 是在应用服务环境中运行的应用实例的 URL，因此浏览器或设备请求 Microsoft Azure DNS 服务器将 FQDN 解析为 IP 地址。 
6. 浏览器或设备将 HTTP/S 请求发送到此 IP 地址。  
7. 请求送达至某个应用服务环境中运行的应用实例之一。

以下控制台图片显示了示例应用自定义域的 DNS 查找；该域已成功解析为在三个示例应用服务环境之一（在本例中，为三个应用服务环境中的第二个）中运行的应用实例：

![DNS 查找][DNSLookup] 

## <a name="additional-links-and-information"></a>其他链接和信息
[README for Application Service Environments](../app-service/app-service-app-service-environments-readme.md)（应用程序服务环境自述文件）中提供了有关应用服务环境的所有文章和操作说明。

有关 Powershell [Azure Resource Manager 流量管理器支持][ARMTrafficManager]的文档。  

[!INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[AzureTrafficManagerProfile]:  https://azure.microsoft.com/documentation/articles/traffic-manager-manage-profiles/
[ARMTrafficManager]:  https://azure.microsoft.com/documentation/articles/traffic-manager-powershell-arm/
[RegisterCustomDomain]:  https://azure.microsoft.com/en-us/documentation/articles/web-sites-custom-domain-name/


<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-geo-distributed-scale/ConceptualArchitecture-1.png
[CNAMEforCustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CNAMECustomDomain-1.png
[DNSLookup]:  ./media/app-service-app-service-environment-geo-distributed-scale/DNSLookup-1.png
[CustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CustomDomain-1.png 

