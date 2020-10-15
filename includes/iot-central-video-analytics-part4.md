---
title: include 文件
description: include 文件
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 164f5803b6e9e62447423735e98f6e4c36c73f13
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876610"
---
### <a name="add-relationships"></a>添加关系

在“LVA Edge 网关”设备模板中的“模块/LVA Edge 网关模块”下，选择“关系”  。 选择“+ 添加关系”，然后添加以下两个关系：

|显示名称               |名称          |目标 |
|-------------------------- |------------- |------ |
|LVA Edge 运动探测器   |使用默认值   |LVA Edge 运动探测器设备 |
|LVA Edge 对象探测器   |使用默认值   |LVA Edge 对象探测器设备 |

再选择“保存”。

:::image type="content" source="media/iot-central-video-analytics-part4/relationships.png" alt-text="添加关系":::

### <a name="add-views"></a>添加视图

“LVA Edge 网关”设备模板不包含任何视图定义。

若要将视图添加到设备模板：

1. 在“LVA Edge 网关”设备模板中，导航到“视图”并选择“可视化设备”磁贴  。

1. 输入“LVA Edge 网关设备”作为视图名称。

1. 将以下磁贴添加到视图：

    * 具有以下设备信息属性的磁贴：设备型号、制造商、操作系统、处理器体系结构、软件版本、总内存和总存储      。
    * 具有“可用内存”和“系统检测信号”遥测值的折线图磁贴 。
    * 具有以下事件的事件历史记录磁贴：创建相机、删除相机、模块重启、模块已启动、模块已停止    。
    * 显示“IoT Central 客户端状态”遥测的 2x1 上一个已知值磁贴。
    * 显示“模块状态”遥测的 2x1 上一个已知值磁贴。
    * 显示“系统检测信号”遥测的 1x1 上一个已知值磁贴。
    * 显示“已连接相机”遥测的 1x1 上一个已知值磁贴。

    :::image type="content" source="media/iot-central-video-analytics-part4/gateway-dashboard.png" alt-text="添加关系":::

1. 选择“保存” 。

### <a name="publish-the-device-template"></a>发布设备模板

在将设备添加到应用程序之前，必须先发布设备模板：

1. 在“LVA Edge 网关”设备模板中选择“发布” 。

1. 在“将此设备模板发布到应用程序”页中，选择“发布” 。

LVA Edge 网关现在可作为设备类型在应用程序的“设备”页上使用。

## <a name="add-a-gateway-device"></a>添加网关设备

若要向应用程序添加“LVA Edge 网关”设备，请执行以下操作：

1. 导航到“设备”页，然后选择“LVA Edge 网关”设备模板 。

1. 选择“+ 新建”。

1. 在“创建新设备”对话框中，将设备名称更改为 LVA Gateway 001，并将设备 ID 改为 lva-gateway-001** 。

    > [!NOTE]
    > 设备 ID 在应用程序中必须是唯一的。

1. 选择“创建”。

设备状态为“已注册”。

### <a name="get-the-device-credentials"></a>获取设备凭据

要使设备能够连接到 IoT Central 应用程序，则需要提供凭据。 若要获取设备凭据：

1. 在“设备”页上，选择创建的 lva-gateway-001 设备 。

1. 选择“连接”。

1. 在“设备连接”页上，在 scratchpad.txt 文件中记下 ID 范围、设备 ID 和设备主密钥  。 稍后要使用这些值。

1. 请确保将连接方法设置为“共享访问签名”。

1. 选择“关闭”。

## <a name="next-steps"></a>后续步骤

现在你已使用“视频分析 - 对象和运动检测”应用程序模板创建了一个 IoT Central 应用程序，为网关设备创建了一个设备模板，并向应用程序中添加了一个网关设备。

如果要使用运行云 VM 的 IoT Edge 模块和模拟视频流试用视频分析 - 对象和运动检测应用程序，请：

> [!div class="nextstepaction"]
> [创建用于视频分析的 IoT Edge 实例 (Linux VM)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-vm.md)

如果要使用运行真实设备的 IoT Edge 模块和真实 ONVIF 相机试用视频分析 - 对象和运动检测应用程序，请：

> [!div class="nextstepaction"]
> [创建用于视频分析的 IoT Edge 实例 (Intel NUC)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-nuc.md)
