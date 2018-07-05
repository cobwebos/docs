---
title: 如何在 Linux 上安装 Azure IoT Edge | Microsoft Docs
description: Linux 上的 Azure IoT Edge 安装说明
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: 43f82341a3cc9d2163afd35e42864aaa7866b1b2
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37034668"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>在 Linux 上安装 Azure IoT Edge 运行时 (x64)

所有 IoT Edge 设备均部署有 Azure IoT Edge 运行时。 它有三个组件。 IoT Edge 安全守护程序提供和维护 Edge 设备上的安全标准。 守护程序在每次开机时启动，并通过启动 IoT Edge 代理启动设备。 IoT Edge 代理协助部署和监视 Edge 设备（包括 IoT Edge 中心）上的模块。 IoT Edge 中心管理 IoT Edge 设备模块之间以及设备和 Azure IoT 中心之间的通信。

本文列出了在 Linux x64 (Intel/AMD) Edge 设备上安装 Azure IoT Edge 运行时的步骤。

>[!NOTE]
>Linux 软件存储库中的包受到每个包中的许可条款限制 (/usr/share/doc/package-name)。 使用包之前请阅读许可条款。 安装和使用包即表示接受这些条款。 如果不同意许可条款，则不要使用包。

## <a name="register-microsoft-key-and-software-repository-feed"></a>注册 Microsoft 密钥和软件存储库源

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```cmd/sh
# Install repository configuration
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```cmd/sh
# Install repository configuration
curl https://packages.microsoft.com/config/ubuntu/18.04/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

### <a name="debian-9"></a>Debian 9

```cmd/sh
# Install repository configuration
curl https://packages.microsoft.com/config/debian/9/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

## <a name="install-the-container-runtime"></a>安装容器运行时 

Azure IoT Edge 依赖于 [OCI 兼容的][lnk-oci]容器运行时（例如 Docker）。 如果 Edge 设备上已安装 Docker CE/EE，则可继续使用它结合 Azure IoT Edge 进行开发和测试。 

对于生产方案，强烈建议使用下面提供的[基于 Moby][lnk-moby] 的引擎。 这是官方唯一支持用于 Azure IoT Edge 的容器引擎。 Docker CE/EE 容器映像与 Moby 运行时完全兼容。

以下说明用于安装 Moby 引擎和命令行接口 (CLI)*。CLI 对开发非常有用，但对生产部署来说是可选的。*

```cmd/sh
sudo apt-get update
sudo apt-get install moby-engine
sudo apt-get install moby-cli
```

## <a name="install-the-azure-iot-edge-security-daemon"></a>安装 Azure IoT Edge 安全守护程序

以下命令还将安装 iothsmlib 的标准版本（如果尚未存在）。

```cmd/sh
sudo apt-get update
sudo apt-get install iotedge
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>配置 Azure IoT Edge 安全守护程序

可使用位于 `/etc/iotedge/config.yaml` 的配置文件配置守护程序。可<!--[automatically via Device Provisioning Service][lnk-dps] or-->配置边缘设备或使用[设备连接字符串][lnk-dcs]手动配置。

对于手动配置，请在 config.yaml 的 provisioning 部分输入设备连接字符串

```yaml
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

该文件默认不可写，因此可能需要使用 `sudo` 来编辑它 *。例如 `sudo nano /etc/iotedge/config.yaml`*

在配置中输入预配信息后，重启守护程序：

```cmd/sh
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>验证是否安装成功

可通过下述项查看 IoT Edge 守护程序的状态：

```cmd/sh
systemctl status iotedge
```

通过下述项检查守护程序日志：

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

还可使用下述项列出正在运行的模块：

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
