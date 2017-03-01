---
title: "将 Intel Edison (C) 连接到 Azure IoT - 故障排除 | Microsoft Docs"
description: "Intel Edison C 体验的故障排除页"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "arduino 故障排除"
ms.assetid: fe20f2fe-490c-4910-82e1-578ed504ae86
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/8/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: c35afd54fb20c3ecd1a70796094093ad748385d0


---
# <a name="troubleshooting"></a>故障排除
## <a name="hardware-issues"></a>硬件问题
有关如何解决 Intel Edison 常见问题的信息，请参阅[官方故障排除页](https://software.intel.com/en-us/node/637974)。

## <a name="nodejs-package-issues"></a>Node.js 程序包问题
### <a name="no-response-during-gulp-tasks"></a>在 Gulp 任务期间没有响应
如果在运行 gulp 任务时遇到问题，可添加 `--verbose` 选项进行调试。 请尝试使用 `Ctrl + C` 终止当前 gulp 任务，然后在控制台窗口中运行以下命令，以便查看调试消息。 在控制台输出中可能会看到详细的错误消息。 

```bash
gulp --verbose
```

### <a name="npm-issues"></a>NPM 问题
请尝试使用以下命令更新 NPM 包：

```bash
npm install -g npm
```

如果问题仍然存在，请在本文末尾留下你的意见或者在我们的[示例存储库][sample-repository]中创建一个 GitHub 问题。

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
[设备资源管理器](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer)在 Windows 本地计算机上运行并连接到 Azure 中的 IoT 中心。 它与以下 [IoT 中心终结点](iot-hub-devguide.md)进行通信：

- _设备标识管理_ -以便预配和管理在 IoT 中心内注册的设备。
- _接收“设备到云”消息_ - 以便可以监视从设备发送到 IoT 中心的消息。
- _发送“云到设备”消息_ - 以便可以将消息从 IoT 中心发送到设备。

在此工具中配置 `IoT hub connection string`，以便使用其所有功能。

### <a name="iot-hub-explorer"></a>IoT 中心资源管理器
[IoT 中心资源管理器](https://github.com/Azure/iothub-explorer)是示例多平台 CLI 工具，可用于管理设备客户端。 可以使用此工具在标识注册表中管理工具、监视“设备到云”消息，以及发送“云到设备”命令。

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
[Microsoft Azure 存储资源管理器（预览版）](http://storageexplorer.com)是 Microsoft 提供的一款独立应用，可用于在 Windows、macOS 和 Linux 上处理 [Azure 存储](https://azure.microsoft.com/en-us/services/storage/)数据。 通过使用此工具，可以连接到表并查看其中的数据。 可以使用此工具排查 Azure 存储问题。

## <a name="next-steps"></a>后续步骤
本页仅包括 Intel Edison 工具包最常见的问题。 可在底部留下注释，报告问题，进行进一步故障排除。

返回[开始使用 Intel Edison (C)](iot-hub-intel-edison-kit-c-get-started.md)

<!-- Images and links -->

[sample-repository]: https://github.com/Azure-Samples/iot-hub-c-edison-getting-started


<!--HONumber=Jan17_HO4-->


