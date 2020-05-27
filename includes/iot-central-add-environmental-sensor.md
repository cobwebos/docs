---
title: include 文件
description: include 文件
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 03/12/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 9717c76b42a63479c77f862057bfb141954eacff
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673401"
---
## <a name="create-a-device-template"></a>创建设备模板

在本地计算机上创建名为 `environmental-sensor` 的文件夹。

下载[环境传感器功能模型](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/9004219bff1e958b7cd6ff2a52209f4b7ae19396/samples/EnvironmentalSensorInline.capabilitymodel.json) JSON 文件，并将其保存到 `environmental-sensor` 文件夹中。

使用文本编辑器将 `{YOUR_COMPANY_NAME_HERE}` 的两个实例替换为下载的 `EnvironmentalSensorInline.capabilitymodel.json` 文件中的公司名称。

在 Azure IoT Central 应用程序中，通过导入 `EnvironmentalSensorInline.capabilitymodel.json` 设备功能模型文件创建名为“环境传感器”的设备模板： 

![包含导入的设备功能模型的设备模板](./media/iot-central-add-environmental-sensor/device-template.png)

设备功能模型包括两个接口：标准的“设备信息”接口，以及自定义的“环境传感器”接口。   “环境传感器”接口定义以下功能： 

| 类型 | 显示名称 | 说明 |
| ---- | ------------ | ----------- |
| properties | 设备状态     | 设备的状态。 有两种状态：联机/脱机。 |
| 属性（可写） | 客户名称    | 当前正在操作设备的客户的名称。 |
| 属性（可写） | 亮度级别 | 设备上指示灯的亮度级别。 可以指定为 1（高）、2（中）、3（低）。 |
| 遥测 | 温度 | 设备检测到的当前温度。 |
| 遥测 | 湿度    | 设备检测到的当前湿度。 |
| Command | blink          | 开始在设备上按给定的时间间隔闪烁 LED。 |
| Command | turnon         | 打开设备上的 LED。 |
| Command | turnoff        | 关闭设备上的 LED。 |
| Command | rundiagnostics | 此异步命令在设备上启动诊断运行。 |

若要自定义“设备状态”属性在 IoT Central 应用程序中的显示方式，请在设备模板中选择“自定义”。   展开“设备状态”项，输入“联机”作为“True 名称”，输入“脱机”作为“False 名称”。      保存更改：

![自定义设备模板](./media/iot-central-add-environmental-sensor/customize-template.png)

## <a name="create-views"></a>创建视图

使用视图可与连接到 IoT Central 应用程序的设备交互。 例如，可以让某些视图显示遥测数据，让某些视图显示属性，并在某些视图中编辑可写属性和云属性。 视图是设备模板的一部分。

若要将一些默认视图添加到“环境传感器”设备模板，请导航到该设备模板，选择“视图”，然后选择“生成默认视图”磁贴。    确保“概览”和“关于”处于“开”状态，然后选择“生成默认仪表板视图”。     现已在模板中定义了两个默认视图。

“环境传感器”接口包括两个可写属性 -“客户名称”和“亮度级别”。    若要创建视图，可以编辑以下属性：

1. 选择“视图”，然后选择“编辑设备和云数据”磁贴。  

1. 输入“属性”作为窗体名称。 

1. 选择“亮度级别”和“客户名称”属性。   然后选择“添加部分”。 

1. 保存所做更改。

![添加一个用于编辑属性的视图](./media/iot-central-add-environmental-sensor/properties-view.png)

## <a name="publish-the-template"></a>发布模板

在添加使用“环境传感器”设备模板的设备之前，必须发布该模板。 

在设备模板中选择“发布”。  在“将此设备模板发布到应用程序”面板中，选择“发布”。  

若要检查模板是否可供使用，请导航到 IoT Central 应用程序中的“设备”页。  “设备”部分显示了应用程序中已发布设备的列表： 

![“设备”页上已发布的模板](./media/iot-central-add-environmental-sensor/published-templates.png)

## <a name="add-a-real-device"></a>添加真实设备

在 Azure IoT Central 应用程序中，将真实设备添加到在上一部分中创建的设备模板：

1. 在“设备”页上，选择“环境传感器”设备模板。  

    > [!TIP]
    > 选择“+ 新建”之前，请务必选择要使用的模板，否则会创建未关联的设备。 

1. 选择“+ 新建”  。

1. 确保“模拟”的选择状态为“关闭”。   然后选择“创建”  。

单击设备名称，然后选择“连接”。  记下“设备连接”页上的设备连接信息 -“ID 范围”、“设备 ID”和“主密钥”。     创建设备代码时需要这些值：

![设备连接信息](./media/iot-central-add-environmental-sensor/device-connection.png)
