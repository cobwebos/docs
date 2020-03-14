---
title: 了解 IoT 设备预配服务（DPS）实例的 IP 地址 |Microsoft Docs
description: 了解如何查询 IoT 设备预配服务（DPS）地址及其属性。 在某些情况下，你的 DPS 实例的 IP 地址可能会发生变化，例如灾难恢复或区域故障转移。
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: f6afd5c4cc5aa0215f943979ae91389b39d449f6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79284923"
---
# <a name="iot-hub-dps-ip-addresses"></a>IoT 中心 DPS IP 地址

IoT 中心设备预配服务（DPS）的公共终结点的 IP 地址前缀定期在_AzureIoTHub_ [服务标记](../virtual-network/service-tags-overview.md)下发布。 你可以使用这些 IP 地址前缀来控制 IoT DPS 实例与设备或网络资产之间的连接，以实现各种网络隔离目标：

| 目标 | 方法 |
|------|----------|
| 确保设备和服务仅与 IoT 中心 DPS 终结点通信 | 使用_AzureIoTHub_服务标记发现 IOT 中心 DPS 实例。 相应地为这些 IP 地址前缀配置 "允许设备上的规则" 和 "服务" 防火墙设置。 配置规则以将流量丢弃到你不希望设备或服务与之通信的其他目标 IP 地址。 |
| 确保 IoT 中心 DPS 终结点仅接收来自你的设备和网络资产的连接 | 使用 IoT DPS [IP 筛选器功能](iot-dps-ip-filtering.md)为设备和 DPS 服务 api 创建筛选规则。 这些筛选规则只能用于允许来自你的设备和网络资产 IP 地址的连接（请参阅[限制](#limitations-and-workarounds)部分）。 | 




## <a name="best-practices"></a>最佳做法

* 在设备的防火墙配置中添加允许规则时，最好提供[适用协议使用的特定端口](../iot-hub/iot-hub-devguide-protocols.md#port-numbers)。

* IoT DPS 实例的 IP 地址前缀可能会发生更改。 这些更改会在生效之前通过服务标记定期发布。 因此，请务必开发定期检索和使用最新服务标记的过程。 可以通过[服务标记发现 API](../virtual-network/service-tags-overview.md#service-tags-on-premises)自动执行此过程。 服务标记发现 API 仍处于预览阶段，在某些情况下，可能不会生成标记和 IP 地址的完整列表。 在发现 API 正式发布之前，请考虑[以可下载的 JSON 格式使用服务标记](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)。 

* 使用*AzureIoTHub. [区域名称]* 标记，用于标识特定区域中 DPS 终结点使用的 IP 前缀。 若要考虑数据中心灾难恢复或[区域故障转移](../iot-hub/iot-hub-ha-dr.md)，请确保还启用了与 DPS 实例的地理对区域的 IP 前缀的连接。

* 为 DPS 实例设置防火墙规则可能会阻止对其运行 Azure CLI 和 PowerShell 命令所需的连接。 若要避免这些连接问题，可以为客户端的 IP 地址前缀添加允许规则，以重新启用 CLI 或 PowerShell 客户端以与 DPS 实例通信。  


## <a name="limitations-and-workarounds"></a>限制和解决方法

* DPS IP 筛选器功能的规则限制为100。 此限制，可通过 Azure 客户支持通过请求引发。 

* 配置的[IP 筛选规则](iot-dps-ip-filtering.md)仅适用于你的 DPS 终结点，而不适用于链接的 IoT 中心终结点。 必须单独配置用于链接的 IoT 中心的 IP 筛选。 有关详细信息，请参阅[IoT 中心 IP 筛选规则](../iot-hub/iot-hub-ip-filtering.md)。

## <a name="support-for-ipv6"></a>对 IPv6 的支持 

当前 IoT 中心或 DPS 不支持 IPv6。

## <a name="next-steps"></a>后续步骤

若要详细了解具有 DPS 的 IP 地址配置，请参阅：

* [配置 IP 筛选](iot-dps-ip-filtering.md)
