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
ms.openlocfilehash: 60a62733a17d1a3dcc4ba80ed7ceb1c37c8ac5d6
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876609"
---
## <a name="create-the-azure-iot-edge-gateway-device"></a>创建 Azure IoT Edge 网关设备

视频分析 - 对象和运动检测应用程序包括“LVA Edge 对象探测器”设备模板和“LVA Edge 运动检测”设备模板 。 在本部分中，你将使用部署清单创建网关设备模板，并将网关设备添加到 IoT Central 应用程序。

### <a name="create-a-device-template-for-the-lva-edge-gateway"></a>为 LVA Edge 网关创建设备模板

若要导入部署清单并创建“LVA Edge 网关”设备模板，请执行以下操作：

1. 在 IoT Central 应用程序中，导航到“设备模板”并选择“+ 新建” 。

1. 在“选择模板类型”页上，选择“Azure IoT Edge”磁贴。  然后选择“下一步:自定义”。

1. 在“上传 Azure IoT Edge 部署清单”页上，输入“LVA Edge 网关”作为模板名称，并选中“具有下游设备的网关设备”。

    暂时不要浏览部署清单。 如果浏览了部署清单，则部署向导需要为每个模块提供一个接口，但你只需要公开 LvaEdgeGatewayModule 的接口。 在后面的步骤中上传清单。

    :::image type="content" source="./media/iot-central-video-analytics-part3/upload-deployment-manifest.png" alt-text="请勿上传部署清单":::

    在完成时选择“下一步:  查看”。

1. 在“查看”页上，选择“创建”   。

### <a name="import-the-device-capability-model"></a>导入设备功能模型

设备模板必须包含设备功能模型。 在“LVA Edge 网关”页上，选择“导入功能模型”磁贴 。 导航到之前创建的 lva-configuration 文件夹，并选择 LvaEdgeGatewayDcm.json 文件 。

“LVA Edge 网关”设备模板现在包含“LVA Edge 网关模块”以及三个接口 ：设备信息、LVA Edge 网关设置和 LVA Edge 网关接口  。
