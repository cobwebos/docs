---
title: 在 Azure IoT Central 中定义新设备类型 | Microsoft Docs
description: 本教程向构建人员介绍如何在 Azure IoT Central 应用程序中创建新的设备类型。 其中介绍了如何为类型定义遥测、状态、属性和命令。
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: a622aa85d1d0a9dcd5d5ad9b2b30e7a3120ea974
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878628"
---
# <a name="tutorial-define-a-new-device-type-in-your-azure-iot-central-application-preview-features"></a>教程：在 Azure IoT Central 应用程序中定义新的设备类型（预览版功能）

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

本教程向构建人员介绍如何使用设备模板在 Microsoft Azure IoT Central 应用程序中定义新的设备类型。 设备模板定义设备的功能。 功能包括设备发送的遥测数据、设备属性以及设备响应的命令。

本教程将创建一个“环境传感器”设备模板。  环境传感器设备：

* 发送温度等遥测数据。
* 报告设备特定的属性，例如亮度级别。
* 响应打开和关闭等命令。
* 报告常规设备属性，例如固件版本和序列号。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建新的设备模板。
> * 导入设备功能模型。
> * 创建云属性。
> * 定义设备遥测的可视化效果。
> * 发布设备模板。
> * 为设备模板创建模拟设备。
> * 查看模拟设备。

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要一个 Azure IoT Central 应用程序。 如果已完成[创建 Azure IoT Central 应用程序](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)快速入门，则可以重复使用此快速入门中创建的应用程序。 否则，请完成以下步骤，以创建一个空的 Azure IoT Central 应用程序：

1. 导航到 Azure IoT Central 的[应用程序管理器](https://aka.ms/iotcentral)页。

1. 如果你有一个 Azure 订阅，请使用用于访问该订阅的凭据登录，否则请使用 Microsoft 帐户登录：

    ![输入组织帐户](./media/tutorial-define-device-type-pnp/sign-in.png)

1. 若要开始创建新的 Azure IoT Central 应用程序，请选择“新建应用程序”  。

若要创建使用预览版功能（包括 IoT 即插即用）的新 Azure IoT Central 应用程序：

1. 选择“试用版”  。 无需 Azure 订阅即可创建试用版应用程序。

    有关目录和订阅的详细信息，请参阅[创建应用程序快速入门](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)。

1. 选择“预览应用程序”。 

1. 可以选择一个友好的应用程序名称，例如 **Contoso Air Conditioners**。 Azure IoT Central 将会生成唯一的 URL 前缀。 可将此 URL 前缀更改为更容易记住的内容。

1. 若要创建试用应用程序，需要提供详细的联系信息。

1. 选择“创建”  。

    ![Azure IoT Central 的“创建应用程序”页](./media/tutorial-define-device-type-pnp/iotcentralcreate.png)

    有关详细信息，请参阅[创建应用程序快速入门](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)。

还需要获取包含 [IoT 即插即用](https://aka.ms/iot-pnp-docs)设备功能模型的 **EnvironmentalSensorInline.capabilitymodel.json** 文件的本地副本。 可以从[此处](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json)下载它。 在页面上单击右键，然后选择“另存为”。 

下载该文件后，在文本编辑器中将其打开，并将出现的两个 `<YOUR_COMPANY_NAME_HERE>` 替换为自己的名称。 请仅使用字符 a-z、A-Z、0-9 和下划线。

## <a name="create-a-template"></a>创建模板

构建人员可在应用程序中创建和编辑设备模板。 发布设备模板后，可以生成模拟设备，或连接用于实施该设备模板的实际设备。 使用模拟设备可以在连接实际设备之前先测试应用程序的行为。

若要将新设备模板添加到应用程序，请转到“设备模板”页。  为此，请在左侧导航菜单中选择“设备模板”选项卡。 

![“设备模板”页](./media/tutorial-define-device-type-pnp/devicedefinitions.png)

### <a name="add-a-device-capability-model"></a>添加设备功能模型

在 IoT Central 中，可通过多个选项来创作设备功能模型。 可以选择从头开始创建自定义模型、从文件导入、从设备目录中选择，或者通过设备优先连接（其中的设备功能模型已在公共存储库中发布）来连接 IoT 即插即用设备。 本教程介绍如何从文件导入设备功能模型。

以下步骤说明如何导入“环境传感器”设备的功能模型。  这些设备将遥测数据（例如温度）发送到应用程序：

1. 若要添加新的设备模板，请在“设备模板”页上选择“+ 新建”。  

1. 从设备功能模型列表中选择“自定义”。 

1. 输入“环境传感器”作为设备模板的名称。 

1. 选择“导入功能模型”，以基于 JSON 文件创建新的设备功能模型。  导航到本地计算机上保存 **EnvironmentalSensorInline.capabilitymodel.json** 文件的文件夹。 选择文件“EnvironmentalSensorInline.capabilitymodel.json”，然后选择“导入”。  环境传感器功能模型包括“环境传感器”和“设备信息”接口：  

    ![环境传感器设备功能模型](./media/tutorial-define-device-type-pnp/newdevicecapabilitymodel.png)

    这些接口定义“环境传感器”设备的功能。  功能包括设备发送的遥测数据、设备报告的属性，以及设备响应的命令。

### <a name="add-cloud-properties"></a>添加云属性

设备模板可以包含云属性。 云属性仅存在于 IoT Central 应用程序中，永远不会与设备相互发送和接收信息。

1. 依次选择“云属性”、“+ 添加云属性”。   使用下表中的信息将云属性添加到设备模板。

    | 显示名称      | 语义类型 | 架构 |
    | ----------------- | ------------- | ------ |
    | 最后维修日期 | 无          | Date   |
    | 客户名称     | 无          | String |

1. 选择“保存”以保存更改： 

    ![云属性](media/tutorial-define-device-type-pnp/cloudproperties.png)

### <a name="add-customizations"></a>添加自定义项

需要修改接口，或者将 IoT Central 特定的功能添加到不需要对设备功能模型进行版本控制的功能时，可以使用自定义项。 当功能模型处于草稿或已发布状态时，可以自定义字段。 只能自定义不会破坏接口兼容性的字段。 例如，可以：

- 自定义功能的显示名称和单位。
- 添加在图表上显示值时要使用的默认颜色。
- 指定属性的初始值、最小值和最大值。

无法自定义功能名称或功能类型。


## <a name="create-views"></a>创建视图

构建人员可将应用程序自定义为向操作员显示有关环境传感器设备的信息。 完成自定义后，操作员可以管理连接到应用程序的环境传感器设备。 可为操作员创建两种类型的视图来让他们与设备交互：

* 用于查看和编辑设备与云属性的窗体。
* 用于可视化设备的仪表板。

### <a name="generate-default-views"></a>生成默认视图

生成默认视图是开始可视化重要设备信息的快捷方法。 最多可为设备模板生成三个默认视图：

* “命令”视图可让操作员将命令发送到设备。 
* “概述”视图使用图表和指标来显示设备遥测数据。 
* “关于”视图显示设备属性。 

选择“生成默认视图”后，这些视图会自动添加到设备模板的“视图”部分下。  

### <a name="configure-a-view-to-visualize-devices"></a>配置用于可视化设备的视图

设备仪表板可让操作员使用图表和指标来可视化设备。 构建人员可以定义要在设备仪表板上显示的信息。 可为设备定义多个仪表板。 若要创建一个仪表板来可视化环境传感器遥测数据，请依次选择“视图”、“可视化设备”：  

1. 所有设备属性、云属性、遥测和静态选项将在右侧面板中列出。 可将其中的任意项拖放到视图中。 将属性拖放到视图中。 可以使用齿轮图标配置磁贴。

1. 若要添加用于绘制遥测数据的图表，请选择“湿度”和“温度”，然后选择“合并”。    若要以任何不同的格式（例如饼图或条形图）查看此图表，请选择磁贴顶部的“更改可视化效果”按钮。 

1. 选择“保存”以保存视图： 

可以添加更多的磁贴用于显示其他属性或遥测值。 还可以添加静态文本、链接和图像。 若要移动仪表板上的某个磁贴或调整其大小，请将鼠标指针移到该磁贴上，然后将该磁贴拖放到新位置或调整其大小。

### <a name="add-a-device-form"></a>添加设备窗体

设备窗体可让操作员编辑可写的设备属性和云属性。 构建人员可以定义多个窗体，并选择要在每个窗体中显示的设备属性和云属性。 还可以在窗体中显示只读的设备属性。

若要创建窗体用于查看和编辑环境传感器属性：

1. 选择“编辑设备和云数据”磁贴。 

1. 输入窗体名称“环境传感器属性”。 

1. 将“客户名称”和“上次检修日期”云属性拖放到窗体中的现有部分。  

1. 选择“亮度级别”和“设备状态”设备属性。   然后选择“添加部分”。  将部分的标题编辑为“传感器属性”。  选择“应用”。 

1. 选择“剩余电池电量”、“设备型号”、“固件版本”、“制造商”和“序列号”设备属性。      然后选择“添加部分”。  将部分的标题编辑为“设备属性”。  选择“应用”。 

1. 选择“保存”以保存视图。 


## <a name="publish-device-template"></a>发布设备模板

在创建模拟环境传感器或连接实际环境传感器之前，需要发布设备模板。

若要发布设备模板：

1. 从“设备模板”页转到你的设备模板。 

1. 选择“发布”  。

1. 在“发布设备模板”对话框中，选择“发布”：  

    ![发布的模型](media/tutorial-define-device-type-pnp/publishedmodel.png)

发布设备模板后，它将显示在“设备”页上，操作员可以看到它。  在发布的设备模板中，除非创建新的版本，否则无法编辑设备功能模型。 但是，在不创建新版本的情况下，可以在发布的设备模板中更新云属性、自定义项和视图。 进行任何更改后，选择“发布”将这些更改推送给操作员。 

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

* 创建新设备模板
* 导入设备功能模型。
* 创建云属性。
* 创建自定义项。
* 定义设备遥测的可视化效果。
* 发布设备模板。

在 Azure IoT Central 应用程序中创建设备模板后，建议接下来执行以下后续步骤：

> [!div class="nextstepaction"]
> [添加设备](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
