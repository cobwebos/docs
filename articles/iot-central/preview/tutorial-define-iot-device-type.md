---
title: 在 Azure IoT Central 中定义新的 IoT 设备类型 | Microsoft Docs
description: 本教程向构建人员介绍如何在 Azure IoT Central 应用程序中创建新的 Azure IoT 设备模板。 其中介绍了如何为类型定义遥测、状态、属性和命令。
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 5642ce6065c4b76bdbd6d772c74fed894de0888f
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2019
ms.locfileid: "73892453"
---
# <a name="tutorial-define-a-new-iot-device-type-in-your-azure-iot-central-application-preview-features"></a>教程：在 Azure IoT Central 应用程序中定义新的 IoT 设备类型（预览版功能）

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

设备模板是一个蓝图，用于定义可连接到 Azure IoT Central 应用程序的某种设备的特征和行为。

例如，构建人员可以为连接的风扇创建设备模板，该模板包括以下特征：

- 发送温度遥测
- 发送位置属性
- 发送风扇电机错误事件
- 发送风扇运行状态
- 可写风扇速度属性
- 用于重启设备的命令
- 显示设备全方位视图的仪表板

在此设备模板中，操作员可以创建和连接实际的风扇设备。 所有这些风扇都具有度量、属性以及操作员随后用来监视和管理的命令。 操作员使用设备仪表板和窗体与风扇设备进行交互。

> [!NOTE]
> 只有构建人员和管理员可以创建、编辑和删除设备模板。 任何用户都可以在“设备”页中基于现有的设备模板创建设备。 

IoT Central 可以通过 [IoT 即插即用](../../iot-pnp/overview-iot-plug-and-play.md)来集成设备，不需你编写任何嵌入式设备代码。 IoT 即插即用的核心是描述设备功能的设备功能模型架构。 在 IoT Central 预览版应用程序中，设备模板使用这些 IoT 即插即用设备功能模型。

作为构建者，你在创建设备模板时有多种选择：

- 在 IoT Central 中设计设备模板，然后在设备代码中实现其设备功能模型。
- 从 [Azure IoT 认证设备目录](https://aka.ms/iotdevcat)导入设备功能模型，然后添加 IoT Central 应用程序所需的任何云属性、自定义和仪表板。
- 使用 Visual Studio Code 创建设备功能模型。 基于模型实现设备代码。 将设备功能模型手动导入 IoT Central 应用程序，然后添加 IoT Central 应用程序所需的任何云属性、自定义和仪表板。
- 使用 Visual Studio Code 创建设备功能模型。 基于模型实现设备代码，并通过设备优先连接将实际设备连接到 IoT Central 应用程序。 IoT Central 从公共存储库中查找并导入设备功能模型。 然后，你可以将 IoT Central 应用程序所需的任何云属性、自定义和仪表板添加到设备模板中。

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要一个 Azure IoT Central 应用程序。 按照此快速入门[创建 Azure IoT Central 应用程序](quick-deploy-iot-central.md)。

## <a name="create-a-device-template-from-the-device-catalog"></a>从设备目录创建设备模板

作为构建人员，你可以使用 [Azure IoT 设备目录](https://catalog.azureiotsolutions.com/alldevices)中列出的 IoT 即插即用认证设备快速开始构建解决方案。 IoT Central 与设备目录进行集成，使你可以从任何 IoT 即插即用认证设备中导入设备功能模型。 要在 IoT Central 中通过以下某一设备创建设备模板：

1. 请转到 IoT Central 应用程序中的“设备模板”页  。
1. 选择“+ 新建”，然后从下面列出的目录中选择任何 IoT 即插即用认证设备  。 IoT Central 基于此设备功能模型创建设备模板。
1. 将任何云属性、自定义或视图添加到设备模板中。
1. 选择“发布”以发布此设备模板，这样操作员便可查看和连接设备  。

## <a name="create-a-device-template-from-scratch"></a>从头开始创建设备模板

设备模板包含：

- 设备功能模型，用于指定设备实现的遥测、属性和命令  。 这些功能被整理到一个或多个接口中。
- 云属性，用于定义 IoT Central 应用程序存储的设备相关信息  。 例如，云属性可记录设备最近一次维修的数据。 此信息从不与设备共享。
- 利用自定义，构建人员可替代设备功能模型中的某些定义  。 例如，构建人员可替代设备属性的名称。 属性名称显示在 IoT Central 仪表板和窗体中。
- 利用仪表板和窗体，构建人员可创建 UI，操作员可使用该 UI 监视和管理连接到应用程序的设备  。

要在 IoT Central 中创建设备模板：

1. 请转到 IoT Central 应用程序中的“设备模板”页  。
1. 选择“+ 新建”，然后选择“自定义”   。
1. 请输入模板的名称，例如“环境传感器”  。
1. 按 **Enter**。 IoT Central 将创建一个空的设备模板。

## <a name="manage-a-device-template"></a>管理设备模板

可从模板主页中重命名或删除模板。

在将设备功能模型添加到模板后，即可发布该模板。 在发布模板之前，将无法连接基于此模板的设备，以便操作员在“设备”页进行查看  。

## <a name="create-a-capability-model"></a>创建功能模型

要创建设备功能模型，可执行以下操作：

- 使用 IoT Central 从头开始创建自定义模型。
- 从 JSON 文件导入模型。 设备构建人员可能已使用 Visual Studio Code 来创建应用程序的设备功能模型。
- 从设备目录中选择一台设备。 此选项将导入制造商已为此设备发布的设备功能模型。 用该方式导入的设备功能模型将自动发布。

## <a name="manage-a-capability-model"></a>管理功能模型

创建设备功能模型后，便可执行以下操作：

- 将接口添加到模型。 模型必须至少具有一个接口。
- 编辑模型元数据，例如其 ID、命名空间和名称。
- 删除模型。

## <a name="create-an-interface"></a>创建接口

设备功能必须至少具有一个接口。 接口是功能的可重用集合。

要创建接口：

1. 请转到设备功能模型并选择“+ 添加接口”  。

1. 在“选择接口”页，可以  ：

    - 从头开始创建自定义接口。
    - 从文件中导入现有接口。 设备构建人员可能已使用 Visual Studio Code 来创建设备接口。
    - 选择其中一个标准接口，如“设备信息”接口  。 标准接口指定许多设备通用的功能。 这些标准接口由 Microsoft Azure IoT 发布，且无法对其进行版本控制或编辑。

1. 创建接口后，选择“编辑标识”以更改接口的显示名称  。

1. 如果选择从头开始创建自定义接口，则可添加设备的功能。 设备功能包括遥测、属性和命令。

### <a name="telemetry"></a>遥测

遥测是从设备发送的值流，通常来自传感器。 例如，传感器可以报告环境温度。

下表显示了遥测功能的配置设置：

| 字段 | 说明 |
| ----- | ----------- |
| 显示名称 | 仪表板和窗体上使用的遥测值的显示名称。 |
| Name | 遥测消息中字段的名称。 IoT Central 将根据显示名称生成此字段的值，但你可根据需要选择自己的值。 |
| 功能类型 | 遥测。 |
| 语义类型 | 遥测的语义类型，如温度、状态或事件。 选择的语义类型将决定以下哪些字段可用。 |
| 架构 | 遥测数据类型，如 double、string 或 vector。 可用的选项取决于语义类型。 架构不可用于事件和状态语义类型。 |
| 严重性 | 仅适用于事件语义类型。 “错误”、“信息”或“警告”    。 |
| 状态值 | 仅适用于状态语义类型。 定义可能的状态值，其中每个状态值都具有显示名称、名称、枚举类型和值。 |
| 单位 | 遥测值的单位，如 mph、% 或 &deg;C    。 |
| 显示单位 | 仪表板和窗体上使用的显示单位。 |
| 注释 | 有关遥测功能的任何注释。 |
| 说明 | 遥测功能的说明。 |

### <a name="properties"></a>属性

属性表示时间值中的点。 例如，设备可使用属性来报告尝试达到的目标温度。 可从 IoT Central 设置可写属性。

下表显示了属性功能的配置设置：

| 字段 | 说明 |
| ----- | ----------- |
| 显示名称 | 仪表板和窗体上使用的属性值的显示名称。 |
| Name | 属性的名称。 IoT Central 将根据显示名称生成此字段的值，但你可根据需要选择自己的值。 |
| 功能类型 | 属性。 |
| 语义类型 | 属性的语义类型，如温度、状态或事件。 选择的语义类型将决定以下哪些字段可用。 |
| 架构 | 属性数据类型，如 double、string 或 vector。 可用的选项取决于语义类型。 架构不可用于事件和状态语义类型。 |
| 可写 | 如果属性不是可写，则设备可向 IoT Central 报告属性值。 如果属性是可写的，则该设备可以向 IoT Central 报告属性值，而 IoT Central 可将属性更新发送到设备。
| 严重性 | 仅适用于事件语义类型。 “错误”、“信息”或“警告”    。 |
| 状态值 | 仅适用于状态语义类型。 定义可能的状态值，其中每个状态值都具有显示名称、名称、枚举类型和值。 |
| 单位 | 属性值的单位，如 mph、% 或 &deg;C    。 |
| 显示单位 | 仪表板和窗体上使用的显示单位。 |
| 注释 | 有关属性功能的任何注释。 |
| 说明 | 属性功能的说明。 |

### <a name="commands"></a>命令

可从 IoT Central 调用设备命令。 命令可以选择将参数传递给设备，并接收来自设备的响应。 例如，你可以调用命令，在 10 秒内重启设备。

下表显示了命令功能的配置设置：

| 字段 | 说明 |
| ----- | ----------- |
| 显示名称 | 仪表板和窗体上使用的命令的显示名称。 |
| Name | 命令的名称。 IoT Central 将根据显示名称生成此字段的值，但你可根据需要选择自己的值。 |
| 功能类型 | 命令 |
| 命令 | SynchronousExecutionType。 |
| 注释 | 有关命令功能的任何注释。 |
| 说明 | 命令功能的说明。 |
| 请求 | 如果启用，则请求参数的定义包括：名称、显示名称、架构、单位和显示单位。 |
| 响应 | 如果启用，则命令响应的定义包括：名称、显示名称、架构、单位和显示单位。 |

## <a name="manage-an-interface"></a>管理接口

如果尚未发布接口，则可以编辑该接口定义的功能。 发布接口后，将需要创建设备模板的新版本并对接口进行版本设置才能进行任何更改。 可在“自定义”部分中进行不需要版本控制的更改，例如显示名称或单位  。

如果要在另一个功能模型中重用该接口，还可将其导出为 JSON 文件。

## <a name="add-cloud-properties"></a>添加云属性

使用云属性在 IoT Central 中存储设备相关信息。 云属性从不发送至设备。 例如，可以使用云属性来存储已安装设备的客户的名称或设备的最近一次维修日期。

下表显示了云属性的配置设置：

| 字段 | 说明 |
| ----- | ----------- |
| 显示名称 | 仪表板和窗体上使用的云属性值的显示名称。 |
| Name | 云属性的名称。 IoT Central 将根据显示名称生成此字段的值，但你可根据需要选择自己的值。 |
| 语义类型 | 属性的语义类型，如温度、状态或事件。 选择的语义类型将决定以下哪些字段可用。 |
| 架构 | 云属性数据类型，如 double、string 或 vector。 可用的选项取决于语义类型。 |

## <a name="add-customizations"></a>添加自定义项

需要修改导入的接口或将 IoT Central 特定的功能添加到功能时，可以使用自定义项。 只能自定义不会破坏接口兼容性的字段。 例如，可以：

- 自定义功能的显示名称和单位。
- 添加在图表上显示值时要使用的默认颜色。
- 指定属性的初始值、最小值和最大值。

无法自定义功能名称或功能类型。 如果无法在“自定义”部分中进行更改，则需要对设备模板和接口进行版本控制才能修改功能  。

### <a name="generate-default-views"></a>生成默认视图

生成默认视图是开始可视化重要设备信息的快捷方法。 最多可为设备模板生成三个默认视图：

- “命令”将提供带有设备命令的视图，并使操作员能够将其调度到你的设备  。
- “概述”将提供包含设备遥测数据的视图，显示图表和指标  。
- “关于”将提供包含设备信息的视图，显示设备属性  。

选择“生成默认视图”后，这些视图会自动添加到设备模板的“视图”部分下   。

## <a name="add-dashboards"></a>添加仪表板

将仪表板添加到设备模板，使操作员能够使用图表和指标来可视化设备。 设备模板可具有多个仪表板。

要将仪表板添加到设备模板：

- 请转到设备模板并选择“视图”  。
- 然后选择“直观显示设备”  。
- 在“仪表板名称”中输入仪表板的名称  。
- 从静态、属性、云属性、遥测和命令磁贴列表中将磁贴添加到仪表板。 拖放想要添加到仪表板的磁贴。
- 要在单个图表磁贴上绘制多个遥测值，请选择各个遥测值，然后选择“合并”  。
- 通过选择齿轮图标或选择图表磁贴上的“更改配置”按钮，配置添加的每个磁贴以自定义其显示数据的方式  。
- 排列仪表板上的磁贴并调整其大小。
- 保存更改。

### <a name="configure-preview-device-to-view-dashboard"></a>配置预览设备以查看仪表板

要查看和测试仪表板，可以选择“配置预览设备”，通过该选项即可在仪表板发布后查看操作员所看到的仪表板  。 利用此选项，可以验证视图是否显示正确的数据。 可以使用设备 ID 从无预览设备、为设备模板配置的实际测试设备或应用程序中的现有设备中进行选择。

## <a name="add-forms"></a>添加窗体

通过查看和设置属性，将窗体添加到设备模板，使操作员可以管理设备。 操作员只能编辑云属性和可写设备属性。 设备模板可具有多个窗体。

要将窗体添加到设备模板：

1. 请转到设备模板并选择“视图”  。
1. 然后选择“编辑设备和云数据”  。
1. 在“窗体名称”中输入窗体的名称  。
1. 选择用于对窗体进行布局的列数。
1. 将属性添加到窗体上的现有节，或选择属性并选择“添加节”  。 使用这些节对窗体上的属性进行分组。 可向某一节添加标题。
1. 配置窗体上的每个属性以自定义其行为。
1. 排列窗体上的属性。
1. 保存更改。

## <a name="publish-a-device-template"></a>发布设备模板

在连接用于实现设备功能模型的设备之前，必须发布设备模板。

发布设备模板后，只能对设备功能模型进行有限的更改。 要修改接口，需要[创建并发布新版本](./howto-version-device-template.md)。

要发布设备模板，请转到你的设备模板，然后选择“发布”  。

发布设备模板后，操作员可以转到“设备”页，并添加使用此设备模板的实际或模拟设备  。 在进行更改时可继续修改并保存设备模板，但是，若要将这些更改推送给操作员供其在“设备”页查看，必须每次都选择“发布”   。

## <a name="define-a-new-iot-gateway-device-type-preview-features"></a>定义新的 IoT 网关设备类型（预览功能）

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

本教程向构建人员介绍如何使用网关设备模板在 Azure IoT Central 应用程序中定义新的 Azure IoT 设备类型。 

在本节中，你将创建一个智能建筑设备模板  。 智能建筑网关设备：

* 发送温度和占用量等遥测数据。
* 在云中更新时响应可写属性，例如遥测数据发送间隔。
* 响应重置温度等命令。
* 允许与其他设备功能模型建立关系

### <a name="create-iot-device-templates"></a>创建 IoT 设备模板

你将创建 IoT 设备模板。 

在左侧导航栏中单击“设备模板”，单击“+ 新建”，选择“IoT 设备”磁贴，然后选择占用传感器磁贴并单击“下一步:    自定义”

![IoT 设备](./media/tutorial-define-iot-device-type/gateway-downstream-new.png)

随即将显示查看页。 单击“创建”  按钮。 

![IoT 设备](./media/tutorial-define-iot-device-type/gateway-downstream-review.png)

现已创建新的设备模板。 

![IoT 设备](./media/tutorial-define-iot-device-type/occupancy-sensor.png)

你将为 S1 传感器创建设备模板。 

在左侧导航栏中单击“设备模板”，单击“+ 新建”，选择“IoT 设备”磁贴，然后选择占用传感器磁贴并单击“下一步:    自定义”

![IoT 设备](./media/tutorial-define-iot-device-type/s1-sensor.png)

随即将显示查看页。 单击“创建”  按钮。 

![下游设备](./media/tutorial-define-iot-device-type/s1-review.png)

现已创建新的设备模板。 

![下游设备](./media/tutorial-define-iot-device-type/s1-template.png)

## <a name="create-an-iot-gateway-device-template"></a>创建 IoT 网关设备模板

你可以选择创建 IoT 网关设备模板。 网关设备将与通过网关设备连接到 IoT Central 的下游设备建立关系。 

### <a name="downstream-device-relationships-with-gateway-device"></a>下游设备与网关设备的关系

IoT 设备可以连接到 Azure IoT 网关设备 

![Central 应用程序页](./media/tutorial-define-iot-device-type/gatewaypattern.png)

作为构建人员，你可在应用程序中创建和编辑 Azure IoT 网关设备模板。 发布设备模板后，可连接用于实施该设备模板的实际设备。

### <a name="select-device-template-type"></a>选择设备模板类型 

若要将新设备模板添加到应用程序，请转到“设备模板”页。  为此，请在左窗格中选择“设备模板”选项卡  。

![Central 应用程序页](./media/tutorial-define-iot-device-type/devicetemplate.png)

单击“+ 新建”，开始创建新的设备模板  。

![设备模板 - 新建](./media/tutorial-define-iot-device-type/devicetemplatenew.png)

![设备模板选择 - 网关](./media/tutorial-define-iot-device-type/gateway-review.png)

你将登陆设备模板类型选择页。 选择“Azure IoT”磁贴并单击底部的“下一步:   自定义”按钮

选择“网关”复选框并单击“创建”  

![设备模板选择 - 网关](./media/tutorial-define-iot-device-type/gateway-customize.png)

随即将显示查看页，单击“创建”  

![设备模板 - 网关](./media/tutorial-define-iot-device-type/gateway-review.png)

输入网关模板名称“智能建筑网关模板”  。 单击“自定义”磁贴  。

添加标准接口“设备信息”  。

### <a name="add-relationships"></a>添加关系

可以将下游关系添加到将连接到网关设备的设备的设备功能模型。

与下游设备功能模型建立关系。 单击“保存” 

![设备模板 - 网关](./media/tutorial-define-iot-device-type/gateway-occupancy-s1-rel.png)

### <a name="add-cloud-properties"></a>添加云属性

设备模板可以包含云属性。 云属性仅存在于 IoT Central 应用程序中，永远不会与设备相互发送和接收信息。

1. 依次选择“云属性”、“+ 添加云属性”。   使用下表中的信息将云属性添加到设备模板。

    | 显示名称      | 语义类型 | 架构 |
    | ----------------- | ------------- | ------ |
    | 最后维修日期 | 无          | Date   |
    | 客户名称     | 无          | String |

2. 选择“保存”以保存更改： 

### <a name="add-customizations"></a>添加自定义项

需要修改接口，或者将 IoT Central 特定的功能添加到不需要对设备功能模型进行版本控制的功能时，可以使用自定义项。 当功能模型处于草稿或已发布状态时，可以自定义字段。 只能自定义不会破坏接口兼容性的字段。 例如，可以：

- 自定义功能的显示名称和单位。
- 添加在图表上显示值时要使用的默认颜色。
- 指定属性的初始值、最小值和最大值。

无法自定义功能名称或功能类型。 单击“保存” 

### <a name="create-views"></a>创建视图

构建人员可将应用程序自定义为向操作员显示有关环境传感器设备的信息。 完成自定义后，操作员可以管理连接到应用程序的环境传感器设备。 可为操作员创建两种类型的视图来让他们与设备交互：

* 用于查看和编辑设备与云属性的窗体。
* 用于可视化设备的仪表板。

### <a name="generate-default-views"></a>生成默认视图

对于本教程，请单击“生成默认视图”。 将生成“概述和关于”仪表板。 

## <a name="publish-device-template"></a>发布设备模板

在创建模拟环境传感器或连接实际环境传感器之前，需要发布设备模板。

若要发布设备模板：

1. 从“设备模板”页转到你的设备模板。 

2. 选择“发布”  。

3. 在“发布设备模板”对话框中，选择“发布”：  

发布设备模板后，它将显示在“设备”页上，操作员可以看到它。  在发布的设备模板中，除非创建新的版本，否则无法编辑设备功能模型。 但是，在不创建新版本的情况下，可以在发布的设备模板中更新云属性、自定义项和视图。 进行任何更改后，选择“发布”将这些更改推送给操作员。 

## <a name="create-gateway-simulated-device"></a>创建网关模拟设备

从设备资源管理器创建模拟智能建筑网关。 

![设备模板 - 网关](./media/tutorial-define-iot-device-type/smartbuildingdevice.png)

## <a name="create-downstream-simulated-devices"></a>创建下游模拟设备

从设备资源管理器创建模拟占用传感器。 

![设备模板 - 占用](./media/tutorial-define-iot-device-type/occupancydevice.png)

从设备资源管理器创建模拟 s1 传感器。 

![设备模板 - s1](./media/tutorial-define-iot-device-type/s1device.png)

## <a name="add-downstream-devices-relationships-to-gateway-device"></a>将下游设备关系添加到网关设备

选择 S1 传感器和占用传感器并单击“连接到网关”  。 

![设备模板 - s1](./media/tutorial-define-iot-device-type/connecttogateway.png)

选择网关设备模板和网关设备实例并单击“联接”  。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

* 创建新的 IoT 网关作为设备模板
* 创建云属性。
* 创建自定义项。
* 定义设备遥测的可视化效果。
* 添加关系
* 发布设备模板。

下面是建议的后续步骤：

> [!div class="nextstepaction"]
> [连接设备](tutorial-connect-pnp-device.md)
