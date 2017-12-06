---
title: "删除虚拟网络网关：Azure 门户：Resource Manager | Microsoft Docs"
description: "在 Resource Manager 部署模型中使用 Azure 门户删除虚拟网络网关。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: cherylmc
ms.openlocfilehash: 1d289c09465cb8d5e4bfa569441dffcbf562b3bf
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2017
---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>使用门户删除虚拟网络网关

> [!div class="op_single_selector"]
> * [Azure 门户](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell（经典）](vpn-gateway-delete-vnet-gateway-classic-powershell.md)

可以使用多种不同的方法来删除 VPN 网关配置中的虚拟网络网关。

- 如果要删除所有信息并从头开始配置（例如，在测试环境中），可以删除资源组。 删除某个资源组时，会删除该组中的所有资源。 仅不想要保留资源组中的任何资源时，才建议使用此方法。 使用这种方法时，无法做到有选择性地删除一部分资源。

- 如果想要保留资源组中的某些资源，则删除虚拟网络网关的过程会略微复杂一些。 在删除虚拟网络网关之前，必须先删除任何依赖于该网关的资源。 遵循的步骤取决于创建的连接类型，以及每个连接的依赖资源。

## <a name="delete-a-vpn-gateway"></a>删除 VPN 网关

若要删除虚拟网络网关，必须先删除与该虚拟网络网关相关的每个资源。 由于存在依赖关系，必须按特定的顺序删除资源。

[!INCLUDE [delete gateway](../../includes/vpn-gateway-delete-vnet-gateway-portal-include.md)]

此时，虚拟网络网关已删除。 接下来的步骤将帮助你删除不再使用的任何资源。

### <a name="to-delete-the-local-network-gateway"></a>删除本地网络网关

1. 在“所有资源”中，找到与每个连接相关联的本地网络网关。
2. 在本地网络网关的“概述”边栏选项卡上，单击“删除”。

### <a name="to-delete-the-public-ip-address-resource-for-the-gateway"></a>删除网关的公共 IP 地址资源

1. 在“所有资源”中，找到已分配给网关的公共 IP 地址资源。 如果虚拟网络网关采用主动-主动配置，会显示两个公共 IP 地址。 
2. 在公共 IP 地址的“概述”页上，单击“删除”，并单击“是”进行确认。

### <a name="to-delete-the-gateway-subnet"></a>删除网关子网

1. 在“所有资源”中，找到虚拟网络。 
2. 在“子网”边栏选项卡上，单击“GatewaySubnet”，并单击“删除”。 
3. 单击“是”确认要删除该网关子网。

## <a name="deleterg"></a>通过删除资源组来删除 VPN 网关

如果不关心是否要保留资源组中的任何资源，而只是要从头开始配置，则可以删除整个资源组。 这种方法可以快速删除所有信息。 以下步骤仅适用于 Resource Manager 部署模型。

1. 在“所有资源”中，找到该资源组并单击以打开边栏选项卡。
2. 单击“删除” 。 在“删除”边栏选项卡上，查看受影响的资源。 请确保要删除所有这些资源。 否则使用本文开头[删除 VPN 网关](#deletegw)中的步骤。
3. 要继续，请键入要删除的资源组的名称，并单击“删除”。