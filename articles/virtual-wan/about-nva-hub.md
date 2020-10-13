---
title: Azure 虚拟 WAN：有关中心网络虚拟设备的信息
description: 本文介绍虚拟 WAN 集线器中的网络虚拟设备。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: scottnap
Customer intent: As someone with a networking background, I want to learn about Network Virtual Appliances in the Virtual WAN hub.
ms.openlocfilehash: 1e4b8a2d801d7d7eccfaf558c3926ead1ab0a953
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91313767"
---
# <a name="about-network-virtual-appliance-in-an-azure-virtual-wan-hub-preview"></a>关于 Azure 虚拟 WAN 集线器中的网络虚拟设备 (预览) 

Azure 虚拟 WAN 与网络合作伙伴合作，以构建自动化，使用户能够轻松地将其客户的本地设备 (CPE) 连接到虚拟中心的 Azure VPN 网关。 Azure 正在与选择的网络合作伙伴合作，使客户能够将第三方网络虚拟设备 () NVA 直接部署到虚拟中心。 这样，想要将其分支 CPE 连接到虚拟中心的同一品牌 NVA 的客户可以利用专用的端到端 SD 功能。

Barracuda 网络是提供 NVA 产品/服务的第一家合作伙伴，可通过其 [Barracuda CLOUDGEN WAN](https://www.barracuda.com/products/cloudgenwan) 产品直接部署到虚拟 WAN 中心。 Azure 正在与更多合作伙伴合作，因此应查看其他产品/服务。

> [!NOTE]
> 只有可部署到虚拟 WAN 中心的 NVA 产品/服务可以部署到虚拟 WAN 集线器。 不能将其部署到 Azure 中的任何虚拟网络。

## <a name="how-does-it-work"></a><a name="how"></a>工作原理

可直接部署到 Azure 虚拟 WAN 中心的 Nva 专门设计为在虚拟中心中使用。 NVA 产品/服务作为托管应用程序发布到 Azure Marketplace，客户可以直接从 Azure Marketplace 部署产品/服务，也可以通过 Azure 门户从虚拟中心部署产品/服务。

:::image type="content" source="./media/about-nva-hub/high-level-process.png" alt-text="过程概述":::

根据其部署要求，每个合作伙伴的 NVA 产品/服务的体验和功能都略有不同。 不过，虚拟 WAN 集线器中 NVA 的所有合作伙伴产品/服务有一些公共功能。

* 通过 Azure Marketplace 提供的托管应用程序体验。
* 基于 NVA 基础结构单元的容量和计费。
* 运行状况指标通过 Azure Monitor。

### <a name="managed-application"></a><a name="managed"></a>托管应用程序

可部署到虚拟 WAN 集线器中的所有 NVA 产品都将拥有一个在 Azure Marketplace 中可用的 **托管应用程序** 。 托管应用程序允许合作伙伴执行以下操作：

* 为其 NVA 生成自定义部署体验。
* 提供专用的资源管理器模板，使其能够在虚拟 WAN 中心直接创建 NVA。
* 直接或通过 Azure Marketplace 计费软件许可成本。
* 公开自定义属性和资源计量。

根据设备部署、配置许可和管理需求，NVA 合作伙伴可能会创建不同的资源。 当客户在虚拟 WAN 中心（如所有托管的应用程序）中创建 NVA 时，会在其订阅中创建两个资源组。

* **客户资源组** -此项将包含托管应用程序的应用程序占位符。 合作伙伴可以使用它来公开在此处选择的任何客户属性。
* **托管资源组** -客户无法直接在此资源组中配置或更改资源，因为这由托管应用程序的发布者控制。 此资源组将包含 **NetworkVirtualAppliances** 资源。

:::image type="content" source="./media/about-nva-hub/managed-app.png" alt-text="过程概述":::

### <a name="nva-infrastructure-units"></a><a name="units"></a>NVA 基础结构单位

在虚拟 WAN 集线器中创建 NVA 时，必须选择要部署的 NVA 基础结构单元的数量。 **NVA 基础结构单位**是虚拟 WAN 集线器中 NVA 的聚合带宽容量单位。 **NVA 基础结构单元**与 VPN[缩放单位](pricing-concepts.md#scale-unit)类似于你对容量和大小进行调整的方式。

* 1 NVA 基础结构单位代表进入此 NVA 的所有分支站点连接的聚合带宽 500 Mbps，成本为 $ 0.25/小时。
* 对于给定的 NVA 虚拟中心部署，Azure 支持 1-80 NVA 基础结构单位。
* 每个合作伙伴可能会提供不同的 NVA 基础结构单元束，它们是所有受支持的 NVA 基础结构单元配置的子集。

与 VPN 缩放单位类似，如果选择 *1 个 NVA 基础结构单位 = 500 Mbps*，这意味着将创建两个冗余实例，每个实例的最大吞吐量为 500 Mbps。 例如，如果你有 5 个分支，每个分支执行 10 Mbps 的 IO，则前端的聚合吞吐量需要达到 50 Mbps。 应在评估支持中心的分支数量所需的容量后，规划 NVA 的聚合容量。

## <a name="network-virtual-appliance-configuration-process"></a><a name="configuration"></a>网络虚拟设备配置过程

合作伙伴已努力提供在部署过程中自动配置 NVA 的体验。 将 NVA 预配到 virtual hub 后，NVA 所需的任何其他配置必须通过 NVA 合作伙伴门户或管理应用程序完成。 无法直接访问 NVA。

## <a name="site-and-connection-resources-with-nvas"></a><a name="resources"></a>具有 Nva 的站点和连接资源

与 Azure VPN 网关配置不同，你无需创建 **站点** 资源、 **站点到站点连接** 资源或 **点到站点连接** 资源，即可将分支站点连接到虚拟 WAN 集线器中的 NVA。 这是通过 NVA 合作伙伴管理的。

仍需创建集线器到 VNet 连接，将虚拟 WAN 中心连接到 Azure 虚拟网络。

## <a name="supported-regions"></a><a name="regions"></a>支持的区域

可在以下区域中预览虚拟中心中的 NVA：

|地缘政治区域 | Azure 区域|
|---|---|
| 北美| 美国西部、美国中南部、美国东部2   |
| 南美洲 | 巴西南部 |
| 欧洲 | 西欧，英国南部|
|  中东 | 阿拉伯联合酋长国北部 |
| 亚洲 | Japan East |
| 澳大利亚 | 澳大利亚东部 |

## <a name="faq"></a>常见问题解答

### <a name="i-am-a-network-appliance-partner-and-want-to-get-our-nva-in-the-hub--can-i-join-this-partner-program"></a>我是网络设备合作伙伴，想要在中心获取我们的 NVA。  能否加入此合作伙伴计划？

遗憾的是，目前尚无容量可用于任何新的合作伙伴产品/服务。 请在11月后查看我们！

### <a name="can-i-deploy-any-nva-from-azure-marketplace-into-the-virtual-wan-hub"></a>是否可以将 Azure Marketplace 中的任何 NVA 部署到虚拟 WAN 中心？

不是。 目前，只有 [Barracuda CLOUDGEN WAN](https://aka.ms/BarracudaMarketPlaceOffer) 可用于部署到虚拟 WAN 中心。

### <a name="what-is-the-cost-of-the-nva"></a>NVA 的成本是多少？

必须从 Barracuda 购买 Barracuda CloudGen WAN NVA 许可证。 有关授权的详细信息，请参阅 [Barracuda 的 CLOUDGEN WAN 页面](https://www.barracuda.com/products/cloudgenwan)。 此外，你还将对你使用的 NVA 基础结构单元以及所使用的任何其他资源产生费用。 有关详细信息，请参阅 [定价概念](pricing-concepts.md)。

### <a name="can-i-deploy-an-nva-to-a-basic-hub"></a>是否可以将 NVA 部署到基本中心？

不是。 如果要部署 NVA，必须使用标准集线器。

### <a name="can-i-deploy-an-nva-into-a-secure-hub"></a>是否可以将 NVA 部署到安全中心？

是。 可以使用 Azure 防火墙将 Barracuda CloudGen WAN 部署到集线器。

### <a name="can-i-connect-any-cpe-device-in-my-branch-office-to-barracuda-cloudgen-wan-nva-in-the-hub"></a>是否可以将分支机构中的任何 CPE 设备连接到中心的 Barracuda CloudGen WAN NVA？

不是。 Barracuda CloudGen WAN 仅与 Barracuda CPE 设备兼容。 若要详细了解 CloudGen WAN 要求，请参阅 [Barracuda 的 CLOUDGEN WAN 页面](https://www.barracuda.com/products/cloudgenwan)。

### <a name="what-routing-scenarios-are-supported-with-nva-in-the-hub"></a>集线器中的 NVA 支持哪些路由方案？

集线器中的 Nva 支持虚拟 WAN 支持的所有路由方案。

## <a name="next-steps"></a>后续步骤

若要了解有关虚拟 WAN 的详细信息，请参阅 [虚拟 Wan 概述](virtual-wan-about.md) 一文。