---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: da43e639779dd4946cb014be777e3a6db0366e24
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671502"
---
* 支持除独立定价计划以外的全部定价计划的多租户系统
* 应用服务环境（ASE），它部署到 VNet 中并支持隔离的定价计划应用

本文档介绍 VNet 集成功能，该功能在多租户应用中使用。 如果你的应用处于[应用服务环境][ASEintro]中，则它已在 vnet 中，无需使用 VNet 集成功能来访问同一 VNet 中的资源。 有关所有网络功能的详细信息，请参阅[应用服务网络功能][Networkingfeatures]

通过 VNet 集成，你的应用可以访问虚拟网络中的资源，但不会从 VNet 授予对应用的入站私有访问权限。 专用站点访问是指使应用只能从专用网络（如 Azure 虚拟网络中的）进行访问。 VNet 集成仅适用于从应用到 VNet 的出站调用。 与同一区域中的 Vnet 和其他区域中的 Vnet 一起使用时，VNet 集成功能的行为方式有所不同。 VNet 集成功能具有两种变体。

* 区域 VNet 集成-连接到同一区域中资源管理器 Vnet 时，必须在要与之集成的 VNet 中具有专用子网。 
* 网关所需的 VNet 集成-连接到其他区域中的 Vnet 或同一区域中的经典 VNet 时，需要在目标 VNet 中预配虚拟网络网关。

VNet 集成功能：

* 需要标准、高级、PremiumV2 或弹性高级定价计划 
* 支持 TCP 和 UDP
* 使用应用服务应用和函数应用

VNet 集成不支持某些功能，其中包括：

* 装载驱动器
* AD 集成 
* NetBios

网关所需的 VNet 集成仅提供对目标 VNet 中的资源的访问权限，或通过对等互连或 Vpn 连接到目标 VNet 的网络中的资源。 网关所需的 VNet 集成不允许访问通过 ExpressRoute 连接提供的资源，也不能与服务终结点一起使用。 

无论使用何种版本，VNet 集成都允许应用访问虚拟网络中的资源，但不会从虚拟网络授予对应用的入站私有访问权限。 专用站点访问指的是仅可从专用网络（例如 Azure 虚拟网络内）对应用进行访问。 VNet 集成仅适用于从应用到 VNet 的出站调用。 

<!--Links-->
[ASEintro]: https://docs.microsoft.com/azure/app-service/environment/intro
[Networkingfeatures]: https://docs.microsoft.com/azure/app-service/networking-features