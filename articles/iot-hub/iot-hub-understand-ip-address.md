---
title: 了解 IoT 中心的 IP 地址 |Microsoft Docs
description: 了解如何查询 IoT 中心 IP 地址及其属性。 IoT 中心的 IP 地址可以在某些情况下更改, 如灾难恢复或区域故障转移。
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 9c9d02eeead4ccbed372e9a55d9b551da83caa39
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642288"
---
# <a name="understanding-the-ip-address-of-your-iot-hub"></a>了解 IoT 中心的 IP 地址

IoT 中心的 IP 地址是中心的负载均衡公共终结点, 而不是专用 IP 地址。 该地址由部署它的 Azure 区域的网络地址范围决定。 此 IP 地址如有更改, 恕不另行通知。 数据中心网络更新、数据中心灾难恢复或 IoT 中心的区域故障转移可更改 IoT 中心的 IP 地址。 有关 Azure IoT 中心区域故障转移和可用性的详细信息, 请参阅[IoT 中心高可用性和灾难恢复](iot-hub-ha-dr.md)。

IoT 中心的 IP 地址会在故障转移到另一个区域后发生更改。 可以按照教程[执行 IoT 中心的手动故障转移](tutorial-manual-failover.md)来测试此功能。

## <a name="discover-your-iot-hub-ip-address"></a>发现 IoT 中心 IP 地址

你的 IoT 中心 IP 地址可以通过对 CNAME 使用反向 DNS 查找来发现 ([*IoT 中心名称*]. azure-devices.net)。 可以使用**nslookup**来验证 IoT 中心实例的 IP 地址:

```cmd/sh
nslookup {YourIoTHubName}.azure-devices.net
```

此 IP 地址可能会更改, 恕不另行通知。 在故障转移或灾难恢复方案中, 你必须在次要区域中轮询你的 IoT 中心 IP 地址。

## <a name="outbound-firewall-rules-for-iot-hub"></a>IoT 中心的出站防火墙规则

尝试基于 IoT 中心主机名或域创建防火墙规则和筛选。 如果只能允许到特定地址的出站流量, 请定期轮询 IoT 中心 IP 地址, 并更新防火墙规则。

