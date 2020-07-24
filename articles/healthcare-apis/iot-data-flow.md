---
title: 概念：用于 FHIR 的 Azure API 的 IoT Connector （预览版）功能中的数据流
description: 了解 IoT 连接器的数据流。 IoT 连接器引入、规范化、分组、转换，并将 IoMT 数据保存到 Azure API for FHIR。
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: punagpal
ms.openlocfilehash: c2d150fcd35bc51478a1d7f4a0407abce1446c06
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096532"
---
# <a name="iot-connector-preview-data-flow"></a>IoT 连接器（预览版）数据流

本文提供 IoT 连接器中数据流的概述。 你将了解 IoT 连接器内将设备数据转换为基于 FHIR 的[观察](https://www.hl7.org/fhir/observation.html)资源的不同数据处理阶段。

![IoT 连接器数据流](media/concepts-iot-data-flow/iot-connector-data-flow.png)

上图显示 IoT 连接器内的不同数据流阶段。 

## <a name="ingest"></a>引入
引入是将设备数据接收到 IoT 连接器的第一阶段。 设备数据的引入终结点托管在[Azure 事件中心](https://docs.microsoft.com/azure/event-hubs/)。 Azure 事件中心平台支持高规模和吞吐量，每秒可以接收和处理数百万条消息。 它还使 IoT 连接器能够以异步方式使用消息，从而无需在处理设备数据的同时等待设备等待。

> [!NOTE]
> 对于设备数据，此时仅支持 JSON 格式。

## <a name="normalize"></a>规范化
规范化是下一个阶段，在此阶段中，从上述 Azure 事件中心检索设备数据，并使用设备映射模板进行处理。 此映射过程导致将设备数据转换为规范化的架构。 

规范化过程不仅简化了以后的数据处理，而且还提供了将一个输入消息投影到多个规范化消息的功能。 例如，设备可能会在单个消息中发送多个针对身体温度、脉冲速率、血压和 respiration 率的重要迹象。 此输入消息将创建四个单独的 FHIR 资源。 每个资源都表示不同的重要符号，并将输入消息投影到四个不同的规范化消息中。

## <a name="group"></a>Group
组是下一阶段中可用的规范化消息使用三个不同的参数分组：设备标识、度量类型和时间段。

设备标识和测量类型分组允许使用[SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData)度量类型。 此类型提供了一种简单的方法来表示来自 FHIR 中的设备的基于时间的度量值。 时间段控制 IoT 连接器生成的观察资源写入到 FHIR 的 Azure API 的滞后时间。

> [!NOTE]
> 时间段值默认为15分钟，并且不能配置为预览。

## <a name="transform"></a>转换
在 "转换" 阶段，按 FHIR 映射模板处理分组规范化的消息。 与模板类型匹配的消息将转换为通过映射指定的基于 FHIR 的观察资源。

此时，还会使用消息中存在的设备标识符从 FHIR 服务器检索[设备](https://www.hl7.org/fhir/device.html)资源及其关联的[患者](https://www.hl7.org/fhir/patient.html)资源。 这些资源将作为对所创建观察资源的引用添加。

> [!NOTE]
> 所有标识查找会在解析后缓存，以减少 FHIR 服务器上的负载。 如果你计划在多个患者上重复使用设备，则建议你创建一个特定于在消息有效负载中的患者和发送虚拟设备标识符的虚拟设备资源。 虚拟设备可以作为父项链接到实际设备资源。

如果 FHIR 服务器中不存在给定设备标识符的设备资源，则结果取决于 `Resolution Type` 创建时设置的值。 如果设置为 `Lookup` ，则忽略特定消息，管道将继续处理其他传入的消息。 如果设置为 `Create` ，IoT 连接器将在 FHIR 服务器上创建一个 "Bare 设备" 和 "患者资源"。  

## <a name="persist"></a>保留
在转换阶段生成了 "观察" FHIR 资源后，资源将保存到 Azure API for FHIR。 如果 FHIR 资源是新资源，则会在服务器上创建该资源。 如果 FHIR 资源已存在，则会进行更新。

## <a name="next-steps"></a>后续步骤

单击下面的 "下一步" 以了解如何创建设备和 FHIR 映射模板。

>[!div class="nextstepaction"]
>[IoT 连接器映射模板](iot-mapping-templates.md)


FHIR 是 HL7 的注册商标，经 HL7 许可使用。
