---
title: 教程 - 在 Azure IoT Central 中创建视频分析 IoT Edge 实例 (Intel NUC)
description: 本教程演示如何创建视频分析 IoT Edge 实例，以便与视频分析 - 对象和运动检测应用程序模板一起使用。
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/27/2020
ms.openlocfilehash: 4ecce689e287673a3b08f8f90f87c28e021106d6
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2020
ms.locfileid: "88037821"
---
# <a name="tutorial-create-an-iot-edge-instance-for-video-analytics-intel-nuc"></a>教程：创建用于视频分析的 IoT Edge 实例 (Intel NUC)

Azure IoT Edge 是一项完全托管的服务，可通过部署和运行以下服务在本地提供云智能：

* 自定义逻辑
* Azure 服务
* 人工智能

在 IoT Edge 中，这些服务直接在跨平台 IoT 设备上运行，从而使你能够安全地、大规模地在云中或脱机运行 IoT 解决方案。

本教程演示如何在 Intel NUC 设备上安装和配置 IoT Edge 运行时。

在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
> * 更新和配置 IoT Edge
> * 设置 IoT Edge 网关
> * 将本地 ONVIF 兼容相机连接到 Intel NUC 设备

## <a name="prerequisites"></a>先决条件

* 在开始之前，应该先完成上一教程 - [在 Azure IoT Central 中创建实时视频分析应用程序](./tutorial-video-analytics-create-app.md)。
* 一个运行 Linux 的设备（如 Intel NUC），该设备可以运行 Docker 容器，并且具有足够的处理能力来运行视频分析。
* 已在设备上[安装 IoT Edge 运行时](../../iot-edge/how-to-install-iot-edge-linux.md)并运行。
* 可以从 Windows 计算机连接到 IoT Edge 设备，你需要 [PuTTY SSH 客户端](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)或等效的实用工具。
* 还需要一个 Azure 订阅。 如果没有 Azure 订阅，则可在 [Azure 注册页](https://aka.ms/createazuresubscription)上免费创建一个。

## <a name="configure-the-iot-edge-device"></a>配置 IoT Edge 设备

如果 Intel NUC 计算机上未安装 IoT Edge 运行时，请参阅[在基于 Debian 的 Linux 系统上安装 Azure IoT Edge 运行时](../../iot-edge/how-to-install-iot-edge-linux.md)说明。

若要更新 IoT Edge 运行时：

1. 使用 PuTTY 实用工具连接到 IoT Edge 设备。

1. 运行以下命令更新和检查 IoT Edge 运行时版本。 撰写本文时，版本为 1.0.9：

    ```bash
    sudo apt-get update
    sudo apt-get install libiothsm iotedge
    sudo iotedge --version
    ```

1. 使用以下命令创建部署所用的具有必需权限的文件夹：

    ```bash
    sudo mkdir -p /data/storage
    sudo mkdir -p /data/media
    sudo chmod -R 777 /data
    ```

若要将 state.json 配置文件添加到 IoT Edge 设备上的 /data/storage 文件夹中，请执行以下操作 ：

1. 使用文本编辑器打开本地计算机上 lva-configuration 文件夹中的 state.json 文件 。

1. 将 `system` 和 `iotCentral > properties` 占位符更新为描述你的网关设备的字符串值。 稍后可在 IoT Central 应用程序仪表板中查看这些值。

1. 将 `iotCentral > appKeys` 占位符更新为你在上一教程的 scratchpad.txt 文件中记下的值。 保存更改。

1. 在命令提示符下使用 PuTTY `scp` 实用工具将刚刚编辑的 state.json 文件复制到 IoT Edge 设备上的 /data/storage 文件夹中 。 此示例使用 `192.168.0.144` 作为 IP 地址示例，请将其替换为你的 IoT Edge 设备的 IP 地址：

    ```cmd
    scp state.json YourUserName@192.168.0.144:/data/storage/state.json`
    ```

配置 IoT Edge 以注册并连接到 IoT Central 应用程序：

1. 使用 PuTTY 实用工具连接到 IoT Edge 设备。

1. 使用文本编辑器（如 `nano`）打开 IoT Edge config.yaml 文件。

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

    > [!WARNING]
    > YAML 文件无法使用制表符进行缩进，请改用两个空格。 顶层项不能有前导空格。

1. 向下滚动，直到看到 `# Manual provisioning configuration`。 注释掉接下来的三行，如以下代码片段中所示：

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "temp"
    ```

1. 向下滚动，直到看到 `# DPS symmetric key provisioning configuration`。 取消注释接下来的八行，如以下代码片段中所示：

    ```yaml
    # DPS symmetric key provisioning configuration
    provisioning:
      source: "dps"
      global_endpoint: "https://global.azure-devices-provisioning.net"
      scope_id: "{scope_id}"
      attestation:
        method: "symmetric_key"
        registration_id: "{registration_id}"
        symmetric_key: "{symmetric_key}"
    ```

1. 将 `{scope_id}` 替换为在上一教程中的 scratchpad.txt 文件中记下的“ID 范围”。

1. 将 `{registration_id}` 替换为在上一教程中创建的设备 lva-gateway-001。

1. 将 `{symmetric_key}` 替换为在上一教程中的 scratchpad.txt 文件中记下的 lva-gateway-001 设备的“主密钥” 。

1. 运行以下命令重启 IoT Edge 守护程序：

    ```bash
    sudo systemctl restart iotedge
    ```

1. 若要检查 IoT Edge 模块的状态，请运行以下命令：

    ```bash
    iotedge list
    ```

    上一命令的输出显示有五个正在运行的模块。 你还可以在 IoT Central 应用程序中查看正在运行的模块的状态。

    > [!TIP]
    > 你可以重新运行此命令来检查状态。 可能需要等待所有模块开始运行。

如果 IoT Edge 模块不能正常启动，请参阅[排查 IoT Edge 设备故障](../../iot-edge/troubleshoot.md)。

## <a name="collect-the-rstp-stream-from-your-camera"></a>收集相机中的 RSTP 流

标识连接到 IoT Edge 设备的相机的 RTSP 流 URL，例如：

`rtsp://192.168.1.64:554/Streaming/Channels/101/`

> [!TIP]
> 尝试使用媒体播放器（如 VLC）在 IoT Edge 计算机上查看相机流。

## <a name="next-steps"></a>后续步骤

现已将 IoT Edge 运行时和 LVA 模块部署到 Intel NUC 网关设备。

若要管理相机，请按照下一教程操作：

> [!div class="nextstepaction"]
> [监视和管理视频分析 - 对象和运动检测应用程序](./tutorial-video-analytics-manage.md)
