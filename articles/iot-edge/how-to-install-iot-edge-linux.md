---
title: 在 Linux 上安装 Azure IoT Edge | Microsoft Docs
description: 运行 Ubuntu 或 Raspbian 的 Linux 设备上的 Azure IoT Edge 安装说明
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: kgremban
ms.openlocfilehash: 4a8725e3ba7be2dc572798d1397e098046a4b352
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510220"
---
# <a name="install-the-azure-iot-edge-runtime-on-debian-based-linux-systems"></a>在基于 Debian 的 Linux 系统上安装 Azure IoT Edge 运行时

使用 Azure IoT Edge 运行时可将设备转变为 IoT Edge 设备。 该运行时可以部署在像 Raspberry Pi 一样小的设备上，也可以部署在像工业服务器一样大的设备上。 使用 IoT Edge 运行时配置设备后，即可开始从云中部署业务逻辑。 若要了解详细信息，请参阅[了解 Azure IoT Edge 运行时及其体系结构](iot-edge-runtime.md)。

本文列出了在 X64、ARM32 或 ARM64 Linux 设备上安装 Azure IoT Edge 运行时的步骤。 为 Ubuntu Server 16.04、Ubuntu Server 18.04 和 Raspbian Stretch 提供安装包。 有关受支持的 Linux 操作系统和体系结构的列表，请参阅[Azure IoT Edge 支持的系统](support.md#operating-systems)。

>[!NOTE]
>ARM64 设备支持[公共预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

> [!NOTE]
> Linux 软件存储库中的包受到每个包中的许可条款限制 (/usr/share/doc/*package-name*)。 使用程序包之前请阅读许可条款。 安装和使用程序包即表示接受这些条款。 如果不同意许可条款，则不要使用程序包。

## <a name="install-the-latest-runtime-version"></a>安装最新的运行时版本

使用以下部分将 Azure IoT Edge 运行时的最新版本安装到你的设备上。

### <a name="register-microsoft-key-and-software-repository-feed"></a>注册 Microsoft 密钥和软件存储库源

准备设备以便 IoT Edge 运行时安装。

安装存储库配置。 选择与设备操作系统匹配的**16.04**或**18.04**命令：

* **Ubuntu Server 16.04**：

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Ubuntu Server 18.04**：

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Raspbian Stretch**：

   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

复制生成的列表。

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

安装 Microsoft GPG 公钥

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-the-container-runtime"></a>安装容器运行时

Azure IoT Edge 依赖于 [OCI 兼容的](https://www.opencontainers.org/)容器运行时。 对于生产方案，建议使用下面提供的[基于小鲸鱼](https://mobyproject.org/)的引擎。 这是官方唯一支持用于 Azure IoT Edge 的容器引擎。 Docker CE/EE 容器映像与 Moby 运行时兼容。

执行 apt 更新。

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

如果在安装小鲸鱼容器运行时遇到错误，请按照本文后面所述的步骤[验证 Linux 内核的小鲸鱼兼容性](#verify-your-linux-kernel-for-moby-compatibility)。

### <a name="install-the-azure-iot-edge-security-daemon"></a>安装 Azure IoT Edge 安全守护程序

**IoT Edge 安全守护**程序在 IoT Edge 设备上提供并维护安全标准。 守护程序在每次开机时启动，并通过启动 IoT Edge 运行时的其余部分来启动设备。

安装命令还会安装标准版本的**libiothsm** （如果尚未存在）。

执行 apt 更新。

   ```bash
   sudo apt-get update
   ```

安装安全守护程序。 该包安装在 `/etc/iotedge/`。

   ```bash
   sudo apt-get install iotedge
   ```

成功安装 IoT Edge 后，输出会提示你更新配置文件。 按照[配置安全守护](#configure-the-security-daemon)程序部分中的步骤完成设备的预配。

## <a name="install-a-specific-runtime-version"></a>安装特定的运行时版本

如果要安装特定版本的小鲸鱼和 Azure IoT Edge 运行时，而不是使用最新版本，则可以直接从 IoT Edge GitHub 存储库定位组件文件。 使用以下步骤获取设备上的所有 IoT Edge 组件：小鲸鱼引擎和 CLI、libiothsm，最后 IoT Edge 安全守护程序。 如果你不想更改到特定运行时版本，请跳到下一节 "[配置安全守护](#configure-the-security-daemon)程序"。

1. 导航到[Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)版本，并找到要以其为目标的发布版本。 

2. 展开该版本的 "**资产**" 部分。

3. 在任何给定版本中，小鲸鱼引擎可能有也可能没有更新。 如果看到以**小鲸鱼**和**小鲸鱼**开头的文件，请使用以下命令更新这些组件。 如果看不到任何小鲸鱼文件，请返回到旧版本的资产，直到找到最新版本。 

   1. 查找与 IoT Edge 设备体系结构匹配的**小鲸鱼**文件。 右键单击文件链接，并复制链接地址。

   2. 使用以下命令中复制的链接安装该版本的小鲸鱼引擎： 

      ```bash
      curl -L <moby-engine link> -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb
      ```

   3. 查找与 IoT Edge 设备体系结构匹配的**小鲸鱼**文件。 小鲸鱼 CLI 是一个可选组件，但在开发过程中可能会有所帮助。 右键单击文件链接，并复制链接地址。 

   4. 使用以下命令中复制的链接安装该版本的小鲸鱼 CLI： 

      ```bash
      curl -L <moby-cli link> -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb
      ```

4. 每个版本都应有 IoT Edge 安全守护程序和 hsmlib 的新文件。 使用以下命令更新这些组件。 

   1. 查找符合 IoT Edge 设备体系结构的**libiothsm 标准**文件。 右键单击文件链接，并复制链接地址。 

   2. 使用以下命令中的复制链接来安装该版本的 hsmlib：

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```

   3. 查找与 IoT Edge 设备体系结构匹配的**iotedge**文件。 右键单击文件链接，并复制链接地址。 

   4. 使用以下命令中的复制链接来安装该版本的 IoT Edge 安全守护程序。 

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

成功安装 IoT Edge 后，输出会提示你更新配置文件。 按照下一部分中的步骤完成设备的预配。 

## <a name="configure-the-security-daemon"></a>配置安全守护程序

配置 IoT Edge 运行时以将物理设备与 Azure IoT 中心中存在的设备标识相链接。

可以使用 `/etc/iotedge/config.yaml` 处的配置文件配置守护程序。 默认情况下，该文件有写保护，你可能需要提升权限才能对其进行编辑。

可以使用 IoT 中心提供的设备连接字符串手动预配单个 IoT Edge 设备。 或者，可以使用设备预配服务自动预配设备，当需要预配多个设备时这会非常有用。 根据预配选项，选择合适的安装脚本。

### <a name="option-1-manual-provisioning"></a>选项 1：手动预配

若要手动预配设备，需要为其提供[设备连接字符串](how-to-register-device.md#register-in-the-azure-portal)，可以通过在 IoT 中心注册新设备来创建该设备连接字符串。

打开配置文件。

```bash
sudo nano /etc/iotedge/config.yaml
```

查找文件的预配配置，并取消注释 "**手动设置配置**" 部分。 使用 IoT Edge 设备的连接字符串更新 **device_connection_string** 的值。 请确保注释掉任何其他预配部分。请确保**预配：** 行没有前面的空格，并且嵌套项按两个空格缩进。

   ```yaml
   # Manual provisioning configuration
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # DPS TPM provisioning configuration
   # provisioning:
   #   source: "dps"
   #   global_endpoint: "https://global.azure-devices-provisioning.net"
   #   scope_id: "{scope_id}"
   #   attestation:
   #     method: "tpm"
   #     registration_id: "{registration_id}"
   ```

若要将剪贴板内容粘贴到 Nano `Shift+Right Click` 或按 `Shift+Insert`。

保存并关闭该文件。

   `CTRL + X`、`Y`、`Enter`

在配置文件中输入预配信息后，重启守护程序：

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>选项 2：自动预配

若要自动预配设备，请[设置设备预配服务并检索设备注册 ID](how-to-auto-provision-simulated-device-linux.md)。 当使用自动预配时，IoT Edge 支持多种证明机制，但你的硬件要求也会影响你的选择。 例如，默认情况下，Raspberry Pi 设备不附带受信任的平台模块（TPM）芯片。

打开配置文件。

```bash
sudo nano /etc/iotedge/config.yaml
```

查找文件的预配配置，并取消注释适用于证明机制的部分。 例如，使用 TPM 证明时，请分别使用 IoT 中心设备预配服务和 IoT Edge 设备的值更新**scope_id**和**registration_id**值。 请确保**预配：** 行没有前面的空格，并且嵌套项按两个空格缩进。

   ```yaml
   # Manual provisioning configuration
   # provisioning:
   #   source: "manual"
   #   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "{scope_id}"
     attestation:
       method: "tpm"
       registration_id: "{registration_id}"
   ```

若要将剪贴板内容粘贴到 Nano `Shift+Right Click` 或按 `Shift+Insert`。

保存并关闭该文件。

   `CTRL + X`、`Y`、`Enter`

在配置文件中输入预配信息后，重启守护程序：

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>验证是否成功安装

如果使用了上一部分中的**手动配置**步骤，则应在设备上成功预配并运行 IoT Edge 运行时。 如果使用了**自动配置**步骤，则需要完成一些额外的步骤，以便运行时可以代表你向 IoT 中心注册你的设备。 有关后续步骤，请参阅[在 Linux 虚拟机上创建和预配模拟的 TPM IoT Edge 设备](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm)。

可以检查 IoT Edge 守护程序的状态：

```bash
systemctl status iotedge
```

检查守护程序日志：

```bash
journalctl -u iotedge --no-pager --no-full
```

针对最常见的配置和网络错误运行自动检查： 

```bash
sudo iotedge check
```

在你将第一个模块部署到设备上 IoT Edge 之前， **$edgeHub**系统模块将不会部署到设备。 因此，自动检查将为 `Edge Hub can bind to ports on host` 连接性检查返回错误。 除非在将模块部署到设备后发生此错误，否则可以忽略此错误。

最后，列出正在运行的模块：

```bash
sudo iotedge list
```

在设备上安装 IoT Edge 后，你应该看到运行的唯一模块是**edgeAgent**。 创建第一个部署后，另一个系统模块 **$edgeHub**也会在设备上启动。 有关详细信息，请参阅[部署 IoT Edge 模块](how-to-deploy-modules-portal.md)。

## <a name="tips-and-troubleshooting"></a>提示和故障排除

需要提升权限才能运行 `iotedge` 命令。 安装运行时后，请从计算机中注销并重新登录以自动更新权限。 在此之前，在任何 `iotedge` 命令前都要使用 **sudo**。

在资源受限的设备上，强烈建议按照[故障排除指南](troubleshoot.md)中的说明将 *OptimizeForPerformance* 环境变量设置为 *false*。

如果网络具有代理服务器，请按照[配置 IoT Edge 设备以通过代理服务器进行通信](how-to-configure-proxy-support.md)中的步骤进行操作。

### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>验证 Linux 内核的小鲸鱼兼容性

许多嵌入式设备制造商提供了包含自定义 Linux 内核的设备映像，但没有容器运行时兼容性所需的功能。 如果在安装建议的小鲸鱼容器运行时遇到问题，可以使用官方[小鲸鱼 GitHub 存储库](https://github.com/moby/moby)中的[检查-Config](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh)脚本对 Linux 内核配置进行故障排除。 在设备上运行以下命令以检查内核配置：

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

这会提供详细的输出，其中包含小鲸鱼运行时使用的内核功能的状态。 你将需要确保启用 `Generally Necessary` 和 `Network Drivers` 下的所有项，以确保内核与小鲸鱼运行时完全兼容。  如果已确定任何缺少的功能，请通过从源重建内核并选择要包含在适当的内核中的关联模块来启用它们。 同样，如果使用的是 defconfig 或 menuconfig 等内核配置生成器，请查找并启用相应的功能，并相应地重建内核。  部署新修改的内核后，再次运行检查-config 脚本，验证是否已成功启用所有必需的功能。


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

如果 IoT Edge 运行时的安装有问题，请查看[故障排除](troubleshoot.md)页。

若要将现有安装更新到最新版本的 IoT Edge，请参阅[更新 IoT Edge 安全守护程序和运行时](how-to-update-iot-edge.md)。
