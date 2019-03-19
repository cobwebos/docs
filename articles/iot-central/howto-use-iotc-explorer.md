---
title: 使用 Azure IoT Central 资源管理器监视设备连接性
description: 监视设备消息，并通过 IoT Central 资源管理器 CLI 观察设备孪生更改。
author: viv-liu
ms.author: viviali
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 16cb27ab330118d1bb59cf4f3d782bf55fa28d43
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2019
ms.locfileid: "57779736"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>使用 Azure IoT Central 资源管理器监视设备连接性

本主题适用于构建者和管理员。

使用 IoT Central 资源管理器 CLI，了解如何将设备发送到 IoT Central，并观察 IoT 中心孪生中的更改。 此开放源代码工具可用于更深入地了解设备连接状态，并且设备的诊断问题消息不会到达云或者设备不响应孪生更改。

[请访问 GitHub 中的 iotc 资源管理器存储库。](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>必备组件

+ Node.js 8.x 或更高版本 - https://nodejs.org
+ 你的应用程序的管理员必须生成为你要在 iotc 资源管理器中使用的访问令牌

## <a name="install-iotc-explorer"></a>安装 iotc 资源管理器

从命令行运行以下命令以安装：

```cmd/sh
npm install -g iotc-explorer
```

> [!NOTE]
> 通常需要运行安装命令与`sudo`类似于 Unix 的环境中。

## <a name="run-iotc-explorer"></a>运行 iotc 资源管理器

以下各节介绍常见命令和选项的运行时可以使用`iotc-explorer`。 若要查看完整的命令和选项集，请将传递`--help`到`iotc-explorer`或任何其子命令。

### <a name="login"></a>登录

在开始之前，需要 IoT Central 应用程序的管理员，以获取访问令牌供你使用。 管理员将执行以下步骤：

1. 导航到**Administration**然后**的访问令牌**。
1. 选择**生成令牌**。
    ![访问令牌页屏幕截图](media/howto-use-iotc-explorer/accesstokenspage.png)

1. 输入标记名称，选择**下一步**，然后**副本**。
    > [!NOTE]
    > 因此它必须关闭对话框前复制令牌值仅一次，显示。 关闭对话框之后, 它将永远不会再次显示。

    ![复制访问令牌的对话框屏幕截图](media/howto-use-iotc-explorer/copyaccesstoken.png)

可以使用该标记，如下所示登录到 CLI:

```cmd/sh
iotc-explorer login "<Token value>"
```

如果想要不具有保存在 shell 历史记录中的令牌，可以将该令牌并改为提供出现提示时：

```cmd/sh
iotc-explorer login
```

### <a name="monitor-device-messages"></a>监视设备消息

可以使用 `monitor-messages` 命令，在应用程序中监视来自特定设备或所有设备的消息。 此命令启动持续输出新消息到达的观察程序：

若要查看应用程序中的所有设备，请运行以下命令：

```cmd/sh
iotc-explorer monitor-messages
```

输出：

![monitor-messages command output](media/howto-use-iotc-explorer/monitormessages.png)

若要观看特定设备，只需将设备 id 添加到命令的末尾：

```cmd/sh
iotc-explorer monitor-messages <your-device-id>
```

也可以通过添加输出的多计算机友好格式`--raw`选项用于命令：

```
iotc-explorer monitor-messages --raw
```

### <a name="get-device-twin"></a>获取设备孪生

可以使用 `get-twin` 命令来获取 IoT Central 设备孪生的内容。 为此，请运行以下命令：

```cmd/sh
iotc-explorer get-twin <your-device-id>
```

输出：

![get-twin command output](media/howto-use-iotc-explorer/getdevicetwin.png)

如同 `monitor-messages`，可以通过传递 `--raw` 选项，获取计算机易识别的输出：

```cmd/sh
iotc-explorer get-twin <your-device-id> --raw
```

## <a name="next-steps"></a>后续步骤

现在，已了解如何使用 IoT 中心资源管理器，建议下一步是探索[管理设备 IoT Central](howto-manage-devices.md)。
