---
title: 在远程监视解决方案中集成 SIM 数据 - Azure | Microsoft Docs
description: 本文介绍如何将 Telefónica SIM 数据集成到远程监视解决方案中。
author: hegate
manager: ''
ms.author: hegate
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/15/2018
ms.topic: conceptual
ms.openlocfilehash: ae8751f429cb4b11199bd8da9d8c2e08c0b98b35
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628277"
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>在远程监视解决方案中集成 SIM 数据

IoT 设备通常使用 SIM 卡连接到云，这样就可以从任何位置发送数据流。 Azure IoT 远程监视解决方案允许集成 IoT 托管的连接数据，因此操作员也可通过 IoT SIM 提供的数据来跟踪设备的运行状况。

远程监视提供可以与 Telefónica IoT 连接集成的现成解决方案，因此客户可以使用其 IoT 连接平台将设备 SIM 连接数据同步到解决方案。 此解决方案在经过扩展后可以通过 GitHub [存储库](http://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)支持其他 IoT 连接提供商。

本教程介绍如何执行下列操作：

* 将 Telefónica IoT SIM 数据集成到远程监视解决方案中
* 查看实时遥测数据
* 查看 SIM 数据

## <a name="telefnica-iot-integration-setup"></a>Telefónica IoT 集成设置

### <a name="prerequisites"></a>先决条件

此附加远程监视功能目前处于预览状态。 若要将连接数据同步到 Azure 远程监视解决方案中，请执行以下步骤：

1. 在 [Telefónica 网站](https://iot.Telefónica.com/contact)上填写一个请求，选择“Azure 远程监视”选项并提供你的联系信息。
2. Telefónica 会激活你的帐户。
3. 如果你还不是 Telefónica 客户并且想要享受这个服务或其他 IoT 连接云就绪服务，请访问 [Telefónica 网站](https://iot.Telefónica.com/contact)并选择“连接”选项。

### <a name="telefnica-sim-setup"></a>Telefónica SIM 设置
Telefónica SIM 和 Azure 孪生设备 ID 的关联基于 Telefónica IoT SIM“别名”属性。 

导航到 [Telefónica IoT 连接平台门户](https://m2m-movistar-es.telefonica.com/) > SIM 清单 > 选择 SIM，并将每个 SIM“别名”更新为所需的孪生设备 ID。 此任务还可以在批量模式下完成（请参阅 Telefónica IoT 连接平台用户手册）。

此任务还可以在批量模式下完成（请参阅 Telefónica IoT 连接平台用户手册）

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

