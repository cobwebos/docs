---
title: 了解 IoT 中心的 IP 地址 | Microsoft Docs
description: 了解如何查询 IoT 中心的 IP 地址及其属性。 IoT 中心的 IP 地址在某些情况下（例如灾难恢复或区域故障转移）可能会更改。
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 5c77fb30ef60c1ad82d0a87442bc8af186c54321
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383900"
---
# <a name="iot-hub-ip-addresses"></a>IoT 中心 IP 地址

IoT 中心的 IP 地址前缀会定期发布在*AzureIoTHub* [service 标记](../virtual-network/service-tags-overview.md)下。 若要确保正常运行，IoT 设备必须具有出站连接才能处理*AzureIoTHub* service 标记下列出的前缀。 IoT 应用程序服务需要额外建立出站连接，以解决在*EventHub* service 标记下列出的前缀。


## <a name="best-practices"></a>最佳实践

* IoT 中心的 IP 地址前缀可能会发生更改。 这些更改会在生效之前通过服务标记定期发布。 因此，请务必开发定期检索和使用最新服务标记的过程。 可以通过[服务标记发现 API](../virtual-network/service-tags-overview.md#service-tags-in-on-premises)自动执行此过程。
* 使用*AzureIoTHub. [区域名称]* 标记，用于标识特定区域的 IoT 中心终结点使用的 IP 前缀。 若要考虑数据中心灾难恢复，或[区域故障转移](iot-hub-ha-dr.md)，请确保也启用与 IoT 中心的地理对区域的 IP 前缀的连接。


## <a name="support-for-ipv6"></a>对 IPv6 的支持 

目前 IoT 中心不支持 IPv6。