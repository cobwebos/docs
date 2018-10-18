---
title: IoT DevKit 到云 -- 将 IoT DevKit AZ3166 连接到远程监视 IoT 解决方案加速器 | Microsoft Docs
description: 本教程介绍如何将 IoT DevKit AZ3166 上的传感器的状态发送到远程监视 IoT 解决方案加速器，以便进行监视和可视化。
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: isacabe
ms.openlocfilehash: 32742b2a680370f443051e2d86f90d94e8632850
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2018
ms.locfileid: "44720576"
---
# <a name="connect-mxchip-iot-devkit-az3166-to-the-iot-remote-monitoring-solution-accelerator"></a>将 MXChip IoT DevKit AZ3166 连接到 IoT 远程监视解决方案加速器

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

你会了解如何在 IoT DevKit 上运行示例应用来将传感器数据发送到解决方案加速器。

[MXChip IoT DevKit](https://aka.ms/iot-devkit) 是具有多种外设和传感器的集成 Arduino 兼容板。 可以在 Visual Studio Code 中使用 [Azure IoT Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) 针对其进行开发。 它附带了一个不断增长的[项目目录](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)，指导你构建物联网 (IoT) 解决方案的原型，以利用 Microsoft Azure 服务。

## <a name="what-you-need"></a>所需条件

阅读[入门指南](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)并且只需完成以下各部分：

* 准备硬件
* 配置 Wi-Fi
* 开始使用 DevKit
* 准备开发环境

## <a name="open-the-remote-monitoring-sample-in-vs-code"></a>在 VS Code 中打开远程监视示例

1. 确保 IoT DevKit **未连接**到计算机。 先启动 VS Code，然后将 DevKit 连接到计算机。

2. 单击 `F1` 打开命令面板，键入并选择 **IoT Workbench: Examples**。 然后选择“IoT DevKit”作为开发板。

3. 找到“远程监视”，然后单击“打开示例”。 它将打开一个新的 VS Code 窗口，其中包含项目文件夹。
  ![IoT Workbench，选择“远程监视”示例](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-example.png)

## <a name="configure-iot-hub-device-connection-string"></a>配置 IoT 中心设备连接字符串

1. 将 IoT DevKit 转换为“配置模式”。 为此，请执行以下操作：
   * 按住按钮 A。
   * 按下然后松开“重置”按钮。

2. 屏幕将显示 DevKit ID 和“配置”。
   
  ![IoT DevKit 配置模式](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/devkit-configuration-mode.png)

3. 单击 `F1` 打开命令面板，输入并选择 IoT Workbench: Device > Config Device Settings。

4. 粘贴刚刚复制的连接字符串，然后单击 `Enter` 进行配置。

## <a name="build-and-upload-the-device-code"></a>生成并上传设备代码

1. 单击 `F1` 打开命令面板，输入并选择 IoT Workbench: Device > Device Upload。
  ![IoT Workbench: Device - > Upload](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-upload.png)

1. VS Code 随后开始编译代码并上传到 DevKit。
  ![IoT Workbench: Device - > Uploaded](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-uploaded.png)

DevKit 将重新启动并开始运行代码。

## <a name="test-the-project"></a>测试项目

### <a name="view-the-telemetry-sent-to-remote-monitoring-solution"></a>查看发送到远程监视解决方案的遥测数据

在示例应用运行时，DevKit 会通过 Wi-Fi 将传感器数据发送到远程监视解决方案。 若要查看结果，请遵循以下步骤：

1. 转到解决方案仪表板，然后单击“设备”。

2. 单击设备名称，在右侧选项卡上可以实时查看 DevKit 上的传感器状态。
  ![Azure IoT 套件中的传感器数据](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard.png)

### <a name="send-a-c2d-message"></a>发送 C2D 消息

可以通过远程监视解决方案在设备上调用远程方法。 示例代码发布了三个方法。当选中传感器时，可以在 Method 节中看到这些方法。

![IoT DevKit 方法](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-methods.png)

我们来尝试使用“LedColor”方法更改一个 DevKit LED 的颜色。

1. 从设备列表中选择设备名称，然后单击“作业”。

  ![创建作业](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-job.png)

2. 按如下所示配置“作业”，然后单击“应用”。
  * 选择作业：运行方法
  * 方法名称：LedColor
  * 作业名称：ChangeLedColor
  
  ![作业设置](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-suite-change-color.png)

在数秒钟内，DevKit 应更改 RGB LED（在按钮 A 的下面）的颜色。

![IoT DevKit 红色 led](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-devkit-led.png)

## <a name="clean-up-resources"></a>清理资源

如果打算继续学习教程，请将远程监视解决方案加速器保持为部署状态。

如果不再需要该解决方案加速器，请在“预配的解决方案”页中选择它，然后单击“删除解决方案”将其删除：

![删除解决方案](media/quickstart-remote-monitoring-deploy/deletesolution.png)

## <a name="problems-and-feedback"></a>问题和反馈

如果遇到问题，请参阅 [IoT DevKit 常见问题解答](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)，或通过以下渠道联系我们：

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [堆栈溢出](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>后续步骤

了解如何将 DevKit 设备连接到 Azure IoT 远程监视解决方案加速器并将传感器数据可视化以后，建议接下来学习以下教程：

* [Azure IoT 解决方案加速器概述](https://docs.microsoft.com/azure/iot-suite/)
* [自定义 UI](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md)
* [将 IoT DevKit 连接到 Azure IoT Central 应用程序](../iot-central/howto-connect-devkit.md)