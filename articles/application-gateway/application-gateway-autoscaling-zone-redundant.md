---
title: Azure 中的自动缩放和区域冗余应用程序网关（公共预览版）| Microsoft Docs
description: 本文将介绍如何使用 Azure 门户应用程序网关的 Web 应用程序防火墙请求大小限制和排除列表。
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 09/26/2018
ms.author: victorh
ms.openlocfilehash: ab1c9405042de02183b8742fa940a3a5a482923a
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2018
ms.locfileid: "47165223"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-public-preview"></a>自动缩放和区域冗余应用程序网关（公共预览版）

应用程序网关和 Web 应用程序防火墙 (WAF) 现依据新 SKU 提供可用的公共预览版，可增强性能并添加对关键新功能（如自动缩放、区域冗余）的支持，并支持静态 VIP。 新 SKU 将继续支持通用版 SKU 的现有功能，并且已知限制部分几乎未列出异常。 新 SKU 包括以下增强功能：

- 自动缩放：凭借自动缩放 SKU，应用程序网关或 WAF 部署可根据变化中的流量负载模式增加或减少。 自动缩放还无需在预配期间要求选择部署大小或实例计数。 因此，SKU 提供了真正的弹性。 在新 SKU 中，应用程序网关可同时在固定容量（自动缩放已禁用）以及启用自动缩放的模式下操作。 固定容量模式对具有一致性和可预测工作负荷的方案非常有用。 自动缩放模式有利于在应用程序流量中出现大量变化的应用程序中。
   
   > [!NOTE]
   > 自动缩放当前不适用于 WAF SKU。 使用固定容量模式，而不是自动缩放模式配置 WAF。
- 区域冗余：应用程序网关或 WAF 部署可跨多个可用性区域，因此不需使用流量管理器在每个区域预配和固定单独的应用程序网关实例。 可以选择一个区域或多个区域来部署应用程序网关实例，以便确保区域故障复原。 应用程序的后端池可以通过类似方式分布在多个可用性区域中。
- 性能增强：与通用版 SKU 相比，自动缩放 SKU 可提供高达 5 倍的 SSL 卸载性能。
- 缩短部署和更新时间：与通用版 SKU 相比，自动缩放 SKU 缩短了部署和更新时间。
- 静态 VIP：应用程序网关 VIP 现在支持独占形式的静态 VIP 类型。 这样可确保与应用程序网关关联的 VIP 即便在重启后也不会更改。

> [!IMPORTANT]
> 自动缩放和区域冗余应用程序网关 SKU 目前处于公共预览状态。 此预览版在提供时没有附带服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>支持的区域
目前美国东部 2、美国中部、美国西部 2、法国中部、西欧和东南亚都提供自动缩放 SKU。

## <a name="pricing"></a>定价
预览版不收费。 除应用程序网关之外的资源（例如 Key Vault、虚拟机等）会收费。 

## <a name="known-issues-and-limitations"></a>已知问题和限制

|问题|详细信息|
|--|--|
|计费|目前不收费。|
|FIPS 模式、WebSocket|目前不支持。|
|“仅 ILB”模式|目前不支持。 同时支持公共和 ILB 模式。|
|Web 应用程序防火墙自动缩放|WAF 不支持自动缩放模式。 支持固定容量模式。|

## <a name="next-steps"></a>后续步骤
- [使用 Azure PowerShell 创建具有预留虚拟 IP 地址的自动缩放区域冗余应用程序网关](tutorial-autoscale-ps.md)
- 了解有关[应用程序网关](overview.md)的详细信息。
- 了解有关 [Azure 防火墙](../firewall/overview.md)的详细信息。 

