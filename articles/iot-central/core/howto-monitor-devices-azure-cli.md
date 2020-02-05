---
title: 使用 Azure IoT Central 资源管理器监视设备连接性
description: 监视设备消息，并通过 IoT Central 资源管理器 CLI 观察设备孪生更改。
author: viv-liu
ms.author: viviali
ms.date: 12/18/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 3f18537b4b038844c9aa824593e354c23c792370
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026778"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>使用 Azure CLI 监视设备连接

本主题适用于构建者和管理员。

使用 Azure CLI IoT 扩展查看设备发送到 IoT Central 的消息，并观察设备克隆中的更改。 你可以使用此工具来调试和观察设备连接，并诊断不能访问云的设备消息的问题或不响应克隆更改的设备。

[有关更多详细信息，请访问 Azure CLI 扩展参考](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/central)

## <a name="prerequisites"></a>必备组件

+ Azure CLI 安装，且版本2.0.7 或更高版本。 通过运行 `az --version`检查 Azure CLI 的版本。 了解如何从[Azure CLI 文档](https://docs.microsoft.com/cli/azure/install-azure-cli)进行安装和更新
+ Azure 中的工作或学校帐户，作为用户添加到 IoT Central 的应用程序中。

## <a name="install-the-iot-central-extension"></a>安装 IoT Central 扩展

从命令行运行以下命令以安装：

```cmd/sh
az extension add --name azure-cli-iot-ext
```

通过运行查看扩展的版本 
```cmd/sh
az --version
```
应会看到 "azure cli-iot 扩展" 扩展0.8.1 或更高版本。 如果不是，则运行
```cmd/sh
az extension update --name azure-cli-iot-ext
```

## <a name="using-the-extension"></a>使用扩展

以下各节介绍了在运行 `az iot central`时可以使用的常见命令和选项。 若要查看完整的命令和选项集，请将 `--help` 传递到 `az iot central` 或它的任何子命令。

### <a name="login"></a>登录

首先登录到 Azure CLI。 

```cmd/sh
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>获取 IoT Central 应用的应用程序 ID
在 "**管理"/"应用程序设置**" 中，复制**应用程序 ID**。 稍后的步骤中会用到它。

### <a name="monitor-messages"></a>监视消息
监视从设备发送到 IoT Central 应用的消息。 这将包括所有标头和批注。

```cmd/sh
az iot central app monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>查看设备属性
查看给定设备的当前读取和读取/写入设备属性。

```cmd/sh
az iot central device-twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>后续步骤

现在，你已了解如何使用 IoT Central 资源管理器，接下来要做的是探索[管理设备 IoT Central](howto-manage-devices.md)。
