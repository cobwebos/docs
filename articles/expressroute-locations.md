<properties
   pageTitle="ExpressRoute 位置"
   description="本页详细说明了服务的上市区域，以及如何连接到 Azure 区域。"
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/13/2015"
   ms.author="cherylmc" />

# 连接提供商和对等互连位置
所有的 Azure 区域和位置都支持 ExpressRoute。以下地图提供了 Azure 区域和 ExpressRoute 位置的列表。ExpressRoute 位置是指 Microsoft 与多个服务提供商对等互连的位置。
 
![](./media/expressroute-locations/expressroute-locations-map.png)

如果你至少与地缘政治区域内的一个 ExpressRoute 位置连接，你将有权访问地缘政治区域内所有区域中的 Azure 服务。下表提供了地缘政治区域内 ExpressRoute 位置与 Azure 区域的映射。

|**地缘政治区域**|**Azure 区域**|**ExpressRoute 位置**|
|---|---|---|
|**美国**|所有美国区域 - 美国东部、美国西部、美国东部 2、美国中部、美国中南部、美国中北部|亚特兰大、芝加哥、达拉斯、洛杉矶、纽约、西雅图、硅谷、华盛顿特区|
|**南美洲**|巴西南部|圣保罗|
|**欧洲**|欧洲北部、欧洲西部|阿姆斯特丹、伦敦|
|**亚洲**|亚洲东部、亚洲东南部|中国香港、新加坡|
|**日本**|日本西部、日本东部|东京|
|**澳大利亚**|澳大利亚东南部、澳大利亚东部|悉尼|

不支持跨地缘政治区域的连接。你可以与连接提供商合作，使用其网络将连接扩展到不同的地缘政治区域。

## Exchange 提供商 (EXP) 位置
- 有关 Exchange 提供商及其支持位置的列表，请参阅此[表格](https://msdn.microsoft.com/library/azure/4da69a0f-8f52-49ea-a990-dacd4202150a#BKMK_EXP)。
-  有关设置连接的步骤，请访问[配置 EXP 连接](https://msdn.microsoft.com/library/azure/dn606306.aspx)。

## 网络服务提供商 (NSP) 位置
- 有关网络服务提供商及其支持位置的列表，请参阅此[表格](https://msdn.microsoft.com/library/azure/4da69a0f-8f52-49ea-a990-dacd4202150a#BKMK_NSP)。
- 有关设置连接的步骤，请访问[配置 NSP 连接](https://msdn.microsoft.com/library/azure/dn643736.aspx)。

## 通过上面未列出的服务提供商建立连接

如果上一部分中未列出你的连接提供商，你仍可以建立连接。

- 请咨询你的连接提供商，以了解他们是否连接到列出的 EXP 位置中的任何 Exchange 提供商。你可以检查下方的链接，以收集 Exchange 提供商所提供的服务详细信息。已有多个连接提供商连接到 EXP 的以太网 Exchange。
	- [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
	- [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
- 让你的连接提供商将你的网络扩展到选择的 Exchange 位置。
	- 确保连接提供商以高可用性方式扩展你的连接，以防出现单点故障。
	- 为确保高可用性，连接提供商（尤其是以太网提供商）可能会要求你采购一对以太网 Exchange 线路 
- 从 Exchange 提供商处订购一条 ExpressRoute 线路以连接到 Azure
	- 根据[配置 EXP 连接](https://msdn.microsoft.com/library/azure/dn606306.aspx)中的步骤来设置连接。

## 后续步骤
- 验证你是否符合 [ExpressRoute 先决条件](expressroute-prerequisites.md)。
- 有关详细信息，请访问[常见问题](expressroute-faqs.md)。
- 选择提供商并配置连接。请参阅 
[配置 EXP 连接](https://msdn.microsoft.com/library/azure/dn606306.aspx)或[配置 NSP 连接](https://msdn.microsoft.com/library/azure/dn643736.aspx)，了解配置信息。

<!---HONumber=56-->