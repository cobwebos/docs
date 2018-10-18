---
title: 使用 Azure IoT Central 资源管理器监视设备连接性
description: 监视设备消息，并通过 IoT Central 资源管理器 CLI 观察设备孪生更改。
author: viv-liu
ms.author: viviali
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 962f394607d20869bf00db624533996b0060eaf2
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2018
ms.locfileid: "45987238"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>使用 Azure IoT Central 资源管理器监视设备连接性

本主题适用于构建者和管理员。

使用 IoT Central 资源管理器 CLI，了解如何将设备发送到 IoT Central，并观察 IoT 中心孪生中的更改。 此开放源代码工具可用于更深入地了解设备连接状态，并且设备的诊断问题消息不会到达云或者设备不响应孪生更改。

## <a name="visit-the-iotc-explorer-repo-in-githubhttpsakamsiotciotcexplorercligithub"></a>[访问 GitHub 中的 iotc-explorer 存储库](https://aka.ms/iotciotcexplorercligithub)

## <a name="prerequisites"></a>先决条件
+ Node.js 8.x 或更高版本 - https://nodejs.org
+ 需要获取应用的管理员，以生成访问令牌，用于 iotc-explorer

## <a name="installing-iotc-explorer"></a>安装 iotc-explorer

从命令行运行以下命令以安装：

```
npm install -g iotc-explorer
```

> [!NOTE]
> 通常情况下，需要在类似 Unix 的环境中对 `sudo` 运行安装命令。

## <a name="running-iotc-explorer"></a>运行 iotc-explorer

下面是一些使用 `iotc-explorer` 时可运行的命令和常用选项。 若要查看完整的命令和选项集，可以将 `--help` 传递到 `iotc-explorer` 或其任何子命令。

### <a name="login"></a>登录

在开始之前，需要 IoT Central 应用程序的管理员，以获取访问令牌供你使用。 管理员将执行以下步骤：
1. 转到“管理/访问令牌”。 
1. 单击“生成”。
![访问令牌页屏幕截图](media/howto-use-iotc-explorer/accesstokenspage.png)

1. 输入令牌名称，单击“下一步”和“复制令牌值”。
    > [!NOTE]
    > 令牌值仅显示一次，因此必须先复制再关闭对话框。 关闭对话框后，它将不会再显示。

    ![复制访问令牌的对话框屏幕截图](media/howto-use-iotc-explorer/copyaccesstoken.png)

然后，可以通过运行以下命令使用令牌登录 CLI：

```sh
iotc-explorer login "<Token value>"
```

如果不想将令牌保存在 shell 历史记录中，可以不考虑令牌，改为在系统提示时提供：

```
iotc-explorer login
```

### <a name="monitor-device-messages"></a>监视设备消息

可以使用 `monitor-messages` 命令，在应用程序中监视来自特定设备或所有设备的消息。 这会启动观察程序，在新消息进入时持续输出。

若要查看应用程序中的所有设备，请运行以下命令：

```
iotc-explorer monitor-messages
```
Output: ![monitor-messages command output](media/howto-use-iotc-explorer/monitormessages.PNG)

若要查看特定设备，只需将设备 ID 添加到命令的末尾：

```
iotc-explorer monitor-messages <your-device-id>
```

还可以通过将 `--raw` 选项添加到命令中，使命令输出采用计算机易识别的格式：

```
iotc-explorer monitor-messages --raw
```

### <a name="get-device-twin"></a>获取设备孪生

可以使用 `get-twin` 命令来获取 IoT Central 设备孪生的内容。 为此，请运行以下命令：

```
iotc-explorer get-twin <your-device-id>
```

Output: ![get-twin command output](media/howto-use-iotc-explorer/getdevicetwin.PNG)

如同 `monitor-messages`，可以通过传递 `--raw` 选项，获取计算机易识别的输出：

```
iotc-explorer get-twin <your-device-id> --raw
```

## <a name="next-steps"></a>后续步骤
现在，你已了解如何使用 IoT Central 资源管理器，建议下一步探索[如何管理设备 IoT Central](howto-manage-devices.md)。
