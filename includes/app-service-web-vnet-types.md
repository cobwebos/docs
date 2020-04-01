---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: 41b7c7163ff56c848b1b66e606c06ba45d36e610
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419567"
---
* 支持除隔离之外的所有定价计划的多租户系统。
* 应用服务环境，部署到虚拟网络并支持隔离定价计划应用。

VNet 集成功能用于多租户应用。 如果应用位于[应用服务环境中][ASEintro]，则它已位于虚拟网络中，并且不需要使用 VNet 集成功能来访问同一虚拟网络中的资源。 有关所有网络功能的详细信息，请参阅[应用服务网络功能][Networkingfeatures]。

VNet 集成使应用可以访问虚拟网络中的资源，但不会从虚拟网络授予对应用的入站专用访问权限。 专用站点访问是指仅从专用网络（如 Azure 虚拟网络内部）访问应用。 VNet 集成仅用于从应用向虚拟网络进行出站呼叫。 当 VNet 集成功能与同一区域中的虚拟网络和其他地区的虚拟网络一起使用时，其行为方式会有所不同。 VNet 集成功能有两种变体：

* **区域 VNet 集成**：连接到同一区域中的 Azure 资源管理器虚拟网络时，必须在要集成的虚拟网络中具有专用子网。
* **网关所需的 VNet 集成**：当您连接到其他区域中的虚拟网络或同一区域中的经典虚拟网络时，需要在目标虚拟网络中预配 Azure 虚拟网络网关。

VNet 集成功能：

* 需要标准、高级、高级 V2 或弹性高级定价计划。
* 支持 TCP 和 UDP。
* 使用 Azure 应用服务应用和功能应用。

VNet 集成不支持某些内容，例如：

* 装载驱动器。
* 活动目录集成。
* 内比奥斯。

网关所需的 VNet 集成仅提供对目标虚拟网络或与目标虚拟网络连接到目标虚拟网络的与对等互连或 VPN 相连的资源的访问。 网关所需的 VNet 集成无法访问 Azure ExpressRoute 连接中可用的资源，或无法与服务终结点配合使用。

无论使用何种版本，VNet 集成都能使应用访问虚拟网络中的资源，但不会从虚拟网络授予对应用的入站专用访问权限。 专用站点访问是指仅从专用网络（如 Azure 虚拟网络内部）使应用可访问。 VNet 集成仅用于从应用向虚拟网络进行出站呼叫。

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features
