---
title: 在 Linux 上安装 Azure IoT Edge | Microsoft Docs
description: 在运行 Ubuntu 的 Linux AMD64 设备上安装 Azure IoT Edge 的相关说明
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: 9d5d6fc436c48bd36e2da64d2b4c7c811e3ddc4f
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2019
ms.locfileid: "55993344"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>在 Linux 上安装 Azure IoT Edge 运行时 (x64)

使用 Azure IoT Edge 运行时可将设备转变为 IoT Edge 设备。 该运行时可以部署在像 Raspberry Pi 一样小的设备上，也可以部署在像工业服务器一样大的设备上。 使用 IoT Edge 运行时配置设备后，即可开始从云中部署业务逻辑。

若要了解有关 IoT Edge 运行时如何工作以及包含哪些组件的详细信息，请参阅[了解 Azure IoT Edge 运行时及其体系结构](iot-edge-runtime.md)。

本文列出了在 Linux x64 (Intel/AMD) Edge 设备上安装 Azure IoT Edge 运行时的步骤。 有关当前支持的 AMD64 操作系统列表，请参阅 [Azure IoT Edge 支持](support.md#operating-systems)。

> [!NOTE]
> Linux 软件存储库中的包受到每个包中的许可条款限制 (/usr/share/doc/*package-name*)。 使用程序包之前请阅读许可条款。 安装和使用程序包即表示接受这些条款。 如果不同意许可条款，则不要使用包。

## <a name="register-microsoft-key-and-software-repository-feed"></a>注册 Microsoft 密钥和软件存储库源

准备设备以进行IoT Edge运行时安装，将 ```<release>``` 替换为适用于 Ubuntu 版本的 **16.04** 或 **18.04**。

```bash
# Install repository configuration
curl https://packages.microsoft.com/config/ubuntu/<release>/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/

# Perform apt update
sudo apt-get update
```

## <a name="install-the-container-runtime"></a>安装容器运行时

Azure IoT Edge 依赖于 [OCI 兼容的](https://www.opencontainers.org/)容器运行时。 对于生产方案，强烈建议使用下面提供的[基于 Moby](https://mobyproject.org/) 的引擎。 这是官方唯一支持用于 Azure IoT Edge 的容器引擎。 Docker CE/EE 容器映像与 Moby 运行时兼容。

更新 apt-get。

```bash
sudo apt-get update
```

安装 Moby 引擎。

```bash
sudo apt-get install moby-engine
```

安装 Moby 命令行接口 (CLI)。 CLI 对开发非常有用，但对生产部署来说是可选的。

```bash
sudo apt-get install moby-cli
```

## <a name="install-the-azure-iot-edge-security-daemon"></a>安装 Azure IoT Edge 安全守护程序

**IoT Edge 安全守护程序**提供和维护 Edge 设备上的安全标准。 守护程序在每次开机时启动，并通过启动 IoT Edge 运行时的其余部分来启动设备。

安装命令还会安装标准版本的 **iothsmlib**（如果尚未存在）。

更新 apt-get。

```bash
sudo apt-get update
```

安装安全守护程序。 该包安装在 `/etc/iotedge/`。

```bash
sudo apt-get install iotedge
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>配置 Azure IoT Edge 安全守护程序

配置 IoT Edge 运行时以将物理设备与 Azure IoT 中心中存在的设备标识相链接。

可以使用 `/etc/iotedge/config.yaml` 处的配置文件配置守护程序。 默认情况下，该文件有写保护，你可能需要提升权限才能对其进行编辑。

可以使用 IoT 中心提供的设备连接字符串手动预配单个 IoT Edge 设备。 或者，可以使用设备预配服务自动预配设备，当需要预配多个设备时这会非常有用。 根据预配选项，选择合适的安装脚本。

### <a name="option-1-manual-provisioning"></a>选项 1：手动预配

若要手动预配设备，需要为其提供[设备连接字符串](how-to-register-device-portal.md)，可以通过在 IoT 中心注册新设备来创建该设备连接字符串。

打开配置文件。

```bash
sudo nano /etc/iotedge/config.yaml
```

找到文件的 provisioning 节并取消注释 **manual** 预配模式。 使用 IoT Edge 设备的连接字符串更新 **device_connection_string** 的值。

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

保存并关闭该文件。

   `CTRL + X`、`Y`、`Enter`

在配置文件中输入预配信息后，重启守护程序：

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>选项 2：自动预配

若要自动预配设备，请[设置设备预配服务并检索设备注册 ID](how-to-auto-provision-simulated-device-linux.md)。 自动预配仅适用于具有受信任的平台模块 (TPM) 芯片的设备。 例如，默认情况下，Raspberry Pi 设备未附带 TPM。

打开配置文件。

```bash
sudo nano /etc/iotedge/config.yaml
```

找到文件的预配部分并取消注释 **dps** 预配模式。 使用 IoT 中心设备预配服务中的值更新 **scope_id** 和 **registration_id** 的值，并使用 TPM 更新 IoT Edge 设备。

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

保存并关闭该文件。

   `CTRL + X`、`Y`、`Enter`

在配置文件中输入预配信息后，重启守护程序：

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>验证是否成功安装

如果使用了上一部分中的**手动配置**步骤，则应在设备上成功预配并运行 IoT Edge 运行时。 如果使用了**自动配置**步骤，则需要完成一些额外的步骤，以便运行时可以代表你向 IoT 中心注册你的设备。 有关后续步骤，请参阅[在 Linux 虚拟机上创建和预配模拟 TPM Edge 设备](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm)。

使用以下命令检查 IoT Edge 守护程序的状态：

```bash
systemctl status iotedge
```

使用以下命令观察守护程序日志：

```bash
journalctl -u iotedge --no-pager --no-full
```

此外，还可使用以下命令列出正在运行的模块：

```bash
sudo iotedge list
```

## <a name="tips-and-suggestions"></a>提示和建议

需要提升的权限才能运行 `iotedge` 命令。 安装运行时后，请从计算机中注销并重新登录以自动更新权限。 在此之前，在任何 `iotedge` 命令前都要使用 **sudo**。

在资源受限的设备上，强烈建议按照[故障排除指南](troubleshoot.md)中的说明将 *OptimizeForPerformance* 环境变量设置为 *false*。

如果网络具有代理服务器，请按照[配置 IoT Edge 设备以通过代理服务器进行通信](how-to-configure-proxy-support.md)中的步骤进行操作。

## <a name="uninstall-iot-edge"></a>卸载 IoT Edge

如果要从 Linux 设备中删除 IoT Edge 安装，请从命令行使用以下命令。

删除 IoT Edge 运行时。

```bash
sudo apt-get remove --purge iotedge
```

删除 IoT Edge 运行时以后，已创建的容器会被停止，但仍存在于设备上。 查看所有容器以了解哪些容器仍然存在。

```bash
sudo docker ps -a
```

从设备中删除容器，包括两个运行时容器。

```bash
sudo docker rm -f <container name>
```

最后，从设备中删除容器运行时。

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>后续步骤

预配了安装运行时的 IoT Edge 设备后，现在可以[部署 IoT Edge 模块](how-to-deploy-modules-portal.md)。

如果无法正确安装 Edge 运行时，请参阅[故障排除](troubleshoot.md)页。

若要将现有安装更新到最新版本的 IoT Edge，请参阅[更新 IoT Edge 安全守护程序和运行时](how-to-update-iot-edge.md)。
