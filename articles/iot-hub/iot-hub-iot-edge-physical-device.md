---
title: "将物理设备与 Azure IoT Edge 配合使用 | Microsoft Docs"
description: "如何使用 Texas Instruments SensorTag 设备通过 Raspberry Pi 3 设备上运行的 IoT Edge 网关将数据发送到 IoT 中心。 该网关使用 Azure IoT Edge 生成。"
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 212dacbf-e5e9-48b2-9c8a-1c14d9e7b913
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/12/2017
ms.author: andbuc
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 02962a91c739a53dfcf947bcc736e5c293b9384f
ms.contentlocale: zh-cn
ms.lasthandoff: 06/26/2017


---
# 在 Raspberry Pi 上使用 Azure IoT Edge 将设备到云消息转发到 IoT 中心
<a id="use-azure-iot-edge-on-a-raspberry-pi-to-forward-device-to-cloud-messages-to-iot-hub" class="xliff"></a>

此[蓝牙低功耗示例][lnk-ble-samplecode]的演练演示如何使用 [Azure IoT Edge][lnk-sdk] 执行以下操作：

* 将“设备到云”遥测从物理设备转发到 IoT 中心。
* 将命令从 IoT 中心路由到物理设备。

本文介绍的内容包括：

* **体系结构**：与蓝牙低功耗示例有关的重要体系结构信息。
* **生成并运行**：生成并运行示例所需的步骤。

## 体系结构
<a id="architecture" class="xliff"></a>

本演练演示了如何在运行 Raspbian Linux 的 Raspberry Pi 3 上生成和运行 IoT Edge 网关。 该网关使用 IoT Edge 生成。 此示例使用 Texas Instruments SensorTag 蓝牙低功耗 (BLE) 设备收集温度数据。

运行 IoT Edge 网关时，它：

* 使用蓝牙低功耗 (BLE) 协议连接到 SensorTag 设备。
* 使用 HTTP 协议连接到 IoT 中心。
* 从 SensorTag 设备将遥测转发到 IoT 中心。
* 从 IoT 中心将命令路由到 SensorTag 设备。

该网关包含以下 IoT Edge 模块：

* *BLE 模块*，与 BLE 设备相连接，从设备接收温度数据并将命令发送到设备。
* *BLE 云到设备模块*，用于将发送自 IoT 中心的 JSON 消息转换为适用于 *BLE 模块*的 BLE 指令。
* *记录器模块*，用于将所有网关消息记录到本地文件中。
* *标识映射模块*，用于在 BLE 设备 MAC 地址和 Azure IoT 中心设备标识之间进行转换。
* *IoT 中心模块*，用于将遥测数据上传到 IoT 中心并接收来自 IoT 中心的设备命令。
* *BLE 打印机模块*，用于解释 BLE 设备的遥测，并将格式化数据输出到控制台，以启用故障排除和调试。

### 数据如何流经网关
<a id="how-data-flows-through-the-gateway" class="xliff"></a>

以下块图说明了遥测上传数据流管道：

![遥测上传网关管道](media/iot-hub-iot-edge-physical-device/gateway_ble_upload_data_flow.png)

通过以下步骤将遥测项从 BLE 设备传输到 IoT 中心：

1. BLE 设备生成温度样本并将其通过蓝牙发送到网关的 BLE 模块。
1. BLE 模块接收该样本，并将其与设备的 MAC 地址一起发布到中转站。
1. 标识映射模块提取此消息，并使用内部表将设备的 MAC 地址转换为 IoT 中心设备标识。 IoT 中心设备标识包含设备 ID 和设备密钥。
1. 标识映射模块会发布一条新消息，其中包含温度样本数据、设备的 MAC 地址、设备 ID 和设备密钥。
1. IoT 中心模块接收此新消息（由标识映射模块生成），并将其发布到 IoT 中心。
1. 记录器模块将中转站中的所有消息记录到本地文件中。

以下块图说明了设备命令数据流管道：

![设备命令网关管道](media/iot-hub-iot-edge-physical-device/gateway_ble_command_data_flow.png)

1. IoT 中心模块会定期在 IoT 中心中轮询新的命令消息。
1. 当 IoT 中心模块收到新的命令消息时，它会将其发布到中转站。
1. 标识映射模块提取该命令消息，并使用内部表将 IoT 中心设备 ID 转换为设备 MAC 地址。 然后，它发布新消息（在消息的属性映射中包括目标设备的 MAC 地址）。
1. BLE“云到设备”模块提取此消息，并为 BLE 模块将其转换为正确的 BLE 指令。 然后它发布新消息。
1. BLE 模块通过 BLE 设备进行通信提取该消息并执行 I/O 指令。
1. 记录器模块将中转站中的所有消息记录到磁盘文件中。

## 先决条件
<a id="prerequisites" class="xliff"></a>

需要有效的 Azure 订阅才能完成此教程。

> [!NOTE]
> 如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用][lnk-free-trial]。

需要在台式机上安装 SSH 客户端，然后才能远程访问 Raspberry Pi 上的命令行。

- Windows 不包括 SSH 客户端。 建议使用 [PuTTY](http://www.putty.org/)。
- 大多数 Linux 分发版和 Mac OS 包括命令行 SSH 实用工具。 有关详细信息，请参阅 [SSH Using Linux or Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md)（使用 Linux 或 Mac OS 的 SSH）。

## 准备硬件
<a id="prepare-your-hardware" class="xliff"></a>

本教程假设使用的是已连接到运行 Raspbian 的 Raspberry Pi 3 的 [Texas Instruments SensorTag](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/index.html) 设备。

### 安装 Raspbian
<a id="install-raspbian" class="xliff"></a>

可以使用以下任一选项在 Raspberry Pi 3 设备上安装 Raspbian。

* 若要安装最新版本的 Raspbian，请使用 [NOOBS][lnk-noobs] 图形用户界面。
* 手动[下载][lnk-raspbian] Raspbian 操作系统的最新映像，并将其写入 SD 卡中。

### 登录并访问终端
<a id="sign-in-and-access-the-terminal" class="xliff"></a>

可以通过两个选项来访问 Raspberry Pi 上的终端环境：

* 如果已将键盘和显示器连接到 Raspberry Pi，则可使用 Raspbian GUI 访问终端窗口。

* 使用台式机中的 SSH 访问 Raspberry Pi 上的命令行。

#### 使用 GUI 中的终端窗口
<a id="use-a-terminal-window-in-the-gui" class="xliff"></a>

Raspbian 的默认凭据为用户名 **pi** 和密码 **raspberry**。 在 GUI 的任务栏中，可以使用外观像显示器的图标启动 **Terminal** 实用工具。

#### 使用 SSH 登录
<a id="sign-in-with-ssh" class="xliff"></a>

可以使用 SSH 对 Raspberry Pi 进行命令行访问。 [SSH（安全外壳）][lnk-pi-ssh]一文介绍了如何在 Raspberry Pi 上配置 SSH，以及如何从 [Windows][lnk-ssh-windows] 或 [Linux 和 Mac OS][lnk-ssh-linux] 进行连接。

使用用户名 **pi** 和密码 **raspberry** 登录。

### 安装 BlueZ 5.37
<a id="install-bluez-537" class="xliff"></a>

BLE 模块通过 BlueZ 堆栈与蓝牙硬件进行通信。 需要 BlueZ 5.37 版才能让模块正常运行。 以下说明可确保安装 BlueZ 的正确版本。

1. 停止当前的蓝牙守护程序：

    ```sh
    sudo systemctl stop bluetooth
    ```

1. 安装 BlueZ 依赖项：

    ```sh
    sudo apt-get update
    sudo apt-get install bluetooth bluez-tools build-essential autoconf glib2.0 libglib2.0-dev libdbus-1-dev libudev-dev libical-dev libreadline-dev
    ```

1. 从 bluez.org 下载 BlueZ 源代码：

    ```sh
    wget http://www.kernel.org/pub/linux/bluetooth/bluez-5.37.tar.xz
    ```

1. 解压缩源代码：

    ```sh
    tar -xvf bluez-5.37.tar.xz
    ```

1. 将目录更改为新创建的文件夹：

    ```sh
    cd bluez-5.37
    ```

1. 配置要生成的 BlueZ 代码：

    ```sh
    ./configure --disable-udev --disable-systemd --enable-experimental
    ```

1. 生成 BlueZ：

    ```sh
    make
    ```

1. 在生成 BlueZ 后安装 BlueZ：

    ```sh
    sudo make install
    ```

1. 在 `/lib/systemd/system/bluetooth.service` 文件中更改蓝牙的 systemd 服务配置，使其指向新的蓝牙守护程序。 将“ExecStart”行替换为以下文本：

    ```conf
    ExecStart=/usr/local/libexec/bluetooth/bluetoothd -E
    ```

### 从 Raspberry Pi 3 设备启用到 SensorTag 设备的连接
<a id="enable-connectivity-to-the-sensortag-device-from-your-raspberry-pi-3-device" class="xliff"></a>

运行示例前，需要确认 Raspberry Pi 3 可以连接到 SensorTag 设备。

1. 确保安装 `rfkill` 实用程序：

    ```sh
    sudo apt-get install rfkill
    ```

1. 打开 Raspberry Pi 3 上的蓝牙，确认版本号为 **5.37**：

    ```sh
    sudo rfkill unblock bluetooth
    bluetoothctl --version
    ```

1. 若要进入交互式蓝牙 shell，请启动蓝牙服务并执行 **bluetoothctl** 命令：

    ```sh
    sudo systemctl start bluetooth
    bluetoothctl
    ```

1. 输入 **power on** 命令，打开蓝牙控制器。 该命令将返回类似于以下内容的输出：

    ```sh
    [NEW] Controller 98:4F:EE:04:1F:DF C3 raspberrypi [default]
    ```

1. 在交互式蓝牙 shell 中，输入 **scan on** 命令以扫描蓝牙设备。 该命令将返回类似于以下内容的输出：

    ```sh
    Discovery started
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: yes
    ```

1. 通过按小按钮（绿色 LED 应闪烁）使 SensorTag 设备可检测到。 Raspberry Pi 3 应当会发现 SensorTag 设备：

    ```sh
    [NEW] Device A0:E6:F8:B5:F6:00 CC2650 SensorTag
    [CHG] Device A0:E6:F8:B5:F6:00 TxPower: 0
    [CHG] Device A0:E6:F8:B5:F6:00 RSSI: -43
    ```

    在此示例中，可看到 SensorTag 设备的 MAC 地址为 **A0:E6:F8:B5:F6:00**。

1. 输入 **scan off** 命令来关闭扫描：

    ```sh
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: no
    Discovery stopped
    ```

1. 输入 **connect \<MAC address\>**，利用 MAC 地址连接到 SensorTag 设备。 为清楚起见，以下示例输出是缩写形式：

    ```sh
    Attempting to connect to A0:E6:F8:B5:F6:00
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: yes
    Connection successful
    [CHG] Device A0:E6:F8:B5:F6:00 UUIDs: 00001800-0000-1000-8000-00805f9b34fb
    ...
    [NEW] Primary Service
            /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
            Device Information
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 GattServices: /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 Name: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Alias: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Modalias: bluetooth:v000Dp0000d0110
    ```

    > 可使用 **list-attributes** 命令重新列出设备的 GATT 特征。

1. 现可使用 **disconnect** 命令断开与设备的连接，然后使用 **quit** 命令退出蓝牙程序：

    ```sh
    Attempting to disconnect from A0:E6:F8:B5:F6:00
    Successful disconnected
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: no
    ```

现在可以在 Raspberry Pi 3 上运行 BLE IoT Edge 示例了。

## 运行 IoT Edge BLE 示例
<a id="run-the-iot-edge-ble-sample" class="xliff"></a>

若要运行 IoT Edge BLE 示例，需要完成三个任务：

* 在 IoT 中心中配置两个示例设备。
* 在 Raspberry Pi 3 设备上生成 IoT Edge。
* 在 Raspberry Pi 3 设备上配置并运行 BLE 示例。

编写本文时，IoT Edge 仅支持在 Linux 上运行的网关中的 BLE 模块。

### 在 IoT 中心中配置两个示例设备
<a id="configure-two-sample-devices-in-your-iot-hub" class="xliff"></a>

* 若要在 Azure 订阅中[创建 IoT 中心][lnk-create-hub]，需要使用中心的名称来完成本演练。 如果没有帐户，只需几分钟即可创建一个[免费帐户][lnk-free-trial]。
* 将名为 **SensorTag_01** 的设备添加到 IoT 中心，记下其 ID 和设备密钥。 可使用[设备资源管理器或 iothub-explorer][lnk-explorer-tools] 工具将此设备添加至上一步中创建的 IoT 中心，然后检索其密钥。 配置网关时，可将此设备映射到 SensorTag 设备。

### 在 Raspberry Pi 3 上生成 Azure IoT Edge
<a id="build-azure-iot-edge-on-your-raspberry-pi-3" class="xliff"></a>

安装 Azure IoT Edge 的依赖项：

```sh
sudo apt-get install cmake uuid-dev curl libcurl4-openssl-dev libssl-dev
```

使用以下命令将 IoT Edge 及其所有子模块克隆到主目录：

```sh
cd ~
git clone https://github.com/Azure/iot-edge.git
```

Raspberry Pi 3 上有 IoT Edge 存储库的完整副本时，可以从包含该 SDK 的文件夹使用以下命令生成它：

```sh
cd ~/iot-edge
./tools/build.sh  --disable-native-remote-modules
```

### 在 Raspberry Pi 3 上配置并运行 BLE 示例
<a id="configure-and-run-the-ble-sample-on-your-raspberry-pi-3" class="xliff"></a>

若要启动和运行示例，必须配置参与网关的每个 IoT Edge 模块。 在 JSON 文件中提供了此配置，必须配置所有五个参与 IoT Edge 模块。 存储库中有一个名为 **gateway\_sample.json** 的示例 JSON 文件，可将它用作自行生成配置文件的起点。 此文件位于 IoT Edge 存储库本地副本的 **samples/ble_gateway/src** 文件夹中。

以下各部分介绍了如何编辑 BLE 示例的此配置文件，并假设 IoT Edge 存储库位于 Raspberry Pi 3 的 **/home/pi/iot-edge/** 文件夹中。 如果存储库在其他位置，需相应地调整路径。

#### 记录器配置
<a id="logger-configuration" class="xliff"></a>

假设网关存储库位于 **/home/pi/iot-edge/** 文件夹中，如下所示配置记录器模块：

```json
{
  "name": "Logger",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path" : "build/modules/logger/liblogger.so"
    }
  },
  "args":
  {
    "filename": "<</path/to/log-file.log>>"
  }
}
```

#### BLE 模块配置
<a id="ble-module-configuration" class="xliff"></a>

BLE 设备的示例配置假定使用 Texas Instruments SensorTag 设备。 任何可以作为 GATT 外围设备运行的标准 BLE 设备都应可以使用，但你可能需要更新 GATT 特征 ID 和数据。 添加 SensorTag 设备的 MAC 地址：

```json
{
  "name": "SensorTag",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/ble/libble.so"
    }
  },
  "args": {
    "controller_index": 0,
    "device_mac_address": "<<AA:BB:CC:DD:EE:FF>>",
    "instructions": [
      {
        "type": "read_once",
        "characteristic_uuid": "00002A24-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A25-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A26-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A27-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A28-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A29-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "write_at_init",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AQ=="
      },
      {
        "type": "read_periodic",
        "characteristic_uuid": "F000AA01-0451-4000-B000-000000000000",
        "interval_in_ms": 1000
      },
      {
        "type": "write_at_exit",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AA=="
      }
    ]
  }
}
```

如果未使用 SensorTag 设备，请查看你的 BLE 设备的文档来确定你是否需要更新 GATT 特征 ID 和数据值。

#### IoT 中心模块
<a id="iot-hub-module" class="xliff"></a>

添加 IoT 中心的名称。 后缀值通常是 **azure-devices.net**：

```json
{
  "name": "IoTHub",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/iothub/libiothub.so"
    }
  },
  "args": {
    "IoTHubName": "<<Azure IoT Hub Name>>",
    "IoTHubSuffix": "<<Azure IoT Hub Suffix>>",
    "Transport" : "amqp"
  }
}
```

#### 标识映射模块配置
<a id="identity-mapping-module-configuration" class="xliff"></a>

添加 SensorTag 设备的 MAC 地址，以及添至 IoT 中心的 **SensorTag_01** 设备的设备 ID 和密钥：

```json
{
  "name": "mapping",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/identitymap/libidentity_map.so"
    }
  },
  "args": [
    {
      "macAddress": "<<AA:BB:CC:DD:EE:FF>>",
      "deviceId": "<<Azure IoT Hub Device ID>>",
      "deviceKey": "<<Azure IoT Hub Device Key>>"
    }
  ]
}
```

#### BLE 打印机模块配置
<a id="ble-printer-module-configuration" class="xliff"></a>

```json
{
  "name": "BLE Printer",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/samples/ble_gateway/ble_printer/libble_printer.so"
    }
  },
  "args": null
}
```

#### BLEC2D 模块配置
<a id="blec2d-module-configuration" class="xliff"></a>

```json
{
  "name": "BLEC2D",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/ble/libble_c2d.so"
    }
  },
  "args": null
}
```

#### 路由配置
<a id="routing-configuration" class="xliff"></a>

以下配置可确保 IoT Edge 模块间的以下路由：

* **Logger** 模块接收并记录所有消息。
* **SensorTag** 模块将消息发送到 **mapping** 和 **BLE Printer** 模块。
* **mapping** 模块将消息发送到 **IoTHub** 模块以向上发送到 IoT 中心。
* **IoTHub** 模块将消息发送回 **mapping** 模块。
* **mapping** 模块将消息发送到 **BLEC2D** 模块。
* **BLEC2D** 模块将消息发回 **Sensor Tag** 模块。

```json
"links" : [
    {"source" : "*", "sink" : "Logger" },
    {"source" : "SensorTag", "sink" : "mapping" },
    {"source" : "SensorTag", "sink" : "BLE Printer" },
    {"source" : "mapping", "sink" : "IoTHub" },
    {"source" : "IoTHub", "sink" : "mapping" },
    {"source" : "mapping", "sink" : "BLEC2D" },
    {"source" : "BLEC2D", "sink" : "SensorTag"}
 ]
```

若要运行示例，请将 JSON 配置文件的路径作为参数传递到 **ble\_gateway** 二进制文件。 以下命令假设你正在使用 **gateway_sample.json** 配置文件。 在 Raspberry Pi 上从 **iot-edge** 文件夹执行此命令：

```sh
./build/samples/ble_gateway/ble_gateway ./samples/ble_gateway/src/gateway_sample.json
```

在运行示例前，可能需要按 SensorTag 设备上的小按钮，使其可被发现。

运行示例时，可使用[设备资源管理器](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer)或 [iothub-explorer](https://github.com/Azure/iothub-explorer) 工具来监视 IoT Edge 网关从 SensorTag 设备转发的消息。 例如，可以使用 iothub-explorer 使用以下命令监视设备到云消息：

```sh
iothub-explorer monitor-events --login "HostName={Your iot hub name}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={Your IoT Hub key}"
```

## 发送“云到设备”消息
<a id="send-cloud-to-device-messages" class="xliff"></a>

BLE 模块还支持从 IoT 中心向设备发送命令。 可使用[设备资源管理器](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer)或 [iothub-explorer](https://github.com/Azure/iothub-explorer) 工具将转发 BLE 网关模块的 JSON 消息发送到 BLE 设备。

如果使用 Texas Instruments SensorTag 设备，可从 IoT 中心发送命令，打开红色 LED、绿色 LED 或蜂鸣器。 从 IoT 中心发送命令前，先按顺序发送以下两个 JSON 消息。 然后，可以发送任何命令，打开指示灯或蜂鸣器。

1. 重置所有 LED 和蜂鸣器（将它们关闭）：

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AA=="
    }
    ```

1. 将 I/O 配置为“远程”：

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA66-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

现在便可以发送以下任何命令，在 SensorTag 设备上打开指示灯或蜂鸣器：

* 打开红色 LED：

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

* 打开绿色 LED：

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "Ag=="
    }
    ```

* 打开蜂鸣器：

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "BA=="
    }
    ```

## 后续步骤
<a id="next-steps" class="xliff"></a>

如果想要深入了解 IoT Edge 并尝试一些代码示例，请访问以下开发人员教程和资源：

* [Azure IoT Edge][lnk-sdk]

若要进一步探索 IoT 中心的功能，请参阅：

* [IoT 中心开发人员指南][lnk-devguide]

<!-- Links -->
[lnk-ble-samplecode]: https://github.com/Azure/iot-edge/tree/master/samples/ble_gateway
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-sdk]: https://github.com/Azure/iot-edge/
[lnk-noobs]: https://www.raspberrypi.org/documentation/installation/noobs.md
[lnk-raspbian]: https://www.raspberrypi.org/downloads/raspbian/
[lnk-devguide]: iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 
[lnk-pi-ssh]: https://www.raspberrypi.org/documentation/remote-access/ssh/README.md
[lnk-ssh-windows]: https://www.raspberrypi.org/documentation/remote-access/ssh/windows.md
[lnk-ssh-linux]: https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md

