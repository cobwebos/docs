---
title: 了解 IoT 中心的 IP 地址 |Microsoft Docs
description: 了解如何查询 IoT 中心 IP 地址及其属性。 IoT 中心的 IP 地址可以在某些情况下更改，如灾难恢复或区域故障转移。
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: c609f2a3843481442e97061739a806de60a680b5
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "79367561"
---
# <a name="iot-hub-ip-addresses"></a>IoT 中心 IP 地址

IoT 中心公共终结点的 IP 地址前缀会定期发布在_AzureIoTHub_ [服务标记](../virtual-network/service-tags-overview.md)下。

> [!NOTE]
> 对于在本地网络中部署的设备，Azure IoT 中心支持 VNET 连接与专用终结点的集成。 有关详细信息，请参阅[IoT 中心对 VNET 的支持](./virtual-network-support.md#ingress-connectivity-to-iot-hub-using-private-endpoints)。


你可以使用这些 IP 地址前缀来控制 IoT 中心与你的设备或网络资产之间的连接，以实现各种网络隔离目标：

| 目标 | 适用方案 | 方法 |
|------|-----------|----------|
| 确保设备和服务仅与 IoT 中心终结点通信 | [设备到云](./iot-hub-devguide-messaging.md)和[云到设备](./iot-hub-devguide-messages-c2d.md)的消息传递、[直接方法](./iot-hub-devguide-direct-methods.md)、[设备和模块孪生](./iot-hub-devguide-device-twins.md)和[设备流](./iot-hub-device-streams-overview.md) | 使用_AzureIoTHub_和_EventHub_服务标记发现 IoT 中心和事件中心 IP 地址前缀，并相应地为这些 IP 地址前缀配置 "允许设备上的规则" 和 "服务" 防火墙设置。将流量丢弃到您不希望设备或服务与之通信的其他目标 IP 地址。 |
| 确保 IoT 中心设备终结点仅接收来自你的设备和网络资产的连接 | [设备到云](./iot-hub-devguide-messaging.md)和[云到设备](./iot-hub-devguide-messages-c2d.md)的消息传递、[直接方法](./iot-hub-devguide-direct-methods.md)、[设备和模块孪生](./iot-hub-devguide-device-twins.md)和[设备流](./iot-hub-device-streams-overview.md) | 使用 IoT 中心[ip 筛选器功能](iot-hub-ip-filtering.md)允许来自设备和网络资产 IP 地址的连接（请参阅[限制](#limitations-and-workarounds)部分）。 | 
| 确保路由的自定义终结点资源（存储帐户、服务总线和事件中心）只能从网络资产中访问 | [消息路由](./iot-hub-devguide-messages-d2c.md) | 遵循资源有关限制连接性的指导（例如通过[防火墙规则](../storage/common/storage-network-security.md)、[专用链接](../private-link/private-endpoint-overview.md)或[服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)）;使用_AzureIoTHub_服务标记发现 IOT 中心 IP 地址前缀，并在资源的防火墙配置中为这些 ip 前缀添加允许规则（请参阅[限制](#limitations-and-workarounds)部分）。 |



## <a name="best-practices"></a>最佳做法

* 在设备的防火墙配置中添加允许规则时，最好提供[适用协议使用的特定端口](./iot-hub-devguide-protocols.md#port-numbers)。

* IoT 中心的 IP 地址前缀可能会发生更改。 这些更改会在生效之前通过服务标记定期发布。 因此，请务必开发定期检索和使用最新服务标记的过程。 可以通过[服务标记发现 API](../virtual-network/service-tags-overview.md#service-tags-on-premises)自动执行此过程。 请注意，服务标记发现 API 仍处于预览阶段，在某些情况下，可能不会生成标记和 IP 地址的完整列表。 在发现 API 正式发布之前，请考虑[以可下载的 JSON 格式使用服务标记](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)。 

* 使用*AzureIoTHub. [区域名称]* 标记，用于标识特定区域的 IoT 中心终结点使用的 IP 前缀。 若要考虑数据中心灾难恢复，或[区域故障转移](iot-hub-ha-dr.md)，请确保也启用与 IoT 中心的地理对区域的 IP 前缀的连接。

* 在 IoT 中心设置防火墙规则可能会阻止对 IoT 中心运行 Azure CLI 和 PowerShell 命令所需的连接。 为避免出现这种情况，可以为客户端的 IP 地址前缀添加允许规则，以重新启用 CLI 或 PowerShell 客户端以与 IoT 中心通信。  


## <a name="limitations-and-workarounds"></a>限制和解决方法

* IoT 中心 IP 筛选器功能的最大限制为10个。 此限制，可通过 Azure 客户支持通过请求引发。 

* 配置的[IP 筛选规则](iot-hub-ip-filtering.md)仅适用于 IOT 中心 IP 终结点，而不适用于 iot 中心的内置事件中心终结点。 如果你还需要在存储消息的事件中心上应用 IP 筛选，你可以将自己的事件中心资源置于你自己的事件中心资源，以便直接配置所需的 IP 筛选规则。 为此，需要预配自己的事件中心资源，并设置[消息路由](./iot-hub-devguide-messages-d2c.md)，以便将消息发送到该资源，而不是 IoT 中心的内置事件中心。 最后，如上表中所述，若要启用消息路由功能，还需要允许从 IoT 中心的 IP 地址前缀连接到已设置的事件中心资源。

* 当路由到存储帐户时，仅当存储帐户与 IoT 中心位于不同的区域时，才允许来自 IoT 中心的 IP 地址前缀的流量。

## <a name="support-for-ipv6"></a>对 IPv6 的支持 

当前 IoT 中心不支持 IPv6。
