---
title: 在远程监视解决方案中集成 SIM 数据 - Azure | Microsoft Docs
description: 本文介绍如何将 Telefónica SIM 数据集成到远程监视解决方案中。
services: iot-suite
suite: iot-suite
author: hegate
manager: timlt
ms.author: hegate
ms.service: iot-suite
ms.date: 05/15/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 590a24113d66edacc6edcfe988330f643f1aa57a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
ms.locfileid: "34367549"
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>在远程监视解决方案中集成 SIM 数据

IoT 设备通常使用 SIM 卡连接到云，这样就可以从任何位置发送数据流。 Azure IoT 远程监视解决方案允许集成 IoT 托管的连接数据，因此操作员也可通过 IoT SIM 提供的数据来跟踪设备的运行状况。

远程监视提供与 Telefónica IoT 连接 的现成集成，因此客户可以使用其 IoT 连接平台将设备 SIM 连接数据同步到其解决方案。 此解决方案在经过扩展后可以通过 GitHub [存储库](http://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)支持其他 IoT 连接提供商。

本教程介绍如何执行下列操作：

* 将 Telefónica IoT SIM 数据集成到远程监视解决方案中
* 查看实时遥测数据
* 查看 SIM 数据

## <a name="telefnica-iot-integration-setup"></a>Telefónica IoT 集成设置

### <a name="prerequisites"></a>先决条件

此附加远程监视功能目前处于预览状态。 若要将连接数据同步到 Azure 远程监视解决方案中，请执行以下步骤：

1. 在 [Telefónica 站点](https://iot.Telefónica.com/contact)上填写一个请求，选择“Azure 远程监视”选项并提供你的联系信息。
2. Telefónica 激活你的帐户。
3. 如果你还不是 Telefónica 客户，但希望享受此项服务或其他 IoT 连接云就绪型服务，请访问 [Telefónica 的站点](https://iot.Telefónica.com/contact)并选择“连接”选项。

### <a name="telefnica-sim-setup"></a>Telefónica SIM 设置
Telefónica SIM 和 Azure 孪生设备 ID 关联基于 Telefónica IoT SIM 的“别名”属性。 

导航到 [Telefónica IoT 连接平台门户](https://m2m-movistar-es.telefonica.com/) >“SIM 清单”，选择你的 SIM，然后使用所需孪生 deviceID 更新每个 SIM“别名”。 此任务也可以批量模式执行（请参阅 Telefónica IoT 连接平台用户手册）。

此任务也可以批处理模式执行（请参阅 Telefónica IoT 连接平台用户手册）

![Telefónica 更新](./media/iot-accelerators-remote-monitoring-telefonica-sim/telefonica_site.png)

若要将设备连接到远程监视，可以按这些使用 [C](iot-accelerators-connecting-devices-linux.md) 或 [Node](iot-accelerators-connecting-devices-node.md) 的教程进行操作。 

## <a name="view-device-telemetry-and-sim-properties"></a>查看设备遥测数据和 SIM 属性

正确配置 Telefónica 帐户并连接设备以后，即可查看设备详细信息和 SIM 数据。

可发布以下连接参数：

* ICCID
* IP
* 网络形式
* SIM 状态
* 基于网络的位置
* 使用的数据流量

![仪表板](./media/iot-accelerators-remote-monitoring-telefonica-sim/dashboard.png)

## <a name="next-steps"></a>后续步骤

大致了解如何将 SIM 数据集成到 Azure IoT 远程监视中以后，即可执行下面这些建议的适用于解决方案加速器的后续步骤：

* [操作 Azure IoT 远程监视解决方案](iot-accelerators-remote-monitoring-explore.md)
* [执行高级监视](iot-accelerators-remote-monitoring-monitor.md)
* [管理设备](iot-accelerators-remote-monitoring-manage.md)
* [排查设备问题](iot-accelerators-remote-monitoring-maintain.md)

