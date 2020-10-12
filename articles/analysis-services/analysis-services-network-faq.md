---
title: 有关 Analysis Services 网络连接的常见问题 |Microsoft Docs
description: 本文针对 Analysis Services 网络连接的一些较常见问题提供了解答。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b60cf34e8efed2ed63b6e35cfaf7445edb701610
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "82838495"
---
# <a name="frequently-asked-questions-about-analysis-services-network-connectivity"></a>有关 Analysis Services 网络连接的常见问题解答

本文提供了有关连接到存储帐户、数据源、防火墙和 IP 地址的常见问题解答。

## <a name="backup-and-restore"></a>备份和还原

**问题** - 如何使用防火墙后面的存储帐户进行备份和还原？   
**解答** - Azure Analysis Services 不使用固定 IP 地址或服务标记。 Analysis Services 服务器使用的 IP 地址范围可以是 Azure 区域的任何 IP 地址范围。 由于服务器 IP 地址是可变的，可能会随时间而变化，因此防火墙规则需要考虑服务器所在的整个 Azure 区域的 IP 地址范围。

**问题** - 我的 Azure 存储帐户与 Analysis Services 服务器在不同的区域中。 如何配置存储帐户防火墙设置？   
**解答** - 如果存储帐户位于另一区域中，请将存储帐户防火墙设置配置为允许从“所选网络”进行访问。 在防火墙的“地址范围”中，指定 Analysis Services 服务器所在区域的 IP 地址范围。 若要获取 Azure 区域的 IP 范围，请参阅 [AZURE Ip 范围和服务标记–公有云](https://www.microsoft.com/download/details.aspx?id=56519)。 支持将存储帐户防火墙设置配置为允许从“所有网络”进行访问，但是最好选择“所选网络”并指定 IP 地址范围。 

**问题** - 我的 Azure 存储帐户与 Analysis Services 服务器在同一区域中。 如何配置存储帐户防火墙设置？   
**答** –因为你的 Analysis Services 服务器和存储帐户位于同一区域，所以它们之间的通信使用内部 IP 地址范围，因此，不支持将防火墙配置为使用所选网络并指定 IP 地址范围。 如果组织策略需要防火墙，则必须将其配置为允许从“所有网络”进行访问。


## <a name="data-source-connections"></a>数据源连接

**问题** - 我的数据源系统有一个 VNET。 如何允许 Analysis Services 服务器从 VNET 访问数据库？   
**解答** - Azure Analysis Services 无法加入 VNET。 此处的最佳解决方案是在 VNET 中安装和配置本地数据网关，然后使用 AlwaysUseGateway 服务器属性配置 Analysis Services 服务器。 若要了解详细信息，请参阅[对 Azure 虚拟网络 (VNet) 上的数据源使用网关](analysis-services-vnet-gateway.md)。

**问题** - 我有一个位于防火墙后面的源数据库。 如何将防火墙配置为允许 Analysis Services 服务器访问它？   
**解答** - Azure Analysis Services 不使用固定 IP 地址或服务标记。 Analysis Services 服务器使用的 IP 地址范围可以是 Azure 区域的任何 IP 地址范围。 必须在源数据库防火墙规则中为服务器的 Azure 区域提供 IP 地址的完整范围。 另一种可能更安全的方法是配置本地数据网关。 然后，可以使用 [AlwaysUseGateway 服务器属性](analysis-services-vnet-gateway.md#configure-alwaysusegateway-property)配置 Analysis Services 服务器，然后确保本地数据网关具有数据源的防火墙规则允许的 IP 地址。

## <a name="azure-apps-with-ip-address"></a>具有 IP 地址的 Azure 应用

**问题** - 我使用一个基于 Azure 的应用程序（例如，Azure Functions、Azure 数据工厂），其 IP 地址会动态更改。 如何管理 Azure Analysis Services 防火墙规则以动态允许执行应用的 IP 地址？   
**解答** - Azure Analysis Services 不支持专用链接、VNET 或服务标记。 有一些开源解决方案（例如 https://github.com/mathwro/Scripts/blob/master/Azure/AllowAzure-AnalysisServer.ps1) ）会检测客户端应用程序的 IP 地址，并自动临时更新防火墙规则。


## <a name="next-steps"></a>后续步骤

[安装并配置本地数据网关](analysis-services-gateway-install.md)   
[使用本地数据网关连接到本地数据源](analysis-services-gateway.md)   
[对 Azure 虚拟网络 (VNet) 上的数据源使用网关](analysis-services-vnet-gateway.md)
