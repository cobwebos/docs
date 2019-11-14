---
title: 教程：设置 Azure 时序见解预览版环境 | Microsoft Docs
description: 了解如何在 Azure 时序见解预览中设置环境。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 11/06/2019
ms.custom: seodec18
ms.openlocfilehash: 25571dbd87e4d01645a3a7a991588a3a943b3e4d
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73719292"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>教程：设置 Azure 时序见解预览环境

本教程将逐步引导你创建一个 Azure 时序见解预览版即付即用 (PAYG) 环境。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
>
> * 创建 Azure 时序见解预览环境。
> * 将 Azure 时序见解预览版环境连接到 IoT 中心。
> * 运行解决方案加速器示例，以将数据流式传输到 Azure 时序见解预览版环境。
> * 对数据进行基本的分析。
> * 定义时序模型类型和层次结构，并将其与实例相关联。
> * 使用 Power BI 连接器并在 Power BI 中实现数据的可视化。

>[!TIP]
> [IoT 解决方案加速器](https://www.azureiotsolutions.com/Accelerators)提供企业级的预配置解决方案，可用于加速自定义 IoT 解决方案的开发。

注册一个[免费的 Azure 订阅](https://azure.microsoft.com/free/)（如果还没有）。

## <a name="prerequisites"></a>先决条件

* 必须至少具有 Azure 订阅的参与者角色  。 有关详细信息，请参阅[使用基于角色的访问控制和 Azure 门户管理访问权限](../role-based-access-control/role-assignments-portal.md)。

## <a name="create-a-device-simulation"></a>创建设备模拟

在本部分中，将创建三个模拟设备，用于将数据发送到 Azure IoT 中心实例。

1. 转到 [Azure IoT 解决方案加速器页](https://www.azureiotsolutions.com/Accelerators)。 该页显示了多个预生成的示例。 使用 Azure 帐户登录。 然后，选择“设备模拟”  。

   [![Azure IoT 解决方案加速器页](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox)

1. 在下一页，选择“立即尝试”  。

   [![设备模拟页](media/v2-update-provision/device-two-try.png)](media/v2-update-provision/device-two-try.png#lightbox)

1. 在“创建设备模拟解决方案”  页上，设置以下参数：

    | 参数 | 操作 |
    | --- | --- |
    | **部署名称** | 输入用于新资源组的唯一值。 将会创建列出的 Azure 资源并将其分配到资源组。 |
    | **Azure 订阅** | 选择将用于创建时序见解环境的订阅。 |
    | **Azure 位置** | 选择将用于存储时序见解环境的区域。 请注意，设备模拟器仅在有限数量的区域中提供，因此，如果没有看到所需的区域，则可以选择仅为教程选择一个位置，然后在准备好移到下一阶段的载入时创建新的 TSI 环境。  |
    | **部署选项** | 选择“预配新的 IoT 中心”  。 |

    1. 选择“创建”  。
    [![设备模拟页](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox)

1. 大约 20 分钟后，解决方案加速器将准备就绪。

    [![设备模拟页](media/v2-update-provision/device-two-ready.png)](media/v2-update-provision/device-two-ready.png#lightbox)

## <a name="create-a-preview-payg-environment"></a>创建预览版 PAYG 环境

本部分介绍如何创建 Azure 时序见解预览版环境，并使用 [Azure 门户](https://portal.azure.com/)将其连接到由 IoT 解决方案加速器创建的 IoT 中心。

1. 使用 Azure 订阅帐户登录到 Azure 门户。

1. 选择“创建资源” > “物联网” > “时序见解”    。

   [![依次选择“物联网”、“时序见解”](media/v2-update-provision/payg-one-azure.png)](media/v2-update-provision/payg-one-azure.png#lightbox)

1. 在“创建时序见解环境”  窗格的“基本信息”  选项卡上，设置以下参数：

    | 参数 | 操作 |
    | --- | ---|
    | **环境名称** | 输入 Azure 时序见解预览版环境的唯一名称。 |
    | **订阅** | 输入想要在其中创建 Azure 时序见解预览版环境的订阅。 最佳做法是使用与设备模拟器创建的其他 IoT 资源相同的订阅。 |
    | **资源组** | 为 Azure 时序见解预览版环境资源选择现有的资源组或创建新的资源组。 资源组是 Azure 资源的容器。 最佳做法是使用与设备模拟器创建的其他 IoT 资源相同的资源组。 |
    | **位置** | 为 Azure 时序见解预览版环境选择数据中心区域。 为了避免额外的延迟，最好是在设备模拟器创建的 IoT 中心所在的区域中创建 Azure 时序见解预览版环境。 |
    | **层** |  选择“PAYG”（即用即付）。   这是 Azure 时序见解预览版产品的 SKU。 |
    | **属性 ID** | 输入一个用于唯一标识时序实例的值。 以后不能更改在“属性 ID”框中输入的值  。 在本教程中，请输入 **iothub-connection-device-id**。若要详细了解时序 ID，请参阅[选择时序 ID 的最佳做法](./time-series-insights-update-how-to-id.md)。 |
    | **存储帐户名称** | 为新存储帐户输入全局唯一名称。|
    |**启用 Warm 存储**|选择“是”  启用 Warm 存储。|
    |**日志保留期（以天为单位）**|选择默认选项 7 天。 |

    在完成时选择“下一步:**事件源”** 。

   [![用于创建时序见解环境的窗格](media/v2-update-provision/payg-two-1-create.png)](media/v2-update-provision/payg-two-1-create.png#lightbox)
   [![用于创建时序见解环境的窗格](media/v2-update-provision/payg-two-2-create.png)](media/v2-update-provision/payg-two-2-create.png#lightbox)

1. 在“事件源”选项卡上，设置以下参数： 

   | 参数 | 操作 |
   | --- | --- |
   | **创建事件源?** | 请选择“是”。 |
   | **名称** | 输入事件源名称的唯一值。 |
   | **源类型** | 选择“IoT 中心”  。 |
   | **选择中心** | 选择“选择现有”。  |
   | **订阅** | 选择曾经用于设备模拟器的订阅。 |
   | **IoT 中心名称** | 选择为设备模拟器创建的 IoT 中心名称。 |
   | **Iot 中心访问策略** | 选择“iothubowner”。  |
   | **IoT 中心使用者组** | 选择“新建”，输入唯一名称，然后选择“添加”。   在 Azure 时序见解预览版中，使用者组必须是唯一值。 |
   | **时间戳属性** | 此值用于标识传入遥测数据中的**时间戳**属性。 在本教程中，请将此框留空。 此模拟器使用 IoT 中心的传入时间戳，时序见解默认使用该时间戳。 |

   选择“查看 + 创建”  。

   [![配置事件源](media/v2-update-provision/payg-five-event-source.png)](media/v2-update-provision/payg-five-event-source.png#lightbox)

1. 选择“创建”  。

    [![包含“创建”按钮的“查看 + 创建”页](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

    可以看到部署状态：

    [![指出部署已完成的通知](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. 如果你是 Azure 订阅的所有者，则默认情况下可以访问 Azure 时序见解预览版环境。 验证你是否具有访问权限：

   1. 搜索资源组，然后选择新创建的 Azure 时序见解预览版环境。 
      [![所选环境](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   1. 在“Azure 时序见解预览版”页上，选择“数据访问策略”  ：[![数据访问策略](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox)

   1. 验证凭据是否已列出：

      [![列出的凭据](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   如果未列出你的凭据，则必须通过选择“添加”并搜索自己的凭据来授予自己访问该环境的权限。 若要详细了解如何设置权限，请参阅[授予数据访问权限](./time-series-insights-data-access.md)。

## <a name="stream-data"></a>流式传输数据

现已部署了时序见解环境，请将数据流式传输以进行分析。

1. 导航回 [Azure IoT 解决方案加速器页](https://www.azureiotsolutions.com/Accelerators)。 在解决方案加速器仪表板中找到你的解决方案，然后选择“启动”  ：

    [![启动设备模拟解决方案](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. 选择“转到解决方案加速器”  。

    [![启动设备模拟解决方案](media/v2-update-provision/device-accelerator.png)](media/v2-update-provision/device-accelerator.png#lightbox)

1. 随后你将重定向到“Microsoft Azure IoT 设备模拟”页  。 在页面右上角选择“新建模拟”。 

    [![Azure IoT 模拟页](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. 在“模拟设置”  窗格中，设置以下参数：

    | 参数 | 操作 |
    | --- | --- |
    | **名称** | 为模拟器输入唯一名称。 |
    | **说明** | 输入定义。 |
    | **模拟持续时间** | 设置为“无限期运行”。  |
    | **设备型号** | 单击 +“添加设备类型”  <br />**名称**：输入“电梯”  。 <br />**数量**：输入 **3**。 <br /> 保留其余默认值 |
    | **目标 IoT 中心** | 设置为“使用预配的 IoT 中心”。  |

    [![要设置的参数](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    选择“开始模拟”  。

    在设备模拟仪表板中，会看到“活动设备数”和“总消息数”   。

    [![Azure IoT 模拟仪表板](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="analyze-data"></a>分析数据

在此部分，使用 [Azure 时序见解预览资源管理器](./time-series-insights-update-explorer.md)对时序数据进行基本的分析。

1. 在 [Azure 门户](https://portal.azure.com/)中的资源页上选择相应的 URL，转到 Azure 时序见解预览版资源管理器。

    [![时序见解预览版资源管理器 URL](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. 在时序见解资源管理器中，会看到横跨屏幕顶部的一个栏。 这是可用性选取器。 确保至少选择了两个 2m，并在需要时通过选中选取器控点并将其向左和向右拖动来扩展期限。

1. 你将可以在左侧看到“时序实例”  。


    [![无父级实例的列表](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. 选择第一个时序实例。 然后，选择“显示压强”。 

    [![所选的时序实例，带有的菜单命令可用于显示平均压强](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

    此时会显示时序图。 将“间隔”更改为 30s   。

    [![时序图](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. 重复步骤 3 和其他两个时序实例，以便查看全部三个时序实例，如以下图表所示：

    [![所有时序的图表](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. 选择右上角的时间跨度选取器。 你可以在此处选择特定的开始时间和结束时间（最小可选择毫秒），或从预先配置的选项中进行选择（如上一小时）。 也可以更改默认时区。

    [![将时间范围设置为小时](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

    下面是在运行模拟一小时后图表窗格的屏幕截图：

    [![图表窗格](media/v2-update-provision/analyze-seven-time.png)](media/v2-update-provision/analyze-seven-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>定义并应用模型

在本部分，你将应用一个模型来构造数据。 若要完成该模型，需要定义类型、层次结构和实例。 若要详细了解数据建模，请参阅[时序模型](./time-series-insights-update-tsm.md)。

1. 在资源管理器中，选择“模型”  选项卡：

   [![资源管理器中的“模型”选项卡](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. 在“类型”  选项卡中，选择“添加”  。

   [![类型的“添加”按钮](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. 输入以下参数：

    | 参数 | 操作 |
    | --- | ---|
    | **名称** | 输入“电梯”  |
    | **说明** | 输入“这是电梯的类型定义”  |

    [![类型的“添加”按钮](media/v2-update-provision/define-three-properties.png)](media/v2-update-provision/define-three-properties.png#lightbox)

1. 接下来，选择“变量”选项卡  。[![选择变量选项卡](media/v2-update-provision/define-four-variables.png)](media/v2-update-provision/define-four-variables.png#lightbox)

1. 选择“+ 添加变量”  并为电梯类型的第一个变量填写以下值。 总共创作三个变量。

    | 参数 | 操作 |
    | --- | --- |
    | **名称** | 输入“平均温度”。  |
    | **种类** | 选择“数值”  |
    | **值** | 从预设中选择：选择“温度(双精度型)”  。 <br /> 注意：在 Azure 时序见解预览版开始接收事件之后，可能需要等待几分钟才会自动填充“值”。 |
    | **聚合操作** | 展开“高级选项”  。 <br /> 选择“AVG”。  |

    [![用于定义温度的选项](media/v2-update-provision/define-five-variable.png)](media/v2-update-provision/define-five-variable.png#lightbox)

    选择“应用”。 

    再次选择“+ 添加变量”  ，并设置以下值：

    | 参数 | 操作 |
    | --- | --- |
    | **名称** | 输入“平均振动”  。 |
    | **种类** | 选择“数值”  |
    | **值** | 从预设中选择：选择“振动(双精度)”  。 <br /> 注意：在 Azure 时序见解预览版开始接收事件之后，可能需要等待几分钟才会自动填充“值”。 |
    | **聚合操作** | 展开“高级选项”  。 <br /> 选择“AVG”。  |

    [![用于定义振动的选项](media/v2-update-provision/define-five-vibration.png)](media/v2-update-provision/define-five-vibration.png#lightbox)

    选择“应用”。 

    再次选择“+ 添加变量”  ，并为第三个和最后一个变量设置以下值：

    | 参数 | 操作 |
    | --- | --- |
    | **名称** | 输入“楼层”  。 |
    | **种类** | 选择“分类”  |
    | **值** | 从预设中选择：选择“楼层(双精度)”  。 <br /> 注意：在 Azure 时序见解预览版开始接收事件之后，可能需要等待几分钟才会自动填充“值”。 |
    | **类别** | <span style="text-decoration: underline">标记 </span>  - <span style="text-decoration: underline">值</span> <br /> 较低：1、2、3、4 <br /> 中层：5、6、7、8、9 <br /> 较高：10、11、12、13、14、15 |
    | **默认类别** | 输入“未知”  |

    [![用于定义振动的选项](media/v2-update-provision/define-five-floor.png)](media/v2-update-provision/define-five-floor.png#lightbox)

    选择“应用”。  应会看到创建的三个变量：

    [![用于定义振动的选项](media/v2-update-provision/define-five-floor2.png)](media/v2-update-provision/define-five-floor2.png#lightbox)

   选择“保存”。  将会看到创建的类型  ：

    [![用于定义振动的选项](media/v2-update-provision/define-five-floor3.png)](media/v2-update-provision/define-five-floor3.png#lightbox)

1. 选择“层次结构”  选项卡。选择“+添加”  。

    [![包含“添加”按钮的“层次结构”选项卡](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. 在“编辑层次结构”  窗格中，设置以下参数：

   | 参数 | 操作 |
   | --- | ---|
   | **名称** | 输入“位置层次结构”。  |
   |**级别**| 输入“国家/地区”  作为第一级的名称 <br> 选择“+ 添加级别”  <br> 输入“城市”作为第二级  ，然后选择“+ 添加级别”  <br> 输入“建筑”  作为第三级和最后一个级别的名称 |

   选择“保存”。 

    [![包含“创建”按钮的“层次结构”字段](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

   可以看到创建的层次结构：

    [![有关层次结构的信息](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. 导航到“实例”  。 在最右侧的“操作”  下，选择铅笔图标以编辑具有以下值的第一个实例：

    | 参数 | 操作 |
    | --- | --- |
    | 类型  | 选择“电梯”  。 |
    | **名称** | 输入“电梯 1” |
    | **说明** | 输入“电梯 1 的实例”  |

    [![选择实例对应的“编辑”按钮](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

    导航到“实例字段”  并输入以下内容：

    | 参数 | 操作 |
    | --- | --- |
    | **层次结构** | 选择“位置层次结构”  |
    | **国家/地区** | 输入“美国”  |
    | **城市** | 输入“西雅图”  |
    | **建筑物** | 输入“太空针塔”  |

    [![选择实例对应的“编辑”按钮](media/v2-update-provision/define-ten2-edit.png)](media/v2-update-provision/define-ten2-edit.png#lightbox)

    选择“保存”。 

1. 对具有以下值的其他两个实例重复步骤 8：

    <span style="text-decoration: underline;">对于电梯 2</span>：

    | 参数 | 操作 |
    | --- | --- |
    | 类型  | 选择“电梯”  。 |
    | **名称** | 输入“电梯 2” |
    | **说明** | 输入“电梯 2 的实例”  |
    | **层次结构** | 选择“位置层次结构”  |
    | **国家/地区** | 输入“美国”  |
    | **城市** | 输入“西雅图”  |
    | **建筑物** | 输入“太平洋科学中心”  |

    <span style="text-decoration: underline;">对于电梯 3</span>：

    | 参数 | 操作 |
    | --- | --- |
    | 类型  | 选择“电梯”  。 |
    | **名称** | 输入“电梯 3” |
    | **说明** | 输入“电梯 3 的实例”  |
    | **层次结构** | 选择“位置层次结构”  |
    | **国家/地区** | 输入“美国”  |
    | **城市** | 输入“纽约”  |
    | **建筑物** | 输入“帝国大厦”  |

1. 导航回到“分析”  选项卡以查看图表窗格。 在“位置层次结构”下展开所有层次结构级别，显示时序实例： 

   [![“分析”选项卡](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. 在“太平洋科学中心”下选择时序实例“电梯 2”，然后选择“显示平均温度”    。

    [![时序实例显示平均温度](media/v2-update-provision/define-eleven-temperature.png)](media/v2-update-provision/define-eleven-temperature.png#lightbox)

1. 对于同一实例“电梯 2”  ，选择“显示楼层”  。

    [![时序实例显示楼层](media/v2-update-provision/define-twelve-floor.png)](media/v2-update-provision/define-twelve-floor.png#lightbox)

    借助分类变量，可以确定电梯在较高、较低和中层楼层耗费的时间。

## <a name="clean-up-resources"></a>清理资源

现在你已完成本教程，请清除已创建的资源：

1. 从 [Azure 门户](https://portal.azure.com)的左侧菜单中，选择“所有资源”  ，找到“Azure 时序见解”资源组。
1. 通过选择“删除”  来删除整个资源组（以及其中包含的所有资源），或者单独删除每个资源。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：  

* 创建并使用设备模拟加速器。
* 创建 Azure 时序见解预览 PAYG 环境。
* 将 Azure 时序见解预览版环境连接到 IoT 中心。
* 运行解决方案加速器示例，以将数据流式传输到 Azure 时序见解预览版环境。
* 对数据进行基本的分析。
* 定义时序模型类型和层次结构，并将其与实例相关联。

知道如何创建自己的 Azure 时序见解预览环境后，现在来了解更多关于 Azure 时序见解的关键概念。

了解 Azure 时序见解存储配置：

> [!div class="nextstepaction"]
> [Azure 时序见解预览版存储和入口](./time-series-insights-update-storage-ingress.md)

详细了解时序模型：

> [!div class="nextstepaction"]
> [Azure 时序见解预览数据模型](./time-series-insights-update-tsm.md)
