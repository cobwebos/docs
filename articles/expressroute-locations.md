<properties pageTitle="ExpressRoute 位置" description="本页详细说明了服务的上市区域，以及如何连接到 Azure 区域。" services="expressroute" documentationCenter="na" authors="cherylmc" manager="adinah" editor="tysonn" />
    
<tags ms.service="expressroute" ms.date="05/14/2015" wacn.date=""/>

# ExpressRoute 合作伙伴和对等位置

本页上的表格提供有关 ExpressRoute 连接提供商（EXP 和 NSP）、ExpressRoute 地理覆盖范围、通过 ExpressRoute 支持的 Microsoft 云服务以及 ExpressRoute 系统集成商 (SI) 的信息。

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

不支持跨地缘政治区域的连接。你可以与连接提供商合作，使用其网络将连接扩展到不同的地缘政治区域。


### Exchange 提供商 (EXP) 位置

|**服务提供商**|**位置**|  
|:-------------------|:--------------|
| **[Aryaka Networks](http://www.aryaka.com/)**| 硅谷、新加坡、华盛顿特区|
| **[Colt Ethernet](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | 阿姆斯特丹、伦敦+ |
| **Comcast** | 硅谷、华盛顿特区 |
| **[Equinix](http://www.equinix.com/solutions/partner-solutions/Microsoft-windows-azure/)**| 阿姆斯特丹、亚特兰大、芝加哥、达拉斯、香港、伦敦、洛杉矶、墨尔本、纽约、圣保罗、西雅图、硅谷、新加坡、悉尼、东京、华盛顿特区|
| **[InterCloud](https://www.intercloud.com/)** | 伦敦、新加坡、华盛顿特区 |
| **Interxion**| 阿姆斯特丹|
| **[Level 3 Communications - Exchange](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)**| 芝加哥、达拉斯、伦敦、西雅图、硅谷、华盛顿特区|
| **NEXTDC**| 墨尔本、悉尼+|
| **[TeleCity Group](http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)**| 阿姆斯特丹、伦敦|
| **[Zayo Group](http://www.zayo.com/)**| 华盛顿特区|

 **+** 表示即将推出

有关设置连接的步骤，请参阅[配置 EXP 连接](expressroute-configuring-exps.md)。

### 网络服务提供商 (NSP) 位置


| **服务提供商**| **位置**|
|----------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| **[AT&T](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | 达拉斯+、硅谷、华盛顿特区 |
| **[British Telecom](http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** | 阿姆斯特丹、伦敦、硅谷+、华盛顿特区 |
| **[Colt IPVPN](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | 阿姆斯特丹、伦敦+|
| **[Internet Initiative Japan Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2013/pdf/Azure_E.pdf)**| 东京|
| **[Level 3 Communications - IPVPN](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | 芝加哥、达拉斯、伦敦、西雅图、硅谷、华盛顿特区 |
| **[Orange](http://www.orange-business.com/)** | 阿姆斯特丹、伦敦、硅谷、华盛顿特区|
| **PCCW Global Limited**| 香港 |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** | 新加坡 |
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** | 阿姆斯特丹、金奈+、香港、伦敦、孟买+、新加坡 |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** | 墨尔本+、悉尼 |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** | 伦敦、香港、硅谷、华盛顿特区 |


 **+** 表示即将推出

有关设置连接的步骤，请参阅[配置 NSP 连接](expressroute-configuring-nsps.md)。

### 通过上面未列出的服务提供商建立连接

如果上一部分中未列出你的连接提供商，你仍可以建立连接。

- 请咨询你的连接提供商，以了解他们是否连接到列出的 EXP 位置中的任何 Exchange 提供商。你可以检查下方的链接，以收集 Exchange 提供商所提供的服务详细信息。已有多个连接提供商连接到 EXP 的以太网 Exchange。
	- [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/) 
	- [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
- 让你的连接提供商将你的网络扩展到选择的 Exchange 位置。
	- 确保连接提供商以高可用性方式扩展你的连接，以防出现单点故障。
	- 为确保高可用性，连接提供商（尤其是以太网提供商）可能会要求你采购一对以太网 Exchange 线路。 
- 从 Exchange 提供商处订购一条 ExpressRoute 线路以连接到 Azure。
	- 根据[配置 EXP 连接](expressroute-configuring-exps.md)中的步骤来设置连接。

|**连接提供商**|**Exchange 提供商**|**对等位置**|
|---|---|---|
|**[XO Communications](http://www.xo.com/)**|Equinix|硅谷|


## ExpressRoute 和 Microsoft 云服务
下表提供了有关连接提供商的详细信息以及这些提供商支持的 Microsoft 云服务列表。请与你的服务提供商联系。

### Exchange 提供商 (EXP)

|**服务提供商**|**Microsoft Azure 服务**|**Office 365 服务**|
|---|---|---|
|**Aryaka**|支持||
|**Colt Ethernet**|支持||
|**Equinix**|支持|即将推出|
|**InterCloud**|支持||
|**Level 3 EVPL Service**|支持||
|**TeleCity Group**|支持||
|**Zayo Group**|支持||

### 网络服务提供商 (NSP)

|**服务提供商**|**Microsoft Azure 服务**|**Office 365 服务**|
|---|---|---|
|**AT&T**|支持|即将推出|
|**British Telecom**|支持|即将推出|
|**Colt IPVPN**|支持||
|**Internet Initiative Japan Inc. - IIJ**|支持||
|**Level3 IPVPN**|支持||
|**Orange**|支持|| 
|**SingTel**|支持||
|**Tata Communications**|支持||
|**Telstra Corporation**|支持||
|**Verizon**|支持|| 


## ExpressRoute 系统集成商
根据网络的规模，有时，很难启用专用连接来满足需要。你可以与下表中列出的任一系统集成商合作，以帮助将你加入 ExpressRoute。


|**系统集成商**|**所在洲**|
|---|---|
|**[Nimbo](http://www.nimbo.com/)**|美国||
|**[Dotnet Solutions](http://www.dotnetsolutions.co.uk/)**|欧洲、非洲和中东|

## 后续步骤
- 验证你是否符合 [ExpressRoute 先决条件](expressroute-prerequisites.md)。
- 有关详细信息，请访问[常见问题](expressroute-faqs.md)。
- 选择提供商并配置连接。请参阅[配置 EXP 连接](expressroute-configuring-exps.md)或[配置 NSP 连接](expressroute-configuring-nsps.md)了解配置信息。

<!---HONumber=60-->