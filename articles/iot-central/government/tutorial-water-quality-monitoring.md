---
title: 教程：使用 Azure IoT Central 创建水质监测应用
description: 教程：了解如何使用 Azure IoT Central 应用程序模板创建水质监测应用程序。
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 7014882e7da9eb7ab7df65cd20403982a46018a3
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112610"
---
# <a name="tutorial-create-a-water-quality-monitoring-application-in-azure-iot-central"></a>教程：在 Azure IoT Central 中创建水质监测应用程序

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

本教程将为你提供分步指导，帮助你在 Azure IoT Central 中创建水质监测应用程序。 可从 Azure IoT Central 水质监测应用程序模板创建应用程序  。

在本教程中，学习：

> [!div class="checklist"]
> * 使用水质监测模板来创建水质监测应用程序  。
> * 浏览并自定义操作员仪表板。
> * 浏览水质监测设备模板。
> * 浏览模拟设备。
> * 浏览并配置规则。
> * 配置作业。
> * 使用白色标签自定义应用程序品牌。

## <a name="prerequisites"></a>先决条件

建议你获取一个 Azure 订阅以完成本教程。 如果没有 Azure 订阅，则可在 [Azure 注册页](https://aka.ms/createazuresubscription)上创建一个。

## <a name="create-a-water-quality-monitoring-application-in-azure-iot-central"></a>在 Azure IoT Central 中创建水质监测应用程序

在本节中，你将使用 Azure IoT Central 水质监测模板来创建水质监测应用程序  。

1. 请转到 [Azure IoT Central 主页](https://aka.ms/iotcentral)。

    如果你有一个 Azure 订阅，请使用用于访问该订阅的凭据登录。 否则，请使用 Microsoft 帐户登录：

    ![登录到你的组织帐户](./media/tutorial-waterqualitymonitoring/sign-in.png)

1. 选择 Azure IoT Central 最左侧窗格上的“生成”，然后选择“政府”选项卡   。“政府”窗格显示了一系列政府应用程序模板。

    ![政府应用程序模板](./media/tutorial-waterqualitymonitoring/iotcentral-government-tab-overview1.png)

1. 选择“水质监测”  应用程序模板。 此应用程序模板包括水质设备模板、模拟设备、操作员仪表板以及预配置的监视规则。

1. 选择“创建应用”  。 “新建应用程序”窗格将打开，并显示以下元素  ：

    * **应用程序名称**：默认情况下，应用程序名称为“水质监测”，后跟 Azure IoT Central 生成的唯一 ID 字符串  。 如果需要，可以输入显示名称，也可以稍后更改应用程序名称。
    * **URL**：可以输入所需的任何 URL，也可以稍后更改 URL 值。
    * 如果你有一个 Azure 订阅，请输入目录、Azure 订阅和区域的值    。 如果你没有订阅，则可以启用 7 天免费试用版并填写所需的联系人信息  。

    有关目录和订阅的详细信息，请参阅[创建应用程序](../core/quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)快速入门。

1. 选择页面左下方的“创建”按钮  。

    ![Azure IoT Central 新建应用程序页](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1.png)

现已通过使用 Azure IoT Central 水质监测模板创建了一个水质监测应用程序  。

新应用程序附带以下预配置组件：

* 操作员仪表板
* 水质监测设备模板
* 模拟水质监测设备
* 规则和作业
* 使用白色标签的品牌

可随时修改应用程序。

接下来，浏览应用程序并进行一些自定义。

## <a name="explore-and-customize-the-operator-dashboard"></a>浏览和自定义操作员仪表板

创建应用程序后，“Wide World 水质仪表板”窗格将打开  。

   ![水质监测仪表板](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-dashboard1.png)

生成人员可在仪表板上创建和自定义视图，以供操作员使用。 但在尝试自定义之前，请先浏览仪表板。

仪表板中显示的所有数据均基于模拟设备数据，这将在下节中进行讨论。

该仪表板包含以下种类的磁贴：

* **Wide World 供水公司图像磁贴**：仪表板左上角的第一个磁贴是一个显示名为 Wide World 的虚构供水公司的图像。 可以自定义磁贴以使用自己的图像，也可以删除该磁贴。

* **平均 pH KPI 磁贴**：“过去 30 分钟内的平均 pH”等 KPI 磁贴位于仪表板窗格的顶部  。 可以自定义 KPI 磁贴，并将每个磁贴设置为不同的类型和时间范围。

* **水监测地区地图**：Azure IoT Central 使用 Azure Maps，你可以在应用程序中直接对其进行设置以显示设备位置。 还可以将位置信息从应用程序映射到设备，然后使用 Azure Maps 显示地图上的信息。 将鼠标悬停在地图上，并尝试使用控件。

* **平均 pH 分布热图图表**：可选择不同的可视化图表，以最适合你应用程序的方式显示设备遥测数据。

* **关键质量指标折线图**：可将一定时间范围内的设备遥测数据可视化为折线图。  

* **化学制剂浓度条形图**：可在条形图中可视化设备遥测数据。

* **操作按钮**：仪表板包含操作磁贴，操作员可以直接从监视仪表板启动这些操作。 此类操作的一个示例是重置设备属性。

* **属性列表磁贴**：仪表板具有多个属性磁贴，它们表示阈值信息、设备运行状况信息以及维护信息。

### <a name="customize-the-dashboard"></a>自定义仪表板

生成人员可以自定义仪表板上的视图，以供操作员使用。

1. 选择“编辑”可自定义“Wide World 水质仪表板”窗格   。 可通过选择“编辑”菜单上的命令来自定义仪表板  。 仪表板进入编辑模式后，可添加新的磁贴，也可配置现有文件。

    ![编辑仪表板](./media/tutorial-waterqualitymonitoring/edit-dashboard.png)

1. 选择“+ 新建”可以创建可配置的新仪表板  。 可以有多个仪表板，并且可以从仪表板菜单中进行导航。

## <a name="explore-a-water-quality-monitoring-device-template"></a>浏览水质监测设备模板

Azure IoT Central 中的设备模板定义了设备的功能。 可用功能包括遥测、属性和命令。 生成人员可在 Azure IoT Central 中定义表示所连接设备功能的设备模板。 还可以创建模拟设备以测试设备模板和应用程序。

已创建的水质监测应用程序附带一个水质监测设备模板。

如需查看设备模板：

1. 选择 Azure IoT Central 中应用程序最左侧窗格上的“设备模板”  。
1. 在设备模板列表中，选择“水质监测器”  。 该设备模板将打开。

    ![设备模板](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate.png)

### <a name="customize-the-device-template"></a>自定义设备模板

练习自定义以下设备模板设置：

1. 在设备模板菜单中，选择“自定义”  。
1. 转到“温度”遥测类型  。
1. 将“显示名称”值更改为“报告的温度”   。
1. 更改度量单位，或设置最小值和最大值   。
1. 选择“保存”。 

#### <a name="add-a-cloud-property"></a>添加云属性

1. 从设备模板菜单中，选择“云属性”  。
1. 若要添加新的云属性，请选择“+ 添加云属性”  。 在 Azure IoT Central 中，可以添加与设备相关但不应由设备发送的属性。 此类属性的一个示例是特定于安装区域、资产信息或维护信息的警报阈值。
1. 选择“保存”。 

### <a name="explore-views"></a>浏览视图

水质监测设备模板附带预定义的视图。 视图定义操作员查看设备数据及设置云属性的方式。 浏览视图并练习更改。

  ![设备模板视图](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate-views.png)

### <a name="publish-the-device-template"></a>发布设备模板

如果进行了任何更改，请务必选择“发布”，以发布设备模板  。

### <a name="create-a-new-device-template"></a>创建新设备模板

1. 选择“+ 新建”以创建新的设备模板，然后执行创建过程  。
1. 创建自定义设备模板或从 Azure IoT 设备目录中选择设备模板。

## <a name="explore-simulated-devices"></a>浏览模拟设备

从应用程序模板创建的水质监测应用程序具有两个模拟设备。 这些设备会映射到水质监测设备模板。

### <a name="view-the-devices"></a>查看设备

1. 选择应用程序最左侧窗格上的“设备”  。

   ![设备](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devices.png)

1. 选择一个模拟设备。

    ![选择设备 1](./media/tutorial-waterqualitymonitoring/waterqualitymonitor-device1.png)

1. 在“云属性”选项卡上，将酸性 (pH) 阈值从 8 更改为 9     。
1. 浏览“设备属性”选项卡和“设备仪表板”选项卡   。

> [!NOTE]
> 已从设备模板视图配置所有选项卡  。

### <a name="add-new-devices"></a>添加新设备

在“设备”选项卡上，选择“+ 新建”以添加新设备   。

## <a name="explore-and-configure-rules"></a>浏览并配置规则

在 Azure IoT Central 中，可创建自动监视设备遥测的规则。 当任一条件满足时，这些规则将触发操作。 发送电子邮件通知是其中一种可能的操作。 其他可能的操作包括 Microsoft Flow 操作或将数据发送到其他服务的 webhook 操作。

已创建的水质监测应用程序具有两个预配置的规则。

### <a name="view-rules"></a>查看规则

1. 选择应用程序最左侧窗格上的“规则”  。

   ![规则](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-rules.png)

1. 选择“高 pH 警报”，它是应用程序中的一项预配置规则  。

   ![高 pH 预警规则](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-highphalert.png)

   高 pH 预警规则配置为检查大于 8 的酸性 (pH) 条件  。

接下来，将电子邮件操作添加到规则：

1. 选择“+ 电子邮件”  。
1. 在“显示名称”框中，输入“高 pH 警报”   。
1. 在“收件人”框中，输入与你的 Azure IoT Central 帐户关联的电子邮件地址  。
1. （可选）输入要包含在电子邮件文本中的注释。
1. 选择“完成”  ，完成操作。
1. 选择“保存”  以保存并激活新规则。

满足配置条件后，你应该能在几分钟内收到电子邮件。

> [!NOTE]
> 每次满足条件后，应用程序都会发送电子邮件。 为某个规则选择“禁用”，以停止接收来自该规则的自动电子邮件  。
  
若要创建新规则，请在应用程序最左侧的窗格上选择“规则”，然后选择“+ 新建”   。

## <a name="configure-jobs"></a>配置作业

使用 Azure IoT Central 作业，可以在多台设备上触发对设备或云属性的更新。 还可以使用作业在多个设备上触发设备命令。 Azure IoT Central 将自动执行工作流。

1. 在应用程序最左侧的窗格上选择“作业”  。
1. 选择“+ 新建”并配置一个或多个作业  。

## <a name="customize-your-application"></a>自定义应用程序

构建者可以更改多个设置来自定义应用程序中的用户体验。

1. 选择“管理” > “自定义应用程序”   。
1. 在“应用程序徽标”下，选择“更改”以选择作为徽标上传的图像   。
1. 在“浏览器图标”下，选择“更改”以选择在浏览器选项卡上显示的图像   。
1. 在“浏览器颜色”下，可将默认值替换为 HTML 十六进制颜色代码  。
1. 选择“设置”可以更改“主题”的值   。

   ![自定义应用程序](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-customize-your-application1.png)

### <a name="update-the-application-image"></a>更新应用程序图像

1. 选择“管理” > “应用程序设置”   。

1. 使用“选择图像”按钮选择要作为应用程序图像上传的图像  。

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用应用程序，请删除应用程序，步骤如下：

1. 打开应用程序最左侧窗格上的“管理”选项卡  。
1. 选择“应用程序设置”，然后选择“删除”按钮   。

    ![删除应用程序](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-application-settings-delete-app1.png)

## <a name="next-steps"></a>后续步骤

* 详细了解[水质监测的概念](./concepts-waterqualitymonitoring-architecture.md)。
