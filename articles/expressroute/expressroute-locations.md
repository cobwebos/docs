---
title: "ExpressRoute 位置 | Microsoft Docs"
description: "本文详细说明了服务的上市区域，以及如何连接到 Azure 区域。"
services: expressroute
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
ms.assetid: c878513a-d594-42ad-8b0e-403efd0c4b25
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: b2d7ba14c7cbc5c082a5f962559936e1a60827f2
ms.openlocfilehash: fd018ed25e5c309a85bb2078d00f2bb0b504e5d2


---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute 合作伙伴和对等位置
本文中的表格提供有关 ExpressRoute 连接提供商、ExpressRoute 地理覆盖范围、通过 ExpressRoute 支持的 Microsoft 云服务以及 ExpressRoute 系统集成商 (SI) 的信息。

## <a name="a-namepartnersaexpressroute-connectivity-providers"></a><a name="partners"></a>ExpressRoute 连接服务提供商
所有的 Azure 区域和位置都支持 ExpressRoute。 以下地图提供了 Azure 区域和 ExpressRoute 位置的列表。 ExpressRoute 位置是指 Microsoft 与多个服务提供商对等互连的位置。

![位置地图][0]

如果你至少与地缘政治区域内的一个 ExpressRoute 位置连接，你将有权访问地缘政治区域内所有区域中的 Azure 服务。

### <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a>地缘政治区域中 ExpressRoute 位置与 Azure 区域的映射。
下表提供了地缘政治区域内 ExpressRoute 位置与 Azure 区域的映射。

| **地缘政治区域** | **Azure 区域** | **ExpressRoute 位置** |
| --- | --- | --- |
| **北美** |美国东部、美国西部、美国东部 2 区、美国中部、美国中南部、美国中北部、加拿大中部、加拿大东部 |亚特兰大、芝加哥、达拉斯、拉斯维加斯、洛杉矶、纽约、西雅图、硅谷、华盛顿特区、蒙特利尔+、魁北克市+、多伦多 |
| **南美洲** |巴西南部 |圣保罗 |
| **欧洲** |北欧、西欧、英国西部、英国南部 |阿姆斯特丹、都柏林、伦敦、纽波特（威尔士）、巴黎 |
| **亚洲** |东亚、东南亚 |中国香港特别行政区、新加坡 |
| **日本** |日本西部、日本东部 |大坂、东京 |
| **澳大利亚** |澳大利亚东南部、澳大利亚东部 |墨尔本、悉尼 |
| **印度** |印度西部、印度中部、印度南部 |金奈、孟买 |

### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>国家/地区云的区域和地缘政治边界
下表提供了国家/地区云的区域和地缘政治边界的信息。

| **地缘政治区域** | **Azure 区域** | **ExpressRoute 位置** |
| --- | --- | --- | --- |
| **美国政府云** |美国爱荷华州政府、美国弗吉尼亚州政府 |芝加哥、达拉斯、纽约、华盛顿特区 |
| **中国** |中国北部、中国东部 |北京、上海 |
| **德国** |德国中部、德国东部 |柏林、法兰克福 |

标准 ExpressRoute SKU 不支持跨地缘政治区域的连接。 你需要启用 ExpressRoute 高级版附加组件才能支持全球连接。 不支持连接到国家/地区云环境。 如有需要，请联系连接服务提供商。

## <a name="a-namelocationsaconnectivity-provider-locations"></a><a name="locations"></a>连接服务提供商位置
> [!div class="op_single_selector"]
> * [按提供商列出的位置](expressroute-locations.md#locations)
> * [按位置列出的提供商](expressroute-locations-providers.md#locations)
>
>

### <a name="production-azure"></a>生产 Azure
| **服务提供商** | **Microsoft Azure** | **Office 365 和 CRM Online** | **位置** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/cloud-services-applications/azure-expressroute/)** |支持 |支持 |墨尔本、悉尼 |
| **[Aryaka Networks](http://www.aryaka.com/)** |支持 |支持 |阿姆斯特丹、达拉斯、硅谷、新加坡、东京、华盛顿特区 |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |支持 |支持 |阿姆斯特丹、芝加哥、达拉斯、伦敦、硅谷、新加坡、悉尼、华盛顿特区 |
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |支持 |支持 |多伦多 |
| **[British Telecom](http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** |支持 |支持 |阿姆斯特丹、中国香港特别行政区、伦敦、硅谷、新加坡、悉尼、东京、华盛顿特区 |
| **[CenturyLink](http://www.centurylink.com/business/enterprise/services/data-network/mpls-vpn.html)** |即将支持 |即将支持 |硅谷 |
| **China Telecom Global** |支持 |不支持 |香港 |
| **[Cologix](http://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** |支持 |支持 |Dallas、Montreal+、Toronto |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |支持 |支持 |阿姆斯特丹、都柏林、伦敦、东京 |
| **Comcast** |支持 |支持 |芝加哥、硅谷、华盛顿特区 |
| **Console**| 支持 | 支持 |硅谷 |
| **[CoreSite](http://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |支持 |支持 |洛杉矶 |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |支持 |支持 |阿姆斯特丹、亚特兰大、芝加哥、达拉斯、中国香港特别行政区、伦敦、洛杉矶、墨尔本、纽约、大阪、巴黎+、圣保罗、西雅图、硅谷、新加坡、悉尼、东京、多伦多、华盛顿特区 |
| **euNetworks** |支持 |支持 |阿姆斯特丹 |
| **GÉANT** |支持 |支持 |阿姆斯特丹 |
| **[Internet Initiative Japan Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |支持 |支持 |大坂、东京 |
| **[InterCloud](https://www.intercloud.com/)** |支持 |支持 |阿姆斯特丹、伦敦、新加坡、华盛顿特区 |
| **Internet 解决方案 - 云连接** |支持 |支持 |阿姆斯特丹、伦敦 |
| **[Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/colocated-hybrid-cloud/microsoft-azure/)** |支持 |支持 |阿姆斯特丹、伦敦、巴黎 |
| **Jisc** |支持 |支持 |伦敦 |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |支持 |支持 |阿姆斯特丹、芝加哥、达拉斯、拉斯维加斯+、伦敦、西雅图、硅谷、华盛顿特区 |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |支持 |支持 |达拉斯、中国香港特别行政区、拉斯维加斯、洛杉矶、墨尔本、纽约、西雅图、新加坡、悉尼、多伦多、华盛顿特区 |
| **MTN** |支持 |支持 |伦敦 |
| **[Next Generation Data](http://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |支持 |支持 |Newport(Wales) |
| **NEXTDC** |支持 |支持 |墨尔本、悉尼 |
| **NTT Communications** |支持 |支持 |伦敦、洛杉矶、大阪、新加坡、东京、华盛顿特区 |
| **[Orange](http://www.orange-business.com/en/products/business-vpn-galerie)** |支持 |支持 |阿姆斯特丹、中国香港特别行政区、伦敦、硅谷、新加坡、悉尼、华盛顿特区 |
| **PCCW Global Limited** |支持 |支持 |香港 |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |支持 |支持 |金奈 |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |支持 |支持 |新加坡 |
| **Softbank** |支持 |支持 |大坂、东京 |
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |支持 |支持 |阿姆斯特丹、金奈、中国香港特别行政区、伦敦、孟买、硅谷、新加坡、华盛顿特区 |
| **[TeleCity Group](http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** |支持 |支持 |阿姆斯特丹、都柏林、伦敦 |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |支持 |支持 |圣保罗 |
| **Telenor** |支持 |支持 |阿姆斯特丹、伦敦 |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |支持 |支持 |墨尔本、悉尼 |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** |支持 |支持 |阿姆斯特丹、中国香港特别行政区、伦敦、硅谷、新加坡、悉尼、东京、华盛顿特区 |
| **[Vodafone](http://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |支持 |不支持 |伦敦 |
| **[Zayo Group](http://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |支持 |支持 |芝加哥、洛杉矶、纽约、硅谷、多伦多、华盛顿特区 |

 **+** 表示即将推出

### <a name="national-cloud-environment"></a>国家/地区云环境

### <a name="us-government-cloud"></a>美国政府云
| **服务提供商** | **Microsoft Azure** | **Office 365** | **位置** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |支持 |支持 |芝加哥、华盛顿特区 |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |支持 |支持 |芝加哥、达拉斯、纽约、华盛顿特区 |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |支持 |支持 |芝加哥、纽约+、华盛顿特区 |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |支持 |支持 |芝加哥、达拉斯、纽约、华盛顿特区 |

### <a name="china"></a>中国
| **服务提供商** | **Microsoft Azure** | **Office 365** | **位置** |
| --- | --- | --- | --- |
| **中国电信** |支持 |不支持 |北京、上海 |

若要了解详细信息，请参阅 [位于中国的 ExpressRoute](http://www.windowsazure.cn/home/features/expressroute/)。

### <a name="germany"></a>德国
| **服务提供商** | **Microsoft Azure** | **Office 365** | **位置** |
| --- | --- | --- | --- |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |支持 |不支持 |柏林+、法兰克福 |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |支持 |不支持 |法兰克福 |
| **e-shelter** |支持 |不支持 |柏林 |
| **Interxion** |支持 |不支持 |法兰克福 |

## <a name="a-namenonpartnersaconnectivity-through-service-providers-not-listed"></a><a name="nonpartners"></a>通过未列出的服务提供商建立连接
如果前面部分中未列出你的连接服务提供商，你仍可以建立连接。

* 请咨询你的连接服务提供商，以确定他们是否已连接到上表中列出的任何 Exchange 提供商。 你可以访问以下链接，以收集 Exchange 提供商所提供的服务的详细信息。 已有多个连接提供商连接到以太网 Exchange。
  * [Cologix](http://www.cologix.com/)
  * [CoreSite](http://www.coresite.com/)
  * [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/colocated-hybrid-cloud/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](http://www.nextdc.com/)
  * [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
* 让你的连接提供商将你的网络扩展到选择的对等互连位置。
  * 确保连接提供商以高可用性方式扩展你的连接，以防出现单点故障。
* 从 Exchange 连接服务提供商处订购一条 ExpressRoute 线路以连接到 Microsoft。
  * 根据 [创建 ExpressRoute 线路](expressroute-howto-circuit-classic.md) 中的步骤来设置连接。

| **连接服务提供商** | **Exchange** | **位置** |
| --- | --- | --- |
| **[1CLOUDSTAR](http://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |新加坡 |
| **[Arteria-Net](https://arteria-net.com/business/service/cloud_access/sca/)** |Equinix |东京 |
| **[Alaska Communications](http://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |西雅图 |
| **[Exponential E](http://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | 伦敦 |
| **[HSO](http://www.hso.co.uk/products/cloud-direct)** |Equinix | 伦敦、斯劳 |
| **[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure)** |Equinix |纽约、华盛顿特区 |
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | 悉尼 |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | 华盛顿特区 |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Telecity | 阿姆斯特丹、法兰克福 |  
| **[Transtelco](http://www.transtelco.net/tcloud/microsoft)** |Equinix | 达拉斯、洛杉矶 |  
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | 法兰克福 |  
| **[Windstream](http://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix |芝加哥、硅谷、华盛顿特区 |
| **[XO Communications](http://www.xo.com/)** |Equinix |硅谷 |
| **[Zertia](http://www.zertia.es/index.php/novedades)**| Level 3 | 马德里 |


## <a name="expressroute-system-integrators"></a>ExpressRoute 系统集成商
根据网络的规模，有时，很难启用专用连接来满足需要。 你可以与下表中列出的任一系统集成商合作，以帮助将你加入 ExpressRoute。

| **系统集成商** | **所在洲** |
| --- | --- |
| **[Avanade Inc.](http://www.avanade.com/)** |亚洲、欧洲、美国 |
| **[Dotnet Solutions](http://www.dotnetsolutions.co.uk/)** |欧洲 |
| **[Equinix Professional Services](http://www.equinix.com/services/consulting/)** |美国 |
| **[Nelite](http://nelite.com/)** |欧洲 |
| **[OneAs1a](http://www.oneas1a.com/express-connect-any-cloud-ecac)** |亚洲 |
| **[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** |美国 |
| **[Project Leadership](http://www.projectleadership.net/azure)** |美国 |


## <a name="next-steps"></a>后续步骤
* 有关 ExpressRoute 的详细信息，请参阅 [ExpressRoute 常见问题](expressroute-faqs.md)。
* 确保符合所有先决条件。 请参阅 [ExpressRoute 先决条件](expressroute-prerequisites.md)。

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "位置地图"



<!--HONumber=Dec16_HO3-->


