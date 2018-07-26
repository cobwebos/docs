---
title: 如何在 Linux 上安装 Azure IoT Edge | Microsoft Docs
description: ARM32 上的 Linux 上的 Azure IoT Edge 安装说明
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: 5b5212d5e1663fee01ff87642432818071d4f4dd
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38988528"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>在 Linux 上安装 Azure IoT Edge 运行时 (ARM32v7/armhf)

Azure IoT Edge 运行时部署在所有 IoT Edge 设备上。 它有三个组件。 **IoT Edge 安全守护程序**提供和维护 Edge 设备上的安全标准。 守护程序在每次开机时启动，并通过启动 IoT Edge 代理来启动设备。 **IoT Edge 代理**协助部署和监视 Edge 设备（包括 IoT Edge 中心）上的模块。 IoT Edge 中心管理 IoT Edge 设备模块之间以及设备和 Azure IoT 中心之间的通信。

本文列出了在 Linux ARM32v7/armhf Edge 设备（例如 Raspberry Pi）上安装 Azure IoT Edge 运行时的步骤。

>[!NOTE]
>Linux 软件存储库中的包受到每个包中的许可条款限制 (/usr/share/doc/*package-name*)。 使用程序包之前请阅读许可条款。 安装和使用程序包即表示接受这些条款。 如果不同意许可条款，则不要使用程序包。

## <a name="install-the-container-runtime"></a>安装容器运行时

Azure IoT Edge 依赖于 [OCI 兼容的][lnk-oci]容器运行时。 对于生产方案，强烈建议使用下面提供的[基于 Moby][lnk-moby] 的引擎。 这是官方唯一支持用于 Azure IoT Edge 的容器引擎。 Docker CE/EE 容器映像与基于 Moby 的运行时兼容。

以下命令用于安装基于 Moby 的引擎和命令行接口 (CLI)。 CLI 对开发非常有用，但对生产部署来说是可选的。

```cmd/sh

# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the moby-engine
curl -L https://aka.ms/moby-engine-armhf-latest -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb

# Download and install the moby-cli
curl -L https://aka.ms/moby-cli-armhf-latest -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb

# Run apt-get fix
sudo apt-get install -f

```

## <a name="install-the-iot-edge-security-daemon"></a>安装 IoT Edge 安全守护程序

```cmd/sh
# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the standard libiothsm implementation
curl -L https://aka.ms/libiothsm-std-linux-armhf-latest -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb

# Download and install the IoT Edge Security Daemon
curl -L https://aka.ms/iotedged-linux-armhf-latest -o iotedge.deb && sudo dpkg -i ./iotedge.deb

# Run apt-get fix
sudo apt-get install -f
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>配置 Azure IoT Edge 安全守护程序


可以使用 `/etc/iotedge/config.yaml` 处的配置文件配置守护程序。 默认情况下，该文件有写保护，你可能需要提升权限才能对其进行编辑。

```bash
sudo nano /etc/iotedge/config.yaml
```

可以使用[设备连接字符串][lnk-dcs]手动配置 edge 设备或[通过设备预配服务自动][lnk-dps]配置。

* 对于手动配置，取消注释**手动**预配模式。 使用 IoT Edge 设备的连接字符串更新 **device_connection_string** 的值。

   ```yaml
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # provisioning: 
   #   source: "dps"
   #   global_endpoint: "https://global.azure-devices-provisioning.net"
   #   scope_id: "{scope_id}"
   #   registration_id: "{registration_id}"
   ```

* 对于自动配置，取消注释 **dps** 预配模式。 使用 IoT 中心 DPS 实例中的值更新 **scope_id** 和 **registration_id** 的值，并使用 TPM 更新 IoT Edge 设备。 

   ```yaml
   # provisioning:
   #   source: "manual"
   #   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   provisioning: 
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "{scope_id}"
     registration_id: "{registration_id}"
   ```

在配置中输入预配信息后，重启守护程序：

```cmd/sh
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>验证是否成功安装

如果使用了上一部分中的**手动配置**步骤，则应在设备上成功预配并运行 IoT Edge 运行时。 如果使用了**自动配置**步骤，则需要完成一些额外的步骤，以便运行时可以代表你向 IoT 中心注册你的设备。 有关后续步骤，请参阅[在 Linux 虚拟机上创建和预配模拟 TPM Edge 设备](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm)。

使用以下命令检查 IoT Edge 守护程序的状态：

```cmd/sh
systemctl status iotedge
```

使用以下命令观察守护程序日志：

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

此外，还可使用以下命令列出正在运行的模块：

```cmd/sh
sudo iotedge list
```
>[!NOTE]
>在像 RaspberryPi 这样的资源受限设备上，强烈建议按照[故障排除指南][lnk-trouble]中的说明将 *OptimizeForPerformance* 环境变量设置为 *false*。


## <a name="next-steps"></a>后续步骤

如果在正确安装 Edge 运行时期间遇到问题，请查看[疑难解答][lnk-trouble]页面。

<!-- Links -->
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-linux.md
[lnk-trouble]: https://docs.microsoft.com/azure/iot-edge/troubleshoot#stability-issues-on-resource-constrained-devices
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
