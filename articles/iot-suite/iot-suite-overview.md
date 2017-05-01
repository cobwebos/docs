---
title: "Microsoft Azure IoT 套件概述 | Microsoft Docs"
description: "概述 Azure IoT 套件如何提供物联网预配置解决方案，以收集、分析和存储数据，提供可视化效果，以及与其他系统集成。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 2d38d08a-4133-4e5c-8b28-f93cadb5df05
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/24/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: 309a36b1aa5db95a287b7a6b21449f9d61880cf4
ms.lasthandoff: 04/25/2017


---
# <a name="overview-of-azure-iot-suite"></a>Azure IoT 套件概述

Azure 物联网 (IoT) 服务提供有各种功能。 这些企业级服务让你能够：

* 从设备收集数据
* 分析移动中的数据流
* 存储和查询大型数据集
* 可视化实时和历史数据
* 与后端办公系统集成
* 管理设备

为了提供这些功能，Azure IoT 套件将多个 Azure 服务与自定义扩展打包在一起作为 *预配置解决方案*。 预配置解决方案是常见 IoT 解决方案模式的基础实现，可帮助你减少交付 IoT 解决方案所花费的时间。 通过使用 [IoT 软件开发工具包][lnk-sdks]，可以自定义和扩展这些解决方案来满足自己的需求。 也可以使用这些解决方案作为开发新 IoT 解决方案时的示例或模板。

以下视频将介绍 Azure IoT 套件：

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON309/player]
> 
> 

## <a name="azure-iot-services-in-azure-iot-suite"></a>Azure IoT 套件中的 Azure IoT 服务
预配置解决方案通常使用下列服务：

* Azure IoT 套件的核心是 [Azure IoT 中心][lnk-iot-hub]服务。 该服务提供设备到云和云到设备的消息传送功能，并充当云和其他主要 IoT 套件服务的网关。 该服务允许你从你的设备大量接收消息，并将命令发送给你的设备。 使用该服务，还能够[管理自己的设备][lnk-device-management]。 例如，可以配置、重启一个或多个连接到该中心的设备，或对其执行恢复出厂设置。
* [Azure 流分析][lnk-asa]提供移动中的数据分析。 IoT 套件使用该服务来处理传入遥测、执行聚合以及检测事件。 预配置解决方案也会使用流分析来处理包含数据（例如元数据或来自设备的命令响应）的信息消息。 这些解决方案使用流分析来处理来自你设备的消息，并将这些消息传送给其他服务。
* [Azure 存储][lnk-azure-storage]和 [Azure DocumentDB][lnk-document-db] 提供数据存储功能。 预配置解决方案使用 blob 存储来存储遥测数据并使其可用于分析。 这些解决方案使用 DocumentDB 来存储设备元数据，以及启用解决方案的设备管理功能。
* [Azure Web 应用][lnk-web-apps] 和 [Microsoft Power BI][lnk-power-bi] 提供数据可视化功能。 借助 Power BI 的灵活性，你可以快速生成自己的交互式仪表板（使用 IoT 套件数据）。

有关典型 IoT 解决方案体系结构的概述，请参阅 [Microsoft Azure 和物联网 (IoT)][iot-suite-what-is-azure-iot]。

## <a name="preconfigured-solutions"></a>预配置解决方案

IoT 套件包含预配置解决方案，允许你快速使用并浏览常见 IoT 方案，例如“远程监视”、“预见性维护”和“连接工厂”。 你可以将这些解决方案部署到 Azure 订阅，然后运行完整的端到端 IoT 方案。

## <a name="next-steps"></a>后续步骤

现在，已大概了解 IoT 套件可以执行哪些操作以及其主要组件是什么，接下来可以详细了解 IoT 套件中的预配置解决方案。 有关详细信息，请参阅 [Azure IoT 预配置解决方案是什么？][lnk-what-are-preconfig]

[lnk-sdks]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-azure-storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-power-bi]: https://powerbi.microsoft.com/
[lnk-web-apps]: https://azure.microsoft.com/documentation/services/app-service/web/
[iot-suite-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-what-are-preconfig]: iot-suite-what-are-preconfigured-solutions.md
[lnk-device-management]: ../iot-hub/iot-hub-device-management-overview.md

