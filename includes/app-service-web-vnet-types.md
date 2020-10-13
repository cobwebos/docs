---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 10/01/2020
ms.author: ccompy
ms.openlocfilehash: 481bd4f50eb527bcad2ba79b5ba4b9df4b872bfc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91739896"
---
* 支持除独立定价计划以外的全部定价计划的多租户系统。
* 部署到 VNet 中且支持独立定价计划应用的应用服务环境。

VNet 集成功能用于多租户应用。 如果应用在[应用服务环境][ASEintro]中，则该应用已处于 VNet 中且不需要使用 VNet 集成功能来获取同一 VNet 中的资源。 有关所有网络功能的详细信息，请参阅[应用服务网络功能][Networkingfeatures]。

VNet 集成允许应用访问 VNet 中的资源，但不允许通过 VNet 对应用进行入站专用访问。 专用站点访问指的是仅可从专用网络（例如 Azure 虚拟网络）对应用进行访问。 VNet 集成仅用来从应用对 VNet 进行出站调用。 VNet 集成功能可以用于同一区域中的 VNet，也可用于其他区域中的 VNet，这两种情况下的行为有所不同。 VNet 集成功能有两种变体：

* **区域 VNet 集成**：连接到同一区域中的 Azure 资源管理器虚拟网络时，必须在要与之集成的 VNet 中具有专用子网。
* **需要网关的 VNet 集成**：连接到其他区域中的 VNet 或同一区域中的经典虚拟网络时，需要在目标 VNet 中预配 Azure 虚拟网关。

VNet 集成功能：

* 需要标准、高级、PremiumV2、PremiumV3 或弹性高级定价计划。
* 支持 TCP 和 UDP。
* 适用于 Azure 应用服务应用和函数应用。

VNet 集成不支持某些功能，例如：

* 装载驱动器。
* Active Directory 集成。
* NetBIOS。

需要网关的 VNet 集成只允许访问目标 VNet 中的资源，或者访问通过对等互连或 VPN 连接到目标 VNet 的网络中的资源。 需要网关的 VNet 集成不支持访问可通过 Azure ExpressRoute 连接使用的资源，也不适用于服务终结点。

无论使用哪个版本，VNet 集成都允许应用访问 VNet 中的资源，但不允许通过 VNet 对应用进行入站专用访问。 专用站点访问指的是仅可从专用网络（例如 Azure VNet）对应用进行访问。 VNet 集成只是为了从应用对 VNet 进行出站调用。

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features
