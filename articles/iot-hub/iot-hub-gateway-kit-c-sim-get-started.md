---
title: "Azure IoT 网关初学者工具包入门 | Microsoft 文档"
description: "开始使用 IoT 网关初学者工具包，创建 Azure IoT 中心，并将网关连接到 IoT 中心"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "azure iot 中心, iot 网关, 物联网入门, iot 工具包"
ms.assetid: 0c110b8b-bee4-4aec-a18a-dfc292aa17a3
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/21/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 65a25dd7a2f6a8d518217512f9e10fc9008ee728
ms.openlocfilehash: c62e0cc560af18945c371af000ec08c0ddc7cd5a


---

# <a name="get-started-with-iot-gateway-starter-kit-with-a-simulated-device"></a>通过模拟设备开始使用 IoT 网关初学者工具包

> [!div class="op_single_selector"]
> * [SensorTag](iot-hub-gateway-kit-c-get-started.md)
> * [模拟设备](iot-hub-gateway-kit-c-sim-get-started.md)

本教程介绍有关如何使用 [IoT 网关初学者工具包](https://aka.ms/gateway-kit)的基础知识。 其中将会介绍如何操作运行 Wind River Linux 的 Intel NUC， 以及如何使用 Azure IoT 中心将设备无缝连接到云。

***
**还没有工具包？**请单击[此处](https://aka.ms/gateway-kit)。
***

## <a name="lesson-1-configure-your-nuc"></a>第 1 课：配置 NUC
![第 1 课端到端示意图](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson1.png)

本课介绍如何将工具包中的 Intel NUC（下一代计算单元，Next Unit of Computing）设置为 Azure IoT 网关、在 NUC 上安装 Azure IoT 网关 SDK 包，以及运行示例应用验证网关功能。

*估计完成时间：15 分钟*

转到[将 Intel NUC 设置为 IoT 网关](iot-hub-gateway-kit-c-sim-lesson1-set-up-nuc.md)

## <a name="lesson-2-create-your-iot-hub"></a>第 2 课：创建 IoT 中心
![第 2 课端到端示意图](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson2.png)

本课介绍如何在主计算机上安装工具和软件。 然后，创建免费的 Azure 帐户，预配 Azure IoT 中心，并在 IoT 中心创建第一个设备。

开始本课之前，请完成第 1 课。

### <a name="get-the-tools"></a>获取工具
在主计算机上安装工具和软件。

*估计完成时间：20 分钟*

转到[获取工具](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-win32.md)

### <a name="create-an-iot-hub-and-register-your-device"></a>创建 IoT 中心并注册设备
使用 Azure CLI 创建资源组，预配第一个 Azure IoT 中心，将第一个设备添加到 IoT 中心。

*估计完成时间：10 分钟*

转到[创建 IoT 中心并注册设备](iot-hub-gateway-kit-c-sim-lesson2-register-device.md)

## <a name="lesson-3-receive-messages-from-the-simulated-device-and-read-messages-from-your-iot-hub"></a>第 3 课：从模拟设备接收消息以及从 IoT 中心读取消息
本课介绍如何使用脚本在网关中自动配置和执行模拟设备应用。 模拟设备应用生成示例温度数据，并将其发送到 IoT 中心模块。 IoT 中心模块打包接收的数据，并通过 Azure IoT 网关 SDK 中提供的网关框架将数据发送到 IoT 中心。

![第 3 课端到端关系图](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson3.png)

### <a name="configure-and-run-a-simulated-device"></a>配置并运行模拟设备
准备示例代码。 然后配置并运行模拟设备示例应用程序。

*估计完成时间：15 分钟*

转到[配置并运行模拟设备](iot-hub-gateway-kit-c-sim-lesson3-configure-simulated-device-app.md)

### <a name="read-messages-from-your-iot-hub"></a>从 IoT 中心读取消息
在主计算机上运行示例代码，从 IoT 中心读取消息。

*估计完成时间：15 分钟*

转到[从 IoT 中心读取消息](iot-hub-gateway-kit-c-sim-lesson3-read-messages-from-hub.md)

## <a name="lesson-4-save-messages-to-azure-table-storage"></a>第 4 课：将消息保存到 Azure 表存储
创建一个 Azure Function App，以便获取 IoT 中心发出的传入消息并将其写入 Azure 表存储。

![第 4 课端到端关系图](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson4.png)

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>创建 Azure Function App 和 Azure 存储帐户
使用 Azure Resource Manager 模板创建一个 Azure Function App 和一个 Azure 存储帐户。

*Estimated time to complete: 10 minutes*

转到[创建 Azure Function App 和 Azure 存储帐户](iot-hub-gateway-kit-c-sim-lesson4-deploy-resource-manager-template.md)

### <a name="read-messages-persisted-in-azure-table-storage"></a>读取保存在 Azure 表存储中的消息
在将网关到云消息写入 Azure 表存储时对其进行监视。

*估计完成时间：5 分钟*

转到[读取保存在 Azure 表存储中的消息](iot-hub-gateway-kit-c-sim-lesson4-read-table-storage.md)。

## <a name="troubleshooting"></a>故障排除
如果在课程中遇到任何问题，可在[故障排除](iot-hub-gateway-kit-c-sim-troubleshooting.md)一文中查找解决方法。

## <a name="explore-more"></a>了解更多
请访问 [Intel IoT 网关工具包开发人员区域](https://software.intel.com/en-us/iot/hardware/gateways/dev-kit)了解详细信息。


<!--HONumber=Dec16_HO3-->


