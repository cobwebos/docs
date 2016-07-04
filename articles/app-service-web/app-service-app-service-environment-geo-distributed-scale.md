<!-- not suitable for Mooncake -->

<properties 
	pageTitle="Azure 环境的异地分布式缩放" 
	description="了解如何在流量管理器和 Azure 环境中使用异地分布来水平缩放应用。" 
	services="app-service" 
	documentationCenter="" 
	authors="stefsch" 
	manager="wpickett" 
	editor=""/>

<tags
	ms.service="app-service"
	ms.date="06/21/2016"
	wacn.date=""/>

# Azure 环境的异地分布式缩放

## 概述 ##
需要极高缩放性的应用程序方案可能超过单个应用部署可用的计算资源容量。例如，投票应用程序、体育活动及电视娱乐活动都属于需要极高缩放性的方案。只要对单个区域内和跨区域的多个应用部署进行应用的水平扩展，即可达到高缩放性要求以处理极高的负载要求。

Azure 环境是水平扩展的理想平台。在选择可支持已知请求率的 Azure 环境配置后，开发人员即可通过以一应百的方式部署其他 Azure 环境，以获得所需的高峰负载容量。

例如，假设在 Azure 环境配置上运行的应用已经过测试，每秒可处理 20K 个请求 (RPS)。如果所需的尖峰负载容量是 100K RPS，则可以创建并设置五 (5) 个 Azure 环境，以确保应用程序能够处理最大预测负载。

由于客户通常使用自定义（或虚构）域访问应用，因此开发人员必须要有将应用请求分散到所有 Azure 环境实例的方法。使用 [Azure 流量管理器配置文件][AzureTrafficManagerProfile]解析自定义域是不错的方法。流量管理器配置文件可配置为指向每个 Azure 环境。流量管理器根据流量管理器配置文件中的负载平衡设置，自动将客户分散到所有 Azure 环境。无论所有的 Azure 环境是部署在单个 Azure 区域还是跨多个 Azure 区域部署于世界各地，此方法都可正常进行。

此外，由于客户通过虚构域访问应用，因此无从得知运行应用的 Azure 环境数。因此，开发人员可以根据观察到的流量负载，快速轻松地添加和删除 Azure 环境。

下面的概念图描绘了在单个区域内以水平方式扩展到三个 Azure 环境的应用。

![概念体系结构][ConceptualArchitecture]

本主题的其余部分将演练如何使用多个 Azure 环境为示例应用设置分布式拓扑所需的步骤。

## 规划拓扑 ##
在规划分布式应用的占用空间之前，最好先准备好几项信息。

- **应用的自定义域：**客户访问应用时使用的自定义域名是什么？ 示例应用的自定义域名是 www.scalableasedemo.com
- **流量管理器域：**创建 [Azure 流量管理器配置文件][AzureTrafficManagerProfile]时需要选择域名。此名称与 trafficmanager.cn 后缀相结合，以注册流量管理器所管理的域条目。就示例应用而言，选择的名称是 scalable-ase-demo。因此，流量管理器所管理的完整域名是 scalable-ase-demo.trafficmanager.net。
- **缩放应用占用空间的策略：**应用程序占用空间是否分布到单个区域中的多个 Azure 环境？ 是多个区域吗？ 两种方法要混搭使用吗？ 决策依据应来自于客户流量的来源位置，以及其余应用的支持后端基础结构的可缩放性。例如，对于 100% 无状态的应用程序，可以使用每一 Azure 区域多个 Azure 环境的组合，乘以跨多个 Azure 区域部署的 Azure 环境数，来大幅缩放应用。由于有 15 个以上的公用 Azure 区域可供选择，客户将可真正构建全球性超高缩放性的应用程序占用空间。在本文所使用的示例应用中，有三个 Azure 环境创建在单个 Azure 区域（中国东部）。
- **Azure 环境的命名约定：**每个 Azure 环境都需要唯一名称。有两个或更多 Azure 环境时，命名约定将有助于标识每个 Azure 环境。示例应用中使用了简单的命名约定。三个 Azure 环境的名称分别是 fe1ase、fe2ase 和 fe3ase。
- **应用的命名约定：**由于将部署多个应用实例，每个部署的应用实例都要有名称。有一项鲜为人知、但非常方便的 Azure 环境功能，是多个 Azure 环境可以使用相同的应用名称。由于每个 Azure 环境都有唯一的域后缀，开发人员可以选择在每个环境中重复使用相同的应用名称。例如，开发人员可以将应用命名如下：myapp.foo1.p.azurewebsites.cn、myapp.foo2.p.azurewebsites.cn、myapp.foo3.p.azurewebsites.cn，依此类推。但示例应用的每个应用实例也都有唯一名称。所用的应用实例名称是 webfrontend1、webfrontend2 和 webfrontend3。


## 设置流量管理器配置文件 ##
将应用的多个实例部署在多个 Azure 环境上之后，可以使用流量管理器来注册单个应用实例。就示例应用而言，scalable-ase-demo.trafficmanager.cn 必须要有流量管理器配置文件，以将客户路由到任何以下已部署的应用实例：

- **webfrontend1.fe1ase.p.azurewebsites.cn：**部署在第一个 Azure 环境中的示例应用实例。
- **webfrontend2.fe2ase.p.azurewebsites.cn：**部署在第二个 Azure 环境中的示例应用实例。
- **webfrontend3.fe3ase.p.azurewebsites.cn：**部署在第三个 Azure 环境中的示例应用实例。

要注册多个 Azure 终结点（全部运行于相同的 Azure 区域中），最简单的方法是使用预览版 Powershell [Azure Resource Manager (ARM) 流量管理器支持][ARMTrafficManager]。

第一个步骤是创建 Azure 流量管理器配置文件。以下代码演示如何为示例应用创建配置文件：

    $profile = New-AzureTrafficManagerProfile -Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

请注意 RelativeDnsName 参数已设置为 scalable-ase-demo。这说明域名 scalable-ase-demo.trafficmanager.cn 是如何创建并与流量管理器配置文件关联的。

TrafficRoutingMethod 参数定义负载平衡策略，供流量管理器用于判断如何将客户负载分散到所有可用的终结点。本示例选择了 Weighted 方法。这使客户请求根据与每个终结点关联的相对加权分散到所有已注册的应用程序终结点。

在创建配置文件后，每个应用实例都添加到配置文件作为外部终结点。以下代码演示了添加到配置文件的三个应用实例的 URL。

    Add-AzureTrafficManagerEndpointConfig -EndpointName webfrontend1 -TrafficManagerProfile $profile -Type ExternalEndpoints -Target webfrontend1.fe1ase.p.chinacloudsites.cn -EndpointStatus Enabled -Weight 10
    Add-AzureTrafficManagerEndpointConfig -EndpointName webfrontend2 -TrafficManagerProfile $profile -Type ExternalEndpoints -Target webfrontend2.fe2ase.p.chinacloudsites.cn -EndpointStatus Enabled -Weight 10
    Add-AzureTrafficManagerEndpointConfig -EndpointName webfrontend3 -TrafficManagerProfile $profile -Type ExternalEndpoints -Target webfrontend3.fe3ase.p.chinacloudsites.cn -EndpointStatus Enabled -Weight 10
    
    Set-AzureTrafficManagerProfile -TrafficManagerProfile $profile

请注意，每个应用实例分别有一个 Add-AzureTrafficManagerEndpointConfig 调用。每个 Powershell 命令中的 Target 参数，分别指向三个已部署的应用实例的完全限定域名 (FQDN)。不同的 FQDN 分别用于定向 scalable-ase-demo.trafficmanager.cn 的 DNS CNAME 链接的值，以将流量负载分散到所有在流量管理器配置文件中注册的终结点。

三个终结点为 Weight 参数使用了相同的值 (10)。这使流量管理器将客户请求较平均地分散到所有三个应用实例。

注意：由于 ARM 流量管理器支持目前仍处于预览阶段，因此 Azure 终结点必须将 Type 参数设置为 ExternalEndpoints。Azure 终结点今后将以终结点类型的形式受到流量管理器 ARM 版本的本机支持。

## 将应用的自定义域指向流量管理器域 ##
最后一个必要步骤是将应用的自定义域指向流量管理器域。就示例应用而言，这意味着将 www.scalableasedemo.com 指向 scalable-ase-demo.trafficmanager.cn。此步骤必须以管理自定义域的域注册机构来完成。

如果使用注册机构的域管理工具，则需要创建一条将自定义域指向流量管理器域的 CNAME 记录。下图显示了此 CNAME 配置的示例：

![自定义域的 CNAME][CNAMEforCustomDomain]

尽管本主题并未说明，但请记住，每个应用实例也都需要注册其自定义域。否则，在对应用实例发出请求时，如果应用程序并未注册应用的自定义域，请求将失败。

在本示例中，自定义域是 www.scalableasedemo.com ，且每个应用程序实例都有其关联的自定义域。

![自定义域][CustomDomain]

有关在 Azure Web Apps 中注册自定义域的汇总信息，请参阅以下有关[注册自定义域][RegisterCustomDomain]的文章。

## 试用分布式拓扑 ##
使用流量管理器及 DNS 配置的最终结果是 www.scalableasedemo.com 的请求经历以下顺序的流程：

1. 浏览器或设备执行 www.scalableasedemo.com 的 DNS 查找
2. 域注册机构上的 CNAME 条目使 DNS 查找重定向到 Azure 流量管理器。
3. 对某个 Azure 流量管理器 DNS 服务器执行 scalable-ase-demo.trafficmanager.cn 的 DNS 查找。
4. 根据负载平衡策略（前面创建流量管理器配置文件时所用的 TrafficRoutingMethod 参数），流量管理器选择其中一个已设置的终结点，并将该终结点的 FQDN 返回到浏览器或设备。
5.  由于终结点的 FQDN 是在 Azure 环境上运行的应用实例的 URL，因此浏览器或设备请求 Microsoft Azure DNS 服务器将 FQDN 解析为 IP 地址。 
6. 浏览器或设备将 HTTP/S 请求发送到此 IP 地址。  
7. 请求送达至某个 Azure 环境上运行的应用实例之一。

以下控制台图片显示了示例应用自定义域的 DNS 查找；该域已成功解析为在三个示例 Azure 环境之一（在本例中，为三个 Azure 环境中的第二个）上运行的应用实例：

![DNS 查找][DNSLookup]

## 其他链接和信息 ##
[应用程序服务环境自述文件](/documentation/articles/app-service-app-service-environments-readme)中提供了有关 Azure 环境的所有文章和操作说明。

有关预览版 Powershell 的文档：[Azure Resource Manager (ARM) 流量管理器支持][ARMTrafficManager]。

[AZURE.INCLUDE [app-service-web-whats-changed](../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[AzureTrafficManagerProfile]: /documentation/articles/traffic-manager-manage-profiles/
[ARMTrafficManager]: /documentation/articles/traffic-manager-powershell-arm/
[RegisterCustomDomain]: /documentation/articles/web-sites-custom-domain-name/


<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-geo-distributed-scale/ConceptualArchitecture-1.png
[CNAMEforCustomDomain]: ./media/app-service-app-service-environment-geo-distributed-scale/CNAMECustomDomain-1.png
[DNSLookup]: ./media/app-service-app-service-environment-geo-distributed-scale/DNSLookup-1.png
[CustomDomain]: ./media/app-service-app-service-environment-geo-distributed-scale/CustomDomain-1.png

<!---HONumber=Mooncake_0627_2016-->