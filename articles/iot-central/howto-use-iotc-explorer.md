---
title: 使用 Azure IoT Central 资源管理器监视设备连接性
description: 监视设备消息，并通过 IoT Central 资源管理器 CLI 观察设备孪生更改。
author: viv-liu
ms.author: viviali
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 38cbe43e9038a47c4e222fd4744f0b844f9ddb4e
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845687"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>使用 Azure IoT Central 资源管理器监视设备连接性

本主题适用于构建者和管理员。

使用 IoT Central 资源管理器 CLI，了解如何将设备发送到 IoT Central，并观察 IoT 中心孪生中的更改。 此开放源代码工具可用于更深入地了解设备连接状态，并且设备的诊断问题消息不会到达云或者设备不响应孪生更改。

[访问 GitHub 中的 iotc 存储库。](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>先决条件

+ Node.js 8.x 或更高版本 - https://nodejs.org
+ 应用程序的管理员必须生成访问令牌，才能在 iotc 中使用

## <a name="install-iotc-explorer"></a>安装 iotc-资源管理器

从命令行运行以下命令以安装：

```cmd/sh
npm install -g iotc-explorer
```

> [!NOTE]
> 通常需要`sudo`在类似于 Unix 的环境中运行安装命令。

## <a name="run-iotc-explorer"></a>运行 iotc-资源管理器

以下各节介绍了可在运行`iotc-explorer`时使用的常见命令和选项。 若要查看完整的命令和选项集，请`--help`传递`iotc-explorer`到或它的任何子命令。

### <a name="login"></a>登录

在开始之前，需要 IoT Central 应用程序的管理员，以获取访问令牌供你使用。 管理员将执行以下步骤：

1. 导航到 "**管理**"，然后**访问令牌**。
1. 选择 "**生成令牌**"。
    ![访问令牌页屏幕截图](media/howto-use-iotc-explorer/accesstokenspage.png)

1. 输入令牌名称，选择 "**下一步**"，然后单击 "**复制**"。
    > [!NOTE]
    > 令牌值仅显示一次，因此必须在关闭对话之前复制它。 关闭对话框后，将不再显示该对话框。

    ![复制访问令牌的对话框屏幕截图](media/howto-use-iotc-explorer/copyaccesstoken.png)

可以使用令牌登录到 CLI，如下所示：

```cmd/sh
iotc-explorer login "<Token value>"
```

如果你不希望在 shell 历史记录中保留标记，则可以在出现提示时保留该令牌：

```cmd/sh
iotc-explorer login
```

### <a name="monitor-device-messages"></a>监视设备消息

可以使用 `monitor-messages` 命令，在应用程序中监视来自特定设备或所有设备的消息。 此命令启动观察程序，在新消息到达时持续输出它们：

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

还可以通过将`--raw`选项添加到命令中，输出更适合计算机的格式：

```cmd/sh
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

现在，你已了解如何使用 IoT Central 资源管理器，接下来要做的是探索[管理设备 IoT Central](howto-manage-devices.md)。
