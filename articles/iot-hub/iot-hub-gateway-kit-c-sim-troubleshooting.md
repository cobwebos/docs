---
title: "模拟设备和 Azure IoT 网关 - 故障排除 | Microsoft Docs"
description: "Intel NUC 网关的故障排除页"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "iot 问题, 物联网问题"
ROBOTS: NOINDEX
ms.assetid: 3ee8f4b0-5799-40a3-8cf0-8d5aa44dbc2b
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: a70de978d4636a31644702c6f3a8ca0d0b80f5c2
ms.lasthandoff: 01/25/2017


---
# <a name="troubleshooting"></a>故障排除

## <a name="hardware-issues"></a>硬件问题

### <a name="ti-sensortag-cannot-be-connected"></a>无法连接 TI SensorTag

若要解决 SensorTag 连接问题，请使用 [SensorTag 应用](http://processors.wiki.ti.com/index.php/SensorTag_User_Guide#SensorTag_App_user_guide)。

### <a name="have-an-issue-with-intel-nuc"></a>Intel NUC 存在问题

若要解决启动问题，请参阅 [Intel NUC 上的无启动问题故障排除](http://www.intel.com/content/www/us/en/support/boards-and-kits/000005845.html)。

若要解决操作系统问题，请参阅 [Intel NUC 上的操作系统问题故障排除](http://www.intel.com/content/www/us/en/support/boards-and-kits/000006018.html)。

若要解决其他问题，请参阅 [Intel NUC 的 Blink 代码和 Beep 代码](http://www.intel.com/content/www/us/en/support/boards-and-kits/intel-nuc-boards/000005854.html)。

## <a name="nodejs-package-issues"></a>Node.js 程序包问题

### <a name="no-response-during-gulp-tasks"></a>执行 gulp 任务期间无响应

如果在运行 gulp 任务时遇到问题，请添加 `--verbose` 选项以便调试。 请尝试使用 `Ctrl + C` 终止当前 gulp 任务，然后在控制台窗口中运行以下命令，以便查看调试消息。 在控制台输出中可能会看到详细的错误消息。

```bash
gulp --verbose
```

### <a name="device-discovery-issues"></a>设备发现问题

在排除 `discover-sensortag` 命令的常见问题时若需帮助，请查看 [wiki 页面](https://wiki.archlinux.org/index.php/bluetooth#Bluetoothctl)。

### <a name="npm-issues"></a>npm 问题

尝试运行以下命令更新 npm 程序包：

```bash
npm install -g npm
```

如果问题仍然存在，请在本文末尾留下你的意见或者在我们的[示例存储库](https://github.com/azure-samples/iot-hub-c-intel-nuc-gateway-getting-started)中创建一个 GitHub 问题。

## <a name="remote-debugging"></a>远程调试
> 以下说明旨在用于调试本教程中使用的 node.js 脚本。
### <a name="run-the-sample-application-in-debug-mode"></a>在调试模式下运行示例应用程序

通过运行以下命令在调试模式下运行示例应用程序：

```bash
gulp run --debug
```

当调试引擎准备就绪时，在控制台输出中应当会看到 `Debugger listening on port 5858`。

### <a name="configure-visual-studio-code-to-connect-to-the-remote-device"></a>配置 Visual Studio Code 以连接到远程设备

1. 打开左侧的“调试”面板。
2. 单击绿色的“启动调试”(F5) 按钮。 Visual Studio Code 将打开 `launch.json` 文件。
3. 使用以下内容更新 `launch.json` 文件。 将 `[device hostname or IP address]` 替换为实际的设备 IP 地址或主机名。

   ``` json
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
            "remoteRoot": "~/ble_sample"
        }
     ]
   }
   ```

![远程调试配置](./media/iot-hub-gateway-kit-lessons/troubleshooting/remote_debugging_configuration.png)

### <a name="attach-to-the-remote-application"></a>附加到远程应用程序

单击绿色的“启动调试”(F5) 按钮以启动调试。

阅读 [VS Code 中的 JavaScript](https://code.visualstudio.com/docs/languages/javascript#_debugging) 以了解调试器的详细信息。

![调试 BLE 示例](./media/iot-hub-gateway-kit-lessons/troubleshooting/debugging_ble_sample.png)

## <a name="azure-cli-issues"></a>Azure CLI 问题

Azure 命令行界面 (Azure CLI) 是一个预览版本。 若要查找解决方案，可以使用[预览版安装指南](https://github.com/Azure/azure-cli/blob/master/doc/preview_install_guide.md)。

如果使用工具时遇到任何 bug，请在 GitHub 存储库的“问题”部分中记录一个[问题](https://github.com/Azure/azure-cli/issues)。

在排除常见问题时若需帮助，请检查[自述文件](https://github.com/Azure/azure-cli/blob/master/README.rst)。

如果遇到“找不到满足需求的版本”，请运行以下命令，将 pip 升级到最新版本。

```bash
python -m pip install --upgrade pip
```

## <a name="python-installation-issues"></a>Python 安装问题

### <a name="legacy-installation-issues-macos"></a>旧安装的问题 (macOS)

安装 pip 时，当使用 **su** 权限安装较旧的程序包时将引发权限错误。 之所以发生此情况是因为未完全卸载以前使用 brew (macOS) 安装的 Python。 以前的安装中的某些 pip 程序包是由 root 创建的，这导致了权限错误。 解决方案是删除由 root 安装的那些程序包。 使用以下步骤完成此任务：

1. 转到 `/usr/local/lib/python2.7/site-packages`
2. 列出由 root 创建的程序包：`ls -l | grep root`
3. 卸载步骤 2 中列出的程序包：`sudo rm -rf {package name}`
4. 重新安装 Python。

## <a name="azure-iot-hub-issues"></a>Azure IoT 中心问题

如果已使用 Azure CLI 成功预配了 Azure IoT 中心，并且需要一个工具来管理连接到 IoT 中心的设备，请尝试以下工具。

### <a name="device-explorer"></a>设备资源管理器

[设备资源管理器](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer)在 Windows 本地计算机上运行并连接到 Azure 中的 IoT 中心。 它与以下 [IoT 中心终结点](https://azure.microsoft.com/en-us/documentation/articles/iot-hub-devguide/)进行通信：

- 设备标识管理 - 以便预配和管理在 IoT 中心内注册的设备。
- 接收“设备到云”消息 - 以便可以监视从设备发送到 IoT 中心的消息。
- 发送“云到设备”消息 - 以便可以将消息从 IoT 中心发送到设备。

在此工具内配置 IoT 中心连接字符串以使用其所有功能。

### <a name="iothub-explorer"></a>iothub-explorer

[iothub-explorer](https://github.com/Azure/iothub-explorer) 是一个用来管理设备客户端的示例多平台 CLI 工具。 可以使用此工具在标识注册表中管理工具、监视“设备到云”消息，以及发送“云到设备”命令。

若要安装 iothub-explorer 工具的最新（预发行版）版本，请运行以下命令：

```bash
npm install -g iothub-explorer@latest
```

若要获取有关所有 iothub-explorer 命令及其参数的其他帮助，请运行以下命令：

```bash
iothub-explorer help
```

### <a name="the-azure-portal"></a>Azure 门户

完整的 CLI 体验可帮助你创建和管理所有 Azure 资源。 你还可能希望使用 [Azure 门户](https://azure.microsoft.com/en-us/documentation/articles/azure-portal-overview/)来帮助预配、管理和调试 Azure 资源。

## <a name="azure-storage-issues"></a>Azure 存储问题

[Microsoft Azure 存储资源管理器（预览版）](http://storageexplorer.com/)是 Microsoft 提供的一款独立应用，可用于在 Windows、macOS 和 Linux 上处理 Azure 存储数据。 通过使用此工具，可以连接到表并查看其中的数据。 可以使用此工具排除 Azure 存储问题。

