---
title: 了解 IoT 中心的 IP 地址 | Microsoft Docs
description: 了解如何查询 IoT 中心的 IP 地址及其属性。 IoT 中心的 IP 地址在某些情况下（例如灾难恢复或区域故障转移）可能会更改。
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: c609f2a3843481442e97061739a806de60a680b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367561"
---
# <a name="iot-hub-ip-addresses"></a>IoT 中心 IP 地址

IoT 中心公共终结点的 IP 地址前缀在 _AzureIoTHub_ [服务标记](../virtual-network/service-tags-overview.md)下定期发布。

> [!NOTE]
> 对于部署在本地网络内的设备，Azure IoT 中心支持 VNET 连接集成与专用终结点。 有关详细信息，请参阅[VNET 的 IoT 中心支持](./virtual-network-support.md#ingress-connectivity-to-iot-hub-using-private-endpoints)。


可以使用这些 IP 地址前缀来控制 IoT 中心与设备或网络资产之间的连接，以实现各种网络隔离目标：

| 目标 | 适用的方案 | 方法 |
|------|-----------|----------|
| 确保设备和服务只与 IoT 中心终结点通信 | [设备到云](./iot-hub-devguide-messaging.md)和[云到设备](./iot-hub-devguide-messages-c2d.md)的消息传送、[直接方法](./iot-hub-devguide-direct-methods.md)、[设备和模块孪生](./iot-hub-devguide-device-twins.md)以及[设备流](./iot-hub-device-streams-overview.md) | 使用 _AzureIoTHub_ 和 _EventHub_ 服务标记发现 IoT 中心和事件中心 IP 地址前缀，并相应地在设备和服务的防火墙设置中为这些 IP 地址前缀配置“允许”规则；丢弃发往你不希望设备或服务与其通信的其他目标 IP 地址的流量。 |
| 确保 IoT 中心设备终结点仅接收来自你的设备和网络资产的连接 | [设备到云](./iot-hub-devguide-messaging.md)和[云到设备](./iot-hub-devguide-messages-c2d.md)的消息传送、[直接方法](./iot-hub-devguide-direct-methods.md)、[设备和模块孪生](./iot-hub-devguide-device-twins.md)以及[设备流](./iot-hub-device-streams-overview.md) | 使用 IoT 中心 [IP 筛选功能](iot-hub-ip-filtering.md)允许来自你的设备和网络资产 IP 地址的连接（参阅[限制](#limitations-and-workarounds)部分）。 | 
| 确保只能从你的网络资产访问路由的自定义终结点资源（存储帐户、服务总线和事件中心） | [消息路由](./iot-hub-devguide-messages-d2c.md) | 遵循有关限制连接的资源指导（例如，通过[防火墙规则](../storage/common/storage-network-security.md)、[专用链接](../private-link/private-endpoint-overview.md)或[服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)进行限制）；使用 _AzureIoTHub_ 服务标记发现 IoT 中心 IP 地址前缀，并在资源的防火墙配置中为这些 IP 前缀添加“允许”规则（参阅[限制](#limitations-and-workarounds)部分）。 |



## <a name="best-practices"></a>最佳做法

* 在设备的防火墙配置中添加“允许”规则时，最好是提供[适用协议使用的特定端口](./iot-hub-devguide-protocols.md#port-numbers)。

* IoT 中心的 IP 地址前缀可能会更改。 这些更改在生效之前通过服务标记定期发布。 因此，必须制定流程来定期检索并使用最新的服务标记。 该流程可以通过[服务标记发现 API](../virtual-network/service-tags-overview.md#service-tags-on-premises) 自动完成。 请注意，服务标记发现 API 目前仍为预览版，在某些情况下，它可能不会生成标记和 IP 地址的完整列表。 在发现 API 推出正式版之前，请考虑使用[可下载 JSON 格式的服务标记](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)。 

* 请使用 *AzureIoTHub.[区域名称]* 标记来确定特定区域中的 IoT 中心终结点使用的 IP 前缀。 如果考虑到数据中心灾难恢复或[区域性故障转移](iot-hub-ha-dr.md)，请确保还允许连接到 IoT 中心的异地配对区域的 IP 前缀。

* 在 IoT 中心设置防火墙规则可能会阻止所需的连接，导致无法对 IoT 中心运行 Azure CLI 和 PowerShell 命令。 为避免出现这种情况，可为客户端的 IP 地址前缀添加“允许”规则，再次使得 CLI 或 PowerShell 客户端能够与 IoT 中心通信。  


## <a name="limitations-and-workarounds"></a>限制和解决方法

* IoT 中心 IP 筛选功能限制为 10 个规则。 可以请求 Azure 客户支持人员提高此限制。 

* 配置的 [IP 筛选规则](iot-hub-ip-filtering.md)仅在 IoT 中心 IP 终结点上应用，而不会在 IoT 中心的内置事件中心终结点上应用。 如果还需要在存储消息的事件中心上应用 IP 筛选，可以提供自己的事件中心资源，在其中可以直接配置所需的 IP 筛选规则。 为此，需要预配自己的事件中心资源，并将[消息路由](./iot-hub-devguide-messages-d2c.md)设置为向该资源发送消息，而不是向 IoT 中心的内置事件中心发送消息。 最后，如上表中所述，若要启用消息路由功能，还需要允许从 IoT 中心的 IP 地址前缀连接到预配的事件中心资源。

* 路由到存储帐户时，仅当存储帐户与 IoT 中心位于不同区域的情况下，才能允许来自 IoT 中心 IP 地址前缀的流量。

## <a name="support-for-ipv6"></a>对 IPv6 的支持 

目前 IoT 中心不支持 IPv6。
