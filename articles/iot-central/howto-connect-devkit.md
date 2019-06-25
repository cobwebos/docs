---
title: 将 DevKit 设备连接到 Azure IoT Central 应用程序 | Microsoft Docs
description: 了解如何以设备开发人员的身份将 MXChip IoT DevKit 设备连接到 Azure IoT Central 应用程序。
author: dominicbetts
ms.author: dobett
ms.date: 03/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 130ca6bc946d44d80cddba5486d405bfb15523cb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66235884"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>将 MXChip IoT DevKit 设备连接到 Azure IoT Central 应用程序

本文介绍如何以设备开发人员的身份将 MXChip IoT DevKit (DevKit) 设备连接到 Microsoft Azure IoT Central 应用程序。

## <a name="before-you-begin"></a>开始之前

若要完成本文中的步骤，需要以下资源：

1. 基于“示例 Devkit”应用程序模板创建的 Azure IoT Central 应用程序  。 有关详细信息，请参阅[创建应用程序快速入门](quick-deploy-iot-central.md)。
1. DevKit 设备。 若要购买 DevKit 设备，请访问 [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/)。

## <a name="sample-devkits-application"></a>示例 Devkits 应用程序

从创建的应用程序**示例 Devkits**应用程序模板包括**MXChip**设备模板，用于定义以下设备特征：

- 遥测数据的度量值**湿度**，**温度**，**压力**，**磁力仪**(测量沿 X、 Y、 Z 轴)， **加速感应器**(测量沿 X、 Y、 Z 轴)，并**陀螺仪**(测量沿 X、 Y、 Z 轴)。
- 状态度量**设备状态**。
- 事件度量**按下按钮 B**。
- 设置**电压**，**当前**，**风扇转速**，和一个**IR**切换。
- 设备属性**骰子数字**并**设备位置**，这是 location 属性。
- 云属性**制造中**。
- 命令**Echo**并**倒计时**。 当在实际设备收到**Echo**命令，它在设备的显示器上显示已发送的值。 当在实际设备收到**倒计时**命令，LED 循环通过一种模式，并返回到 IoT 中心的设备发送倒计时值。

有关配置的完整详细信息，请参阅[MXChip 设备模板详细信息](#mxchip-device-template-details)

## <a name="add-a-real-device"></a>添加真实设备

### <a name="get-your-device-connection-details"></a>获取你的设备连接详细信息

Azure IoT Central 应用程序中添加真实的设备，从**MXChip**设备模板，并记下的设备连接详细信息：**作用域 ID、 设备 ID 和 Primary key**:

1. 添加**真实的设备**从 Device Explorer 中，选择 **+ 新建 > 实际**添加真实的设备。

    * 输入小写**设备 ID**，或使用建议**设备 ID**。
    * 输入**设备名称**，或使用建议的名称

    ![添加设备](media/howto-connect-devkit/add-device.png)

1. 若要获取连接详细信息的设备**作用域 ID**，**设备 ID**，和**主键**，选择**Connect**设备页上。

    ![连接详细信息](media/howto-connect-devkit/device-connect.png)

1. 记下连接详细信息。 暂时在断开 internet 时准备 DevKit 设备下一步。

### <a name="prepare-the-devkit-device"></a>准备 DevKit 设备

如果以前已使用了设备并且想要重新配置为使用不同的 WiFi 网络、 连接字符串或遥测度量，则按两**A**并**B**按钮，在同一时间。 如果它不起作用，请按**重置**按钮，然后重试。

#### <a name="to-prepare-the-devkit-device"></a>准备 DevKit 设备

1. 从 GitHub 上的[发布](https://aka.ms/iotcentral-docs-MXChip-releases)页下载 MXChip 的最新预建 Azure IoT Central 固件。
1. 使用 USB 线缆将 DevKit 设备连接到开发计算机。 在 Windows 中，已映射到 DevKit 设备上的存储的驱动器中会打开一个文件资源管理器窗口。 例如，该驱动器可能名为 **AZ3166 (D:)** 。
1. 将 **iotCentral.bin** 文件拖放到驱动器窗口。 复制完成后，设备会使用新固件重新启动。

1. 当 DevKit 设备重启时，会显示以下屏幕：

    ```
    Connect HotSpot:
    AZ3166_??????
    go-> 192.168.0.1
    PIN CODE xxxxx
    ```

    > [!NOTE]
    > 如果屏幕显示任何其他内容，请重置设备并同时按设备上的 A 和 B 按钮以重启设备   。

1. 现在，设备处于接入点 (AP) 模式。 可以从计算机或移动设备连接到此 WiFi 接入点。

1. 在计算机、手机或平板电脑上，连接到设备屏幕上显示的 WiFi 网络名称。 当连接到此网络时，你没有 internet 访问权限。 希望此状态，并在配置设备时仅连接到此网络的短时间。

1. 打开 Web 浏览器并导航到 [http://192.168.0.1/start](http://192.168.0.1/start)。 将显示以下网页：

    ![设备配置页](media/howto-connect-devkit/configpage.png)

    在 web 页上，输入：
    - WiFi 网络的名称
    - WiFi 网络密码
    - 设备的显示器上显示的 PIN 代码
    - 连接详细信息**作用域 ID**，**设备 ID**，并**主键**的设备 （您应已保存此执行的步骤）
    - 选择所有可用遥测度量

1. 选择“配置设备”后，会看到以下页： 

    ![设备已配置](media/howto-connect-devkit/deviceconfigured.png)

1. 按设备上的“重置”按钮。 

## <a name="view-the-telemetry"></a>查看遥测

当 DevKit 设备重启时，设备上的屏幕会显示：

* 已发送的遥测消息数。
* 失败数。
* 收到的所需属性数，以及发送的报告属性数。

> [!NOTE]
> 如果设备出现循环尝试连接时，检查设备是否**已阻止**中 IoT Central 和**解除阻止**设备，以便它可以连接到应用。

摇动设备发送报告的属性。 设备以“模具编号”设备属性的形式发送随机数。 

可以在 Azure IoT Central 查看遥测度量和报告属性值，以及配置设置：

1. 使用“Device Explorer”导航到所添加的真实 MXChip 设备的“度量”页：  

    ![导航到真实设备](media/howto-connect-devkit/realdevicenew.png)

1. 在“度量”页上，可以看到来自 MXChip 设备的遥测数据： 

    ![查看来自真实设备的遥测数据](media/howto-connect-devkit/devicetelemetrynew.png)

1. 在“属性”页上，可以查看设备报告的上一个模具编号和设备位置： 

    ![查看设备属性](media/howto-connect-devkit/devicepropertynew.png)

1. 在“设置”页上，可以更新 MXChip 设备上的设置： 

    ![查看设备设置](media/howto-connect-devkit/devicesettingsnew.png)

1. 上**命令**页上，可以调用**Echo**并**倒计时**命令：

    ![调用命令](media/howto-connect-devkit/devicecommands.png)

1. 在“仪表板”  页上，可以看到位置地图

    ![查看设备仪表板](media/howto-connect-devkit/devicedashboardnew.png)

## <a name="download-the-source-code"></a>下载源代码

若要浏览和修改设备代码，可以从 GitHub 下载该代码。 若要修改代码，应遵照这些说明来[准备](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/#step-5-prepare-the-development-environment)桌面操作系统的开发环境。

若要下载源代码，请在台式机上运行以下命令：

```cmd/sh
git clone https://github.com/Azure/iot-central-firmware
```

上述命令会将源代码下载到名为 `iot-central-firmware` 的文件夹。

> [!NOTE]
> 如果开发环境中未安装 git，可以从 [https://git-scm.com/download](https://git-scm.com/download) 下载  。

## <a name="review-the-code"></a>查看代码

使用 Visual Studio Code 打开`MXCHIP/mxchip_advanced`文件夹中的`iot-central-firmware`文件夹：

![Visual Studio Code](media/howto-connect-devkit/vscodeview.png)

若要查看如何将遥测数据发送到 Azure IoT Central 应用程序，请打开**telemetry.cpp**文件中`src`文件夹：

- 函数 `TelemetryController::buildTelemetryPayload` 使用设备传感器发送的数据创建 JSON 遥测有效负载。

- 该函数`TelemetryController::sendTelemetryPayload`调用`sendTelemetry`中**AzureIOTClient.cpp**将发送到 IoT 中心的 Azure IoT Central 应用程序所使用的 JSON 有效负载。

若要查看如何向 Azure IoT Central 应用程序报告属性值，请打开**telemetry.cpp**文件中`src`文件夹：

- 该函数`TelemetryController::loop`发送**位置**报告大约每隔 30 秒的属性。 它使用`sendReportedProperty`函数，在**AzureIOTClient.cpp**源文件。

- 该函数`TelemetryController::loop`发送**dieNumber**设备加速感应器检测到双点击时报告属性。 它使用`sendReportedProperty`函数，在**AzureIOTClient.cpp**源文件。

若要查看设备如何响应从 IoT Central 应用程序调用的命令，打开**registeredMethodHandlers.cpp**文件中`src`文件夹：

- **DmEcho**函数是为处理程序**echo**命令。 它显示**displayedValue**中设备的屏幕上的负载字段。

- **DmCountdown**函数是为处理程序**倒计时**命令。 它可以更改设备的 LED 的颜色并使用报告的属性将返回到 IoT 中心应用程序发送的倒计时值。 报告的属性具有与命令相同的名称。 该函数使用`sendReportedProperty`函数，在**AzureIOTClient.cpp**源文件。

中的代码**AzureIOTClient.cpp**源文件使用函数从[Microsoft Azure IoT Sdk 和适用于 C 库](https://github.com/Azure/azure-iot-sdk-c)与 IoT 中心进行交互。

有关如何修改、生成示例代码并将其上传到设备的信息，请参阅 `MXCHIP/mxchip_advanced` 文件夹中的 **readme.md** 文件。

## <a name="mxchip-device-template-details"></a>MXChip 设备模板详细信息

基于“示例 Devkit”应用程序模板创建的应用程序包含一个具有以下特征的 MXChip 设备模板：

### <a name="measurements"></a>度量

#### <a name="telemetry"></a>遥测

| 字段名     | 单位  | 最小值 | 最大值 | 小数位数 |
| -------------- | ------ | ------- | ------- | -------------- |
| 湿度       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| 压力       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

#### <a name="states"></a>States 
| 名称          | Display name   | 正常 | 小心 | 危险 | 
| ------------- | -------------- | ------ | ------- | ------ | 
| DeviceState   | 设备状态   | 绿色  | 橙色  | 红色    | 

#### <a name="events"></a>Events 
| 名称             | Display name      | 
| ---------------- | ----------------- | 
| ButtonBPressed   | 按钮 B 已按下  | 

### <a name="settings"></a>设置

数字设置

| 显示名称 | 字段名 | 单位 | 小数位数 | 最小值 | 最大值 | 初始 |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| 电压      | setVoltage | 伏 | 0              | 0       | 240     | 0       |
| 当前      | setCurrent | 安培  | 0              | 0       | 100     | 0       |
| 风扇速度    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

切换设置

| 显示名称 | 字段名 | 打开文本 | 关闭文本 | 初始 |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ON      | OFF      | 关闭     |

### <a name="properties"></a>属性

| 类型            | 显示名称 | 字段名 | 数据类型 |
| --------------- | ------------ | ---------- | --------- |
| 设备属性 | 模具号   | dieNumber  | 数字    |
| 设备属性 | 设备位置   | 位置  | 位置    |
| Text            | 制造于     | manufacturedIn   | 不适用       |

### <a name="commands"></a>命令

| Display name | 字段名 | 返回类型 | 输入的字段显示名称 | 输入的字段名称 | 输入的字段类型 |
| ------------ | ---------- | ----------- | ------------------------ | ---------------- | ---------------- |
| echo         | echo       | text        | 若要显示的值         | displayedValue   | text             |
| 倒计时    | 倒计时  | 数字      | 从计数               | countFrom        | 数字           |

## <a name="next-steps"></a>后续步骤

现在，已了解如何将 MXChip IoT DevKit 连接到 Azure IoT Central 应用程序，建议下一步是了解如何[设置自定义设备模板](howto-set-up-template.md)IoT 设备。
