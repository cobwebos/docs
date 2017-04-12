---
title: "SensorTag 设备和 Azure IoT 网关 - 第 3 课：运行示例应用 | Microsoft Docs"
description: "运行 BLE 示例应用程序，从 BLE SensorTag 和 IoT 中心接收数据。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "ble 应用, 传感器监视器应用, 传感器数据收集, 来自传感器的数据, 到云的传感器数据"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-gateway-kit-c-lesson1-set-up-nuc
ms.assetid: b33e53a1-1df7-4412-ade1-45185aec5bef
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: a9f689c0b231af3cdf9257e2179bf86ff7bc9a31
ms.lasthandoff: 01/25/2017


---
# <a name="configure-and-run-a-ble-sample-application"></a>配置并运行 BLE 示例应用程序

## <a name="what-you-will-do"></a>执行的操作

- 克隆示例存储库。 
- 在 SensorTag 与 Intel NUC 之间设置连接。 
- 使用 Azure CLI 获取 BLE（蓝牙低能耗）示例应用程序的 IoT 中心和 SensorTag 信息。 配置并运行 BLE 示例应用程序。 

如果有任何问题，请在[故障排除页面](iot-hub-gateway-kit-c-troubleshooting.md)上查找解决方案。

## <a name="what-you-will-learn"></a>你要学习的知识

在本文中，将学习以下内容：

- 如何配置和运行 BLE 示例应用程序。

## <a name="what-you-need"></a>所需条件

必须已成功完成以下任务

- [创建 IoT 中心并注册 SensorTag](iot-hub-gateway-kit-c-lesson2-register-device.md)

## <a name="clone-the-sample-repository-to-the-host-computer"></a>将示例存储库克隆到主计算机

若要克隆示例存储库，请在主计算机上执行以下步骤：

1. 在 Windows 中打开一个命令提示窗口，或者在 macOS 或 Ubuntu 中打开一个终端。
2. 运行以下命令：

   ```bash
   git clone https://github.com/Azure-samples/iot-hub-c-intel-nuc-gateway-getting-started
   cd iot-hub-c-intel-nuc-gateway-getting-started
   ```

## <a name="set-up-the-connectivity-between-sensortag-and-intel-nuc"></a>在 SensorTag 与 Intel NUC 之间设置连接

若要设置连接，请在主机上执行以下步骤：

1. 通过运行以下命令初始化配置文件：

   ```bash
   cd Lesson3
   npm install
   gulp init
   ```

2. 运行以下命令，在 Visual Studio Code 中打开 `config-gateway.json`：

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-gateway.json
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-gateway.json
   ```

3. 找到以下代码行，将 `[device hostname or IP address]` 替换为 Intel NUC 的 IP 地址或主机名。
   ![配置网关的屏幕截图](media/iot-hub-gateway-kit-lessons/lesson3/config_gateway.png)

4. 运行以下命令，在 Intel NUC 上安装帮助器工具：

   ```bash
   gulp install-tools
   ```

5. 如下图所示按下电源按钮打开 SensorTag，此时绿色 LED 将会闪烁。

   ![打开 SensorTag](media/iot-hub-gateway-kit-lessons/lesson3/turn on_off sensortag.jpg)

6. 运行以下命令扫描 SensorTag 设备：

   ```bash
   gulp discover-sensortag
   ```

7. 运行以下命令，测试 SensorTag 与 Intel NUC 之间的连接：

   ```bash
   gulp test-connectivity --mac {mac address}
   ```

   将 `{mac address}` 替换为在上一步骤中获取的 MAC 地址。

## <a name="get-the-connection-string-of-sensortag"></a>获取 SensorTag 的连接字符串

若要获取 SensorTag 的 Azure IoT 中心连接字符串，请在主计算机上运行以下命令：

```bash
az iot device show-connection-string --hub-name {IoT hub name} --device-id mydevice --resource-group iot-gateway
```

`{IoT hub name}` 是使用的 IoT 中心名称。 使用 iot-gateway 作为 `{resource group name}` 的值，使用 mydevice 作为 `{device id}` 的值（如果未在第 2 课中更改该值）。

## <a name="configure-the-ble-sample-application"></a>配置 BLE 示例应用程序

若要配置并运行 BLE 示例应用程序，请在主计算机上执行以下步骤：

1. 运行以下命令，在 Visual Studio Code 中打开 `config-sensortag.json`：

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-sensortag.json
   # For macOS or Ubuntu
   code ~/.iot-hub-getting-started/config-sensortag.json
   ```

   ![配置 SensorTag 的屏幕截图](media/iot-hub-gateway-kit-lessons/lesson3/config_sensortag.png)

2. 在代码中进行以下替换：
   - 将 `[IoT hub name]` 替换为使用的 IoT 中心名称。
   - 将 `[IoT device connection string]` 替换为获取的 SensorTag 连接字符串。
   - 将 `[device_mac_address]` 替换为获取的 SensorTag MAC 地址。

3. 运行 BLE 示例应用程序。

   若要运行 BLE 示例应用程序，请在主计算机上执行以下步骤：

   1. 打开 SensorTag。

   2. 运行以下命令，在 Intel NUC 上部署并运行 BLE 示例应用程序：
   
      ```bash
      gulp run
      ```

## <a name="verify-that-the-ble-sample-application-works"></a>验证 BLE 示例应用程序是否正常工作

现在，应会看到如下输出：

![BLE 示例应用程序输出](media/iot-hub-gateway-kit-lessons/lesson3/BLE_running.png)

该示例应用程序将不断收集温度数据，并将其发送到 IoT 中心。 发送 40 秒后，该示例应用程序将自动终止。

## <a name="summary"></a>摘要

现已成功设置 SensorTag 与 Intel NUC 之间的连接，以及运行从 SensorTag 收集数据并将数据发送到 IoT 中心的 BLE 示例应用程序。 接下来，可以继续学习如何确认 IoT 中心已收到数据。

## <a name="next-steps"></a>后续步骤
[从 IoT 中心读取消息](iot-hub-gateway-kit-c-lesson3-read-messages-from-hub.md)
