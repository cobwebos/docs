---
title: 教程：使用 Azure IoT Central 创建水消耗量监测应用
description: 教程：了解如何使用 Azure IoT Central 应用程序模板创建水消耗量监测应用程序。
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 7f00947504e5c6355379ce1e400fd2325016e05a
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77122075"
---
# <a name="tutorial-create-a-water-consumption-monitoring-application-with-azure-iot-central"></a>教程：使用 Azure IoT Central 创建水消耗量监测应用程序

本教程介绍如何使用 Azure IoT Central 水消耗量监测应用程序模板创建 Azure IoT Central 水消耗量监测应用程序。

在本教程中，您将学习如何执行以下操作：

> [!div class="checklist"]
> * 使用 Azure IoT Central 水消耗量监测模板创建水消耗量监测应用程序。
> * 浏览和自定义操作员仪表板。
> * 浏览设备模板。
> * 浏览模拟设备。
> * 浏览并配置规则。
> * 配置作业。
> * 使用白色标签自定义应用程序品牌。

## <a name="prerequisites"></a>必备条件

要完成本教程，需要：

- Azure 订阅。 如果没有 Azure 订阅，则可在 [Azure 注册页](https://aka.ms/createazuresubscription)上创建一个。

## <a name="create-a-water-consumption-monitoring-app-with-azure-iot-central"></a>使用 Azure IoT Central 创建水消耗量监测应用

本部分将使用 Azure IoT Central 水消耗量监测模板在 Azure IoT Central 中创建水消耗量监测应用程序。

若要创建新的 Azure IoT Central 水消耗量监测应用程序：

1. 转到 [Azure IoT Central 主页](https://aka.ms/iotcentral)网站。

    如果你有一个 Azure 订阅，请使用用于访问该订阅的凭据登录。 否则，请使用 Microsoft 帐户登录。

    ![输入组织帐户](media/tutorial-waterconsumptionmonitoring/sign-in.png)

1. 在左侧窗格中选择“生成”，然后选择“政府”选项卡   。“政府”页显示了几个政府应用程序模板  。

   ![生成政府应用模板](./media/tutorial-waterconsumptionmonitoring/iotcentral-government-tab-overview1.png)

1. 选择“水消耗量监测”应用程序模板  。
此模板包括示例水消耗量设备模板、模拟设备、操作员仪表板以及预配置的监测规则。

1. 选择“创建应用”打开“新建应用程序”创建窗体，其中包含以下字段   ：
    * **应用程序名称**：默认情况下，应用程序使用“水消耗量监测”，后跟 Azure IoT Central 生成的唯一 ID 字符串  。 （可选）选择一个易记的应用程序名称。 稍后也可以更改应用程序名称。
    * **URL**：Azure IoT Central 将根据应用程序名称自动生成 URL。 你可以根据自己的喜好选择更新 URL。 也可以稍后更改 URL。
    * 如果你有一个 Azure 订阅，请输入“目录”、“Azure 订阅”和“位置”信息    。 如果你没有订阅，可以选择“7 天免费试用”选项并填写所需的联系信息  。

    有关目录和订阅的详细信息，请参阅[创建应用程序](../core/quick-deploy-iot-central.md)快速入门。

1. 在页面底部选择“创建”  。

    ![Azure IoT Central -“新建应用程序”页](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring.png)

    ![Azure IoT Central -“计费信息”页](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring-billinginfo.png)

现在，你已使用 Azure IoT Central 水消耗量监测模板创建了水消耗量监测应用。

该水消耗量监测应用程序中已预配置了以下组件：

* 示例操作员仪表板。
* 示例预定义水流量和阀设备模板。
* 模拟水流量和智能阀设备。
* 规则和作业。
* 使用白色标签的示例品牌。

这是你的应用程序，你可以随时修改它。 现在，让我们浏览应用程序并进行一些自定义。

## <a name="explore-and-customize-the-operator-dashboard"></a>浏览和自定义操作员仪表板

创建应用程序后，会打开“Wide World 水消耗量监测仪表板”示例  。

   ![水消耗量监测仪表板](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboardfull.png)

生成人员可在仪表板上创建和自定义视图，以供操作员使用。 在尝试自定义之前，让我们浏览仪表板。

> [!NOTE]
> 仪表板中显示的所有数据均基于模拟设备数据，这将在下节中进行探讨。
  
仪表板包含不同种类的磁贴：

* **Wide World 供水公司图像磁贴**：仪表板中的第一个磁贴是虚构的供水公司 Wide World Water 的图像磁贴。 可以通过插入自己的图像来自定义磁贴，或将其删除。
* **平均水流量 KPI 磁贴**：KPI 磁贴配置为显示为“最近 30 分钟内的平均值”示例  。 可以自定义 KPI 磁贴，并将其设置为不同的类型和时间范围。
* **设备命令磁贴**：这些磁贴包括“关闭阀门”、“打开阀门”和“设置阀门位置”磁贴。    选择命令将转到模拟设备命令页。 在 Azure IoT Central 中，命令是一种设备功能类型   。 稍后我们会在本教程的“设备模板”部分探讨此概念。
* **水分布区域地图**：该地图使用 Azure Maps，你可以直接在 Azure IoT Central 中对其进行配置。 地图磁贴显示设备位置。 将鼠标悬停在地图上，然后尝试使用地图上的控件，如放大、缩小或展开    。

    ![水消耗量监测仪表板地图](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboard-map.png)

* **平均水流量折线图**和**环境条件折线图**：可以可视化绘制为所需时间范围内的折线图的一个或多个设备遥测。
* **平均阀门压力热度地图图表**：对于你想要通过颜色索引查看在一定时间范围内的分布情况的设备遥测数据，可以选择这些数据的热度地图可视化类型。
* **重置警报阈值内容磁贴**：可以包含调用操作内容磁贴，并嵌入操作页的链接。 在此情况下，重置警报阈值会转到应用程序的“作业”，可在其中运行设备属性的更新  。 稍后我们将在本教程的“配置作业”部分对此探讨此选项。
* **属性磁贴**：仪表板显示“阀门操作信息”、“流量警报阈值”和“维护信息”磁贴    。

### <a name="customize-the-dashboard"></a>自定义仪表板

生成人员可在仪表板上自定义视图，以供操作员使用。

1. 选择“编辑”可自定义“Wide World 水消耗量仪表板”   。 可通过选择“编辑”菜单来自定义仪表板  。 仪表板进入编辑模式后，可添加新的磁贴，或对其进行配置  。

     ![编辑仪表板](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-edit-dashboard.png)

1. 选择“+ 新建”创建新的仪表板并从头开始配置  。 你可能有多个仪表板，在此情况下，可以通过仪表板菜单在仪表板之间切换。

## <a name="explore-the-device-template"></a>浏览设备模板

Azure IoT Central 中的设备模板定义设备的功能，这些功能可以是遥测、属性或命令。 构建人员可在 Azure IoT Central 中定义一个或多个表示要连接的设备功能的设备模板。

水消耗量监测应用程序附带了两个引用设备模板，分别表示流量计和智能阀设备   。

如需查看设备模板：

1. 选择 Azure IoT Central 中应用程序左侧窗格中的“设备模板”  。 在“设备模板”列表中，将看到两个设备模板：“智能阀”和“流量计”    。

   ![设备模板](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate.png)

1. 选择“流量计”  设备模板并熟悉设备功能。

     ![设备模板 - 流量计](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-flowmeter.png)

### <a name="customize-the-device-template"></a>自定义设备模板

若要自定义设备模板：

1. 在“设备模板”菜单中转到“自定义”   。
1. 找到 `Temperature` 遥测类型。
1. 将 `Temperature` 的“显示名称”更新为 `Reported temperature`  。
1. 更新度量单位，或设置“最小值”和“最大值”   。
1. 选择“保存”  以保存所有更改。

### <a name="add-a-cloud-property"></a>添加云属性

1. 在“设备模板”菜单中转到“云属性”   。
1. 选择“+ 添加云属性”添加新的云属性  。
    在 Azure IoT Central 中，可以添加与设备相关的属性。 例如，云属性可以是特定于安装区域、资产信息或其他维护信息的警报阈值。
1. 选择“保存”  以保存所有更改。

### <a name="views"></a>视图

水消耗量监测设备模板附带预定义的视图。 浏览视图。可以进行更新。 这些视图定义了操作员查看设备数据及输入云属性的方式。

  ![设备模板视图](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-views.png)

### <a name="publish"></a>发布

如果进行了任何更改，请确保**发布**设备模板。

### <a name="create-a-new-device-template"></a>创建新设备模板

选择“+ 新建”以创建新的设备模板，然后执行创建过程  。
可以从头开始创建自定义设备模板，也可以从 Azure 设备目录中选择设备模板。

## <a name="explore-simulated-devices"></a>浏览模拟设备

在 Azure IoT Central 中，可以创建模拟设备以测试设备模板和应用程序。 水消耗量监测应用程序具有映射到流量计和智能阀设备模板的两个模拟设备   。

### <a name="view-the-devices"></a>查看设备

1. 在左侧窗格中选择“设备” > “所有设备”   。

   ![“所有设备”窗格](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devices.png)

1. 选择“智能阀 1”  。

    ![智能阀 1](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitor-device1.png)

1. 在“命令”选项卡上，可以看到三个设备命令：“打开阀门”、“关闭阀门”和“设置阀门位置”，它们是智能阀设备模板中定义的功能      。

1. 浏览“设备属性”选项卡和“设备仪表板”选项卡   。

> [!NOTE]
> 请注意，所有选项卡都是从设备模板视图配置的。

### <a name="add-new-devices"></a>添加新设备

选择“设备”选项卡上的“+ 新建”来添加新设备   。

## <a name="explore-and-configure-rules"></a>浏览并配置规则

在 Azure IoT Central 中，可以创建规则来自动监视设备遥测，并在满足一个或多个条件时触发操作。 这些操作可能包括发送电子邮件通知或触发 Microsoft Power Automate 或向其他服务发送数据的 Webhook 操作。

创建的水消耗量监测应用程序中预配置了三个规则。

### <a name="view-rules"></a>查看规则

1. 在左侧窗格中选择“规则”  。

   ![“规则”窗格](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-rules.png)

1. 选择“高 pH 警报”，它是应用程序中的一项预配置规则  。

     ![高 pH 警报](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-highflowalert.png)

    `High flow alert` 规则配置为根据条件 `Acidity (pH)``greater than``Max flow threshold` 进行检查。 最大流量阈值是在“智能阀”设备模板中定义的云属性  。 每个设备实例均设有 `Max flow threshold` 值。

现在，让我们创建电子邮件操作。

若要向规则添加操作，请执行以下操作：

1. 选择“+ 电子邮件”  。
1. 输入“高 pH 警报”作为操作的易记显示名称   。
1. 在“收件人”中输入与 Azure IoT Central 帐户关联的电子邮件地址  。
1. （可选）输入要包含在电子邮件文本中的注释。
1. 选择“完成”  ，完成操作。
1. 选择“保存”  以保存并激活新规则。

满足配置条件后，你应该能在几分钟内收到电子邮件。

> [!NOTE]
> 每次满足条件后，应用程序都会发送电子邮件。 选择“禁用”会禁止该规则停止从自动规则接收电子邮件  。
  
若要创建新规则：

* 在左侧窗格中的“规则”选项卡上选择“+ 新建”   。

## <a name="configure-jobs"></a>配置作业

在 Azure IoT Central 中，作业允许你在多个设备上触发设备或云属性更新。 除了属性外，还可以使用作业在多个设备上触发设备命令。 Azure IoT Central 将自动执行工作流。

1. 在左侧窗格中选择“作业”  。
1. 选择“+ 新建”并配置一个或多个作业  。

## <a name="customize-your-application"></a>自定义应用程序

构建者可以更改多个设置来自定义应用程序中的用户体验。

1. 选择“管理” > “自定义应用程序”   。
1. 若要选择要作为应用程序徽标上传的图像，请选择“更改”按钮   。
1. 若要选择将显示在浏览器选项卡上的浏览器图标图像，请选择“更改”按钮   。
1. 还可以通过添加 HTML 十六进制颜色代码来替换默认“浏览器颜色”  。

   ![应用程序徽标、浏览器图标和浏览器颜色的选项](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-customize-your-application.png)

1. 也可以选择“管理”   > “应用程序设置”  来更改应用程序图像。 若要选择要作为应用程序图像上传的图像，请选择“选择图像”按钮  。
1. 最后，还可以选择应用程序右上角的“设置”图标来更改“主题”   。

## <a name="clean-up-resources"></a>清理资源

如果你不打算继续使用此应用程序，请将其删除。

1. 在 Azure IoT Central 应用程序的左侧窗格中选择“管理”  。
1. 选择“应用程序设置”，然后选择页面底部的“删除”   。

## <a name="next-steps"></a>后续步骤

* 详细了解[水消耗量监测的概念](./concepts-waterconsumptionmonitoring-architecture.md)。
