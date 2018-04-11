---
title: Azure IoT 中心缩放 | Microsoft Docs
description: 如何缩放 IoT 中心来支持预期的消息吞吐量和所需的功能。 概括介绍了每层支持的吞吐量和分片选项。
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.assetid: e7bd4968-db46-46cf-865d-9c944f683832
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2018
ms.author: kgremban
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f3237ee41f8596ed3ce508857adf7dc29cee1ada
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="choose-the-right-iot-hub-tier-for-your-solution"></a>选择适用于解决方案的 IoT 中心层

每个 IoT 解决方案都是不同的，因此 Azure IoT 中心会根据定价和缩放提供多个选项。 本文旨在介绍如何评估 IoT 中心需求。 有关 IoT 中心层的定价信息，请参阅 [IoT 中心定价](https://azure.microsoft.com/pricing/details/iot-hub)。 

若要确定哪个 IoT 中心层适用于解决方案，请向自己提问两个问题：

**我计划使用哪些功能？**
Azure IoT 中心提供两个层，即基本层和标准层，这两个层在所支持的功能数目上有所不同。 如果 IoT 解决方案需要先从设备收集数据，然后再集中进行分析，则可能适合使用基本层。 如果需要使用更高级的配置来远程控制 IoT 设备，或者需要将某些工作负荷分发到设备本身，则应考虑标准层。 若要详细了解每一层中包括哪些功能，请转到[基本层和标准层](#basic-and-standard-tiers)。

**我计划每天移动多少数据？**
每个 IoT 中心层提供三种大小，具体取决于每天能够处理的数据吞吐量。 这些大小以数字的形式标为 1、2、3。 例如，1 级 IoT 中心的每个单元每天可以处理 40 万条消息，而 3 级单元则可以处理 3 亿条消息。 有关数据准则的更多详细信息，请转到[消息吞吐量](#message-throughput)。

## <a name="basic-and-standard-tiers"></a>基本层和标准层

IoT 中心的标准层启用了所有功能，是任何需要使用双向通信功能的 IoT 解决方案所必需的。 基本层启用了部分功能，适用于只需单向通信（从设备到云）的 IoT 解决方案。 这两个层提供相同的安全性和身份验证功能。

创建 IoT 中心以后，即可从基本层升级到标准层，不需中断现有的操作。 有关详细信息，请参阅[如何升级 IoT 中心](iot-hub-upgrade.md)。

| 功能 | 基本层 | 标准层 |
| ---------- | ---------- | ------------- |
| [设备到云的遥测](iot-hub-devguide-messaging.md) | 是 | 是 |
| [每设备标识](iot-hub-devguide-identity-registry.md) | 是 | 是 |
| [消息路由](iot-hub-devguide-messages-read-custom.md)和[事件网格集成](iot-hub-event-grid.md) | 是 | 是 |
| [HTTP、AMQP 和 MQTT 协议](iot-hub-devguide-protocols.md) | 是 | 是 |
| [设备预配服务](../iot-dps/about-iot-dps.md) | 是 | 是 |
| [监视和诊断](iot-hub-monitor-resource-health.md) | 是 | 是 |
| [云到设备的消息传递](iot-hub-devguide-c2d-guidance.md) |   | 是 |
| [设备孪生](iot-hub-devguide-device-twins.md)和[设备管理](iot-hub-device-management-overview.md) |   | 是 |
| [Azure IoT Edge](../iot-edge/how-iot-edge-works.md) |   | 是 |

IoT 中心还提供一个免费层，用于测试和评估。 它具有标准层的所有功能，但消息传递有限额。 不能从免费层升级到基本层或标准层。 

### <a name="iot-hub-rest-apis"></a>IoT 中心 REST API

IoT 中心基本层和标准层所支持的功能存在差异，也就是说，某些 API 调用在基本层中心不适用。 下表显示了哪些 API 可用： 

| API | 基本层 | 标准层 |
| --- | ---------- | ------------- |
| [删除设备](https://docs.microsoft.com/rest/api/iothub/deviceapi/deletedevice) | 是 | 是 |
| [获取设备](https://docs.microsoft.com/rest/api/iothub/deviceapi/getdevice) | 是 | 是 |
| [获取注册表统计信息](https://docs.microsoft.com/rest/api/iothub/deviceapi/getregistrystatistics) | 是 | 是 |
| [获取服务统计信息](https://docs.microsoft.com/rest/api/iothub/deviceapi/getservicestatistics) | 是 | 是 |
| [放置设备](https://docs.microsoft.com/rest/api/iothub/deviceapi/putdevice) | 是 | 是 |
| [查询设备](https://docs.microsoft.com/rest/api/iothub/deviceapi/querydevices) | 是 | 是 |
| [创建文件上传 SAS URI](https://docs.microsoft.com/rest/api/iothub/httpruntime/createfileuploadsasuri) | 是 | 是 |
| [接收发往设备的通知](https://docs.microsoft.com/rest/api/iothub/httpruntime/receivedeviceboundnotification) | 是 | 是 |
| [发送设备事件](https://docs.microsoft.com/rest/api/iothub/httpruntime/senddeviceevent) | 是 | 是 |
| [更新文件上传状态](https://docs.microsoft.com/rest/api/iothub/httpruntime/updatefileuploadstatus) | 是 | 是 |
| [批量设备操作](https://docs.microsoft.com/rest/api/iothub/deviceapi/bulkdeviceoperation) | 是的，IoT Edge 功能除外 | 是 | 
| [清除命令队列](https://docs.microsoft.com/rest/api/iothub/deviceapi/purgecommandqueue) |   | 是 |
| [获取设备孪生](https://docs.microsoft.com/rest/api/iothub/devicetwinapi/getdevicetwin) |   | 是 |
| [调用设备方法](https://docs.microsoft.com/rest/api/iothub/devicetwinapi/invokedevicemethod) |   | 是 |
| [更新设备孪生](https://docs.microsoft.com/rest/api/iothub/devicetwinapi/updatedevicetwin) |   | 是 | 
| [放弃发往设备的通知](https://docs.microsoft.com/rest/api/iothub/httpruntime/abandondeviceboundnotification) |   | 是 |
| [完成发往设备的通知](https://docs.microsoft.com/rest/api/iothub/httpruntime/completedeviceboundnotification) |   | 是 |
| [取消作业](https://docs.microsoft.com/rest/api/iothub/jobapi/canceljob) |   | 是 |
| [创建作业](https://docs.microsoft.com/rest/api/iothub/jobapi/createjob) |   | 是 |
| [获取作业](https://docs.microsoft.com/rest/api/iothub/jobapi/getjob) |   | 是 |
| [查询作业](https://docs.microsoft.com/rest/api/iothub/jobapi/queryjobs) |   | 是 |

## <a name="message-throughput"></a>消息吞吐量

若要基于每个单位评估流量，最佳方式是调整 IoT 中心解决方案的大小。 尤其要考虑以下类别的操作所需的高峰吞吐量：

* 设备到云的消息
* 云到设备的消息
* 标识注册表操作

流量按单元而不是中心来度量。 1 级或 2 级 IoT 中心实例最多可以有 200 个与之关联的单元。 3 级 IoT 中心实例最多可以有 10 个单元。 创建 IoT 中心以后，即可更改单元数，或者在特定层的不同大小（1、2、3）之间切换，而不会中断现有的操作。 有关详细信息，请参阅[如何升级 IoT 中心](iot-hub-upgrade.md)。

例如，就每个层的流量功能来说，设备到云的消息遵循以下持续吞吐量指导原则：

| 层 | 持续吞吐量 | 持续发送速率 |
| --- | --- | --- |
| B1、S1 |每个单元最多 1111 KB/分钟<br/>（1.5 GB/天/单元） |每个单元平均 278 条消息/分钟<br/>（400000 条消息/天/单元） |
| B2、S2 |每个单元最多 16 MB/分钟<br/>（22.8 GB/天/单元） |每个单元平均 4,167 条消息/分钟<br/>（600 万条消息/天/单元） |
| B3、S3 |每个单元最多 814 MB/分钟<br/>（1144.4 GB/天/单元） |每个单元平均 208,333 条消息/分钟<br/>（3 亿条消息/天/单元） |

除了此吞吐量信息，另请参阅 [IoT 中心配额和限制][IoT Hub quotas and throttles]，并相应地设计解决方案。

### <a name="identity-registry-operation-throughput"></a>标识注册表操作吞吐量
由于大多数 IoT 中心标识注册表操作都与设备预配相关，因此不认为这些操作是运行时操作。

有关具体的突发性能数字，请参阅 [IoT 中心配额和限制][IoT Hub quotas and throttles]。

## <a name="sharding"></a>分片
尽管单个 IoT 中心可以扩展到数百万个设备，但有时解决方案所需的具体性能特征无法由单个 IoT 中心提供保证。 在这种情况下，可以跨多个 IoT 中心将设备分区。 多个 IoT 中心可以缓解流量喷发，并获得所需的吞吐量或操作速率。

## <a name="next-steps"></a>后续步骤

* 若要详细了解 IoT 中心功能和性能，请参阅 [IoT 中心定价][link-pricing]或 [IoT 中心配额和限制][IoT Hub quotas and throttles]。
* 若要更改 IoT 中心层，请执行[升级 IoT 中心](iot-hub-upgrade.md)中的步骤。

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[IoT Hub quotas and throttles]: iot-hub-devguide-quotas-throttling.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
