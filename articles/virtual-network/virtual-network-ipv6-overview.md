<!-- not suitable for Mooncake -->

<properties 
   pageTitle="IPv6 概述 | Azure"
   description="了解 Azure 中的 IPv6 寻址"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags
	ms.service="virtual-network"
	ms.date="03/04/2016"
	wacn.date=""/>

# Azure 中的 IPv6 支持

IPv6 协议首先由 Internet 工程任务组 (IETF) 于1998 年提出，目的是解决 32 位 IPv4 地址在公共 Internet 上存在的限制问题。随着 Internet 连接型移动设备数量的增加以及物联网 (IoT) 的兴起，IPv6 地址在公共 Internet 上的使用正变得越来越频繁。

>[AZURE.IMPORTANT] Azure 中的 IPv6 目前以专用预览版的形式提供，而且并不是提供给所有客户。当该功能可供所有客户使用时，将会对本页进行更新。

以下列表说明了 Azure 中的 IPv6 功能。

- **Azure DNS**
	- Azure DNS 支持 IPv6 并提供 AAAA 记录，以及 IPv4 A 记录。
	- Azure DNS 使用查找用的 AAAA 记录和 A 记录来响应查询（当两种记录都存在时）。 
- **Office365**
	- Office365 服务支持 IPv6。
	- 你可以通过 ExpressRoute 线路从本地网络连接到 Office365 中的 IPv6 资源。

## Azure DNS

你可以在 Azure DNS 中托管 IPv6 AAAA 记录。Azure DNS 在收到对资源的查询时，会使用适用于主机名的所有记录进行响应。例如，如果给定的主机名（例如 www.contoso.com） 存在 A 记录和 AAAA 记录，Azure DNS 会将这两种地址作为查询响应进行发送。在收到来自 Azure DNS 或任何其他支持 AAAA 记录的 DNS 服务器的响应后，DNS 客户端有权决定是使用 IPv4 还是 IPv6 终结点进行连接。

>[AZURE.NOTE] 在尝试 IPv4 连接之前，Windows 计算机始终会先尝试使用 IPv6 进行连接。

## Office 365

你可以通过 ExpressRoute 线路和 Microsoft 对等互连在本地网络和 Office365 之间使用 IPv6 连接。在使用 Microsoft 对等互连进行连接时，请确保你了解 [ExpressRoute 工作原理](/documentation/articles/expressroute-introduction)。

## 后续步骤

- 了解 [Azure DNS](/documentation/articles/dns-overview)。
- 了解 [ExpressRoute](/documentation/articles/expressroute-introduction)。

<!---HONumber=Mooncake_0418_2016-->