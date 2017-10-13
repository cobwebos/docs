---
title: "已连接好的工厂 Azure IoT 套件解决方案演练 | Microsoft Docs"
description: "介绍了 Azure IoT 预配置解决方案已连接好的工厂及其体系结构。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 31fe13af-0482-47be-b4c8-e98e36625855
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2017
ms.author: dobett
ms.openlocfilehash: 517e908a744734139ed0aeee314a4f3b9eda86cc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="connected-factory-preconfigured-solution-walkthrough"></a>已连接好的工厂预配置解决方案演练

IoT 套件已连接好的工厂[预配置解决方案][lnk-preconfigured-solutions]是端到端工业解决方案的实现，具有以下功能：

* 同时连接到在模拟工厂生产线中运行 OPC UA 服务器的模拟工业设备以及真实的 OPC UA 服务器设备。 有关 OPC UA 的详细信息，请参阅[连接工厂常见问题解答](iot-suite-faq-cf.md)。
* 显示那些设备和生产线的运行 KPI 和 OEE。
* 演示如何使用基于云的应用程序来与 OPC UA 服务器系统进行交互。
* 允许连接自己的 OPC UA 服务器设备。
* 允许浏览和修改 OPC UA 服务器数据。
* 与 Azure Time Series Insights (TSI) 服务集成来提供 OPC UA 服务器中数据的自定义视图。

可以将其用作自己实现的起点，并可以根据特定的业务要求[自定义][lnk-customize]该解决方案。

本文将分步讲解已连接好的工厂解决方案的一些关键要素，以帮助你了解其工作原理。 这一知识有助于：

* 排查解决方案中的问题。
* 规划如何根据具体要求自定义该解决方案。
* 设计自己的 IoT 解决方案，以使用 Azure 服务。

有关详细信息，请参阅[连接工厂常见问题解答](iot-suite-faq-cf.md)。

## <a name="logical-architecture"></a>逻辑体系结构

下图概述该预配置解决方案的逻辑组件：

![已连接好的工厂逻辑体系结构][connected-factory-logical]

## <a name="communication-patterns"></a>通信模式

此解决方案按照 [OPC UA Pub/Sub 规范](https://opcfoundation.org/news/opc-foundation-news/opc-foundation-announces-support-of-publish-subscribe-for-opc-ua/)将 OPC UA 遥测数据以 JSON 格式发送到 IoT 中心。 此解决方案将 [OPC 发布服务器](https://github.com/Azure/iot-edge-opc-publisher) IoT Edge 模块用于此目的。

此解决方案还将 OPC UA 客户端集成到 Web 应用程序中，后者可以与本地 OPC UA 服务器建立连接。 该客户端使用[反向代理](https://wikipedia.org/wiki/Reverse_proxy)并从 IoT 中心获取帮助，不需本地防火墙上的开放端口即可进行连接。 此通信模式称为[服务辅助通信](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/)。 此解决方案将 [OPC 代理](https://github.com/Azure/iot-edge-opc-proxy/) IoT Edge 模块用于此目的。


## <a name="simulation"></a>模拟

模拟工作站和模拟制造执行系统 (MES) 构成了工厂生产线。 模拟设备和 OPC 发布服务器模块基于 OPC Foundation 发布的 [OPC UA .NET 标准][lnk-OPC-UA-NET-Standard]。

OPC 代理和 OPC 发布服务器以基于 [Azure IoT Edge][lnk-Azure-IoT-Gateway] 的模块方式实现。 每条模拟生产线都附加了一个指定的网关。

所有模拟组件都在 Azure Linux VM 中托管的 Docker 容器中运行。 默认情况下，模拟配置为运行八条模拟生产线。

## <a name="simulated-production-line"></a>模拟生产线

生产线制造各种部件。 它包括不同的工作站：组装工作站、测试工作站和打包工作站。

模拟运行并更新通过 OPC UA 节点公开的数据。 所有模拟生产线工作站都由 MES 通过 OPC UA 进行安排。

## <a name="simulated-manufacturing-execution-system"></a>模拟制造执行系统

MES 通过 OPC UA 监视生产线中的每个工作站来检测工作站状态更改。 它调用 OPC UA 的方法来控制工作站并将产品从一个工作站传送到下一个工作站，直到产品完成为止。

## <a name="gateway-opc-publisher-module"></a>网关 OPC 发布服务器模块

OPC 发布服务器模块连接到工作站 OPC UA 服务器并订阅要发布的 OPC 节点。 该模块将节点数据转换为 JSON 格式，对其进行加密，并将其作为 OPC UA 发布/订阅消息发送到 IoT 中心。

OPC 发布服务器模块仅需要出站 https 端口 (443) 并且可以与现有企业基础结构一起工作。

## <a name="gateway-opc-proxy-module"></a>网关 OPC Proxy 模块

网关 OPC UA 代理模块对二进制 OPC UA 命令和控制消息进行隧道操作，并且仅需要出站 https 端口 (443)。 它可以与现有企业基础结构一起工作，包括 Web 代理。

它使用 IoT 中心设备的方法在应用程序层传输打包后的 TCP/IP 数据，这样可以确保使用 SSL/TLS 实现终结点信任、数据加密和完整性。

通过代理本身进行中继的 OPC UA 二进制协议使用 UA 身份验证和加密。

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

网关 OPC 发布服务器模块订阅 OPC UA 服务器节点来检测数据值的更改。 如果在其中某个节点中检测到数据更改，则此模块会向 Azure IoT 中心发送消息。

IoT 中心为 Azure TSI 提供了事件来源。 TSI 根据附加到消息的时间戳将数据存储 30 天。 该数据包括：

* OPC UA ApplicationUri
* OPC UA NodeId
* 节点的值
* 来源时间戳
* OPC UA DisplayName

当前，TSI 不允许客户自定义他们希望将数据保留的时间长度。

TSI 使用一个搜索跨度（Time.From、Time.To）对节点数据进行查询并按 OPC UA ApplicationUri、OPC UA NodeId 或 OPC UA DisplayName 进行聚合。

要为 OEE 和 KPI 仪表以及时序图表检索数据，数据将按事件数、总和、平均值、最小值和最大值进行聚合。

时序是使用不同的流程构建的。 OEE 和 KPI 是基于工作站基础数据计算得到的，并且针对应用程序中的拓扑（生产线、工厂和企业）向上传输。

另外，每当所显示的时间跨度准备就绪后，都会在应用程序中计算 OEE 和 KPI 拓扑的时序。 例如，日视图每隔一个小时会更新。

节点数据的时序视图直接来自对时间跨度使用聚合的 TSI。

## <a name="iot-hub"></a>IoT 中心
[IoT 中心][lnk-IoT Hub]检索从 OPC 发布服务器模块发送到云中的数据，并使其可供 Azure TSI 服务使用。 

该解决方案中的 IoT 中心还承担以下职责：
- 维护标识注册表，其中存储着所有 OPC 发布服务器模块和所有 OPC 代理模块的 ID。
- 用作 OPC 代理模块的双向通信的传输通道。

## <a name="azure-storage"></a>Azure 存储
该解决方案使用 Azure blob 存储作为 VM 的磁盘存储并存储部署数据。

## <a name="web-app"></a>Web 应用
作为预配置解决方案的一部分部署的 Web 应用包括集成的 OPC UA 客户端以及警报处理和遥测可视化功能。

## <a name="next-steps"></a>后续步骤

可以通过阅读以下文章继续开始使用 IoT 套件：

* [azureiotsuite.com 站点权限][lnk-permissions]
* [在 Windows 或 Linux 上为连接工厂预配置解决方案部署网关](iot-suite-connected-factory-gateway-deployment.md)

[connected-factory-logical]:media/iot-suite-connected-factory-walkthrough/cf-logical-architecture.png

[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-IoT Hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-OPC-UA-NET-Standard]:https://github.com/OPCFoundation/UA-.NETStandardLibrary
[lnk-Azure-IoT-Gateway]: https://github.com/azure/iot-edge
[lnk-permissions]: iot-suite-permissions.md
