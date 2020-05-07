---
title: 有关 Analysis Services 网络连接的常见问题 |Microsoft Docs
description: 本文提供了一些关于 Analysis Services 网络连接的常见问题的解答。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b60cf34e8efed2ed63b6e35cfaf7445edb701610
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82838495"
---
# <a name="frequently-asked-questions-about-analysis-services-network-connectivity"></a>有关 Analysis Services 网络连接的常见问题

本文提供有关连接到存储帐户、数据源、防火墙和 IP 地址的常见问题的解答。

## <a name="backup-and-restore"></a>备份和还原

**问**：如何使用防火墙后面的存储帐户来备份和还原？   
**答案**-Azure Analysis Services 不使用固定 IP 地址或服务标记。 Analysis Services 服务器使用的 IP 地址范围可以是*Azure 区域*的 ip 地址范围内的任何内容。 由于你的服务器 IP 地址是可变的，并且可能会随时间而变化，因此防火墙规则需要允许服务器所在的整个 Azure 区域 IP 地址范围。

**问**：我的 Azure 存储帐户与 Analysis Services 服务器在不同的区域中。 如何实现配置存储帐户防火墙设置？   
**答案**-如果存储帐户位于不同的区域，请将存储帐户防火墙设置配置为允许从**所选网络**进行访问。 在 "防火墙**地址范围**" 中，指定 Analysis Services 服务器所在的区域的 IP 地址范围。 若要获取 Azure 区域的 IP 范围，请参阅[AZURE Ip 范围和服务标记–公有云](https://www.microsoft.com/download/details.aspx?id=56519)。 支持将存储帐户防火墙设置配置为允许从所有网络进行访问，但最好选择所选网络并指定 IP 地址范围。 

**问**：我的 Azure 存储帐户与我的 Analysis Services 服务器位于同一区域。 如何配置存储帐户防火墙设置？   
**答**–因为你的 Analysis Services 服务器和存储帐户位于同一区域，所以它们之间的通信使用内部 IP 地址范围，因此，不支持将防火墙配置为使用所选网络并指定 IP 地址范围。 如果组织策略需要防火墙，则必须将其配置为允许来自所有网络的访问。


## <a name="data-source-connections"></a>数据源连接

**问**：我的数据源系统具有 VNET。 如何允许我的 Analysis Services 服务器从 VNET 访问数据库？   
**答案**Azure Analysis Services 无法加入 VNET。 此处的最佳解决方案是在 VNET 中安装和配置本地数据网关，然后使用**AlwaysUseGateway** server 属性配置 Analysis Services 服务器。 若要了解详细信息，请参阅[在 Azure 虚拟网络（VNet）上使用数据源的网关](analysis-services-vnet-gateway.md)。

**问**：我有一个防火墙后面的源数据库。 如何将防火墙配置为允许 Analysis Services 服务器访问它？   
**答案**-Azure Analysis Services 不使用固定 IP 地址或服务标记。 Analysis Services 服务器使用的 IP 地址范围可以是*Azure 区域*的 ip 地址范围内的任何内容。 你必须在源数据库防火墙规则中为服务器的 Azure 区域提供完整的 IP 地址*范围*。 另一种方法是配置本地数据网关，可能更安全。 然后，可以使用[AlwaysUseGateway server 属性](analysis-services-vnet-gateway.md#configure-alwaysusegateway-property)配置 Analysis Services 服务器，然后确保本地数据网关具有数据源的防火墙规则允许的 IP 地址。

## <a name="azure-apps-with-ip-address"></a>具有 IP 地址的 Azure 应用

**问**：我将基于 Azure 的应用程序（例如 Azure Functions、Azure 数据工厂）用于动态变化的 IP 地址。 如何管理 Azure Analysis Services 防火墙规则以动态地允许应用程序在其中执行的 IP 地址？   
**答案**-Azure Analysis Services 不支持私有链接、Vnet 或服务标记。 有一些开源解决方案（例如， https://github.com/mathwro/Scripts/blob/master/Azure/AllowAzure-AnalysisServer.ps1)可检测客户端应用程序的 IP 地址，并自动并临时更新防火墙规则。


## <a name="next-steps"></a>后续步骤

[安装和配置本地数据网关](analysis-services-gateway-install.md)   
[通过本地数据网关连接到本地数据源](analysis-services-gateway.md)   
[为 Azure 虚拟网络 (VNet) 上的数据源使用网关](analysis-services-vnet-gateway.md)
