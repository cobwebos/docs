---
title: 在远程监视解决方案中集成 SIM 数据 - Azure | Microsoft Docs
description: 本文介绍如何将 Telefonica SIM 数据集成到远程监视解决方案中。
services: iot-suite
suite: iot-suite
author: hegate
manager: corywink
ms.author: hegate
ms.service: iot-suite
ms.date: 04/30/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: c82bb8ff3d0f903e1de627f13337ae5fc633458c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>在远程监视解决方案中集成 SIM 数据

## <a name="overview"></a>概述
IoT 设备通常使用 SIM 卡连接到云，这样就可以从任何位置发送数据流。 Azure IoT 远程监视解决方案允许集成 SIM 管理数据，因此操作员也可通过 SIM 提供的数据来跟踪设备的运行状况。 远程监视提供可以与 Telefonica IoT 集成的现成解决方案，因此客户可以使用其 IoT 连接平台将设备 SIM 连接数据同步到解决方案。 此解决方案在经过扩展后可以通过 GitHub 存储库支持其他电话公司提供商。
本教程介绍如何执行下列操作：
* 将 SIM 数据集成到远程监视解决方案中
* 查看实时遥测数据
* 查看 SIM 数据 

## <a name="telefonica-iot-integration-setup"></a>Telefonica IoT 集成设置

### <a name="prerequisites"></a>先决条件
若要将连接数据同步到 Azure 远程监视解决方案中，请执行以下步骤：

1.  在 [Telefonica 站点](https://iot.telefonica.com/contact)上填写一个请求，选择“Azure 远程监视”选项并提供你的联系信息。
2.  Telefonica 会激活你的帐户。 
3.  如果你还不是 Telefónica 客户，但希望享受此项 IoT 连接云就绪型服务或其他此类服务，请访问 [Telefonica 的站点](https://iot.telefonica.com/contact)并选择“连接”选项。

### <a name="telefonica-sim-setup"></a>Telefonica SIM 设置
Telefónica SIM 和 Azure 孪生设备 ID 关联将取决于 Telefónica IoT SIM 的“别名”属性。 

导航到 [Telefónica IoT 连接平台门户](https://m2m-movistar-es.telefonica.com/) >“SIM 清单”，选择你的 SIM，然后使用所需孪生 deviceID 更新每个 SIM“别名”。 

此任务也可以批处理模式执行（请参阅 Telefónica IoT 连接平台用户手册）

![Telefonica 更新](media/iot-suite-remote-monitoring-telefonica/telefonica_site.png)

若要将设备连接到远程监视，可以按这些使用 [C](iot-suite-connecting-devices-linux.md) 或 [Node](iot-suite-connecting-devices-node.md) 的教程进行操作。 

## <a name="view-device-telemetry-and-sim-properties"></a>查看设备遥测数据和 SIM 属性
正确配置 Telefonica 帐户并连接设备以后，即可查看设备详细信息和 SIM 数据。
可发布以下连接参数：
* ICCID
* IP
* 网络形式
* SIM 状态
* 基于网络的位置
* 使用的数据流量

![仪表板](media/iot-suite-remote-monitoring-telefonica/dashboard.png)
 
## <a name="next-steps"></a>后续步骤

大致了解如何将 SIM 数据集成到 Azure IoT 远程监视中以后，即可执行下面这些建议的适用于解决方案加速器的后续步骤：

* [操作 Azure IoT 远程监视解决方案](iot-suite-remote-monitoring-explore.md)
* [执行高级监视](iot-suite-remote-monitoring-monitor.md)
* [管理设备](iot-suite-remote-monitoring-manage.md)
* [排查设备问题](iot-suite-remote-monitoring-maintain.md)

