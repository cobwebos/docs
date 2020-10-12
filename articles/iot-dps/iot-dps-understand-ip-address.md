---
title: 了解 IoT 设备预配服务 (DPS) 实例的 IP 地址 | Microsoft Docs
description: 了解如何查询 IoT 设备预配服务 (DPS) 地址及其属性。 DPS 实例的 IP 地址在某些情况下（例如灾难恢复或区域故障转移）可能会更改。
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: f6afd5c4cc5aa0215f943979ae91389b39d449f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "79284923"
---
# <a name="iot-hub-dps-ip-addresses"></a>IoT 中心 DPS IP 地址

IoT 中心设备预配服务 (DPS) 的公共终结点的 IP 地址前缀定期发布在 _AzureIoTHub_ [服务标记](../virtual-network/service-tags-overview.md)下。 可以使用这些 IP 地址前缀来控制 IoT DPS 实例与设备或网络资产之间的连接，以实现各种网络隔离目标：

| 目标 | 方法 |
|------|----------|
| 确保设备和服务只与 IoT 中心 DPS 终结点通信 | 使用 _AzureIoTHub_ 服务标记发现 IoT 中心 DPS 实例。 相应地在设备和服务的防火墙设置中为这些 IP 地址前缀配置“允许”规则。 配置规则，以便丢弃发往你不希望设备或服务与其通信的其他目标 IP 地址的流量。 |
| 确保 IoT 中心 DPS 终结点仅接收来自你的设备和网络资产的连接 | 使用 IoT DPS [IP 筛选器功能](iot-dps-ip-filtering.md)，为设备和 DPS 服务 API 创建筛选器规则。 可以使用这些筛选器规则，只允许来自你的设备和网络资产 IP 地址的连接（参阅[限制](#limitations-and-workarounds)部分）。 | 




## <a name="best-practices"></a>最佳做法

* 在设备的防火墙配置中添加“允许”规则时，最好是提供[适用协议使用的特定端口](../iot-hub/iot-hub-devguide-protocols.md#port-numbers)。

* IoT DPS 实例的 IP 地址前缀可能会更改。 这些更改在生效之前通过服务标记定期发布。 因此，必须制定流程来定期检索并使用最新的服务标记。 该流程可以通过[服务标记发现 API](../virtual-network/service-tags-overview.md#service-tags-on-premises) 自动完成。 服务标记发现 API 目前仍为预览版，在某些情况下，它可能不会生成标记和 IP 地址的完整列表。 在发现 API 推出正式版之前，请考虑使用[可下载 JSON 格式的服务标记](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)。 

* 请使用 *AzureIoTHub.[区域名称]* 标记来确定特定区域中的 DPS 终结点使用的 IP 前缀。 如果考虑到数据中心灾难恢复或[区域性故障转移](../iot-hub/iot-hub-ha-dr.md)，请确保还允许连接到 DPS 实例的异地配对区域的 IP 前缀。

* 为 DPS 实例设置防火墙规则可能会阻止所需的连接，导致无法对它运行 Azure CLI 和 PowerShell 命令。 为避免这些连接性问题，可为客户端的 IP 地址前缀添加“允许”规则，再次使得 CLI 或 PowerShell 客户端能够与 DPS 实例通信。  


## <a name="limitations-and-workarounds"></a>限制和解决方法

* DPS 实例 IP 筛选功能限制为 100 个规则。 可以请求 Azure 客户支持人员提高此限制。 

* 配置的 [IP 筛选规则](iot-dps-ip-filtering.md)仅在 DPS 终结点上应用，而不会在关联的 IoT 中心终结点上应用。 必须单独配置用于关联的 IoT 中心的 IP 筛选。 有关详细信息，请参阅 [IoT 中心 IP 筛选规则](../iot-hub/iot-hub-ip-filtering.md)。

## <a name="support-for-ipv6"></a>对 IPv6 的支持 

目前 IoT 中心或 DPS 不支持 IPv6。

## <a name="next-steps"></a>后续步骤

若要详细了解通过 DPS 进行的 IP 地址配置，请参阅：

* [配置 IP 筛选](iot-dps-ip-filtering.md)
