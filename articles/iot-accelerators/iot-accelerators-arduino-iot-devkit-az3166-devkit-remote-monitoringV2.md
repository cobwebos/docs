---
title: IoT DevKit 到云：将 IoT DevKit AZ3166 连接到远程监视 IoT 解决方案加速器 | Microsoft Docs
description: 本教程介绍如何将 IoT DevKit AZ3166 上的传感器的状态发送到远程监视 IoT 解决方案加速器，以便进行监视和可视化。
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: isacabe
ms.openlocfilehash: e900b952ab9bb2054b9e4174670894027cdd2618
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969446"
---
# <a name="connect-mxchip-iot-devkit-az3166-to-the-iot-remote-monitoring-solution-accelerator"></a>将 MXChip IoT DevKit AZ3166 连接到 IoT 远程监视解决方案加速器


[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

本教程介绍如何在 DevKit 上运行示例应用来将传感器数据发送到解决方案加速器。

[MXChip IoT DevKit](https://aka.ms/iot-devkit) 是具有多种外设和传感器的集成 Arduino 兼容板。 可以使用[适用于 Arduino 的 Visual Studio Code 扩展](https://aka.ms/arduino)针对其进行开发。 它附带了一个不断增长的[项目目录](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)，指导你构建物联网 (IoT) 解决方案的原型，以利用 Microsoft Azure 服务。

## <a name="what-you-need"></a>所需条件

完成[入门指南](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)来实现以下目的：

* 将 DevKit 连接到 Wi-Fi
* 准备开发环境


## <a name="open-the-remotemonitoring-sample"></a>打开 RemoteMonitoring 示例

1. 如果 DevKit 已连接，将其从计算机断开连接。

2. 启动 VS Code。

3. 将 DevKit 连接到计算机。 VS Code 将自动检测 DevKit 并打开以下页面：
  * DevKit 简介页面。
  * Arduino 示例：DevKit 入门练习示例。

4. 展开左侧的“ARDUINO 示例”部分，浏览到“MXCHIP AZ3166 的示例”>“AzureIoT”，然后选择“RemoteMonitoringv2”。 它将打开一个新的 VS Code 窗口，其中包含一个项目文件夹。

  ![打开远程监视项目](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-arduino-examples.png)


  > [!NOTE]
  > 如果无意中关闭了窗格，可以重新打开它。 使用 `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) 打开命令面板，键入“Arduino”，然后找到并选择“Arduino: Examples”。

## <a name="add-a-new-physical-device"></a>添加新的物理设备

在门户中转到“设备”部分，然后在其中单击“+新建设备”按钮。 

![添加新设备](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-device.png)

应在“新建设备”窗体中进行填充。
1. 在“设备类型”部分单击“物理”。
2. 定义自己的设备 ID（例如 *MXChip* 或 *AZ3166*）。
3. 在“身份验证密钥”部分选择“自动生成密钥”。
4. 单击“应用”按钮。

![“添加新设备”窗体](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-new-device-form.png)

等待门户完成新设备的预配。

![预配新设备 ](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-device-provisioning.png)


然后会显示新设备的配置。
复制生成的**连接字符串**。

![设备连接字符串](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-new-device-connstring.png)


此连接字符串将在下一部分使用。





## <a name="build-and-upload-the-device-code"></a>生成并上传设备代码

回到 Visual Studio Code： 

1. 使用 `Ctrl+P` (macOS: `Cmd + P`) 并键入 **task config-device-connection**。

  ![选择 Azure 订阅和 IoT 中心](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-task-config-device-conexion.png)

2. 终端会询问你是否要使用所需 IoT 设备的连接字符串。 选择“新建”，然后进行粘贴。

  ![粘贴连接字符串](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-task-config-device-conexion-choose-iot-hub-press-button-A.png)

3. 终端有时会提示进入配置模式。 为此，请按住按钮 A，然后按下重置按钮并松开，再松开按钮 A。屏幕会显示 DevKit ID 和“配置”。

  ![设备 DevKit 屏幕](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-devkit-screen.png)

  > [!NOTE]
  > 如果执行了本教程上一部分的操作，则连接字符串会保存在剪贴板中。 否则，应转到 Azure 门户并查找远程监视资源组的 IoT 中心。 在该处可以看到 IoT 中心连接设备并可复制设备连接字符串。

  ![查找连接字符串](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-connection-string-of-a-device.png)


现在，可以在 VS Code 的“Azure IoT 中心设备”部分看到新的物理设备：

![可以看到新的 IoT 中心设备](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-new-iot-hub-device.png)

## <a name="test-the-project"></a>测试项目

在示例应用运行时，DevKit 将通过 Wi-Fi 将传感器数据发送到 IoT 解决方案加速器。 若要查看结果，请遵循以下步骤：

1. 转到 IoT 解决方案加速器，并单击“仪表板”。

2. 在 IoT 解决方案加速器控制台上，将会看到 DevKit 传感器状态。 

![IoT 解决方案加速器中的传感器数据](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-dashboard.png)

如果单击传感器名称 (AZ3166)，则会在仪表板的右侧打开一个选项卡，可以在其中实时查看 MX Chip 传感器图表。


## <a name="send-a-c2d-message"></a>发送 C2D 消息
可以通过远程监视 v2 在设备上调用远程方法。
MX Chip 示例代码发布了三个方法。当选中传感器时，可以在 Method 节中看到这些方法。

![方法  MX Chip](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-methods.png)

可以使用“LedColor”方法更改某个 MX Chip Led 的颜色。 为此，请选择设备的复选框，然后单击“计划”按钮。 

![方法  MX Chip](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-schedule.png)

在显示所有方法的下拉列表中选择名为 ChangeColor 的方法，编写一个名称，然后单击“应用”。

![下拉列表  MX Chip](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-change-color.png)

在数秒钟内，物理 MX Chip 就会更改 RGB Led（在 A 按钮的下面）的颜色

![Led  MX Chip](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-devkit-led.png)


## <a name="change-device-id"></a>更改设备 ID

可以根据[此指南](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/)在 IoT 中心内更改设备 ID。


## <a name="problems-and-feedback"></a>问题和反馈

如果遇到问题，请参阅[常见问题解答](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)或通过以下渠道联系我们：

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [堆栈溢出](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>后续步骤

现在，你已学习了如何将 DevKit 设备连接到 IoT 解决方案加速器并将传感器数据可视化，下面是建议执行的后续步骤：

* [IoT 解决方案加速器概述](https://docs.microsoft.com/azure/iot-suite/)
* [将 MXChip IoT DevKit 设备连接到 Microsoft IoT Central 应用程序](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
