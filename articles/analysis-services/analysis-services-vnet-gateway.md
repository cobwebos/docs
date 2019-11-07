---
title: 为 VNet 数据源配置 Azure Analysis Services |Microsoft Docs
description: 了解如何配置 Azure Analysis Services 服务器，以便在 Azure 虚拟网络（VNet）上为数据源使用网关。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 384f4c2ee3021678c530aa5c98eaf4dbfd661e95
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73572268"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>为 Azure 虚拟网络 (VNet) 上的数据源使用网关

本文介绍了当数据源位于[Azure 虚拟网络（VNet）](../virtual-network/virtual-networks-overview.md)上时要使用的 Azure Analysis Services **AlwaysUseGateway**服务器属性。

## <a name="server-access-to-vnet-data-sources"></a>对 VNet 数据源的服务器访问

如果通过 VNet 访问数据源，则 Azure Analysis Services 服务器必须连接到那些数据源，就像它们位于本地自己的环境中一样。 可以配置 **AlwaysUseGateway** 服务器属性来指定服务器通过[本地网关](analysis-services-gateway.md)访问所有数据源。 

Azure SQL 数据库托管实例数据源使用专用 IP 地址在 Azure VNet 中运行。 如果在实例上启用了公共终结点，则不需要网关。 如果未启用公共终结点，则需要本地数据网关，并且必须将 AlwaysUseGateway 属性设置为 true。

> [!NOTE]
> 仅当安装并配置了[本地数据网关](analysis-services-gateway.md)时，此属性才有效。 网关可以在 VNet 上。

## <a name="configure-alwaysusegateway-property"></a>配置 AlwaysUseGateway 属性

1. 在“SSMS”>“服务器”>“属性” **“常规”中，选择“显示高级(全部)属性”。**  > 
2. 在“ASPaaS\AlwaysUseGateway”中，选择“true”。

    ![始终使用网关属性](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>另请参阅
[连接到本地数据源](analysis-services-gateway.md)   
[安装并配置本地数据网关](analysis-services-gateway-install.md)   
[Azure 虚拟网络 (VNET)](../virtual-network/virtual-networks-overview.md)   

