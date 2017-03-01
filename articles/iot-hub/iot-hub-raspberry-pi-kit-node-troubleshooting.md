---
title: "将 Raspberry Pi (C) 连接到 Azure IoT - 故障排除 | Microsoft Docs"
description: "Raspberry Pi Node.js 体验的故障排除页面"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "iot 问题, 物联网问题"
ms.assetid: 22cf50dc-8206-42a2-a1fc-f75fa85135fa
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 2c5e2955584b165ca0ca95ce424a2050fb3f6ccd


---
# <a name="troubleshooting"></a>故障排除
## <a name="hardware-issues"></a>硬件问题
### <a name="the-application-runs-well-but-the-led-is-not-blinking"></a>应用程序运行良好但 LED 不闪烁
此问题始终与硬件线路连接相关。 可使用以下步骤查明问题：

1. 检查你是否在电路板上选择了正确的 **GPIO**。 两个端口应当是 **GPIO GND（引脚 6）**和 **GPIO 04（引脚 7）**。
2. 检查 LED 的极性是否正确。 较长的针脚表示**正极**（阳极引脚）。
3. 使用 Raspberry Pi 3 上的 **3.3V 引脚**和 **GND 引脚**。 将 Pi 作为直流电源。 检查 LED 是否正常工作。

![LED 规格](media/iot-hub-raspberry-pi-lessons/troubleshooting/led_spec.png)

### <a name="other-hardware-issues"></a>其他硬件问题
有关解决 Raspberry Pi 3 上的常见问题的信息，请参阅[官方的故障排除页面](http://elinux.org/R-Pi_Troubleshooting)。

## <a name="nodejs-package-issues"></a>Node.js 程序包问题
### <a name="no-response-during-gulp-tasks"></a>执行 gulp 任务期间无响应
如果在运行 gulp 任务时遇到问题，请添加 `--verbose` 选项以便调试。 通过按 Ctrl + C 键尝试终止当前的 gulp 任务，然后在控制台窗口中运行以下命令以查看调试消息。 在控制台输出中可能会看到详细的错误消息。

```bash
gulp --verbose
```

### <a name="device-discovery-issues"></a>设备发现问题
在排除 `devdisco` 命令的常见问题时若需帮助，请检查[自述文件](https://github.com/Azure/device-discovery-cli/blob/develop/readme.md)。

### <a name="npm-issues"></a>npm 问题
尝试使用以下命令更新 npm 程序包：

```bash
npm install -g npm
```

如果问题仍然存在，请在本文末尾留下你的意见或者在我们的[示例存储库](https://github.com/Azure-Samples/iot-hub-node-raspberrypi-getting-started)中创建一个 GitHub 问题。

## <a name="remote-debugging"></a>远程调试
### <a name="run-the-sample-application-in-debug-mode"></a>在调试模式下运行示例应用程序
```bash
gulp run --debug
```

当调试引擎准备就绪时，在控制台输出中应当会看到 ```Debugger listening on port 5858```。

### <a name="configure-visual-studio-code-to-connect-to-the-remote-device"></a>配置 Visual Studio Code 以连接到远程设备
1. 打开左侧的“调试”面板。
2. 单击绿色的“启动调试”(F5) 按钮。 Visual Studio Code 将打开 launch.json 文件。
3. 使用以下内容更新 launch.json 文件。 将 `[device hostname or IP address]` 替换为实际的设备 IP 地址或主机名。

> [!NOTE]
> 若要了解有关 Visual Studio 调试的详细信息，请参阅[在 Visual Studio Code 中进行调试](https://code.visualstudio.com/Docs/editor/debugging#_launchjson-attributes)。


```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Attach",
            "type": "node",
            "request": "attach",
            "port": 5858,
            "address": "[device hostname or IP address]",
            "restart": false,
            "sourceMaps": false,
            "outDir": null,
            "localRoot": "${workspaceRoot}",
            "remoteRoot": null
        }
    ]
}
```

![远程调试配置](media/iot-hub-raspberry-pi-lessons/troubleshooting/remote_debugging_configuration.png)

### <a name="attach-to-the-remote-application"></a>附加到远程应用程序
单击绿色的“启动调试”(F5) 按钮以启动调试。

阅读 [VS Code 中的 JavaScript](https://code.visualstudio.com/docs/languages/javascript#_debugging) 以了解调试器的详细信息。

![远程交互式调试](media/iot-hub-raspberry-pi-lessons/troubleshooting/remote_debugging_interactive.png)

## <a name="azure-cli-issues"></a>Azure CLI 问题
Azure 命令行界面 (Azure CLI) 是一个预览版本。 若要查找解决方案，可以使用[预览版安装指南](https://github.com/Azure/azure-cli/blob/master/doc/preview_install_guide.md)。

如果使用工具时遇到任何 bug，请在 GitHub 存储库的“问题”部分中记录一个[问题](https://github.com/Azure/azure-cli/issues)。

在排除常见问题时若需帮助，请检查[自述文件](https://github.com/Azure/azure-cli/blob/master/README.rst)。

## <a name="python-installation-issues"></a>Python 安装问题
### <a name="legacy-installation-issues-macos"></a>旧安装的问题 (macOS)
安装 pip 时，当使用 **su** 权限安装较旧的程序包时将引发权限错误。 之所以发生此情况是因为未完全卸载以前使用 brew (macOS) 安装的 Python。 以前的安装中的某些 pip 程序包是由 root 创建的，这导致了权限错误。 解决方案是删除由 root 安装的那些程序包。 使用以下步骤完成此任务：

1. 转到：/usr/local/lib/python2.7/site-packages
2. 列出由 root 创建的程序包：`ls -l | grep root`
3. 卸载步骤 2 中列出的程序包：`sudo rm -rf {package name}`
4. 重新安装 Python。

## <a name="azure-iot-hub-issues"></a>Azure IoT 中心问题
如果已使用 Azure CLI 成功预配了 Azure IoT 中心，并且需要一个工具来管理连接到 IoT 中心的设备，请尝试以下工具。

### <a name="device-explorer"></a>设备资源管理器
[设备资源管理器](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer)工具在 Windows 本地计算机上运行，并连接到 Azure 的 IoT 中心。 它与以下 [IoT 中心终结点](iot-hub-devguide.md)进行通信：


* *设备标识管理* -以便预配和管理在 IoT 中心内注册的设备。
* *接收“设备到云”消息* - 以便可以监视从设备发送到 IoT 中心的消息。
* *发送“云到设备”消息* - 以便可以将消息从 IoT 中心发送到设备。

在此工具内配置 IoT 中心连接字符串以使用其所有功能。

### <a name="iothub-explorer"></a>iothub-explorer
[iothub-explorer](https://github.com/Azure/iothub-explorer) 是一个用来管理设备的示例多平台 CLI 工具。 可以使用此工具在标识注册表中管理工具、监视“设备到云”消息，以及发送“云到设备”消息。

若要安装 iothub-explorer 工具的最新（预发行版）版本，请在命令行环境中运行以下命令：

```bash
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




<!--HONumber=Jan17_HO4-->


