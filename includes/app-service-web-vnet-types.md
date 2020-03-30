---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: da43e639779dd4946cb014be777e3a6db0366e24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671502"
---
* 支持除独立定价计划以外的全部定价计划的多租户系统
* 部署到 VNet 中且支持独立定价计划应用的应用服务环境 (ASE)

本文档通过 VNet 集成功能，该功能用于多租户应用。 如果应用在[应用服务环境][ASEintro]中，则该应用已处于 VNet 中且不需要使用 VNet 集成功能来获取同一 VNet 中的资源。 有关所有网络功能的详细信息，请阅读[应用服务网络功能][Networkingfeatures]

VNet 集成使应用可以访问虚拟网络中的资源，但不会从 VNet 授予对应用的入站专用访问权限。 专用站点访问是指仅从专用网络（如 Azure 虚拟网络内部）访问应用。 VNet 集成只是为了从应用对 VNet 进行出站调用。 当在同一区域中的 VNet 和与其他地区的 VNet 一起使用时，VNet 集成功能的行为会有所不同。 VNet 集成功能有两种变体。

* 区域 VNet 集成 - 连接到同一区域中的资源管理器 VNet 时，必须在要集成的 VNet 中具有专用子网。 
* 网关需要 VNet 集成 - 连接到其他区域中的 VNet 或同一区域中的经典 VNet 时，您需要在目标 VNet 中预配虚拟网络网关。

VNet 集成功能：

* 需要标准、高级、高级V2或弹性高级定价计划 
* 支持 TCP 和 UDP
* 使用应用服务应用和功能应用

VNet 集成不支持某些功能，其中包括：

* 装载驱动器
* AD 集成 
* NetBios

网关所需的 VNet 集成仅提供对目标 VNet 或连接到目标 VNet 的具有对等或 VPN 的网络中的资源的访问。 网关所需的 VNet 集成无法访问 ExpressRoute 连接中可用的资源，或无法与服务终结点配合使用。 

无论使用何种版本，VNet 集成都能使应用访问虚拟网络中的资源，但不会从虚拟网络授予对应用的入站专用访问权限。 专用站点访问指的是仅可从专用网络（例如 Azure 虚拟网络内）对应用进行访问。 VNet 集成只是为了从应用对 VNet 进行出站调用。 

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features