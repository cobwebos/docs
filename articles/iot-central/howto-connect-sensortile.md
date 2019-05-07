---
title: SensorTile.box 设备连接到 Azure IoT Central 应用程序 |Microsoft Docs
description: 作为设备开发人员，了解如何将 SensorTile.box 设备连接到 Azure IoT Central 应用程序。
author: sarahhubbard
ms.author: sahubbar
ms.date: 04/24/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: sandeep.pujar
ms.openlocfilehash: 580a8baa19e8ed4fc3f4449ead9d8aedbc4c039a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160900"
---
# <a name="connect-sensortilebox-device-to-your-azure-iot-central-application"></a>SensorTile.box 设备连接到 Azure IoT Central 应用程序

本指南介绍了如何为设备开发人员，SensorTile.box 设备连接到 Microsoft Azure IoT Central 应用程序。

## <a name="before-you-begin"></a>开始之前

若要完成本文中的步骤，需要以下资源：

* SensorTile.box 设备，请参阅[SensorTile.box](https://www.st.com/content/st_com/en/products/evaluation-tools/SensorTile.box)有关详细信息。
* 在 Android 设备，你可以 [从此处下载] 上安装的 ST BLE 传感器应用程序 (https://play.google.com/store/apps/details?id=com.st.bluems)。 有关详细信息，请访问: [ST BLE 传感器] (http://www.st.com/stblesensor)
* 从创建的 Azure IoT Central 应用程序**DevKits**应用程序模板。 有关详细信息，请参阅[创建应用程序快速入门](quick-deploy-iot-central.md)。
* 添加**SensorTile.box**引入 IoT Central 应用程序通过访问设备模板**设备模板**页上，单击 **+ 新建**，然后选择**SensorTile**模板。

### <a name="get-your-device-connection-details"></a>获取你的设备连接详细信息

Azure IoT Central 应用程序中添加真实的设备，从**SensorTile.box**设备模板，并记下的设备连接详细信息：**作用域 ID、 设备 ID 和 Primary key**:

1. 从 Device Explorer 中添加设备。 选择**新建 > 实际**添加真实的设备。

    * 输入小写**设备 ID**，或使用建议**设备 ID**。
    * 输入**设备名称**，或使用建议的名称

    ![添加设备](media/howto-connect-sensortile/real-device.png)

1. 若要获取连接详细信息的设备**作用域 ID**，**设备 ID**，和**主键**，选择**Connect**设备页上。

    ![连接详细信息](media/howto-connect-sensortile/connect-device.png)

1. 记下连接详细信息。 暂时在断开 internet 时准备 DevKit 设备下一步。

## <a name="set-up-the-sensortilebox-with-the-mobile-application"></a>设置移动应用程序与 SensorTile.box

在本部分中，将了解如何推送到设备上的应用程序固件和设备将数据发送到 IoT 中心通过 ST BLE 传感器移动应用通过蓝牙低功耗 (BLE) 连接。
1. 打开 ST BLE 传感器应用程序，并按**创建新的应用**按钮。

    ![创建应用](media/howto-connect-sensortile/create-app.png)

1. 选择**计量仪**应用程序。
1. 按上载按钮。

    ![计量仪上传](media/howto-connect-sensortile/barometer-upload.png)

1. 按播放按钮与你 SensorTile.box 相关联。
1. 该过程完成后，SensorTile.box 将通过 BLE 流式温度、 压力和湿度。

## <a name="connect-the-sensortilebox-to-the-cloud"></a>连接到云的 SensorTile.box

在本部分中将了解如何连接 SensorTile.box 到移动应用程序，并连接到云的移动应用程序。
1. 使用左侧的菜单中，选择**云日志记录**按钮。

    ![云日志记录](media/howto-connect-sensortile/cloud-logging.png)

1. 选择**Azure IoT Central**为云提供程序。
1. 插入的设备 ID 和以前记录的作用域 ID。

    ![凭据](media/howto-connect-sensortile/credentials.png)

1. 选择**应用程序密钥**单选按钮。
1. 单击**Connect** ，然后选择你想要上传遥测数据。
1. 几秒钟后，将 IoT Central 应用程序仪表板上显示数据。

## <a name="sensortilebox-device-template-details"></a>SensorTile.box 设备模板详细信息

从具有以下特征的 SensorTile.box 设备模板创建的应用程序：

### <a name="telemetry"></a>遥测

| 字段名     | 单位  | 最小值 | 最大值 | 小数位数 |
| -------------- | ------ | ------- | ------- | -------------- |
| 湿度       | %      | 30       | 90     | 1              |
| temp           | °C     | 0     | 40     | 第              |
| 压力       | mbar    | 900     | 1100    | 2              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | dps   | -3276   | 3276    | 1              |
| gyroscopeY     | dps   | -3276   | 3276    | 第              |
| gyroscopeZ     | dps   | -3276   | 3276    | 第              |
| FFT_X     |    |    |     |               |
| FFT_Y     |    |    |     |               |
| FFT_Z     |    |    |     |               |


## <a name="next-steps"></a>后续步骤

现在，已了解如何将 SensorTile.box 连接到 Azure IoT Central 应用程序，建议下一步是了解如何[设置自定义设备模板](howto-set-up-template.md)IoT 设备。
