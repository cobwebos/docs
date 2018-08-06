---
title: 使用 Azure 设备孪生控制 MXChip IoT DevKit 用户 LED | Microsoft Docs
description: 本教程介绍如何使用 Azure IoT 中心设备孪生来监视 DevKit 状态和控制用户 LED。
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/04/2018
ms.author: liydu
ms.openlocfilehash: 6bc1255c5bbb9cf74c97b88600f34e7fcd90ae4f
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2018
ms.locfileid: "39343140"
---
# <a name="mxchip-iot-devkit"></a>MXChip IoT DevKit

可以参考此示例，使用 Azure IoT 中心设备孪生来监视 MXChip IoT DevKit WiFi 信息和传感器状态，以及控制用户 LED 的颜色。

## <a name="what-you-learn"></a>学习内容

- 如何监视 MXChip IoT DevKit 传感器状态。

- 如何使用 Azure 设备孪生控制 DevKit RGB LED 的颜色。

## <a name="what-you-need"></a>所需条件

- 遵照[入门指南](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)设置开发环境。

- 在 GitBash 终端窗口（或其他 Git 命令行接口）中键入以下命令：

   ```bash
   git clone https://github.com/DevKitExamples/DevKitState.git
   cd DevKitState
   code .
   ```

## <a name="provision-azure-services"></a>预配 Azure 服务

1. 在 Visual Studio Code 中单击“任务”下拉菜单，并选择“运行任务...” - “cloud-provision”。

2. “欢迎”面板的“终端”选项卡下会显示进度。

3. 出现“要选择哪个订阅”提示消息时，请选择一个订阅。

4. 选择一个资源组。 
 
   > [!NOTE]
   > 如果已有一个免费的 IoT 中心，则可以跳过此步骤。

5. 出现“要选择哪个 IoT 中心”提示消息时，请选择或创建一个 IoT 中心。

6. 随后会显示类似于“函数应用: 函数应用名称: xxx”的内容。 记下函数应用名称，因为在后续步骤中需要用到。

7. 等待 Azure 资源管理器模板部署完成。显示“资源管理器模板部署: 完成”消息即表示部署完成。

## <a name="deploy-function-app"></a>部署函数应用

1. 在 Visual Studio Code 中单击“任务”下拉菜单，并选择“运行任务...” - “cloud-deploy”。

2. 等待函数应用代码上传过程完成；此时会显示“函数应用部署: 完成”消息。

## <a name="configure-iot-hub-device-connection-string-in-devkit"></a>在 DevKit 中配置 IoT 中心设备连接字符串

1. 将 MXChip IoT DevKit 连接到计算机。

2. 在 Visual Studio Code 中单击“任务”下拉菜单，并选择“运行任务...” - “config-device-connection”

3. 在 MXChip IoT DevKit 上，按住按钮 **A**，按下“重置”按钮，然后松开按钮 **A**，使 DekKit 进入配置模式。

4. 等待连接字符串配置过程完成。

## <a name="upload-arduino-code-to-devkit"></a>将 Arduino 代码上传到 DevKit

在 MXChip IoT DevKit 已连接到计算机的情况下：

1. 在 Visual Studio Code 中单击“任务”下拉菜单，并选择“运行生成任务...”随即会编译 Arduino 草案并将其上传到 DevKit。

2. 成功上传草案后，会显示“生成和上传草案: 成功”消息。

## <a name="monitor-devkit-state-in-browser"></a>在浏览器中监视 DevKit 状态

1. 在 Web 浏览器中，打开在执行[所需条件](#whatyouneed)步骤时创建的 `DevKitState\web\index.html` 文件。

2. 将显示以下网页：![指定函数应用名称。](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state-function-app-name.png)

3. 输入前面记下的函数应用名称。

4. 单击“连接”按钮

5. 在几秒钟内，页面将会刷新，并显示 DevKit 的 WiFi 连接状态，以及每个板载传感器的状态。

## <a name="control-the-devkits-user-led"></a>控制 DevKit 的用户 LED

1. 单击网页演示中的用户 LED 图形。

2. 在几秒钟内，屏幕将会刷新，并显示用户 LED 的当前颜色状态。

3. 单击 RGB 滑块控件上的不同位置，尝试更改 RGB LED 的颜色值。

## <a name="example-operation"></a>示例操作

![示例测试过程](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state.gif)

> [!NOTE]
> 可以在 Azure 门户中查看设备孪生的原始数据：“IoT 中心”-\>“IoT 设备” -\>*\<你的设备\>* -\>“设备孪生”。

## <a name="next-steps"></a>后续步骤

我们现已了解以下操作：
- 将 MXChip IoT DevKit 设备连接到 Azure IoT 远程监视解决方案加速器。
- 使用 Azure IoT 设备孪生函数来感应和控制 DevKit 的 RGB LED 颜色。

下面是建议的后续步骤：

* [Azure IoT 远程监视解决方案加速器概述](https://docs.microsoft.com/azure/iot-suite/)
* [将 MXChip IoT DevKit 设备连接到 Azure IoT Central 应用程序](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
