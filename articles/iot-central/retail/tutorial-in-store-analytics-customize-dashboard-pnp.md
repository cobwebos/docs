---
title: 在 Azure IoT Central 中自定义操作员仪表板 | Microsoft Docs
description: 本教程介绍如何在 IoT Central 应用程序中自定义操作员仪表板和管理设备。
services: iot-central
ms.service: iot-central
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 10/03/2019
ms.openlocfilehash: c145903d4fee1d0fbb804aa9dbc7b99b141529b3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495136"
---
# <a name="tutorial--customize-the-operator-dashboard-and-manage-devices-in-azure-iot-central"></a>教程：在 Azure IoT Central 中自定义操作员仪表板和管理设备

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

本教程介绍如何作为构建者在 Azure IoT Central 店内分析应用程序中自定义操作员仪表板。 应用程序操作员可以使用自定义仪表板来运行应用程序和管理连接的设备。

本教程介绍如何执行下列操作：
> [!div class="checklist"]
> * 更改仪表板名称
> * 自定义仪表板上的图像磁贴
> * 排列磁贴以修改布局
> * 添加遥测数据磁贴以显示情况
> * 添加属性磁贴以显示设备详细信息
> * 添加命令磁贴以运行命令

## <a name="prerequisites"></a>先决条件

在开始本教程之前，构建者应完成第一篇教程以创建 Azure IoT Central 店内分析应用程序并添加设备：

* [在 Azure IoT Central 中创建店内分析应用程序](./tutorial-in-store-analytics-create-app-pnp.md)（必需）

## <a name="change-the-dashboard-name"></a>更改仪表板名称
若要自定义操作员仪表板，请在应用程序中编辑默认仪表板。 （可选）可以创建其他新仪表板。 自定义应用程序中的仪表板的第一步是更改名称。

1. 导航到 [Azure IoT Central 应用程序管理器](https://aka.ms/iotcentral)网站。

1. 打开在[在 Azure IoT Central 中创建店内分析应用程序](./tutorial-in-store-analytics-create-app-pnp.md)教程中创建的情况监视应用程序。

1. 在仪表板工具栏上，选择“编辑”  。 在编辑模式下，可以自定义仪表板的外观、布局和内容。

    ![Azure IoT Central 编辑仪表板](./media/tutorial-in-store-analytics-customize-dashboard-pnp/dashboard-edit.png)

1. （可选）隐藏左窗格。 隐藏左窗格后，可以有更大的工作区域来编辑仪表板。

1. 在“仪表板名称”中输入仪表板的易记名称  。 本教程使用名为 Contoso 的虚构公司，示例仪表板名称为“Contoso 仪表板”  。 

1. 选择“保存”。  这会保存对仪表板的更改并禁用编辑模式。

    ![Azure IoT Central 更改仪表板名称](./media/tutorial-in-store-analytics-customize-dashboard-pnp/dashboard-change-name.png)

## <a name="customize-image-tiles-on-the-dashboard"></a>自定义仪表板上的图像磁贴
Azure IoT Central 应用程序仪表板由一个或多个磁贴组成。 磁贴是一个用来显示仪表板内容的矩形容器。 可以将各种类型的内容与磁贴相关联，并拖放和重设磁贴大小，以自定义仪表板布局。 有多种类型的磁贴可用于显示内容。 图像磁贴包含图像，你可以添加一个 URL 以使用户能够单击该图像。 标签磁贴显示纯文本。 Markdown 磁贴包含格式化的内容，可让你设置以 HTML 格式呈现的图像、URL、标题和 Markdown 代码。 遥测数据、属性或命令磁贴显示特定于设备的数据。 

本部分介绍如何自定义仪表板上的图像磁贴。

自定义仪表板上显示品牌图像的图像磁贴：

1. 在仪表板工具栏上，选择“编辑”  。 

1. 在显示 Northwind 品牌图像的图像磁贴上选择“配置”  。 

    ![Azure IoT Central 编辑品牌图像](./media/tutorial-in-store-analytics-customize-dashboard-pnp/brand-image-edit.png)

1. 更改“标题”  。 当用户将鼠标悬停在图像上时，会显示标题。

1. 选择“图像”  。 此时将打开一个对话框，你可以在其中上载自定义图像。 

1. （可选）指定图像的 URL。

1. 选择“更新配置”  。 “更新配置”  按钮保存对仪表板的更改并启用编辑模式。

    ![Azure IoT Central 保存品牌图像](./media/tutorial-in-store-analytics-customize-dashboard-pnp/brand-image-save.png)

1. （可选）在标题为“文档”的磁贴上选择“配置”，   ，并为支持内容指定 URL。 

自定义图像磁贴以显示商店中传感器区域的地图：

1. 在显示默认商店区域地图的图像磁贴上选择“配置”  。 

1. 选择“图像”  ，并使用对话框上载商店区域地图的自定义图像。 

1. 选择“更新配置”  。

    ![Azure IoT Central 保存商店地图](./media/tutorial-in-store-analytics-customize-dashboard-pnp/store-map-save.png)

    Contoso 商店地图示例显示了四个区域：两个结帐区域、一个服装和个人护理品区域，以及一个百货和熟食区域。 在本教程中，将传感器与这些区域关联以提供遥测数据。

    ![Azure IoT Central 商店区域](./media/tutorial-in-store-analytics-customize-dashboard-pnp/store-zones.png)

1. 选择“保存”。  

## <a name="arrange-tiles-to-modify-the-layout"></a>排列磁贴以修改布局
自定义仪表板的一个关键步骤是重新排列磁贴，以创建有用的视图。 应用程序操作员使用仪表板可视化设备遥测数据、管理设备和监视商店中的情况。 Azure IoT Central 简化了应用程序构建者的创建仪表板任务。 通过仪表板编辑模式，可以快速添加、移动、删除磁贴和重设磁贴大小。 “店内分析 - 结帐”  应用程序模板也简化了创建仪表板的任务。 它提供工作仪表板布局（其中连接了传感器），以及显示结帐行计数和环境情况的磁贴。

在本部分中，将在“店内分析 - 结帐”  应用程序模板中重新排列仪表板，以创建自定义布局。

删除不打算在应用程序中使用的磁贴：

1. 在仪表板工具栏上，选择“编辑”  。 

1. 选择“X 删除”  以删除以下磁贴：“返回到所有区域”  、“访问商店仪表板”  、“等待时间”  ，以及与“结帐 3”  关联的所有三个磁贴。 Contoso 商店仪表板不使用这些磁贴。 

    ![Azure IoT Central 删除磁贴](./media/tutorial-in-store-analytics-customize-dashboard-pnp/delete-tiles.png)

1. 滚动以使剩余的仪表板磁贴进入视图。

1. 选择“X 删除”  以删除以下磁贴：“预热结帐区域”  、“冷却结帐区域”  “占用传感器设置”  、“恒温器传感器设置”  和“环境情况”  。 

   ![Azure IoT Central 删除其余磁贴](./media/tutorial-in-store-analytics-customize-dashboard-pnp/delete-tiles-2.png)

1. 选择“保存”。  删除未使用的磁贴可释放编辑页面中的空间，简化操作员的仪表板视图。

1. 查看对仪表板的更改。

   ![Azure IoT Central 删除磁贴后](./media/tutorial-in-store-analytics-customize-dashboard-pnp/after-delete-tiles.png)

删除未使用的磁贴后，重新排列其余磁贴以创建有组织的布局。 新布局包含了你在后面步骤中添加的磁贴的空间。

重新排列其余磁贴：

1. 选择“编辑”  。

1. 选择“占用固件”  磁贴，将其拖动到“占用”  电池磁贴的右侧。

1. 选择“恒温器固件”  ，将其拖动到“恒温器”  电池磁贴的右侧。

1. 选择“保存”。 

1. 查看布局更改。 

    ![Azure IoT Central 固件电池磁贴](./media/tutorial-in-store-analytics-customize-dashboard-pnp/firmware-battery-tiles.png)

## <a name="add-telemetry-tiles-to-display-conditions"></a>添加遥测数据磁贴以显示情况
自定义仪表板布局后，就可以添加磁贴以显示遥测数据了。 若要创建遥测数据磁贴，请选择设备模板和设备实例，然后选择要在磁贴中显示的特定于设备的遥测数据。 “店内分析-结帐”  应用程序模板在仪表板中包含若干遥测数据磁贴。 两个结帐区域中的四个磁贴显示模拟占用传感器的遥测数据。 “人员流量”  磁贴显示两个结帐区域中的计数。 

在本部分中，将添加另外两个遥测数据磁贴，以显示在[在 Azure IoT Central 中创建店内分析应用程序](./tutorial-in-store-analytics-create-app-pnp.md)教程中添加的 RuuviTag 传感器的环境遥测数据。 

添加磁贴以显示 RuuviTag 传感器的环境数据：

1. 选择“编辑”  。

1. 在“设备模板”  列表中选择 `RuuviTag`。 

1. 选择两个 RuuviTag 传感器之一的“设备实例”  。 在示例 Contoso 商店中，选择 `Zone 1 Ruuvi` 以创建区域 1 的遥测数据磁贴。 

1. 在“遥测数据”列表中选择 `Relative humidity` 和 `temperature`  。 这些是为磁贴上每个区域显示的遥测数据项目。

1. 选择“合并”  。 

    ![Azure IoT Central 添加 RuuviTag 磁贴 1](./media/tutorial-in-store-analytics-customize-dashboard-pnp/add-zone1-ruuvi.png)

    显示新的磁贴，以显示选定传感器的合并湿度和温度遥测数据。 

1. 对于 RuuviTag 传感器，选择新磁贴上的“配置”  。 

1. 将“磁贴”  更改为“区域 1 环境”  。 

1. 选择“更新配置”  。

1. 重复以上步骤以创建第二个传感器实例的磁贴。 将“磁贴”  设置为“区域 2 环境”  ，然后选择“更新配置”  。

1. 将标题为“区域 2 环境”的磁贴  拖动到“恒温器固件”  磁贴下。 

1. 将标题为“区域 1 环境”的磁贴  拖动到“人员流量”  磁贴下。 

1. 选择“保存”。  仪表板显示两个新磁贴中的区域遥测数据。

    ![Azure IoT Central 所有 RuuviTag 磁贴](./media/tutorial-in-store-analytics-customize-dashboard-pnp/all-ruuvitag-tiles.png)

编辑“人员流量”  磁贴以仅显示两个结帐区域的遥测数据：

1. 选择“编辑”  。 

1. 选择“人员流量”  磁贴上的“配置”  。

1. 在“遥测数据”  中，选择“计数 1”  、“计数 2”  和“计数 3”  。 

1. 选择“更新配置”  。 这会清除磁贴上的现有配置。 

1. 再次选择“人员流量”  磁贴上的“配置”  。

1. 在“遥测数据”  中，选择“计数 1”  和“计数 2”  。 

1. 选择“更新配置”  。 

1. 选择“保存”。   更新的仪表板仅显示两个结帐区域的计数，这些区域基于模拟占用传感器。

    ![Azure IoT Central 人员流向两个通道](./media/tutorial-in-store-analytics-customize-dashboard-pnp/people-traffic-two-lanes.png)

## <a name="add-property-tiles-to-display-device-details"></a>添加属性磁贴以显示设备详细信息
应用程序操作员使用仪表板来管理设备和监视状态。 为每个 RuuviTag 添加磁贴，使操作员能够查看软件版本。 

为每个 RuuviTag 添加属性磁贴：

1. 选择“编辑”  。

1. 在“设备模板”  列表中选择 `RuuviTag`。 

1. 选择两个 RuuviTag 传感器之一的“设备实例”  。 在示例 Contoso 商店中，选择 `Zone 1 Ruuvi` 以创建区域 1 的遥测数据磁贴。 

1. 选择“属性”>“软件版本”** **。

1. 选择“合并”  。 

1. 在标题为“软件版本”  的新创建的磁贴上选择“配置”  。 

1. 将“标题”  更改为“Ruuvi 1 软件版本”  。

1. 选择“更新配置”  。 

1. 将标题为“Ruuv 1 软件版本”的磁贴拖动到“区域 1 环境”   磁贴下。

1. 重复前面的步骤，为第二个 RuuviTag 创建软件版本属性磁贴。 

1. 选择“保存”。   

    ![Azure IoT Central RuuviTag 属性磁贴](./media/tutorial-in-store-analytics-customize-dashboard-pnp/add-ruuvi-property-tiles.png)

## <a name="add-command-tiles-to-run-commands"></a>添加命令磁贴以运行命令
应用程序操作员还可以通过运行命令使用仪表板来管理设备。 可以将命令磁贴添加到仪表板，以便在设备上执行预定义的命令。 在本部分中，将添加一个命令磁贴，使操作员能够重新启动 Rigado 网关。 

添加命令磁贴来重新启动网关：

1. 选择“编辑”  。 

1. 在“设备模板”  列表中选择 `C500`。 这是 Rigado C500 网关的模板。 

1. 选择“设备实例”  中的网关实例。

1. 选择“命令”>“重新启动”  ，然后将其拖到商店地图旁边的仪表板中。 

1. 选择“保存”。  

1. 查看已完成的 Contoso 仪表板。 

    ![Azure IoT Central 完成仪表板自定义](./media/tutorial-in-store-analytics-customize-dashboard-pnp/completed-dashboard.png)

1. （可选）选择“重新启动”  磁贴，以在网关上运行重新启动命令。

## <a name="next-steps"></a>后续步骤
本教程介绍了如何：

* 更改仪表板名称
* 自定义仪表板上的图像磁贴
* 排列磁贴以修改布局
* 添加遥测数据磁贴以显示情况
* 添加属性磁贴以显示设备详细信息
* 添加命令磁贴以运行命令

在 Azure IoT Central 店内分析应用程序中自定义仪表板后，建议接下来执行以下后续步骤：

> [!div class="nextstepaction"]
> [导出数据并直观呈现见解](./tutorial-in-store-analytics-export-data-visualize-insights-pnp.md)
