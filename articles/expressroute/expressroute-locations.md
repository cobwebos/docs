<properties
   pageTitle="ExpressRoute 位置 | Microsoft Azure"
   description="本文详细说明了服务的上市区域，以及如何连接到 Azure 区域。"
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor="" />
<tags
   ms.service="expressroute"
   ms.date="12/02/2015"
   wacn.date="" />

# ExpressRoute 合作伙伴和对等位置

本文中的表格提供有关 ExpressRoute 连接提供商、ExpressRoute 地理覆盖范围、通过 ExpressRoute 支持的 Microsoft 云服务以及 ExpressRoute 系统集成商 (SI) 的信息。

## ExpressRoute 连接提供商

所有的 Azure 区域和位置都支持 ExpressRoute。以下地图提供了 Azure 区域和 ExpressRoute 位置的列表。ExpressRoute 位置是指 Microsoft 与多个服务提供商对等互连的位置。

![](./media/expressroute-locations/expressroute-locations-map.png)

如果你至少与地缘政治区域内的一个 ExpressRoute 位置连接，你将有权访问地缘政治区域内所有区域中的 Azure 服务。下表提供了地缘政治区域内 ExpressRoute 位置与 Azure 区域的映射。

|**地缘政治区域**|**Azure 区域**|**ExpressRoute 位置**|
|---|---|---|
|**美国**|所有美国区域 - 美国东部、美国西部、美国东部 2 区、美国中部，美国中南部、美国中北部|亚特兰大、芝加哥、达拉斯、洛杉矶、纽约、西雅图、硅谷、华盛顿特区|
|**南美洲**|巴西南部|圣保罗|
|**欧洲**|欧洲北部、欧洲西部|阿姆斯特丹、伦敦|
|**亚洲**|亚洲东部、亚洲东南亚，中国北部、中国东部|北京、上海、香港、新加坡|  
|**日本**|日本西部、日本东部|东京|
|**澳大利亚**|澳大利亚东南部、澳大利亚东部|墨尔本、悉尼|
|**印度**|印度西部、印度中部、印度南部|金奈、孟买|



下表提供了国家/地区云的区域和地缘政治边界的信息。

|**地缘政治区域**|**Azure 区域**|**ExpressRoute 位置**|
|---|---|---|---|
|**美国政府云**|美国政府|爱荷华州、弗吉尼亚州|芝加哥市阿什本|


标准 ExpressRoute SKU 不支持跨地缘政治区域的连接。你需要启用 ExpressRoute 高级版附加组件才能支持全球连接。不支持连接到国家/地区云环境。如有需要，请联系连接服务提供商。


## 连接服务提供商位置

### 生产 Azure

| **服务提供商** |**Microsoft Azure** | **Office 365** | **位置** |
|-----------------------|--------------------|----------------|---------------|
| **[Aryaka Networks](http://www.aryaka.com/)** | 支持 | 不支持 | 硅谷、新加坡、华盛顿特区 |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | 支持 | 支持 | 阿姆斯特丹、伦敦+、达拉斯、硅谷、华盛顿特区 |
| **[British Telecom](http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** | 支持 | 支持 | 阿姆斯特丹、伦敦、硅谷+、华盛顿特区 |
|**China Telecom Global** | 即将推出 | 不支持 | 香港+ |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | 支持 | 不支持 | 阿姆斯特丹、伦敦 |
| **Comcast** | 支持 | 不支持 | 硅谷、华盛顿特区 |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | 支持 | 支持 | 阿姆斯特丹、亚特兰大、芝加哥、达拉斯、香港、伦敦、洛杉矶、墨尔本、纽约、圣保罗、西雅图、硅谷、新加坡、悉尼、东京、华盛顿特区 |
| **[Internet Initiative Japan Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2013/pdf/Azure_E.pdf)** | 支持 | 不支持 | 东京 |
| **[InterCloud](https://www.intercloud.com/)** | 支持 | 不支持 | 阿姆斯特丹、伦敦、新加坡、华盛顿特区 |
| **Internet 解决方案 - 云连接** | 支持 | 不支持 | 阿姆斯特丹、伦敦 |
| **Interxion** | 支持 | 不支持 | 阿姆斯特丹 |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | 支持 | 不支持 | 阿姆斯特丹、芝加哥、达拉斯、伦敦、西雅图、硅谷、华盛顿特区 |
| **Megaport** | 支持 | 不支持 | 墨尔本、悉尼 |
| **MTN** | 支持 | 不支持 | 伦敦 |
| **NTT Communications** | 支持 | 不支持 | 伦敦+、东京 |
| **NEXTDC** | 支持 | 不支持 | 墨尔本、悉尼+ |
| **[Orange](http://www.orange-business.com/)** | 支持 | 不支持 | 阿姆斯特丹、伦敦、硅谷、华盛顿特区 |
| **PCCW Global Limited** | 支持 | 不支持 | 香港 |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** | 支持 | 不支持 | 新加坡 |
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** | 支持 | 支持 | 阿姆斯特丹、金奈+、香港、伦敦、孟买+、新加坡 |
| **[TeleCity Group](http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** | 支持 | 支持 | 阿姆斯特丹、伦敦 |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** | 支持 | 不支持 | 墨尔本+、悉尼 |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** | 支持 | 支持 | 伦敦、香港、硅谷、悉尼、东京、华盛顿特区 |
| **[Zayo Group](http://www.zayo.com/)** | 支持 | 不支持 | 芝加哥、硅谷、华盛顿特区 |

 **+** 表示即将推出

### 国家/地区云环境

#### 美国政府云

| **服务提供商** |**Microsoft Azure** | **Office 365** | **位置** |
|-----------------------|--------------------|----------------|---------------|
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | 即将推出 | 不支持 | 芝加哥+、华盛顿特区+ |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | 即将推出 | 不支持 | 芝加哥、华盛顿特区 |
| **[Level 3 Communications - IPVPN](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | 即将推出 | 不支持 | 芝加哥、华盛顿特区 |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** | 支持 | 不支持 | 芝加哥、华盛顿特区 |

## 通过未列出的服务提供商建立连接

如果前面部分中未列出你的连接服务提供商，你仍可以建立连接。

- 请咨询你的连接服务提供商，以确定他们是否已连接到上表中列出的任何 Exchange 提供商。你可以访问以下链接，以收集 Exchange 提供商所提供的服务的详细信息。已有多个连接提供商连接到以太网 Exchange。

	- [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
	- [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
	- [InterXion](http://www.interxion.com/)
	- [NextDC](http://www.nextdc.com/)
	- [CoreSite](http://www.coresite.com/)
- 让你的连接提供商将你的网络扩展到选择的对等互连位置。
	- 确保连接提供商以高可用性方式扩展你的连接，以防出现单点故障。
- 从 Exchange 连接服务提供商处订购一条 ExpressRoute 线路以连接到 Microsoft。
	- 根据[创建 ExpressRoute 线路](/documentation/articles/expressroute-howto-circuit-classic)中的步骤来设置连接。

|**连接服务提供商**|**Exchange**|**对等位置**|
|---|---|---|
|**[XO Communications](http://www.xo.com/)**|Equinix|硅谷|

## ExpressRoute 系统集成商

根据网络的规模，有时，很难启用专用连接来满足需要。你可以与下表中列出的任一系统集成商合作，以帮助将你加入 ExpressRoute。

|**系统集成商**|**所在洲**|
|---|---|
|**[Nimbo](http://www.nimbo.com/)**|美国||
|**[Dotnet Solutions](http://www.dotnetsolutions.co.uk/)**|欧洲、非洲和中东|

## 后续步骤

- 有关 ExpressRoute 的详细信息，请参阅 [ExpressRoute 常见问题](/documentation/articles/expressroute-faqs)。
- 确保符合所有先决条件。请参阅 [ExpressRoute 先决条件](/documentation/articles/expressroute-prerequisites)。

<!---HONumber=Mooncake_0104_2016-->