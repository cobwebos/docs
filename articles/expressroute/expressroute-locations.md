---
title: 连接服务提供商及其位置：Azure ExpressRoute | Microsoft Docs
description: 本文详细说明了服务的上市区域，以及如何连接到 Azure 区域。 按连接服务提供商排序。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 11/15/2019
ms.author: cherylmc
ms.openlocfilehash: 793d88ec67fbd839851894077e8f5c481b9ebda7
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74217097"
---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute 合作伙伴和对等位置

> [!div class="op_single_selector"]
> * [按提供商列出的位置](expressroute-locations.md)
> * [按位置列出的提供商](expressroute-locations-providers.md)


The tables in this article provide information on ExpressRoute geographical coverage and locations, ExpressRoute connectivity providers,and ExpressRoute System Integrators (SIs).

> [!Note]
> Azure regions and ExpressRoute locations are two distinct and different concepts, understanding the difference between the two is critical to exploring Azure hybrid networking connectivity. 
>
>

## <a name="azure-regions"></a>Azure 区域
Azure regions are global datacenters where Azure compute, networking and storage resources are located. When creating an Azure resource, a customer needs to select a resource location. The resource location determines which Azure datacenter (or availability zone) the resource is created in.

## <a name="expressroute-locations"></a>ExpressRoute locations
ExpressRoute locations (sometimes referred to as peering locations or meet-me-locations) are co-location facilities where Microsoft Enterprise Edge (MSEE) devices are located. ExpressRoute locations are the entry point to Microsoft’s network – and are globally distributed, providing customers the opportunity to connect to Microsoft’s network around the world. These locations are where ExpressRoute partners and ExpressRoute Direct customers issue cross connections to Microsoft’s network. In general, the ExpressRoute location does not need to match the Azure region. For example, a customer can create an ExpressRoute circuit with the resource location *East US*, in the *Seattle* Peering location.

如果至少与地缘政治区域内的一个 ExpressRoute 位置连接，将有权访问地缘政治区域内所有区域中的 Azure 服务。

## <a name="locations"></a>Azure regions to ExpressRoute locations within a geopolitical region.
下表提供了地缘政治区域内 ExpressRoute 位置与 Azure 区域的映射。

| **地缘政治区域** | **Azure 区域** | **ExpressRoute 位置** |
| --- | --- | --- |
| **澳大利亚政府** |澳大利亚中部、澳大利亚中部 2 |堪培拉、堪培拉 2 |
| **欧洲** | 法国中部、法国南部、北欧、西欧、英国西部、英国南部 |Amsterdam, Amsterdam2, Copenhagen, Dublin, Frankfurt, Geneva, London, London2, Marseille, Milan, Munich, Newport(Wales), Oslo, Paris, Stavanger, Stockholm, Zurich |
| **北美** |美国东部、美国西部、美国东部 2 区、美国西部 2 区、美国中部、美国中南部、美国中北部、美国中西部、加拿大中部、加拿大东部 |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, New York, San Antonio, Seattle, Silicon Valley, Silicon Valley2, Washington DC, Washington DC2, Montreal, Quebec City, Toronto |
| **亚洲** | 东亚、东南亚 |Hong Kong SAR, Jakarta, Kuala Lumpur, Singapore, Singapore2, Taipei |
| **印度** | 印度西部、印度中部、印度南部 |金奈、金奈 2、孟买、孟买 2 |
| **日本** | 日本西部、日本东部 |大坂、东京 |
| **大洋洲** | 澳大利亚东南部、澳大利亚东部 |Auckland, Melbourne, Perth, Sydney, Sydney2 |
| **韩国** | 韩国中部、韩国南部 |釜山、首尔|
| **UAE** | UAE Central, UAE North | Dubai, Dubai2 |
| **南非** | South Africa West, South Africa North |开普敦、约翰内斯堡 |
| **南美洲** | 巴西南部 |圣保罗 |


## <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>国家/地区云的区域和地缘政治边界
下表提供了国家/地区云的区域和地缘政治边界的信息。

| **地缘政治区域** | **Azure 区域** | **ExpressRoute 位置** |
| --- | --- | --- |
| **美国政府云** |US Gov 亚利桑那州、US Gov 爱荷华州、US Gov 德克萨斯州、US Gov 弗吉尼亚州、US DoD 中部、US DoD 东部  |芝加哥、达拉斯、纽约、凤凰城、圣安东尼奥、西雅图、硅谷、华盛顿特区 |
| **中国东部** |中国东部、中国东部 2 |上海、上海 2 |
| **中国北部** |中国北部、中国北部 2 |北京、北京 2 |
| **德国** |德国中部、德国东部 |柏林、法兰克福 |

标准 ExpressRoute SKU 不支持跨地缘政治区域的连接。 需要启用 ExpressRoute 高级版附加组件才能支持全球连接。 不支持连接到国家/地区云环境。 如有需要，请联系连接服务提供商。

## <a name="partners"></a>ExpressRoute 连接服务提供商

下表显示按服务提供商列出的位置。 若要按位置查看可用的提供商，请参阅[按位置列出的服务提供商](expressroute-locations-providers.md)。


### <a name="global-commercial-azure"></a>Global commercial Azure

| **服务提供商** | **Microsoft Azure** | **Office 365**  | **位置** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/connectivity-services/azure-expressroute)** |受支持 |受支持 |墨尔本、悉尼 |
| **[Airtel](https://www.airtel.in/business/#/)** | 受支持 | 受支持 | 金奈 2、孟买 2 |
| **[Aryaka Networks](https://www.aryaka.com/)** |受支持 |受支持 |Amsterdam, Chicago, Dallas, Hong Kong SAR, Sao Paulo, Seattle, Silicon Valley, Singapore, Tokyo, Washington DC |
| **[Ascenty Data Centers](https://www.ascenty.com/en/cloud/microsoft-express-route)** |受支持 |受支持 |圣保罗 |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |受支持 |受支持 |阿姆斯特丹、芝加哥、达拉斯、伦敦、硅谷、新加坡、悉尼、东京、多伦多、华盛顿特区 |
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |受支持 |受支持 |蒙特利尔、多伦多、魁北克市 |
| **[British Telecom](https://www.globalservices.bt.com/en/solutions/products/bt-compute-for-microsoft-azure)** |受支持 |受支持 |Amsterdam, Hong Kong SAR, Johannesburg, London, Newport(Wales), Sao Paulo, Silicon Valley, Singapore, Sydney, Tokyo, Washington DC |
| **[C3ntro](https://www.c3ntro.com/data1/express-route1.php)** |受支持 |受支持 |迈阿密 |
| **CDC** | 受支持 | 受支持 | 堪培拉、堪培拉 2 |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |受支持 |受支持 |Amsterdam2, Chicago, Hong Kong, Las Vegas, New York, Paris, San Antonio, Silicon Valley, Tokyo, Toronto, Washington DC |
| **[Chief Telecom](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** |受支持 |受支持 |Hong Kong, Taipei |
| **China Telecom Global** |受支持 |受支持 |香港特别行政区 |
| **[Cologix](https://www.cologix.com/hyperscale/microsoft-azure/)** |受支持 |受支持 |芝加哥、达拉斯、蒙特利尔、多伦多、华盛顿特区 |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |受支持 |受支持 |Amsterdam, Amsterdam2, Dublin, London, Newport, New York, Osaka, Paris, Silicon Valley, Silicon Valley2, Singapore2, Tokyo |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |受支持 |受支持 |芝加哥、硅谷、华盛顿特区 |
| **[CoreSite](https://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |受支持 |受支持 |Chicago, Denver, Los Angeles, New York, Silicon Valley, Silicon Valley2, Washington DC, Washington DC2 |
| **[DE-CIX](https://www.de-cix.net/en/de-cix-service-world/directcloud/find-a-cloud-service/detail/microsoft-azure)** | 受支持 |受支持 |Amsterdam2, Frankfurt, Marseille|
| **[Devoli](https://devoli.com/expressroute)** | 受支持 |受支持 | Auckland, Melbourne, Sydney |
| **du datamena** |受支持 |受支持 | Dubai2 |
| **eir** |受支持 |受支持 |都柏林|
| **[Epsilon Global Communications](https://www.epsilontel.com/solutions/direct-cloud-connect)** |受支持 |受支持 |新加坡、新加坡 2 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |受支持 |受支持 |Amsterdam, Atlanta, Chicago, Dallas, Dublin,  Hong Kong SAR, London, London2, Los Angeles, Melbourne, Miami, New York, Osaka, Paris, Sao Paulo, Seattle, Silicon Valley, Singapore, Stockholm, Sydney, Tokyo, Toronto, Washington DC |
| **Etisalat UAE** |受支持 |受支持 |迪拜|
| **[euNetworks](https://eunetworks.com/services/solutions/cloud-connect/microsoft-azure-expressroute/)** |受支持 |受支持 |Amsterdam, Amsterdam2, Dublin, London |
| **FarEasTone** |受支持 |受支持 |台北|
| **GÉANT** |受支持 |受支持 |阿姆斯特丹 |
| **[Global Cloud Xchange (GCX)](https://globalcloudxchange.com/cloud-platform/cloud-x-fusion/)** | 受支持| 受支持 | 金奈、孟买 |
| **[InterCloud](https://www.intercloud.com/)** |受支持 |受支持 |Amsterdam, Chicago, London, New York, Paris, Silicon Valley, Singapore, Washington DC, Zurich |
| **[Internet2](https://www.internet2.edu/products-services/cloud-services-applications/microsoft-azure/#service-cloud-connect)** |受支持 |受支持 |Chicago, Dallas, Silicon Valley, Washington DC |
| **[Internet Initiative Japan Inc. - IIJ](https://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |受支持 |受支持 |大坂、东京 |
| **[Internet Solutions - Cloud Connect](https://www.is.co.za/solution/cloud-connect/)** |受支持 |受支持 |开普敦、约翰内斯堡、伦敦 |
| **[Interxion](https://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |受支持 |受支持 |Amsterdam, Amsterdam2, Copenhagen, Dublin, Frankfurt, London, Marseille, Paris, Zurich |
| **[IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)**|受支持 |受支持 | Amsterdam, London2, Silicon Valley, Toronto |
| **Jaguar Network** |受支持 |受支持 |马赛|
| **[Jisc](https://www.jisc.ac.uk/microsoft-azure-expressroute)** |受支持 |受支持 |伦敦 |
| **[KINX](https://www.kinx.net/service/network/cloudhub/ms-expressroute/?lang=en)** |受支持 |受支持 |首尔 |
| **[Kordia](https://www.kordia.co.nz/cloudconnect)** | 受支持 |受支持 |Auckland, Sydney |
| **[KPN](https://www.kpn.com/zakelijk/cloud/connect.htm)** | 受支持 | 受支持 | 阿姆斯特丹 | 
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |受支持 |受支持 |阿姆斯特丹、芝加哥、达拉斯、伦敦、新港（威尔士）、圣保罗、西雅图、硅谷、新加坡、华盛顿特区 |
| **LG CNS** |受支持 |受支持 |釜山、首尔 |
| **[Liquid Telecom](https://www.liquidtelecom.com/products-and-services/cloud.html)** |受支持 |受支持 |开普敦、约翰内斯堡 |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |受支持 |受支持 |Amsterdam, Atlanta, Auckland, Chicago, Dallas, Denver, Dubai2, Dublin, Hong Kong SAR, Las Vegas, London, Los Angeles, Melbourne, Miami, Montreal, New York, Perth, Quebec City, San Antonio, Seattle, Silicon Valley, Singapore, Singapore2, Sydney, Toronto, Washington DC |
| **[MTN](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** |受支持 |受支持 |伦敦 |
| **[Neutrona Networks](https://www.neutrona.com/index.php/azure-expressroute/)** |受支持 |受支持 |Dallas, Los Angeles, Miami, Sao Paulo, Washington DC |
| **[Next Generation Data](https://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |受支持 |受支持 |Newport(Wales) |
| **[NEXTDC](https://www.nextdc.com/services/axon-ethernet/microsoft-expressroute)** |受支持 |受支持 |Melbourne, Perth, Sydney, Sydney2 |
| **[NTT Communications](https://www.ntt.com/en/services/network/virtual-private-network.html)** |受支持 |受支持 |Amsterdam, Hong Kong SAR, London, Los Angeles, Osaka, Singapore, Sydney, Tokyo, Washington DC |
| **[NTT EAST](https://flets.com/cloudgateway/crossconnect/)** |受支持 |受支持 |东京 |
| **[NTT SmartConnect](https://cloud.nttsmc.com/cxc/azure.html)** |受支持 |受支持 |大阪 |
| **[Optus](https://www.optus.com.au/enterprise/)** |受支持 |受支持 |墨尔本、悉尼 |
| **[Orange](https://www.orange-business.com/en/products/business-vpn-galerie)** |受支持 |受支持 |Amsterdam, Frankfurt, Hong Kong SAR, Johannesburg, London, Paris, Sao Paulo, Silicon Valley, Singapore, Sydney, Tokyo, Washington DC |
| **[Orixcom](https://www.orixcom.com/cloud-solutions/)** | 受支持 | 受支持 | Dubai2 |
| **[PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)** |受支持 |受支持 |芝加哥、硅谷、华盛顿特区 |
| **[PCCW Global Limited](https://consoleconnect.com/clouds/#azureRegions)** |受支持 |受支持 |Chicago, Hong Kong SAR, London |
| **[Sejong Telecom](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |受支持 |受支持 |首尔 |
| **[SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)** | 受支持 |受支持 | 华盛顿 |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |受支持 |受支持 |金奈、孟买 2 |
| **[SingTel](https://www.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |受支持 |受支持 |新加坡、新加坡 2 |
| **[Softbank](https://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |受支持 |受支持 |大坂、东京 |
| **[Spark NZ](https://www.sparkdigital.co.nz/solutions/connectivity/cloud-connect/)** |受支持 |受支持 |Auckland, Sydney |
| **[Sprint](https://business.sprint.com/solutions/cloud-networking/)** |受支持 |受支持 |芝加哥、硅谷、华盛顿特区 |
| **[Swisscom](https://www.swisscom.ch/en/business/enterprise/offer/cloud-data-center/microsoft-cloud-services/microsoft-azure-von-swisscom.html)** | 受支持 | 受支持 | 苏黎世 |
| **[Tata Communications](https://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |受支持 |受支持 |Amsterdam, Chennai, Hong Kong SAR, London, Mumbai, Sao Paulo, Silicon Valley, Singapore, Washington DC |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |受支持 |受支持 |阿姆斯特丹、圣保罗 |
| **[Telehouse - KDDI](https://www.telehouse.net/solutions/cloud-services/cloud-link)** |受支持 |受支持 |London, London2 |
| **Telenor** |受支持 |受支持 |Amsterdam, London, Oslo |
| **[Telia Carrier](https://teliacarrier.com/our-services/connectivity/cloud-connect.html?title=Cloud%20Connect)** | 受支持 | 受支持 |Amsterdam, Chicago, Dallas, Hong Kong, London, Paris, Silicon Valley, Stockholm, Washington DC |
| **Telmex Uninet**| 受支持 | 受支持 | 达拉斯 |
| **[Telstra Corporation](https://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |受支持 |受支持 |墨尔本、新加坡、悉尼 |
| **[Telus](https://www.telus.com)** |受支持 |受支持 |Montreal, Seattle, Toronto |
| **[Teraco](https://www.teraco.co.za/services/africa-cloud-exchange/)** |受支持 |受支持 |开普敦、约翰内斯堡 |
| **[TIME dotCom](https://www.time.com.my/enterprise/connectivity/cloud-interconnect)** | 受支持 | 受支持 | 吉隆坡 |
| **[Transtelco](https://transtelco.net/enterprise-services/)** |受支持 |受支持 |达拉斯、洛杉矶|
| **[UOLDIVEO](https://www.uoldiveo.com.br/)** |受支持 |受支持 |圣保罗 |
| **[Verizon](https://enterprise.verizon.com/products/network/application-enablement/secure-cloud-interconnect/)** |受支持 |受支持 |Amsterdam, Chicago, Dallas, Hong Kong SAR, London, Mumbai, Silicon Valley, Singapore, Sydney, Tokyo, Toronto, Washington DC |
| **[Viasat](http://www.directcloud.viasatbusiness.com/)** | 受支持 | 受支持 | 华盛顿特区 2 |
| **[Vocus Group NZ](https://www.vocus.co.nz/business/cloud-data-centres)** | 受支持 | 受支持 | Auckland, Sydney |
| **[Vodafone](https://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |受支持 |受支持 |Amsterdam2, London, Singapore |
| **[Vodafone Idea](https://discover.vodafone.in/business/enterprise-solutions/connectivity/vpn-extended-connect)** | 受支持 | 受支持 | 孟买、孟买 2 |
| **[Zayo](https://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |受支持 |受支持 |Amsterdam, Chicago, Dallas, Denver, London, Los Angeles, Montreal, New York, Paris, Seattle, Silicon Valley, Toronto, Washington DC, Washington DC2 |

 **+** 表示即将推出

### <a name="national-cloud-environment"></a>国家/地区云环境

Azure national clouds are isolated from each other and from global commerical Azure. ExpressRoute for one Azure cloud can't connect to the Azure regions in the others. 

### <a name="us-government-cloud"></a>美国政府云

| **服务提供商** | **Microsoft Azure** | **Office 365** | **位置** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |受支持 |受支持 |Chicago, Phoenix,  Washington DC |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |受支持 |受支持 |New York, Phoenix, San Antonio, Washington DC |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |受支持 |受支持 |芝加哥、达拉斯、纽约、西雅图、硅谷、华盛顿特区 |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |受支持 |受支持 |芝加哥、硅谷、华盛顿特区 |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |受支持 | 受支持 | Chicago, Dallas, San Antonio, Seattle, Washington DC |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |受支持 |受支持 |芝加哥、达拉斯、纽约、硅谷、华盛顿特区 |

### <a name="china"></a>中国

| **服务提供商** | **Microsoft Azure** | **Office 365** | **位置** |
| --- | --- | --- | --- |
| **中国电信** |受支持 |不支持 |Beijing, Beijing2, Shanghai, Shanghai2 |
| **[GDS](http://en.gds-services.com/news_detail/newsId=21.html)** |受支持 |不支持 |北京 2、上海 2 |

若要了解详细信息，请参阅 [位于中国的 ExpressRoute](http://www.windowsazure.cn/home/features/expressroute/)。

### <a name="germany"></a>德国

| **服务提供商** | **Microsoft Azure** | **Office 365** | **位置** |
| --- | --- | --- | --- |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |受支持 |不支持 |法兰克福 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |受支持 |不支持 |法兰克福 |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroutetm)** |受支持 |不支持 |柏林 |
| **Interxion** |受支持 |不支持 |法兰克福 |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |受支持  | 不支持 | 柏林 |
| **T-Systems** |受支持 |不支持 |柏林 |

## <a name="connectivity-through-exchange-providers"></a>Connectivity through Exchange providers

如果前面部分中未列出连接服务提供商，仍可以建立连接。

* 请咨询连接服务提供商，以确定他们是否已连接到上表中列出的任何 Exchange 提供商。 可以访问以下链接，以收集 Exchange 提供商所提供的服务的详细信息。 已有多个连接提供商连接到以太网 Exchange。
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](https://www.interxion.com/products/interconnection/cloud-connect/)
  * [IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](https://www.nextdc.com/)
  * [PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/) 
* 让连接提供商将网络扩展到选择的对等互连位置。
  * 确保连接提供商以高可用性方式扩展连接，以防出现单点故障。
* 从 Exchange 连接服务提供商处订购一条 ExpressRoute 线路以连接到 Microsoft。
  * 根据 [创建 ExpressRoute 线路](expressroute-howto-circuit-classic.md) 中的步骤来设置连接。

## <a name="connectivity-through-satellite-operators"></a>Connectivity through satellite operators
If you are remote and don't have fiber connectivity or you want to explore other connectivity options you can check the following satellite operators. 

* Intelsat
* [SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a>Connectivity through additional service providers

| **连接服务提供商** | **Exchange** | **位置** |
| --- | --- | --- |
| **[1CLOUDSTAR](https://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |新加坡 |
| **[Airgate Technologies, Inc.](https://www.airgate.ca/expressroute)** | Equinix Cologix | 多伦多、蒙特利尔 |
| **[Alaska Communications](https://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |西雅图 |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |纽约、华盛顿特区 |
| **[Arteria Networks Corporation](https://www.arteria-net.com/business/service/cloud/sca/)** |Equinix |东京 |
| **[Axtel](https://alestra.mx/landing/expressrouteazure/)** |Equinix |达拉斯|
| **[Beanfield Metroconnect](https://www.beanfield.com/cloud-exchange/)** |Megaport |多伦多|
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | 伦敦 |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/)** | Equinix | 阿姆斯特丹、法兰克福、伦敦、新加坡、华盛顿特区 |
| **[BroadBand Tower, Inc.](https://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | 东京 |
| **[C3ntro Telecom](https://www.c3ntro.com/data/express-route)** | Equinix、Megaport | 达拉斯 |
| **[Chief](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** | Equinix | 香港特别行政区 |
| **[Cinia](https://www.cinia.fi/en/services/connectivity-services/direct-public-cloud-connection.html)** | Equinix、Megaport | 法兰克福、汉堡 |
| **[CloudXpress](https://www2.telenet.be/fr/business/produits-services/internet/cloudxpress/)** | Equinix | 阿姆斯特丹 | 
| **[CMC Telecom](https://cmctelecom.vn/san-pham/value-added-service-and-it/cmc-telecom-cloud-express-en/)** | Equinix | 新加坡 | 
| **[Aptum Technologies](https://aptum.com/services/cloud/managed-azure/)**| Equinix | 蒙特利尔、多伦多 |
| **[CoreAzure](http://www.coreazure.com/expressroute/)**| Equinix | 伦敦 |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)**| Equinix | 达拉斯、硅谷、华盛顿特区 |
| **[Crown Castle](https://fiber.crowncastle.com/solutions/added/cloud-connect)**| Equinix | Atlanta, Chicago, Dallas, Los Angeles, New York, Washington DC |
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | 达拉斯 |
| **[Epsilon Telecommunications Limited](https://www.epsilontel.com/data-connectivity/cloud-access/)** | Equinix | 伦敦、新加坡、华盛顿特区 |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | 阿姆斯特丹 |
| **[Exponential E](https://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | 伦敦 |
| **[Fastweb S.p.A](https://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | 阿姆斯特丹 |
| **[Fibrenoire](https://www.fibrenoire.ca/en/cloudextn)** | Megaport | 魁北克市 |
| **[Gtt Communications Inc](https://www.gtt.net)** |Equinix | 华盛顿 |
| **[Gulf Bridge International](https://www.gbiinc.com/microsoft-azure-expressroute/)** | Equinix | 阿姆斯特丹 |
| **[HSO](https://www.hso.co.uk/products/cloud-direct)** |Equinix | 伦敦、斯劳 |
| **[IVedha Inc](http://www.ivedha.com/cloud/manage-azure-cloud/express-route-4/)**| Equinix | 多伦多 |
| **[Kaalam Telecom Bahrain B.S.C](http://www.kalaam-telecom.com/en/inbusiness/expressroute.html)**| Level 3 Communications |阿姆斯特丹 |
| **LGA Telecom** |Equinix |新加坡|
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |香港特别行政区 
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | 悉尼 |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | 阿姆斯特丹 |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | 华盛顿 |
| **[MTN](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** | Teraco | 开普敦、约翰内斯堡 |
| **[NexGen Networks](https://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | 伦敦 |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Equinix | 阿姆斯特丹、法兰克福 |
| **[POST Telecom Luxembourg](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | 阿姆斯特丹 |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | 阿姆斯特丹、都柏林、伦敦、巴黎 |
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | 法兰克福 |  
| **Rogers** | Cologix、Equinix | 蒙特利尔、多伦多 |
| **[Spectrum Enterprise](https://enterprise.spectrum.com/services/cloud/cloud-connect.html)** | Equinix | Chicago, Dallas, Los Angeles, New York, Silicon Valley | 
| **[Tamares Telecom](https://www.tamarestelecom.com/our-services/#Connectivity)** | Equinix | 伦敦 | 
| **[TDC Erhverv](https://tdc.dk/Produkter/cloudaccessplus)** | Equinix | 阿姆斯特丹 | 
| **[Telecom Italia Sparkle](https://www.tisparkle.com/our-platform/corporate-platform/sparkle-cloud-connect#catalogue)**| Equinix | 阿姆斯特丹 |
| **[Telekom Deutschland GmbH](https://cloud.telekom.de/de/infrastruktur/managed-it-services/managed-hybrid-infrastructure-mit-microsoft-azure)** | Interxion | 阿姆斯特丹、法兰克福 |
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | 阿姆斯特丹 |
| **[ThinkTel](https://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | 多伦多 | 
| **[United Information Highway (UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | 新加坡 |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Equinix | 圣保罗 |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | 纽约 |
| **[Windstream](https://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | 芝加哥、硅谷、华盛顿特区 |
| **[X2nsat Inc.](https://www.x2nsat.com/expressroute/)** |Coresite |Silicon Valley, Silicon Valley 2|
| **Zain** |Equinix |伦敦|
| **[Zertia](https://www.zertia.es)**| Level 3 | 马德里 |
| **[Zirro](https://zirro.com/services/)**| Cologix、Equinix | 蒙特利尔、多伦多 |

## <a name="connectivity-through-datacenter-providers"></a>Connectivity through datacenter providers

| **提供程序** | **Exchange** |
| --- | --- |
| **[CyrusOne](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Megaport, PacketFabric |
| **[Cyxtera](https://www.cyxtera.com/data-center-services/interconnection)** | Megaport, PacketFabric |
| **[Databank](https://www.databank.com/platforms/connectivity/cloud-direct-connect/)** | Megaport |
| **[DataFoundry](https://www.datafoundry.com/services/cloud-connect/)** | Megaport |
| **[Digital Realty](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | IX Reach, Megaport PacketFabric |
| **[EdgeConnex](https://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport, PacketFabric |
| **[Flexential](https://www.flexential.com/connectivity/cloud-connect-microsoft-azure-expressroute)** | IX Reach, Megaport, PacketFabric |
| **[QTS Data Centers](https://www.qtsdatacenters.com/hybrid-solutions/connectivity/azure-cloud )** | Megaport, PacketFabric |
| **[Stream Data Centers]( https://www.streamdatacenters.com/products-services/network-cloud/ )** | Megaport |
| **[RagingWire Data Centers](https://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | IX Reach, Megaport, PacketFabric |
| **[vXchnge](https://www.vxchnge.com/colocation-services/interconnection)** | IX Reach, Megaport |
| **[T5 Datacenters](https://t5datacenters.com/network-cloud-connect/)** | IX Reach |

## <a name="connectivity-through-national-research-and-education-networks-nren"></a>Connectivity through National Research and Education Networks (NREN)

| **提供程序**|
| --- |
| **AARNET**| 
| **DeIC，通过 GÉANT**|
| **GARR，通过 GÉANT**|
| **GÉANT**|
| **HEAnet，通过 GÉANT**|
| **Internet2**|
| **JISC**|
| **RedIRIS，通过 GÉANT**|
| **SINET**|
| **Surfnet，通过 GÉANT**|

* 如果连接提供商未在此处列出，请查看其是否连接到上面列出的任何 ExpressRoute Exchange 合作伙伴。

## <a name="expressroute-system-integrators"></a>ExpressRoute 系统集成商
根据网络的规模，有时，很难启用专用连接来满足需要。 可以与下表中列出的任一系统集成商合作，以帮助你将加入 ExpressRoute。

| **系统集成商** | **所在洲** |
| --- | --- |
| **[Altogee](https://altogee.be/diensten/express-route/)** | 欧洲 |
| **[Avanade Inc.](https://www.avanade.com/)** | 亚洲、欧洲、北美、南美 |
| **[Bright Skies GmbH](https://bskies.io/expressroute)** | 欧洲
| **[Ensyst](https://www.ensyst.com.au)** | 亚洲
| **[Equinix Professional Services](https://www.equinix.com/services/consulting/)** | 北美 |
| **[FlexManage](https://www.flexmanage.com/cloud)** | 北美 |
| **[Lightstream](https://www.lightstream.tech/partners/microsoft-azure/)** | 北美 |
| **[The IT Consultancy Group](https://itconsult.com.au/)** | 澳大利亚 |
| **[MOQdigital](https://www.moqdigital.com.au/insights/technical/network-connectivity-options-for-azure)** | 澳大利亚 |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | 欧洲（德国） |
| **[Nelite](https://www.exakis-nelite.com/offres/)** | 欧洲 |
| **[New Signature](https://newsignature.com/technologies/express-route/)** | 欧洲 |
| **[OneAs1a](https://www.oneas1a.com/connectivity.html)** | 亚洲 |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | 欧洲 |
| **[Perficient](https://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | 北美 |
| **[Presidio](https://info.presidio.com/microsoft-azure-expressroute)** | 北美 |
| **[sol-tec](https://www.sol-tec.com/what-we-do/)** | 欧洲 |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | 南美 |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | 澳大利亚 |

## <a name="next-steps"></a>后续步骤
* 有关 ExpressRoute 的详细信息，请参阅 [ExpressRoute 常见问题](expressroute-faqs.md)。
* 确保符合所有先决条件。 请参阅 [ExpressRoute 先决条件](expressroute-prerequisites.md)。

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "位置地图"
