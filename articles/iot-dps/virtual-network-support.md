---
title: Azure IoT 设备预配服务（DPS）对虚拟网络的支持
description: 如何在 Azure IoT 设备预配服务（DPS）中使用虚拟网络连接模式
services: iot-hub
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: wesmc
ms.openlocfilehash: 97168c95893ffdea99180c997265f259176ed07f
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531051"
---
# <a name="azure-iot-hub-device-provisioning-service-dps-support-for-virtual-networks"></a>Azure IoT 中心设备预配服务（DPS）对虚拟网络的支持

本文介绍使用 DPS 通过 IoT 中心预配的 IoT 设备的虚拟网络（VNET）连接模式。 此模式在客户拥有的 Azure VNET 内的设备、DPS 和 IoT 中心之间提供专用连接。 

在大多数情况下，使用 VNET 配置了 DPS 后，还会在同一 VNET 中配置 IoT 中心。 有关 IoT 中心的 VNET 支持和配置的更多特定信息，请参阅[Iot 中心虚拟网络支持](../iot-hub/virtual-network-support.md)。



## <a name="introduction"></a>简介

默认情况下，DPS 主机名映射到一个公共终结点，该终结点具有通过 Internet 通过公共方式路由的 IP 地址。 此公共终结点对所有客户都可见。 IoT 设备可以通过广域网络以及本地网络来尝试访问公共终结点。

出于多种原因，客户可能想要限制与 DPS 等 Azure 资源的连接。 其原因包括：

* 通过公共 Internet 防止连接泄露。 可以通过为 IoT 中心和 DPS 资源的网络级隔离引入附加的安全层，来降低曝光度

* 提供来自本地网络资产的专用连接体验，确保数据和流量直接传输到 Azure 主干网络。

* 阻止来自敏感的本地网络的渗透攻击。 

* 采用 Azure 范围内常见的[专用终结点](../private-link/private-endpoint-overview.md)连接模式。

限制连接的常见方法包括[DPS IP 筛选规则](./iot-dps-ip-filtering.md)和具有[专用终结点](../private-link/private-endpoint-overview.md)的虚拟网络（VNET）。 本文的目的是介绍使用专用终结点的 DPS 的 VNET 方法。 

在本地网络中运行的设备可以使用[虚拟专用网络（VPN）](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)或[ExpressRoute](https://azure.microsoft.com/services/expressroute/)专用对等互连连接到 Azure 中的 VNET，并通过专用终结点访问 DPS 资源。 

专用终结点是在客户拥有的 VNET 中分配的专用 IP 地址，可通过该地址访问 Azure 资源。 通过使用 DPS 资源的专用终结点，你可以允许在 VNET 中运行的设备请求你的 DPS 资源进行预配，而不允许向公共终结点发送流量。


## <a name="prerequisites"></a>先决条件

在继续操作之前，请确保满足以下先决条件：

* 已创建 DPS 资源并将其链接到 IoT 中心。 有关设置新的 DPS 资源的指南，请参阅[使用 Azure 门户设置 IoT 中心设备预配服务](./quick-setup-auto-provision.md)

* 你已设置了一个 Azure VNET，其中包含将在其中创建专用终结点的子网。 有关详细信息，请参阅[使用 Azure CLI 创建虚拟网络](../virtual-network/quick-create-cli.md)。

* 对于在本地网络内部运行的设备，请在 Azure VNET 中设置[虚拟专用网络（VPN）](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)或[ExpressRoute](https://azure.microsoft.com/services/expressroute/)专用对等互连。

## <a name="private-endpoint-limitations"></a>私有终结点限制

使用专用终结点时，请注意以下对 DPS 的当前限制：

* 当 DPS 资源和链接的中心位于不同云中时，专用终结点将无法与 DPS 一起使用。 例如， [Azure 政府版和全球 azure](../azure-government/documentation-government-welcome.md)。

* 目前，[使用 DPS Azure Functions 的自定义分配策略](how-to-use-custom-allocation-policies.md)将不会使用 VNET 和专用终结点。 

* 最新的 DPS VNET 支持仅适用于进入 DPS 的数据。 数据传出（从 DPS 到 IoT 中心的流量）使用内部服务到服务机制，而不是专用 VNET。 当前不支持在 DPS 和 IoT 中心之间进行基于 VNET 的完全锁定的出口支持。

* 最小滞后时间分配策略用于将设备分配到具有最低延迟的 IoT 中心。 此分配策略在虚拟网络环境中不可靠。 

## <a name="set-up-a-private-endpoint"></a>设置专用终结点

若要设置专用终结点，请执行以下步骤：

1. 在[Azure 门户](https://portal.azure.com/)中，打开你的 DPS 资源，然后单击 "**网络**" 选项卡。单击 "**专用终结点连接**" 和 " **+ 专用终结点**"。

    ![为 DPS 添加新的专用终结点](./media/virtual-network-support/networking-tab-add-private-endpoint.png)

2. 在 "_创建专用终结点_基本信息" 页上，输入下表中所述的信息。

    ![配置新的专用终结点映射到的资源](./media/virtual-network-support/create-private-endpoint-basics.png)

    | 字段 | 值 |
    | :---- | :-----|
    | **订阅** | 选择要包含专用终结点的所需 Azure 订阅。  |
    | **资源组** | 选择或创建一个资源组以包含专用终结点 |
    | **名称**       | 输入专用终结点的名称 |
    | **区域**     | 选择的区域必须与包含 VNET 的区域相同，但它不一定要与 DPS 资源相同。 |

    单击 "**下一步：资源**" 以配置专用终结点将指向的资源。

3. 在 "_创建专用终结点资源_" 页上，输入下表中所述的信息。

    ![配置新的专用终结点映射到的资源](./media/virtual-network-support/create-private-endpoint-resource.png)

    | 字段 | 值 |
    | :---- | :-----|
    | **订阅**        | 选择包含私有终结点将指向的 DPS 资源的 Azure 订阅。  |
    | **资源类型**       | 选择 " **ProvisioningServices/设备**"。 |
    | **资源**            | 选择专用终结点将映射到的 DPS 资源。 |
    | **目标子资源** | 选择**iotDps**。 |

    > [!TIP]
    > 本文的 "[请求专用终结点](#request-a-private-endpoint)" 部分中提供了**按资源 ID 或别名连接到 Azure 资源**的信息。


    单击 "**下一步：配置**" 可为专用终结点配置 VNET。

4. 在 "_创建专用终结点配置_" 页上，选择要在其中创建专用终结点的虚拟网络和子网。
 
    单击 "**下一步：标记**"，并根据需要提供资源的任何标记。

    ![配置新的专用终结点映射到的资源](./media/virtual-network-support/create-private-endpoint-configuration.png)

6. 单击 "**查看 + 创建**"，然后单击 "**创建**" 创建专用终结点资源。


## <a name="request-a-private-endpoint"></a>请求专用终结点

可以按资源 ID 请求将专用终结点分发到 DPS 资源。 为了发出此请求，需要资源所有者提供资源 ID。 

1. 资源 ID 在 DPS 资源的 "属性" 选项卡上提供，如下所示。

    !["DPS 属性" 选项卡](./media/virtual-network-support/dps-properties.png)

    > [!CAUTION]
    > 请注意，资源 ID 确实包含订阅 ID。 

2. 获得资源 ID 后，请按照_创建专用终结点资源_页上的步骤3中的 "[设置专用终结点](#set-up-a-private-endpoint)" 中的步骤进行操作。 单击 "**按资源 ID 或别名连接到 Azure 资源**"，并输入下表中的信息。 

    | 字段 | 值 |
    | :---- | :-----|
    | **资源 ID 或别名** | 输入 DPS 资源的资源 ID。 |
    | **目标子资源** | 输入**iotDps** |
    | **请求消息** | 输入 DPS 资源所有者的请求消息。<br>例如， <br>`Please approve this new private endpoint`<br>`for IoT devices in site 23 to access this DPS instance`  |

    单击 "**下一步：配置**" 可为专用终结点配置 VNET。

3. 在 "_创建专用终结点配置_" 页上，选择要在其中创建专用终结点的虚拟网络和子网。
 
    单击 "**下一步：标记**"，并根据需要提供资源的任何标记。

4. 单击 "**查看 + 创建**"，然后单击 "**创建**" 创建专用终结点请求。

5. DPS 所有者将在 "DPS 网络" 选项卡上的**专用终结点连接**列表中看到专用终结点请求。在该页上，所有者可以**批准**或**拒绝**专用终结点请求，如下所示。

    !["DPS 属性" 选项卡](./media/virtual-network-support/approve-dps-private-endpoint.png)


## <a name="pricing-private-endpoints"></a>定价专用终结点

有关定价详细信息，请参阅 [Azure 专用链接定价](https://azure.microsoft.com/pricing/details/private-link)。



## <a name="next-steps"></a>后续步骤

使用以下链接了解有关 DPS 安全功能的详细信息：

* [安全性](concepts-security.md)
* [TLS 1.2 支持](tls-support.md)
