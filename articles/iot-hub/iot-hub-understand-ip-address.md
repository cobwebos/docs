---
title: 了解 IoT 中心的 IP 地址 | Microsoft Docs
description: 了解如何查询 IoT 中心 IP 地址及其属性。 在某些情况下（如灾难恢复或区域故障转移）可以更改 IoT 中心的 IP 地址。
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 1bfae8c7afbfdc6e73dd8bb17b94e6543361e9ce
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848222"
---
# <a name="iot-hub-ip-addresses"></a>IoT 中心 IP 地址

IoT 中心公共终结点的 IP 地址前缀会定期发布在 _AzureIoTHub_ [服务标记](../virtual-network/service-tags-overview.md)下。

> [!NOTE]
> 对于在本地网络中部署的设备，Azure IoT 中心支持 VNET 连接与专用终结点的集成。 有关详细信息，请参阅 [IoT 中心 VNet 支持](./virtual-network-support.md)。


你可以使用这些 IP 地址前缀来控制 IoT 中心与你的设备或网络资产之间的连接，以实现各种网络隔离目标：

| 目标 | 适用方案 | 方法 |
|------|-----------|----------|
| 确保设备和服务仅与 IoT 中心终结点通信 | [设备到云](./iot-hub-devguide-messaging.md)、[云到设备](./iot-hub-devguide-messages-c2d.md)消息传送、[直接方法](./iot-hub-devguide-direct-methods.md)、[设备和模块孪生](./iot-hub-devguide-device-twins.md)以及[设备流](./iot-hub-device-streams-overview.md) | 使用 AzureIoTHub 和 EventHub 服务标记发现 IoT 中心和事件中心 IP 地址前缀，并在设备和服务的防火墙设置上相应地为这些 IP 地址前缀配置允许规则；将流量放到你不希望设备或服务与之通信的其他目标 IP 地址。 |
| 确保 IoT 中心设备终结点仅接收来自你的设备和网络资产的连接 | [设备到云](./iot-hub-devguide-messaging.md)、[云到设备](./iot-hub-devguide-messages-c2d.md)消息传送、[直接方法](./iot-hub-devguide-direct-methods.md)、[设备和模块孪生](./iot-hub-devguide-device-twins.md)以及[设备流](./iot-hub-device-streams-overview.md) | 使用 IoT 中心 [IP 筛选功能](iot-hub-ip-filtering.md)以允许来自设备和网络资产 IP 地址的连接（请参阅[限制](#limitations-and-workarounds)部分）。 | 
| 确保路由的自定义终结点资源（存储帐户、服务总线和事件中心）只能通过你的网络资产访问 | [消息路由](./iot-hub-devguide-messages-d2c.md) | 遵循资源的限制连接指南（例如，通过[防火墙规则](../storage/common/storage-network-security.md)、[专用链接](../private-link/private-endpoint-overview.md)或[服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)）；使用 _AzureIoTHub_ 服务标记发现 IoT 中心 IP 地址前缀，并在资源的防火墙配置中为这些 IP 前缀添加允许规则（请参阅[限制](#limitations-and-workarounds)部分）。 |



## <a name="best-practices"></a>最佳做法

* 在设备的防火墙配置中添加允许规则时，最好提供[适用协议使用的特定端口](./iot-hub-devguide-protocols.md#port-numbers)。

* IoT 中心的 IP 地址前缀随时可能更改。 这些更改通过服务标记定期发布，然后生效。 因此，请务必开发可定期检索并使用最新服务标记的进程。 可以通过[服务标记发现 API](../virtual-network/service-tags-overview.md#service-tags-on-premises) 自动执行此进程。 请注意，服务标记发现 API 仍处于预览阶段，在某些情况下，可能不会生成标记和 IP 地址的完整列表。 在发现 API 正式发布之前，请考虑使用[可下载 JSON 格式的服务标记](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)。 

* 使用 *AzureIoTHub.[region name]* 标记来标识特定区域内 IoT 中心终结点使用的 IP 前缀。 考虑到数据中心灾难恢复或[区域故障转移](iot-hub-ha-dr.md)，请确保还启用了到 IoT 中心异地对区域的 IP 前缀的连接。

* 在 IoT 中心设置防火墙规则可能会阻止针对 IoT 中心运行 Azure CLI 和 PowerShell 命令所需的连接。 为避免出现这种情况，可以为客户端的 IP 地址前缀添加允许规则，以重新允许 CLI 或 PowerShell 客户端与 IoT 中心通信。  


## <a name="limitations-and-workarounds"></a>限制和解决方法

* IoT 中心 IP 筛选功能的限制为 10 个规则。 可通过 Azure 客户支持请求提高此数量。 

* 配置的 [IP 筛选规则](iot-hub-ip-filtering.md)仅适用于 IoT 中心 IP 终结点，而不适用于 IoT 中心的内置事件中心终结点。 如果你还需要在存储消息的事件中心上应用 IP 筛选，你可以使用自己的事件中心资源，以便直接配置所需的 IP 筛选规则。 为此，你需要预配自己的事件中心资源，并设置[消息路由](./iot-hub-devguide-messages-d2c.md)，以将消息发送到该资源，而不是 IoT 中心的内置事件中心。 最后，如上表中所述，若要启用消息路由功能，还需要允许从 IoT 中心 IP 地址前缀连接到预配的事件中心资源。

* 当路由到存储帐户时，仅当存储帐户与 IoT 中心位于不同区域时，才允许来自 IoT 中心 IP 地址前缀的流量。

## <a name="support-for-ipv6"></a>支持 IPv6 

IoT 中心目前不支持 IPv6。
