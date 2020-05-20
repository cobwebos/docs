---
title: 创建自定义模拟设备 - Azure | Microsoft Docs
description: 在本教程中，请使用设备模拟来创建在模拟中使用的自定义模拟设备。
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/25/2018
ms.author: troyhop
ms.openlocfilehash: 302b863e7ad7d6df286adf53342356f279ab92d2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "61450498"
---
# <a name="tutorial-create-a-custom-simulated-device"></a>教程：创建自定义模拟设备

在本教程中，请使用设备模拟来创建在模拟中使用的自定义模拟设备。 若要着手使用设备模拟，可以使用包括的示例模拟设备之一。 也可按照本文中的说明创建自定义模拟设备。 如需更多自定义选项，请参阅[创建高级设备模型](iot-accelerators-device-simulation-advanced-device.md)。

本教程介绍以下操作：

>[!div class="checklist"]
> * 查看模拟设备模型的列表
> * 创建自定义模拟设备
> * 克隆设备模型
> * 删除设备模型

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必备条件

若要按本教程操作，需在 Azure 订阅中部署设备模拟的实例。

如果尚未部署设备模拟，则应完成[在 Azure 中部署并运行 IoT 设备模拟](quickstart-device-simulation-deploy.md)快速入门。

## <a name="open-device-simulation"></a>打开设备模拟

若要在浏览器中运行设备模拟，请先导航到 [Microsoft Azure IoT 解决方案加速器](https://www.azureiotsolutions.com)。

可能会要求使用 Azure 订阅凭据登录。

然后在[在 Azure 中部署并运行 IoT 设备模拟](quickstart-device-simulation-deploy.md)快速入门中部署的设备模拟的磁贴上单击“启动”。

## <a name="view-your-device-models"></a>查看设备模型

在菜单栏中选择“设备模型”。  “设备模型”页列出了此设备模拟实例中所有可用的设备模型： 

![设备模型](media/iot-accelerators-device-simulation-create-custom-device/devicemodelnav.png)

## <a name="create-a-device-model"></a>创建设备模型

在页面右上角单击“+ 添加设备模型”： 

![添加设备模型](media/iot-accelerators-device-simulation-create-custom-device/devicemodels.png)

在本教程中，请创建一个可以发送温度和湿度数据的模拟冰箱。

使用以下信息填写窗体：

| 设置             | 值                                                |
| ------------------- | ---------------------------------------------------- |
| 设备模型名称   | 冰箱                                         |
| 模型说明   | 带温度和湿度传感器的冰箱 |
| 版本             | 1.0                                                  |

> [!NOTE]
> 设备模型名称必须是唯一的。

单击“+ 添加数据点”，  为温度和湿度添加具有以下值的数据点：

| 数据点          | 行为        | 最小值 | 最大值 | 单位 |
| ------------------- | --------------- | --------- | --------- | ---- |
| 温度         | 随机          | -50       | 100       | F    |
| 湿度            | 随机          | 0         | 100       | %    |

单击“保存”保存设备模型。 

![创建设备模型](media/iot-accelerators-device-simulation-create-custom-device/adddevicemodel.png)

冰箱现在包括在设备模型列表中。 可能需单击“下一步”，才能转到下一页去查看冰箱。 

## <a name="clone-a-device-model"></a>克隆设备模型

克隆设备模型即可创建现有设备模型的副本。 然后，可以根据特定的需要编辑该副本。 在需要创建类似的设备模型时，克隆可以节省时间。

若要克隆设备模型，请勾选模型旁边的框，然后单击操作栏中的“克隆”： 

![删除设备模型](media/iot-accelerators-device-simulation-create-custom-device/clonedevice.png)

## <a name="delete-a-device-model"></a>删除设备模型

可以删除任何自定义设备模型。 若要删除设备模型，请勾选模型旁边的框，然后单击操作栏中的“删除”： 

![删除设备模型](media/iot-accelerators-device-simulation-create-custom-device/deletedevice.png)

## <a name="next-steps"></a>后续步骤

本教程介绍了如何创建、克隆和删除自定义设备模型。 若要详细了解设备模型，请参阅以下操作方法文章：

> [!div class="nextstepaction"]
> [创建高级设备模型](iot-accelerators-device-simulation-advanced-device.md)