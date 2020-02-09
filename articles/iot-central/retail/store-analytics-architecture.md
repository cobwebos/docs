---
title: 商店分析体系结构
description: 了解如何使用 IoT Central 中的结账应用程序模板构建店内分析应用程序
author: avneets
ms.author: avneets
ms.date: 10/13/2019
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
manager: eliotgra
ms.openlocfilehash: 6c2514bd078cc3feee4bd2802cf314079b824311
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022114"
---
# <a name="in-store-analytics-architecture"></a>店内分析体系结构



店内分析解决方案使你能够监视零售商店环境中的各种情况。 可以使用 IoT Central 中的某个应用程序模板和下面的体系结构作为指导来构建这些解决方案。


![Azure IoT Central 商店分析](./media/architecture/store-analytics-architecture-frame.png)

- 一组将遥测数据发送到网关设备的 IoT 传感器
- 将遥测和聚合见解发送到 IoT Central 的网关设备
- 连续数据导出到所需的 Azure 服务以进行操作
- 可按所需格式对数据进行结构化并将其发送到存储服务
- 业务应用程序可以查询数据并生成可助力零售运营的见解
 
让我们了解店内分析解决方案中常用的一些关键组件。

## <a name="condition-monitoring-sensors"></a>环境监视传感器

IoT 解决方案的基础是从零售商店环境内捕获有意义信号的一组传感器。 上面的体系结构图最左侧的各种传感器反映了这一点。

## <a name="gateway-devices"></a>网关设备

许多 IoT 传感器可将原始信号直接送到云或位于其附近的网关设备。 网关设备先在边缘执行数据聚合，然后将摘要见解发送到 IoT Central 应用程序。 网关设备还负责将命令和控制操作中继到传感器设备（如果适用）。 

## <a name="iot-central-application"></a>IoT Central 应用程序

Azure IoT Central 应用程序从各种 IoT 传感器以及零售商店环境中的网关设备引入数据，并生成一组有意义的见解。

Azure IoT Central 还为商店运营商提供定制的体验，使他们能够远程监视和管理基础结构设备。

## <a name="data-transform"></a>数据转换
解决方案中的 Azure IoT Central 应用程序可配置为将原始或聚合见解导出到一组 Azure PaaS（平台即服务）服务，这些服务可执行数据操作并丰富这些见解，然后将其放入业务应用程序。 

## <a name="business-application"></a>业务应用程序
IoT 数据可用于为零售环境中部署的各种业务应用程序提供支持。 零售店经理或员工可以使用这些应用程序将业务见解可视化，并实时采取有意义的行动。 若要了解如何为零售团队构建实时 Power BI 仪表板，请按照[教程](./tutorial-in-store-analytics-create-app-pnp.md)操作。

## <a name="next-steps"></a>后续步骤
* 开始使用[店内分析结账](https://aka.ms/checkouttemplate)和[店内分析环境监视](https://aka.ms/conditiontemplate)应用程序模板。 
* 请查看[端到端教程](https://aka.ms/storeanalytics-tutorial)，该教程将指导你如何使用店内分析应用程序模板之一构建解决方案。
