---
title: 为 Azure 虚拟网络数据源使用本地数据网关 | Microsoft Docs
description: 了解如何对服务器进行配置来为 VNET 上的数据源使用网关。
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 61c4463060016ecbd319b027d48c87f3a6a8ad48
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
---
# <a name="use-gateway-for-datasources-on-an-azure-virtual-network-vnet"></a>为 Azure 虚拟网络 (VNET) 上的数据源使用网关

本文介绍了当数据源位于 [Azure 虚拟网络 (VNET)](../virtual-network/virtual-networks-overview.md) 上时要使用的 **AlwaysUseGateway** 服务器属性。

## <a name="server-access-to-vnet-datasources"></a>对 VNET 数据源的服务器访问

如果通过 VNET 访问数据源，则 Azure Analysis Services 服务器必须连接到那些数据源，就像那些数据源位于本地在你自己的环境中一样。 可以配置 **AlwaysUseGateway** 服务器属性来指定服务器通过[本地网关](analysis-services-gateway.md)访问所有数据源数据。 

> [!NOTE]
> 仅当安装并配置了[本地数据网关](analysis-services-gateway.md)时此属性才有效。 网关可以在 VNET 上。

## <a name="configure-alwaysusegateway-property"></a>配置 AlwaysUseGateway 属性

1. 在“SSMS”>“服务器”>“属性” > “常规”中，选择“显示高级(全部)属性”。
2. 在“ASPaaS\AlwaysUseGateway”中，选择“true”。

    ![始终使用网关属性](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>另请参阅
[连接到本地数据源](analysis-services-gateway.md)   
[安装并配置本地数据网关](analysis-services-gateway-install.md)   
[Azure 虚拟网络 (VNET)](../virtual-network/virtual-networks-overview.md)   

