---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 04/15/2020
ms.author: ccompy
ms.openlocfilehash: c31a5aaa9866a4ce97cd3cd59a8e363834f70587
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81312839"
---
* 支持除隔离以外的所有定价计划的多租户系统。
* 应用服务环境，它将部署到 VNet 中并支持隔离的定价计划应用。

在多租户应用中使用 VNet 集成功能。 如果应用在[应用服务环境][ASEintro]中，则该应用已处于 VNet 中且不需要使用 VNet 集成功能来获取同一 VNet 中的资源。 有关所有网络功能的详细信息，请参阅[应用服务网络功能][Networkingfeatures]。

通过 VNet 集成，你的应用程序可以访问 VNet 中的资源，但不会向应用程序授予对 VNet 的入站私有访问权限。 专用站点访问是指使应用只能从专用网络（如 Azure 虚拟网络中的）访问。 VNet 集成仅用于从应用到 VNet 的出站调用。 VNet 集成功能在同一区域中与 VNet 一起使用时，它的行为方式不同，而在其他区域使用 VNet。 VNet 集成功能具有两种变体：

* **区域 VNet 集成**：连接到同一区域中的 Azure 资源管理器虚拟网络时，必须在要与之集成的 VNet 中具有专用子网。
* **网关-必需的 VNet 集成**：连接到其他区域中的 VNet 或同一区域中的经典虚拟网络时，需要在目标 VNet 中预配 Azure 虚拟网络网关。

VNet 集成功能：

* 需要标准、高级、PremiumV2 或弹性高级定价计划。
* 支持 TCP 和 UDP。
* 使用 Azure App Service 应用和函数应用。

VNet 集成不支持某些功能，如：

* 装载驱动器。
* Active Directory 集成。
* NetBIOS.

网关-必需的 VNet 集成仅提供对目标 VNet 中的资源或使用对等互连或 Vpn 连接到目标 VNet 的网络中的资源的访问权限。 网关-必需的 VNet 集成不允许访问通过 Azure ExpressRoute 连接提供的资源，也不能与服务终结点一起使用。

无论使用何种版本，VNet 集成都可以让应用访问 VNet 中的资源，但不会从 VNet 授予对应用的入站私有访问权限。 专用站点访问是指使应用只能从专用网络（如 Azure VNet 中的）访问。 VNet 集成只是为了从应用对 VNet 进行出站调用。

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features
