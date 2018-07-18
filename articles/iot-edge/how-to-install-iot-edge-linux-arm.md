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
ms.openlocfilehash: ad70fcc6b9779cb33772a3fce2fb11b4cec804ee
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37062592"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>在 Linux 上安装 Azure IoT Edge 运行时 (ARM32v7/armhf)

Azure IoT Edge 运行时部署在所有 IoT Edge 设备上。 它有三个组件。 **IoT Edge 安全守护程序**提供和维护 Edge 设备上的安全标准。 守护程序在每次开机时启动，并通过启动 IoT Edge 代理来启动设备。 **IoT Edge 代理**协助部署和监视 Edge 设备（包括 IoT Edge 中心）上的模块。 IoT Edge 中心管理 IoT Edge 设备模块之间以及设备和 Azure IoT 中心之间的通信。

本文列出了在 Linux ARM32v7/armhf Edge 设备（例如 Raspberry Pi）上安装 Azure IoT Edge 运行时的步骤。

>[!NOTE]
>Linux 软件存储库中的包受到每个包中的许可条款限制 (/usr/share/doc/*package-name*)。 使用程序包之前请阅读许可条款。 安装和使用程序包即表示接受这些条款。 如果不同意许可条款，则不要使用程序包。

## <a name="install-the-container-runtime"></a>安装容器运行时

Azure IoT Edge 依赖于 [OCI 兼容的][lnk-oci]容器运行时（例如 Docker）。 如果 Edge 设备上已安装 Docker CE/EE，则可继续使用它结合 Azure IoT Edge 进行开发和测试。 

对于生产方案，强烈建议使用下面提供的[基于 Moby][lnk-moby] 的引擎。 这是官方唯一支持用于 Azure IoT Edge 的容器引擎。 Docker CE/EE 容器映像与 Moby 运行时完全兼容。

以下命令用于安装 moby 引擎和命令行接口 (CLI)。 CLI 对开发非常有用，但对生产部署来说是可选的。

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

可使用位于 `/etc/iotedge/config.yaml` 的配置文件配置守护程序。可<!--[automatically via Device Provisioning Service][lnk-dps] or-->配置边缘设备或使用[设备连接字符串][lnk-dcs]手动配置。

对于手动配置，请在 **config.yaml** 的 **provisioning** 部分中输入设备连接字符串

```yaml
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

*该文件默认不可写，因此可能需要使用 `sudo` 来编辑它。例如 `sudo nano /etc/iotedge/config.yaml`*

在配置中输入预配信息后，重启守护程序：

```cmd/sh
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>验证是否成功安装

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
iotedge list
```

## <a name="next-steps"></a>后续步骤

如果在正确安装 Edge 运行时期间遇到问题，请查看[疑难解答][lnk-trouble]页面。

<!-- Links -->
[lnk-dcs]: ../iot-hub/quickstart-send-telemetry-dotnet.md#register-a-device
[lnk-dps]: how-to-simulate-dps-tpm.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md