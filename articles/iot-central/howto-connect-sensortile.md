---
title: 将 SensorTile 设备连接到 Azure IoT Central 应用程序 |Microsoft Docs
description: 作为设备开发人员, 了解如何将 SensorTile 设备连接到 Azure IoT Central 应用程序。
author: sarahhubbard
ms.author: sahubbar
ms.date: 08/24/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: sandeep.pujar
ms.openlocfilehash: 3d804b1e14d1b79266a74340e8682f1bf03d8f30
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050556"
---
# <a name="connect-sensortilebox-device-to-your-azure-iot-central-application"></a>将 SensorTile 设备连接到 Azure IoT Central 应用程序

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

本文介绍如何作为设备开发人员将 SensorTile 设备连接到 Microsoft Azure IoT Central 应用程序。

## <a name="before-you-begin"></a>开始之前

若要完成本文中的步骤, 需要以下资源:

* SensorTile 设备。 有关详细信息, 请参阅[SensorTile](https://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mems-motion-sensor-eval-boards/steval-mksbox1v1.html)。
* 你的 Android 设备上安装了 ST BLE 传感器应用, 你可以[从此处下载它](https://play.google.com/store/apps/details?id=com.st.bluems)。 有关详细信息, 请访问:[ST BLE 传感器](https://www.st.com/stblesensor)
* 从**DevKits**应用程序模板创建的 Azure IoT Central 应用程序。 有关详细信息，请参阅[创建应用程序快速入门](quick-deploy-iot-central.md)。
* 通过访问 "**设备模板**" 页, 单击 " **+ 新建**", 然后选择 " **SensorTile** " 模板, 将**SensorTile**设备模板添加到 IoT Central 应用程序。

### <a name="get-your-device-connection-details"></a>获取设备连接详细信息

在 Azure IoT Central 应用程序中, 从**SensorTile**设备模板添加真实设备, 并记下设备连接详细信息:**作用域 id**、**设备 id**和**主键**:

1. 从设备添加设备。 选择 " **+ 新建 > real** " 以添加实际设备。

    * 输入小写**设备 id**, 或使用建议的**设备 id**。
    * 输入**设备名称**, 或使用建议的名称

    ![添加设备](media/howto-connect-sensortile/real-device.png)

1. 若要获取设备连接详细信息、**作用域 id**、**设备 id**和**主密钥**, 请在 "设备" 页上选择 "**连接**"。

    ![连接详细信息](media/howto-connect-sensortile/connect-device.png)

1. 记下 "连接详细信息"。 在下一步中准备 DevKit 设备时, 会暂时断开 internet 连接。

## <a name="set-up-the-sensortilebox-with-the-mobile-application"></a>设置移动应用程序的 SensorTile

本部分介绍如何将应用程序固件推送到设备上。 然后, 使用蓝牙低功耗 (BLE) 连接将设备数据发送到使用 ST BLE 传感器移动应用程序 IoT Central。

1. 打开 ST BLE 传感器应用, 并按 "**创建新应用**" 按钮。

    ![创建应用](media/howto-connect-sensortile/create-app.png)

1. 选择**Barometer**应用程序。
1. 按 "上传" 按钮。

    ![Barometer 上传](media/howto-connect-sensortile/barometer-upload.png)

1. 按下与 SensorTile 关联的 "播放" 按钮。
1. 完成此过程后, SensorTile 会在 BLE 上流式传输温度、压力和湿度。

## <a name="connect-the-sensortilebox-to-the-cloud"></a>将 SensorTile 连接到云

本部分介绍如何将 SensorTile 连接到移动应用程序, 以及如何将移动应用程序连接到云。

1. 使用左侧菜单中, 选择 "**云日志记录**" 按钮。

    ![云日志记录](media/howto-connect-sensortile/cloud-logging.png)

1. 选择 " **Azure IoT Central**作为云提供商。
1. 插入先前记下的设备 ID 和作用域 ID。

    ![凭据](media/howto-connect-sensortile/credentials.png)

1. 选择 "**应用程序密钥**" 单选按钮。
1. 单击 "**连接**", 然后选择要上传的遥测数据。
1. 几秒钟后, 数据将显示在 "IoT Central" 应用程序仪表板上。

## <a name="sensortilebox-device-template-details"></a>SensorTile 设备模板详细信息

通过 SensorTile 设备模板创建的应用程序具有以下特征:

### <a name="telemetry"></a>遥测

| 字段名称     | 单位  | 最小值 | 最大值 | 小数位数 |
| -------------- | ------ | ------- | ------- | -------------- |
| 湿度       | %      | 30       | 90     | 1              |
| temp           | °C     | 0     | 40     | 1              |
| 压力       | mbar    | 900     | 1100    | 2              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | dps   | -3276   | 3276    | 1              |
| gyroscopeY     | dps   | -3276   | 3276    | 1              |
| gyroscopeZ     | dps   | -3276   | 3276    | 1              |
| FFT_X     |    |    |     |               |
| FFT_Y     |    |    |     |               |
| FFT_Z     |    |    |     |               |

## <a name="next-steps"></a>后续步骤

现在, 你已了解如何将 SensorTile 连接到 Azure IoT Central 应用程序, 接下来建议的下一步是了解如何为你自己的 IoT 设备[设置自定义设备模板](howto-set-up-template.md)。
