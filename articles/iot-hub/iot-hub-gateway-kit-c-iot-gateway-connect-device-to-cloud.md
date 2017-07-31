---
title: "使用 IoT 网关将设备连接到 Azure IoT 中心 | Microsoft Docs"
description: "了解如何将 Intel NUC 用作 IoT 网关，以连接 TI SensorTag 并将传感器数据发送到云中的 Azure IoT 中心。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "IoT 网关将设备连接到云"
ms.assetid: cb851648-018c-4a7e-860f-b62ed3b493a5
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/25/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 61cb8eb0ad23a7d4b333bf54342b872a3a8ae1da
ms.contentlocale: zh-cn
ms.lasthandoff: 06/28/2017

---
# <a name="use-iot-gateway-to-connect-things-to-the-cloud---sensortag-to-azure-iot-hub"></a>使用 IoT 网关将设备连接到 Azure IoT 中心的云 - SensorTag

> [!NOTE]
> 在开始本教程前，请确保[将 Intel NUC 设置为 IoT 网关](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)已完成。 在[将 Intel NUC 设置为 IoT 网关](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)中，用户将 Intel NUC 设备设置为 IoT 网关。

## <a name="what-you-will-learn"></a>你要学习的知识

了解如何使用 IoT 网关将 Texas Instruments SensorTag (CC2650STK) 连接到 Azure IoT 中心。 IoT 网关将从 SensorTag 收集的温度和湿度数据发送到 Azure IoT 中心。

## <a name="what-you-will-do"></a>执行的操作

- 创建 IoT 中心。
- 在 IoT 中心内为 SensorTag 注册一台设备。
- 启用 IoT 网关和 SensorTag 之间的连接。
- 运行 BLE 示例应用程序，以将 SensorTag 数据发送到 IoT 中心。

## <a name="what-you-need"></a>所需条件

- 将 Intel NUC 设置为 IoT 网关后，即已完成[将 Intel NUC 设置为 IoT 网关](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)教程。
- * 一个有效的 Azure 订阅。 如果没有 Azure 帐户，只需几分钟时间就能[创建一个免费的 Azure 试用帐户](https://azure.microsoft.com/free/)。
- 在主计算机上运行的 SSH 客户端。 建议在 Windows 上使用 PuTTY。 Linux 和 macOS 已附带 SSH 客户端。
- IP 地址以及访问 SSH 客户端网关的用户名和密码。
- Internet 连接。

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

> [!NOTE]
> 在此处为 SensorTag 注册这台新设备

## <a name="enable-the-connection-between-the-iot-gateway-and-the-sensortag"></a>启用 IoT 网关和 SensorTag 之间的连接

在本节中，执行以下任务：

- 获取 SensorTag 的 MAC 地址以进行蓝牙连接。
- 启动从 IoT 网关到 SensorTag 的蓝牙连接。

### <a name="get-the-mac-address-of-the-sensortag-for-bluetooth-connection"></a>获取 SensorTag 的 MAC 地址以进行蓝牙连接

1. 在主计算机上，运行 SSH 客户端并连接到 IoT 网关。
1. 运行以下命令，取消阻止蓝牙：

   ```bash
   sudo rfkill unblock bluetooth
   ```

1. 在 IoT 网关上启动蓝牙服务，并输入蓝牙 shell，以通过运行以下命令配置蓝牙：

   ```bash
   sudo systemctl start bluetooth
   bluetoothctl
   ```

1. 在蓝牙 shell 上运行以下命令，打开蓝牙控制器：

   ```bash
   power on
   ```

   ![在 IoT 网关上通过 bluetoothctl 打开蓝牙控制器](./media/iot-hub-iot-gateway-connect-device-to-cloud/8_power-on-bluetooth-controller-at-bluetooth-shell-bluetoothctl.png)

1. 通过运行以下命令，开始扫描附近的蓝牙设备：

   ```bash
   scan on
   ```

   ![通过 bluetoothctl 扫描附近的蓝牙设备](./media/iot-hub-iot-gateway-connect-device-to-cloud/9_start-scan-nearby-bluetooth-devices-at-bluetooth-shell-bluetoothctl.png)

1. 按 SensorTag 上的“配对”按钮。 SensorTag 上的绿色 LED 灯将闪烁。
1. 在蓝牙 shell 上，应该看到已找到 SensorTag。 记下 SensorTag 的 MAC 地址。 在此示例中，SensorTag 的 MAC 地址是 `24:71:89:C0:7F:82`。
1. 通过运行以下命令，关闭扫描：

   ```bash
   scan off
   ```

   ![通过 bluetoothctl 停止扫描附近的蓝牙设备](./media/iot-hub-iot-gateway-connect-device-to-cloud/10_stop-scanning-nearby-bluetooth-devices-at-bluetooth-shell-bluetoothctl.png)

### <a name="initiate-a-bluetooth-connection-from-the-iot-gateway-to-the-sensortag"></a>启动从 IoT 网关到 SensorTag 的蓝牙连接

1. 通过运行以下命令，连接到 SensorTag：

   ```bash
   connect <MAC address>
   ```

   ![通过 bluetoothctl 连接到 SensorTag](./media/iot-hub-iot-gateway-connect-device-to-cloud/11_connect-to-sensortag-at-bluetooth-shell-bluetoothctl.png)

1. 通过运行以下命令，从 SensorTag 断开连接，并退出蓝牙 shell：

   ```bash
   disconnect
   exit
   ```

   ![通过 bluetoothctl 从 SensorTag 断开连接](./media/iot-hub-iot-gateway-connect-device-to-cloud/12_disconnect-from-sensortag-at-bluetooth-shell-bluetoothctl.png)

已成功启用 SensorTag 和 IoT 网关之间的连接。

## <a name="run-a-ble-sample-application-to-send-sensortag-data-to-your-iot-hub"></a>运行 BLE 示例应用程序，将 SensorTag 数据发送到 IoT 中心

Azure IoT Edge 提供了蓝牙低功耗 (BLE) 示例应用程序。 该示例应用程序收集 BLE 连接的数据，并将该数据发送到 IoT 中心。 若要运行示例应用程序，需要：

1. 配置该示例应用程序。
1. 在 IoT 网关上运行该示例应用程序。

### <a name="configure-the-sample-application"></a>配置示例应用程序

1. 通过运行以下命令，转到示例应用程序的文件夹：

   ```bash
   cd /usr/share/azureiotgatewaysdk/samples/ble_gateway
   ```

1. 通过运行以下命令，打开配置文件：

   ```bash
   vi ble_gateway.json
   ```

1. 在配置文件中，填写以下值：

   **IoTHubName**：IoT 中心的名称。

   **IoTHubSuffix**：从记下的设备连接字符串的主键中获取 IoTHubSuffix。 确保获取的是设备连接字符串的主键，而非 IoT 中心连接字符串的主键。 设备连接字符串的主键采用 `HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY` 的格式。

   **Transport**：默认值为 `amqp`。 此值在传输期间显示协议。 它可以是 `http`、`amqp` 或 `mqtt`。

   **macAddress**：记下的 SensorTag 的 MAC 地址。

   **deviceID**：在 IoT 中心内创建的设备的 ID。

   **deviceKey**：设备连接字符串的主键。

   ![完成 BLE 示例应用程序的配置文件](./media/iot-hub-iot-gateway-connect-device-to-cloud/13_edit-config-file-of-ble-sample.png)

1. 按 `ESC` 并键入 `:wq` 以保存文件。

### <a name="run-the-sample-application"></a>运行示例应用程序

1. 确保已打开 SensorTag。
1. 运行以下命令：

   ```bash
   ./ble_gateway ble_gateway.json
   ```

## <a name="next-steps"></a>后续步骤

[通过 Azure IoT Edge，使用 IoT 网关进行传感器数据转换](iot-hub-gateway-kit-c-use-iot-gateway-for-data-conversion.md)

