---
title: 教程：使用 IoT Central 创建太阳能板监视应用
description: 教程：了解如何使用 Azure IoT Central 应用程序模板创建太阳能板应用程序。
author: op-ravi
ms.author: omravi
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: d5ea3d3420cb598693ccaede7ee10d2f8c4fd839
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "77025769"
---
# <a name="tutorial-create-and-walk-through-the-solar-panel-monitoring-app-template"></a>教程：创建并演练太阳能板监视应用模板 



本教程将指导你完成创建太阳能板监视应用程序的过程，其中包括具有模拟数据的示例设备模型。 在本教程中，学习：


> [!div class="checklist"]
> * 免费创建太阳能板应用
> * 应用程序演练
> * 清理资源


如果没有订阅，请[创建免费试用帐户](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>先决条件
- 无
- 建议使用 Azure 订阅，但不是试用所必需的


## <a name="create-a-solar-panel-monitoring-app"></a>创建太阳能板监视应用 

可以通过三个简单的步骤来创建此应用程序：

1. 打开 [Azure IoT Central 主页](https://apps.azureiotcentral.com)，然后单击“构建”  以创建新应用程序。 

2. 选择“能源”  选项卡，然后在“太阳能板监视”  应用程序磁贴下单击“创建应用”  。 

    > [!div class="mx-imgBorder"]
    > ![构建应用](media/tutorial-iot-central-solar-panel/solar-panel-build.png)
  
3. 创建应用将打开新应用程序窗体   。 填写所需的详细信息，如下图所示：
    * **应用程序名称**：为 IoT Central 应用程序选择一个名称。 
    * **URL**：选择 IoT Central URL，平台将验证其唯一性。
    * **7 天免费试用版**：如果已有 Azure 订阅，则建议使用默认设置。 如果没有 Azure 订阅，请从免费试用版入手。
    * **计费信息**：应用程序本身是免费的。 预配应用的资源时需要目录、Azure 订阅和区域详细信息。
    * 单击页面底部的“创建”  按钮，将在一分钟左右创建应用。
        ![“新建应用程序”窗体](media/tutorial-iot-central-solar-panel/solar-panel-create-app.png)
        
        ![“新建应用程序”表单 - 计费信息](media/tutorial-iot-central-solar-panel/solar-panel-create-app-billinginfo.png)


### <a name="verify-the-application-and-simulated-data"></a>验证应用程序和模拟数据

新创建的太阳能板应用是你的应用，你可以随时修改它。 在修改应用之前，请确保应用已部署并按预期方式工作。

若要验证应用创建和数据模拟，请转到“仪表板”  。 如果可以看到包含某些数据的磁贴，则表明应用部署成功。 数据模拟可能需要几分钟的时间来生成数据，因此请提供 1-2 分钟。 

## <a name="application-walk-through"></a>应用程序演练
成功部署应用模板之后，附带了示例智能计量设备、设备型号和仪表板。

Adatum 是一家虚构的能源公司，负责监视和管理太阳能板。 在太阳能板监视仪表板上，可以看到太阳能板属性、数据和示例命令。 它使操作员和支持团队能够在转变为支持事件之前主动执行以下活动：
* 查看地图上的最新面板信息及其安装位置
* 主动检查面板状态和连接状态
* 查看能源产生和温度趋势，以捕获任何异常模式
* 跟踪计划和计费用途的总能耗产生
* 命令和控制操作，例如激活面板和更新固件版本。 在模板中，命令按钮显示可能的功能，并且不发送实际命令。

> [!div class="mx-imgBorder"]
> ![太阳能板监视仪表板](media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png)

### <a name="devices"></a>设备
该应用附带了一个示例太阳能板设备。 单击“设备”  选项卡可以看到设备详细信息。

> [!div class="mx-imgBorder"]
> ![太阳能板设备](media/tutorial-iot-central-solar-panel/solar-panel-device.png)


单击示例设备“SP0123456789”  链接以查看设备详细信息。 在“更新属性”页上，可以更新设备的可写属性，并在仪表板上可视化更新的值  。 

> [!div class="mx-imgBorder"]
> ![太阳能板属性](media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png)


### <a name="device-template"></a>设备模板
单击“设备模板”  选项卡以查看太阳能板设备型号。 模型为数据、属性、命令和视图预定义了接口。

> [!div class="mx-imgBorder"]
> ![太阳能板设备模板](media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png)


## <a name="clean-up-resources"></a>清理资源
如果决定不继续使用此应用程序，请使用以下步骤删除应用程序：

1. 在左侧窗格中，打开“管理”选项卡
2. 选择“应用程序设置”，然后单击页面底部的“删除”按钮。 

    > [!div class="mx-imgBorder"]
    > ![删除应用程序](media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png)


## <a name="next-steps"></a>后续步骤
* 若要了解太阳能板应用体系结构，请参阅[概念一文](https://docs.microsoft.com/azure/iot-central/energy/concept-iot-central-solar-panel-app)
* 免费创建太阳能板应用程序模板：[太阳能板应用](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* 若要详细了解 IoT Central，请参阅 [IoT Central 概述](https://docs.microsoft.com/azure/iot-central/)

