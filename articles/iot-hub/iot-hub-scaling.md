---
title: "Azure IoT 中心缩放 | Microsoft Docs"
description: "介绍如何缩放 Azure IoT 中心。"
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: e7bd4968-db46-46cf-865d-9c944f683832
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2016
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 992c271fe3426f4a1e59f4e546b0f7f4e0d59e59


---
# <a name="scaling-iot-hub"></a>缩放 IoT 中心
Azure IoT 中心可支持多达一百万台设备同时连接。 有关详细信息，请参阅 [IoT 中心定价][lnk-pricing]。 每个 IoT 中心单元允许一定数量的每日消息。

若要正确地缩放解决方案，请考虑你的 IoT 中心特定使用情况。 尤其要考虑以下类别的操作所需的高峰吞吐量：

* 设备到云的消息
* 云到设备的消息
* 标识注册表操作

除了此吞吐量信息，另请参阅 [IoT 中心配额和限制][IoT 中心配额和限制]并相应地设计解决方案。

## <a name="device-to-cloud-and-cloud-to-device-message-throughput"></a>设备到云和云到设备的消息吞吐量
若要基于每个单位评估流量，最佳方式是调整 IoT 中心解决方案的大小。

设备到云的消息遵循以下持续吞吐量指导原则。

| 层 | 持续吞吐量 | 持续发送速率 |
| --- | --- | --- |
| S1 |每个单元最多 1111 KB/分钟<br/>（1.5 GB/天/单元） |每个单元平均 278 条消息/分钟<br/>（400000 条消息/天/单元） |
| S2 |每个单元最多 16 MB/分钟<br/>（22.8 GB/天/单元） |每个单元平均 4167 条消息/分钟<br/>（600 万条消息/天/单元） |
| S3 |每个单元最多 814 MB/分钟<br/>（1144.4 GB/天/单元） |每个单元平均 208,333 条消息/分钟<br/>（3 亿条消息/天/单元） |

## <a name="identity-registry-operation-throughput"></a>标识注册表操作吞吐量
由于大多数 IoT 中心标识注册表操作都与设备预配相关，因此不认为这些操作是运行时操作。

有关特定突发性能数字，请参阅 [IoT 中心配额和限制][IoT 中心配额和限制]。

## <a name="sharding"></a>分片
尽管单个 IoT 中心可以扩展到数百万个设备，但有时解决方案所需的具体性能特征无法由单个 IoT 中心提供保证。 在此情况下，建议将设备分区成多个 IoT 中心。 多个 IoT 中心可以缓解流量喷发，并获得所需的吞吐量或操作速率。

## <a name="next-steps"></a>后续步骤
若要进一步探索 IoT 中心的功能，请参阅：

* [开发人员指南][lnk-devguide]
* [使用 IoT 网关 SDK 模拟设备][lnk-gateway]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[IoT 中心配额和限制]: iot-hub-devguide-quotas-throttling.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md



<!--HONumber=Nov16_HO3-->


