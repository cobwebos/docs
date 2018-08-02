---
title: 将 DevKit 设备连接到 Azure IoT Central 应用程序 | Microsoft Docs
description: 了解如何以设备开发人员的身份将 MXChip IoT DevKit 设备连接到 Azure IoT Central 应用程序。
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: ea9ff8f93ede3b9ec5e7eed83c6049b0c23de7e8
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205453"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>将 MXChip IoT DevKit 设备连接到 Azure IoT Central 应用程序

本文介绍如何以设备开发人员的身份将 MXChip IoT DevKit (DevKit) 设备连接到 Microsoft Azure IoT Central 应用程序。

## <a name="before-you-begin"></a>开始之前

若要完成本文中的步骤，需要以下各项：

1. 基于“示例 Devkit”应用程序模板创建的 Azure IoT Central 应用程序。 有关详细信息，请参阅[创建 Azure IoT Central 应用程序](howto-create-application.md)。
1. DevKit 设备。 若要购买 DevKit 设备，请访问 [MXChip IoT DevKit](http://mxchip.com/az3166)。


## <a name="sample-devkits-application"></a>**示例 Devkits** 应用程序

基于“示例 Devkit”应用程序模板创建的应用程序包含一个具有以下特征的 **MXChip** 设备模板： 

- 包含设备**湿度**、**温度**、**压力**、**磁力计**（沿 X、Y、Z 轴度量）、**加速计**（沿 X、Y、Z 轴度量）和**陀螺仪**（沿 X、Y、Z 轴度量）度量值的遥测数据。
- 包含**设备状态**的示例度量值的状态。
- “按钮 B 已按下”事件的事件度量值。 
- 显示**电压**、**电流**、**风扇速度**和 **IR** 切换的设置。
- 包含设备属性“模板号”和“设备位置”（也是“制造于”云属性中的位置属性）的属性。 


有关该配置的完整详细信息，请参阅 [MXChip 设备模板详细信息](howto-connect-devkit.md#mxchip-device-template-details)


## <a name="add-a-real-device"></a>添加真实设备

在 Azure IoT Central 应用程序中，从 **MXChip** 设备模板添加真实设备，并记下设备连接字符串。 有关详细信息，请参阅[将真实设备添加到 Azure IoT Central 应用程序](tutorial-add-device.md)。

### <a name="prepare-the-devkit-device"></a>准备 DevKit 设备

> [!NOTE]
> 如果以前使用过该设备且已存储 wifi 凭据，并且想要重新配置该设备以使用其他 WiFi 网络、连接字符串或遥测度量，请在开发板上同时按 **A** 和 **B** 按钮。 如果这不起作用，请按“重置”按钮并重试。

#### <a name="before-you-start-configuring-the-device"></a>在开始配置设备之前：
1. 在 IoT Central **示例 Devkits** 中，转到 `Device Explorer`-> `select MXChip Template` -> `Click on +New and choose **Real** Device` -> `Connect this device`（在右上角） 
2. 复制主连接字符串
3. 确保保存该连接字符串，因为在准备 DevKit 设备时，将临时断开与 Internet 的连接。 


#### <a name="to-prepare-the-devkit-device"></a>若要准备 DevKit 设备：


1. 从 GitHub 上的[发布](https://github.com/Azure/iot-central-firmware/releases)页下载 MXChip 的最新预建 Azure IoT Central 固件。 “发布”页上的下载文件名类似于 `AZ3166-IoT-Central-X.X.X.bin`。

1. 使用 USB 线缆将 DevKit 设备连接到开发计算机。 在 Windows 中，已映射到 DevKit 设备上的存储的驱动器中会打开一个文件资源管理器窗口。 例如，该驱动器可能名为 **AZ3166 (D:)**。

1. 将 **iotCentral.bin** 文件拖放到驱动器窗口。 复制完成后，设备会使用新固件重新启动。

1. 当 DevKit 设备重启时，会显示以下屏幕：

    ```
    Connect HotSpot:
    AZ3166_??????
    go-> 192.168.0.1 
    PIN CODE xxxxx
    ```

    > [!NOTE]
    > 如果屏幕显示任何其他内容，请同时按设备上的 **A** 和 **B** 按钮重新启动设备。 

1. 现在，设备处于接入点 (AP) 模式。 可以从计算机或移动设备连接到此 WiFi 接入点。

1. 在计算机、手机或平板电脑上，连接到设备屏幕上显示的 WiFi 网络名称。 连接到此网络时，不需要通过 Internet 访问。 此状态符合预期，只是在配置设备时才要与此网络保持短时间的连接。

1. 打开 Web 浏览器并导航到 [http://192.168.0.1/start](http://192.168.0.1/start)。 将显示以下网页：

    ![设备配置页](media/howto-connect-devkit/configpage.png)

    在该网页中： 
    - 添加 WiFi 网络的名称 
    - WiFi 网络密码
    - 设备 LCD 上显示的 PIN 码 
    - 设备的连接字符串（应该已经按照相关步骤保存此字符串），可以在 `https://apps.iotcentral.com`->`Device Explorer`->`Device`->`Select or Create a new Real Device`->`Connect this device`（右上角）找到连接字符串
    - 选择所有的可用遥测度量！ 

1. 选择“配置设备”后，会看到以下页：

    ![设备已配置](media/howto-connect-devkit/deviceconfigured.png)

1. 按设备上的“重置”按钮。



## <a name="view-the-telemetry"></a>查看遥测

当 DevKit 设备重启时，设备上的屏幕会显示：

* 已发送的遥测消息数。
* 失败数。
* 收到的所需属性数，以及发送的报告属性数。

摇晃设备会递增发送的报告属性数。 设备以“模具编号”设备属性的形式发送随机数。

可以在 Azure IoT Central 查看遥测度量和报告属性值，以及配置设置：

1. 使用“Device Explorer”导航到所添加的真实 MXChip 设备的“度量”页：

    ![导航到真实设备](media/howto-connect-devkit/realdevicenew.png)

1. 在“度量”页上，可以看到来自 MXChip 设备的遥测数据：

    ![查看来自真实设备的遥测数据](media/howto-connect-devkit/devicetelemetrynew.png)

1. 在“属性”页上，可以查看设备报告的上一个模具编号和设备位置：

    ![查看设备属性](media/howto-connect-devkit/devicepropertynew.png)

1. 在“设置”页上，可以更新 MXChip 设备上的设置：

    ![查看设备设置](media/howto-connect-devkit/devicesettingsnew.png)

1. 在“仪表板”页上，可以看到位置地图

    ![查看设备仪表板](media/howto-connect-devkit/devicedashboardnew.png)


## <a name="download-the-source-code"></a>下载源代码

若要浏览和修改设备代码，可以从 GitHub 下载该代码。 若要修改代码，应遵照这些说明来[准备](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/#step-5-prepare-the-development-environment)桌面操作系统的开发环境。

若要下载源代码，请在台式机上运行以下命令：

```cmd/sh
git clone https://github.com/Azure/iot-central-firmware
```

上述命令会将源代码下载到名为 `iot-central-firmware` 的文件夹。 

> [!NOTE]
> 如果开发环境中未安装 **git**，可以从 [https://git-scm.com/download](https://git-scm.com/download) 下载。

## <a name="review-the-code"></a>查看代码

使用准备开发环境时已安装的 Visual Studio Code 打开 `iot-central-firmware` 文件夹中的 `AZ3166` 文件夹： 

![Visual Studio Code](media/howto-connect-devkit/vscodeview.png)

若要查看遥测数据如何发送到 Azure IoT Central 应用程序，请打开源文件夹中的 **main_telemetry.cpp** 文件。

函数 `buildTelemetryPayload` 使用设备传感器发送的数据创建 JSON 遥测有效负载。

函数 `sendTelemetryPayload` 调用 **iotHubClient.cpp** 中的 `sendTelemetry`，将该 JSON 有效负载发送到 Azure IoT Central 应用程序使用的 IoT 中心。

若要查看如何向 Azure IoT Central 应用程序报告属性值，请打开源文件夹中的 **main_telemetry.cpp** 文件。

当加速度传感器检测到双击时，函数 `telemetryLoop` 会发送 **doubleTap** 报告属性。 它使用 **iotHubClient.cpp** 源文件中的 `sendReportedProperty` 函数。

**iotHubClient.cpp** 源文件中的代码使用 [Microsoft Azure IoT SDK 和 C 库](https://github.com/Azure/azure-iot-sdk-c)中的函数来与 IoT 中心交互。

有关如何修改、生成示例代码并将其上传到设备的信息，请参阅 `AZ3166` 文件夹中的 **readme.md** 文件。

## <a name="mxchip-device-template-details"></a>MXChip 设备模板详细信息 

基于“示例 Devkit”应用程序模板创建的应用程序包含一个具有以下特征的 MXChip 设备模板：

### <a name="measurements"></a>度量

#### <a name="telemetry"></a>遥测 

| 字段名称     | 单位  | 最小值 | 最大值 | 小数位数 |
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
| 名称          | 显示名称   | 正常 | 小心 | 危险 | 
| ------------- | -------------- | ------ | ------- | ------ | 
| DeviceState   | 设备状态   | 绿色  | 橙色  | 红色    | 

#### <a name="events"></a>活动 
| 名称             | 显示名称      | 
| ---------------- | ----------------- | 
| ButtonBPressed   | 按钮 B 已按下  | 

### <a name="settings"></a>设置

数字设置

| 显示名称 | 字段名称 | 单位 | 小数位数 | 最小值 | 最大值 | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| 电压      | setVoltage | 伏 | 0              | 0       | 240     | 0       |
| Current      | setCurrent | 安培  | 0              | 0       | 100     | 0       |
| 风扇速度    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

切换设置

| 显示名称 | 字段名称 | 打开文本 | 关闭文本 | Initial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | 亮起      | 熄灭      | 关闭     |

### <a name="properties"></a>属性

| Type            | 显示名称 | 字段名称 | 数据类型 |
| --------------- | ------------ | ---------- | --------- |
| 设备属性 | 模具编号   | dieNumber  | 数字    |
| 设备属性 | 设备位置   | location  | location    |
| 文本            | 制造于     | manufacturedIn   | 不适用       |



## <a name="next-steps"></a>后续步骤

了解如何将 DevKit 设备连接到 Azure IoT Central 应用程序后，建议接下来执行以下步骤：

* [准备和连接 Raspberry Pi](howto-connect-raspberry-pi-python.md)
