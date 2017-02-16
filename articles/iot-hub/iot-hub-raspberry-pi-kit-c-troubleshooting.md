---
title: "故障排除 | Microsoft 文档"
description: "Raspberry Pi Node.js 体验的故障排除页"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "iot 问题, 物联网问题"
ms.assetid: 3653c79b-8a0c-41d4-b0bf-f6b4edb4d233
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: e3e4ad430d8941a09543ce2dc97f8e449a39bced
ms.openlocfilehash: 755961b8a0b141a716530f75e21b65c00fa8d2d8


---
# <a name="troubleshooting"></a>故障排除
## <a name="hardware-issues"></a>硬件问题
### <a name="the-application-runs-well-but-the-led-is-not-blinking"></a>应用程序运行正常，但 LED 不闪烁
此问题总是与硬件线路连接有关。 使用以下步骤确定问题。

1. 检查你是否在电路板上选择了正确的 **GPIO**。 两个端口应当是 **GPIO GND（引脚 6）**和 **GPIO 04（引脚 7）**。
2. 检查 LED 的极性是否正确。 较长的针脚表示**正极**（阳极引脚）。
3. 使用 Raspberry Pi 3 上的 **3.3V 引脚**和 **GND 引脚**。 将 Pi 作为直流电源。 检查 LED 是否正常工作。

![LED 规格](media/iot-hub-raspberry-pi-lessons/troubleshooting/led_spec.png)

### <a name="other-hardware-issues"></a>其他硬件问题
有关解决 Raspberry Pi 3 上的常见问题的信息，请参阅[官方的故障排除页面](http://elinux.org/R-Pi_Troubleshooting)。

## <a name="nodejs-package-issues"></a>Node.js 程序包问题
### <a name="no-response-during-gulp-tasks"></a>在 Gulp 任务期间没有响应
如果在运行 gulp 任务时遇到问题，可添加 `--verbose` 选项进行调试。 请尝试使用 `Ctrl + C` 终止当前 gulp 任务，然后在控制台窗口中运行以下命令，以便查看调试消息。 在控制台输出中可能会看到详细的错误消息。 

```bash
gulp --verbose
```

### <a name="device-discovery-issues"></a>设备发现问题
在排除 `devdisco` 命令的常见问题时若需帮助，请检查[自述文件](https://github.com/Azure/device-discovery-cli/blob/develop/readme.md)。

### <a name="npm-issues"></a>NPM 问题
请尝试使用以下命令更新 NPM 包：

```bash
npm install -g npm
```

如果问题仍然存在，请在本文末尾留下你的意见或者在我们的[示例存储库](https://github.com/Azure-Samples/iot-hub-c-raspberrypi-getting-started)中创建一个 GitHub 问题

## <a name="remote-debugging"></a>远程调试

即将推出在 Visual Studio 代码C/C++ 扩展中可用的远程调试。

同时，你可以通过最喜爱的 SSH 终端使用 GDB：

```bash
cd c-pi-lesson-x
sudo gdb app
```

## <a name="azure-cli-issues"></a>Azure-CLI 问题
Azure 命令行界面 (Azure CLI) 是一个预览版本。 在[预览版安装指南](https://github.com/Azure/azure-cli/blob/master/doc/preview_install_guide.md)中寻求解决方案。 如果命令不按预期工作，请尝试升级到最新版本的 Azure-cli。

如果使用工具时遇到任何 bug，请在 GitHub 存储库的“问题”部分中记录一个[问题](https://github.com/Azure/azure-cli/issues)。

如需常见问题的疑难解答帮助，请查看[自述文件](https://github.com/Azure/azure-cli/blob/master/README.rst)。

如果遇到“找不到满足需求的版本”，请运行以下命令，将 pip 升级到最新版本。

```bash
python -m pip install --upgrade pip
```

## <a name="python-installation-issues"></a>Python 安装问题
### <a name="legacy-installation-issues-macos"></a>旧安装的问题 (macOS)
安装 **pip** 时，如果使用 **su** 权限安装的包较旧，则会引发权限错误。 之所以发生此情况是因为未完全卸载以前使用 brew (macOS) 安装的 Python。 以前的安装中的某些 **pip** 程序包是由 root 创建的，这导致了权限错误。 解决方案是删除由 root 安装的那些程序包。 使用以下步骤完成此任务：

1. 转到：/usr/local/lib/python2.7/site-packages
2. 列出由根权限创建的包：`ls -l | grep root`
3. 卸载步骤 2 中的包：`sudo rm -rf {package name}`
4. 重新安装 Python。

## <a name="azure-iot-hub-issues"></a>Azure IoT 中心问题
如果已通过 `azure-cli` 成功预配 Azure IoT 中心，且需使用工具管理连接到 IoT 中心的设备，可尝试以下工具：

### <a name="device-explorer"></a>设备资源管理器
[设备资源管理器](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer)在 Windows 本地计算机上运行并连接到 Azure 中的 IoT 中心。 它与以下 [IoT 中心终结点](iot-hub-devguide.md)进行通信：

* *设备标识管理* -以便预配和管理在 IoT 中心内注册的设备。
* *接收“设备到云”消息* - 以便可以监视从设备发送到 IoT 中心的消息。
* *发送“云到设备”消息* - 以便可以将消息从 IoT 中心发送到设备。

在此工具中配置 `IoT hub connection string`，以便使用其所有功能。

### <a name="iot-hub-explorer"></a>IoT 中心资源管理器
[IoT 中心资源管理器](https://github.com/Azure/iothub-explorer)是示例多平台 CLI 工具，可用于管理设备客户端。 可以使用此工具在标识注册表中管理工具、监视“设备到云”消息，以及发送“云到设备”命令。

若要安装 iothub-explorer 工具的最新（预发行版）版本，请在命令行环境中运行以下命令：

```
npm install -g iothub-explorer@latest
```

可以使用以下命令获取有关所有 iothub-explorer 命令及其参数的更多帮助信息：

```bash
iothub-explorer help
```

### <a name="azure-portal"></a>Azure 门户
完整的 CLI 体验可帮助你创建和管理所有 Azure 资源。 你还可能希望使用 [Azure 门户](../azure-portal-overview.md)来帮助预配、管理和调试 Azure 资源。

## <a name="azure-storage-issues"></a>Azure 存储问题
[Microsoft Azure 存储资源管理器（预览版）](http://storageexplorer.com)是 Microsoft 提供的一款独立应用，可用于在 Windows、macOS 和 Linux 上处理 Azure 存储数据。 通过使用此工具，可以连接到表并查看其中的数据。 可以使用此工具排除 Azure 存储问题。



<!--HONumber=Jan17_HO2-->


