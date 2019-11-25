---
title: 在 Linux 上安装 Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge installation instructions on Linux devices running Ubuntu or Raspbian
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: kgremban
ms.openlocfilehash: ec463efb1282c311757bb90fd614e1247459c80f
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457324"
---
# <a name="install-the-azure-iot-edge-runtime-on-debian-based-linux-systems"></a>Install the Azure IoT Edge runtime on Debian-based Linux systems

使用 Azure IoT Edge 运行时可将设备转变为 IoT Edge 设备。 该运行时可以部署在像 Raspberry Pi 一样小的设备上，也可以部署在像工业服务器一样大的设备上。 使用 IoT Edge 运行时配置设备后，即可开始从云中部署业务逻辑。 To learn more, see [Understand the Azure IoT Edge runtime and its architecture](iot-edge-runtime.md).

This article lists the steps to install the Azure IoT Edge runtime on an X64, ARM32, or ARM64 Linux device. Installation packages are provided for Ubuntu Server 16.04, Ubuntu Server 18.04, and Raspbian Stretch. Refer to [Azure IoT Edge supported systems](support.md#operating-systems) for a list of supported Linux operating systems and architectures.

>[!NOTE]
>Support for ARM64 devices is in [public preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!NOTE]
> Linux 软件存储库中的包受到每个包中的许可条款限制 (/usr/share/doc/*package-name*)。 使用程序包之前请阅读许可条款。 安装和使用程序包即表示接受这些条款。 如果不同意许可条款，则不要使用包。

## <a name="install-the-latest-runtime-version"></a>Install the latest runtime version

Use the following sections to install the most recent version of the Azure IoT Edge runtime onto your device. 

### <a name="register-microsoft-key-and-software-repository-feed"></a>注册 Microsoft 密钥和软件存储库源

Prepare your device for the IoT Edge runtime installation.

Install the repository configuration. Choose the **16.04** or **18.04** command that matches your device operating system:

* **Ubuntu Server 16.04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Ubuntu Server 18.04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Raspbian Stretch**:
   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

Copy the generated list.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Install Microsoft GPG public key

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-the-container-runtime"></a>安装容器运行时

Azure IoT Edge 依赖于 [OCI 兼容的](https://www.opencontainers.org/)容器运行时。 For production scenarios, we recommended that you use the [Moby-based](https://mobyproject.org/) engine provided below. 这是官方唯一支持用于 Azure IoT Edge 的容器引擎。 Docker CE/EE 容器映像与 Moby 运行时兼容。

Perform apt update.

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

If you encounter errors when installing the Moby container runtime, follow the steps to [Verify your Linux kernel for Moby compatibility](#verify-your-linux-kernel-for-moby-compatibility), provided later in this article. 

### <a name="install-the-azure-iot-edge-security-daemon"></a>安装 Azure IoT Edge 安全守护程序

The **IoT Edge security daemon** provides and maintains security standards on the IoT Edge device. 守护程序在每次开机时启动，并通过启动 IoT Edge 运行时的其余部分来启动设备。

The installation command also installs the standard version of the **libiothsm** if not already present.

Perform apt update.

   ```bash
   sudo apt-get update
   ```

安装安全守护程序。 该包安装在 `/etc/iotedge/`。

   ```bash
   sudo apt-get install iotedge
   ```

Once IoT Edge is successfully installed, the output will prompt you to update the configuration file. Follow the steps in the [Configure the Azure IoT Edge security daemon](#configure-the-security-daemon) section to finish provisioning your device. 

## <a name="install-a-specific-runtime-version"></a>Install a specific runtime version

If you want to install a specific version of the Azure IoT Edge runtime, you can target the component files directly from the IoT Edge GitHub repository. Use the following steps to get all of the IoT Edge components onto your device: the Moby engine and CLI, the libiothsm, and finally the IoT Edge security daemon.

1. Navigate to the [Azure IoT Edge releases](https://github.com/Azure/azure-iotedge/releases), and find the release version that you want to target. 

2. Expand the **Assets** section for that version.

3. There may or may not be updates to the Moby engine in any given release. If you see files that start with **moby-engine** and **moby-cli**, use the following commands to update those components. If you don't see any Moby files, go back through the older release assets until you find the most recent version. 

   1. Find the **moby-engine** file that matches your IoT Edge device's architecture. Right-click on the file link and copy the link address.

   2. Use the copied link in the following command to install that version of the Moby engine: 

      ```bash
      curl -L <moby-engine link> -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb
      ```

   3. Find the **moby-cli** file that matches your IoT Edge device's architecture. The Moby CLI is an optional component, but can be helpful during development. Right-click on the file link and copy the link address. 

   4. Use the copied link in the following command to install that version of the Moby CLI: 

      ```bash
      curl -L <moby-cli link> -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb
      ```

4. Every release should have new files for the IoT Edge security daemon and the hsmlib. Use the following commands to update those components. 

   1. Find the **libiothsm-std** file that matches your IoT Edge device's architecture. Right-click on the file link and copy the link address. 

   2. Use the copied link in the following command to install that version of the hsmlib:

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```
   
   3. Find the **iotedge** file that matches your IoT Edge device's architecture. Right-click on the file link and copy the link address. 

   4. Use the copied link in the following command to install that version of the IoT Edge security daemon. 

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

Once IoT Edge is successfully installed, the output will prompt you to update the configuration file. Follow the steps in the next section to finish provisioning your device. 

## <a name="configure-the-security-daemon"></a>Configure the security daemon

配置 IoT Edge 运行时以将物理设备与 Azure IoT 中心中存在的设备标识相链接。

可以使用 `/etc/iotedge/config.yaml` 处的配置文件配置守护程序。 默认情况下，该文件有写保护，你可能需要提升权限才能对其进行编辑。

可以使用 IoT 中心提供的设备连接字符串手动预配单个 IoT Edge 设备。 或者，可以使用设备预配服务自动预配设备，当需要预配多个设备时这会非常有用。 根据预配选项，选择合适的安装脚本。

### <a name="option-1-manual-provisioning"></a>选项 1：手动预配

若要手动预配设备，需要为其提供[设备连接字符串](how-to-register-device.md#register-in-the-azure-portal)，可以通过在 IoT 中心注册新设备来创建该设备连接字符串。

打开配置文件。

```bash
sudo nano /etc/iotedge/config.yaml
```

Find the provisioning configurations of the file and uncomment the **Manual provisioning configuration** section. 使用 IoT Edge 设备的连接字符串更新 **device_connection_string** 的值。 Make sure any other provisioning sections are commented out.

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
To paste clipboard contents into Nano `Shift+Right Click` or press `Shift+Insert`.

保存并关闭该文件。

   `CTRL + X`、`Y`、`Enter`

在配置文件中输入预配信息后，重启守护程序：

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>选项 2：自动预配

若要自动预配设备，请[设置设备预配服务并检索设备注册 ID](how-to-auto-provision-simulated-device-linux.md)。 There are a number of attestation mechanisms supported by IoT Edge when using automatic provisioning but your hardware requirements also impact your choices. For example, Raspberry Pi devices do not come with a Trusted Platform Module (TPM) chip by default.

打开配置文件。

```bash
sudo nano /etc/iotedge/config.yaml
```

Find the provisioning configurations of the file and uncomment the section appropriate for your attestation mechanism. When using TPM attestation, for example, update the values of **scope_id** and **registration_id** with the values from your IoT Hub Device Provisioning service and your IoT Edge device with TPM, respectively.

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

To paste clipboard contents into Nano `Shift+Right Click` or press `Shift+Insert`.

保存并关闭该文件。

   `CTRL + X`、`Y`、`Enter`

在配置文件中输入预配信息后，重启守护程序：

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>验证是否成功安装

如果使用了上一部分中的**手动配置**步骤，则应在设备上成功预配并运行 IoT Edge 运行时。 如果使用了**自动配置**步骤，则需要完成一些额外的步骤，以便运行时可以代表你向 IoT 中心注册你的设备。 For next steps, see [Create and provision a simulated TPM IoT Edge device on a Linux virtual machine](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

You can check the status of the IoT Edge Daemon:

```bash
systemctl status iotedge
```

Examine daemon logs:

```bash
journalctl -u iotedge --no-pager --no-full
```

Run an automated check for the most common configuration and networking errors: 

```bash
sudo iotedge check
```

And, list running modules:

```bash
sudo iotedge list
```

After installing IoT Edge on your device, the only module you should see running is **edgeAgent**. Once you create your first deployment, the other system module **$edgeHub** will start on the device as well. For more information, see [deploy IoT Edge modules](how-to-deploy-modules-portal.md).

## <a name="tips-and-troubleshooting"></a>提示和故障排除

需要提升权限才能运行 `iotedge` 命令。 安装运行时后，请从计算机中注销并重新登录以自动更新权限。 在此之前，在任何 `iotedge` 命令前都要使用 **sudo**。

在资源受限的设备上，强烈建议按照[故障排除指南](troubleshoot.md)中的说明将 *OptimizeForPerformance* 环境变量设置为 *false*。

如果网络具有代理服务器，请按照[配置 IoT Edge 设备以通过代理服务器进行通信](how-to-configure-proxy-support.md)中的步骤进行操作。

### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>Verify your Linux kernel for Moby compatibility

Many embedded device manufacturers ship device images that contain custom Linux kernels without the features required for container runtime compatibility. If you encounter issues while installing the recommended Moby container runtime, you may be able to troubleshoot your Linux kernel configuration using the [check-config](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) script from the official [Moby GitHub repository](https://github.com/moby/moby). Run the following commands on the device to check your kernel configuration:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

This will provide a detailed output that contains the status of kernel features that are used by the Moby runtime. You will want to ensure that all items under `Generally Necessary` and  `Network Drivers` are enabled to ensure that your kernel is fully compatible with the Moby runtime.  If you have identified any missing features, enable them by rebuilding your kernel from source and selecting the associated modules for inclusion in the appropriate kernel .config.  Similarly, if you are using a kernel configuration generator like defconfig or menuconfig, find and enable the respective features and rebuild your kernel accordingly.  Once you have deployed your newly modified kernel, run the check-config script again to verify that all the required features were successfully enabled.


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

If you are having problems with the IoT Edge runtime installing properly, check out the [troubleshooting](troubleshoot.md) page.

若要将现有安装更新到最新版本的 IoT Edge，请参阅[更新 IoT Edge 安全守护程序和运行时](how-to-update-iot-edge.md)。
