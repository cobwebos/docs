---
title: 在 Azure IoT Central 应用程序中设置设备模板 | Microsoft Docs
description: 了解如何使用度量、设置、属性、规则和仪表板设置设备模板。
author: viv-liu
ms.author: viviali
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: bda056a75ae9d696dab389b85fe1bfb2935ee1a8
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261978"
---
# <a name="set-up-a-device-template"></a>设置设备模板

设备模板是一个蓝图，用于定义可连接到 Microsoft Azure IoT Central 应用程序的某种设备的特征和行为。

例如，构建人员可为连接到 IoT 的风扇创建设备模板，其中包含：

- 温度遥测度量

- 风扇电机错误事件度量

- 风扇运行状态度量

- 风扇速度设置

- 位置属性

- 发送警报的规则

- 显示设备全方位视图的仪表板

在此设备模板中，操作员可以创建和连接名为 **fan-1** 和 **fan-2** 等的真实风扇设备。 所有这些风扇具有度量、设置、属性、规则和仪表板，应用程序用户可对其进行监视和管理。

> [!NOTE]
只有构建人员和管理员可以创建、编辑和删除设备模板。 任何用户可以在“Device Explorer”页中基于现有的设备模板创建设备。

## <a name="create-a-new-device-template"></a>创建新设备模板

1. 导航到“应用程序构建人员”页。

1. 若要创建空白模板，请依次选择“创建设备模板”、“自定义”。

1. 输入新设备模板的名称，选择“创建”。

    ![设备详细信息页](./media/howto-set-up-template/devicedetailspage.png)

1. 随后将会转到新模拟设备的“设备详细信息”页。 创建新设备模板时，系统会自动创建模拟设备。 模拟设备会报告数据，可以像控制真实设备一样控制它。

现在，让我们看看“设备详细信息”页上的每个选项卡。

## <a name="measurements"></a>度量

度量是来自设备的数据。 可将多个度量添加到设备模板，以匹配设备的功能。 目前，支持的度量类型为遥测和事件。

- **遥测**度量是设备在一段时间内收集的数字型数据点，以连续数据流的形式显示。 例如温度。
- **事件**度量是表示设备上发生的重要事情的时间点数据。 事件具有关联的严重性，严重性表示该事件的重要性。 例如，风扇电机错误
- **状态**度量表示设备或其组件在一段时间内的状态。 例如，可将“正在运行”和“已停止”定义为风扇模式的两种可能状态。

### <a name="create-a-telemetry-measurement"></a>创建遥测度量
若要添加新的遥测度量，请单击“+ 新建度量”按钮打开一个窗体，其中包含用于选择度量类型的选项。 选择“遥测”，在“创建遥测”窗体中输入详细信息。

> [!NOTE]
> 连接真实设备后，请注意设备报告的度量名称。 该名称必须与度量的“字段名称”完全匹配。

例如，可以添加新的温度遥测度量：

![度量窗体](./media/howto-set-up-template/measurementsform.png)

选择“保存”后，“温度”度量会显示在度量列表中，操作员可以查看设备正在收集的温度数据的可视化效果。

![度量图](./media/howto-set-up-template/measurementsgraph.png)

### <a name="create-an-event-measurement"></a>创建事件度量
若要添加新的事件度量，请单击“+ 新建度量”按钮打开一个窗体，其中包含用于选择度量类型的选项。 选择“事件”，在“创建事件”窗体中输入详细信息。

此窗体提供了事件的“显示名称”、“字段名称”和“严重性”。 可以从三个可用的严重性级别中选择 -“错误”、“警告”和“信息”。  

例如，可以添加新的“风扇电机错误”事件。

![事件度量窗体](./media/howto-set-up-template/eventmeasurementsform.png)

选择“保存”后，“风扇电机错误”度量会显示在度量列表中，操作员可以查看设备正在发送的事件数据的可视化效果。

![事件度量图表](./media/howto-set-up-template/eventmeasurementschart.png)

若要查看有关事件的更多详细信息，请在图表中单击相应的事件图标：

![事件度量详细信息](./media/howto-set-up-template/eventmeasurementsdetail.png)


### <a name="create-a-state-measurement"></a>创建状态度量
若要添加新的状态度量，请单击“+ 新建度量”按钮打开一个窗体，其中包含用于选择度量类型的选项。 选择“状态”，在“创建状态”窗体中输入详细信息。

此窗体提供了状态的“显示名称”、“字段名称”和可能的**值**。 每个**值**还可能附带一个显示名称，在图表和表格中显示该值时，会使用该名称。

例如，可以添加新的“风扇模式”状态，其中包含设备可以发送的两个可能值：“正在运行”和“已停止”。

![状态度量窗体](./media/howto-set-up-template/statemeasurementsform.png)

选择“保存”后，“风扇模式”状态度量会显示在度量列表中，操作员可以查看设备正在发送的状态数据的可视化效果。

![状态度量图表](./media/howto-set-up-template/statemeasurementschart.png)

如果设备在短时间内发送了过多的数据点，状态度量值会以不同的视觉效果显示，如下所示。 如果单击图表，该时间段内的所有数据点将按时间顺序显示。 还可以缩小时间范围，以查看图表上绘制的度量值。

![状态度量详细信息](./media/howto-set-up-template/statemeasurementsdetail.png)


## <a name="settings"></a>设置

设置用于控制设备。 应用程序的操作员可以使用设置来提供设备的输入。 可将多个设置添加到设备模板。这些设置将在“设置”选项卡上显示为可供操作员使用的磁贴。 可以添加六种类型的设置：数字、文本、日期、切换开关、选取列表和部分标签。

> [!NOTE]
> 连接真实设备后，请注意设备报告的设置名称。 该名称必须与某个设置的“字段名称”完全匹配。

设置可以处于三种状态之一。 这些状态由设备报告。

- **已同步**：设备已更改以反映设置值。

- **挂起**：设备当前正在更改为设置值。

- **错误**：设备返回了错误。

例如，可以添加新的风扇速度设置：

![设置窗体](./media/howto-set-up-template/settingsform.png)

选择“保存”后，“风扇速度”设置会显示为磁贴，随时可以使用该设置来更改设备的风扇速度。

> [!NOTE]
> 创建新磁贴后，可以试用新的设置。 首先，在屏幕右上角关闭设计模式：

![设置磁贴](./media/howto-set-up-template/settingstile.png)

## <a name="properties"></a>属性

属性是与设备关联的设备元数据，例如设备位置和序列号。 可将多个属性添加到设备模板，这些属性将在“属性”选项卡上显示为磁贴。操作员可以在创建新设备时指定属性的值，并随时可以编辑这些值。 可以添加六种类型的属性：数字、文本、日期、切换开关、设备属性和标签。

有两种类型的属性：

- **设备属性**是设备报告的属性。
- **应用程序属性**只是存储在应用程序中的属性。 设备不知道应用程序属性。

> [!NOTE]
> 对于设备属性，在连接真实设备后，请注意设备报告的属性名称。 该名称必须与属性的“字段名称”完全匹配。 对于应用程序属性，字段名称可以是任意所需内容，只要该名称在设备模板中唯一即可。

例如，可以添加设备位置作为新属性：

![属性窗体](./media/howto-set-up-template/propertiesform.png)

选择“保存”后，设备位置会显示为磁贴：

![属性磁贴](./media/howto-set-up-template/propertiestile.png)

> [!NOTE]
> 创建新磁贴后，可以更改属性值。 首先，在屏幕右上角关闭设计模式。

### <a name="create-a-location-property-powered-by-azure-maps"></a>创建由 Azure Maps 提供支持的位置属性
可以在 Azure IoT Central 中为位置数据提供地理上下文，并映射街道地址的任何纬度和经度坐标，或者仅映射纬度和经度坐标。 Azure IoT Central 中的此功能由 Azure Maps 提供支持。

可以添加两种类型的位置属性：
- **应用程序位置属性**，它将仅仅存储在应用程序中。 设备不知道应用程序属性。
- **设备位置属性**，将由设备报告。

####<a name="adding-location-as-an-application-property"></a>添加应用程序位置属性 
可以在 Azure IoT Central 应用程序中使用 Azure Maps 将位置属性创建为应用程序属性。 例如，可以添加设备安装地址。 

1. 导航到“设备属性”选项卡；确保已启用“设计模式”。

![位置属性](./media/howto-set-up-template/locationcloudproperty1.png)

2. 在“属性”选项卡上，单击“位置”。
3. （可选）配置位置的显示名称、字段名称和初始值。 

![“位置属性”格式](./media/howto-set-up-template/locationcloudproperty2.png)

有两种支持的格式可以添加位置：
- **作为地址的位置**
- **作为坐标的位置** 

4. 单击“保存”。 

![“位置属性”字段](./media/howto-set-up-template/locationcloudproperty3.png)

现在，操作员可以用位置字段格式更新位置值。 

####<a name="adding-location-as-a-device-property"></a>添加设备位置属性 

可以将位置属性创建为由设备报告的设备属性。
例如，想要跟踪设备位置。

1.  导航到“设备属性”选项卡；确保已启用“设计模式”。
2.  单击库中的“设备属性”。

![“位置属性”字段](./media/howto-set-up-template/locationdeviceproperty1.png)

3.  配置显示名称、字段名称，并选择“location”作为数据类型。 

> [!NOTE]
字段名称必须与设备报告的属性名称完全匹配。 

![“位置属性”字段](./media/howto-set-up-template/locationdeviceproperty2.png)

![“位置属性”操作员视图](./media/howto-set-up-template/locationdeviceproperty2.png)

现在，你已经配置了位置属性，将能够添加一个地图以在设备仪表板中直观显示位置。 了解如何[在仪表板中添加位置 Azure 地图](howto-set-up-template.md)。




## <a name="rules"></a>规则

操作员可以使用规则近乎实时地监视设备。 规则会自动调用**操作**，例如，在触发规则时发送电子邮件。 目前只有一种类型的规则：

- **遥测规则：** 当选定的设备遥测超过指定的阈值时，会触发遥测规则。 详细了解[遥测规则](howto-create-telemetry-rules.md)。

## <a name="dashboard"></a>仪表板

操作员可以转到仪表板来查看有关设备的信息。 构建人员可在此页中添加磁贴，以帮助操作员了解设备的行为。 可将多个仪表板磁贴添加到设备模板。 可以添加六种类型的仪表板磁贴：图像、折线图、条形图、KPI、设置和属性，以及标签。

例如，可以添加“设置和属性”磁贴，以显示所需的当前设置和属性值：

![仪表板设备详细信息窗体](./media/howto-set-up-template/dashboardsettingsandpropertiesform.png)

现在，当操作员查看仪表板时，可以看到此磁贴，其中显示了设备的属性和设置：

![仪表板磁贴](./media/howto-set-up-template/dashboardtile.png)

### <a name="add-location-azure-map-in-dashboard"></a>在仪表板中添加位置 Azure 地图

如果已按照步骤 [创建由 Azure Maps 提供支持的位置属性]((howto-set-up-template.md) 配置位置属性，则可以在设备仪表板中直接使用地图直观显示位置。

1.  导航到“设备仪表板”选项卡；确保已启用“设计模式”。
2.  在“设备仪表板”中，从“库”中选择“地图”。 

![仪表板位置：Azure 地图选择](./media/howto-set-up-template/locationcloudproperty4map.png)

3.  指定标题，并选择之前已配置为设备属性的一部分的位置属性。

![仪表板位置：Azure 地图配置](./media/howto-set-up-template/locationcloudproperty5map.png)

4.  保存设置，此时会看到显示已选择的位置的地图图块。 

![仪表板位置：Azure 地图可视化](./media/howto-set-up-template/locationcloudproperty6map.png) 

可以根据需要重设地图大小。

现在，当操作员查看仪表板时，他们可以看到已配置的所有仪表板磁贴，包括位置地图！

![仪表板位置：Azure 地图仪表板](./media/howto-set-up-template/locationcloudproperty7map.png) 



## <a name="next-steps"></a>后续步骤

了解如何在 Azure IoT Central 应用程序中使用设备模板后，建议接下来执行以下步骤：

> [!div class="nextstepaction"]
> [创建新设备模板版本](howto-version-devicetemplate.md)
