---
title: 教程：设置预览版环境 - Azure 时序见解 | Microsoft Docs
description: 教程：了解如何在 Azure 时序见解预览中设置环境。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 12/26/2019
ms.custom: seodec18
ms.openlocfilehash: bebc7dde30dad57157d0abee7f2294d9da58fd5c
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75861806"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>教程：设置 Azure 时序见解预览环境

本教程将逐步引导你创建一个 Azure 时序见解预览版即付即用 (PAYG) 环境  。

在本教程中，你将了解如何执行以下操作：

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

## <a name="prerequisites"></a>必备条件

* 必须至少具有 Azure 订阅的参与者角色  。 有关详细信息，请阅读[使用基于角色的访问控制和 Azure 门户管理访问权限](../role-based-access-control/role-assignments-portal.md)。

## <a name="create-a-device-simulation"></a>创建设备模拟

在本部分中，将创建三个模拟设备，用于将数据发送到 Azure IoT 中心实例。

1. 转到 [Azure IoT 解决方案加速器页](https://www.azureiotsolutions.com/Accelerators)。 该页显示了多个预生成的示例。 使用 Azure 帐户登录。 然后，选择“设备模拟”  。

   [![Azure IoT 解决方案加速器页。](media/v2-update-provision/iot-solution-accelerators-landing-page.png)](media/v2-update-provision/iot-solution-accelerators-landing-page.png#lightbox)

1. 在下一页，选择“立即尝试”  。 然后，在“创建设备模拟解决方案”  页上输入所需的参数。

   参数|说明
   ---|---
   **部署名称** | 此唯一值用于创建新资源组。 将会创建列出的 Azure 资源并将其分配到资源组。
   **Azure 订阅** | 指定在上一部分用于创建时序见解环境的同一订阅。
   **部署选项** | 选择“预配新的 IoT 中心”  可创建特定于本教程的新 IoT 中心。
   **Azure 位置** | 指定在上一部分用于创建时序见解环境的同一区域。

   完成后选择“创建”，以预配解决方案的 Azure 资源。  完成此过程可能最多需要 20 分钟的时间。

   [![预配设备模拟解决方案。](media/v2-update-provision/iot-solution-accelerators-configuration.png)](media/v2-update-provision/iot-solution-accelerators-configuration.png#lightbox)

1. 预配完成后，将显示两个通知，宣布部署状态已从“正在预配”  变为“就绪”  。 

   >[!IMPORTANT]
   > 还不要转到解决方案加速器！ 请让此网页保持打开状态，因为稍后需要返回此处。

   [![设备模拟解决方案预配完成。](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. 现在，在 Azure 门户中检查新建的资源。 在“资源组”页上，可以看到使用最后一个步骤中提供的“解决方案名称”新建了一个资源组。   请记下已为设备模拟创建的资源。

   [![设备模拟资源。](media/v2-update-provision/tsi-device-sim-solution-resources.png)](media/v2-update-provision/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-a-preview-payg-environment"></a>创建预览版 PAYG 环境

本部分介绍如何创建 Azure 时序见解预览版环境，并使用 [Azure 门户](https://portal.azure.com/)将其连接到由 IoT 解决方案加速器创建的 IoT 中心。

1. 使用 Azure 订阅帐户登录到 [Azure 门户](https://portal.azure.com)。 
1. 选择左上角的“+ 创建资源”。  
1. 依次选择“物联网”类别、“时序见解”   。 

   [![选择时序见解环境资源。](media/v2-update-provision/tsi-create-new-environment.png)](media/v2-update-provision/tsi-create-new-environment.png#lightbox)

1. 在“创建时序见解环境”  窗格的“基本信息”  选项卡上，设置以下参数：

    | 参数 | 操作 |
    | --- | ---|
    | **环境名称** | 输入 Azure 时序见解预览版环境的唯一名称。 |
    | **订阅** | 输入想要在其中创建 Azure 时序见解预览版环境的订阅。 最佳做法是使用与设备模拟器创建的其他 IoT 资源相同的订阅。 |
    | **资源组** | 为 Azure 时序见解预览版环境资源选择现有的资源组或创建新的资源组。 资源组是 Azure 资源的容器。 最佳做法是使用与设备模拟器创建的其他 IoT 资源相同的资源组。 |
    | **位置** | 为 Azure 时序见解预览版环境选择数据中心区域。 为了避免额外的延迟，最好是在设备模拟器创建的 IoT 中心所在的区域中创建 Azure 时序见解预览版环境。 |
    | **层** |  选择“PAYG”（即用即付）。   这是 Azure 时序见解预览版产品的 SKU。 |
    | **属性名称** | 输入一个用于唯一标识时序实例的值。 以后不能更改在“属性 ID”框中输入的值  。 在本教程中，请输入 ***iothub-connection-device-id***。若要详细了解时序 ID，请阅读[选择时序 ID 的最佳做法](./time-series-insights-update-how-to-id.md)。 |
    | **存储帐户名称** | 为新存储帐户输入全局唯一名称。|
    |**启用 Warm 存储**|选择“是”  启用 Warm 存储。 可以稍后返回并启用此设置。 |
    |**日志保留期（以天为单位）**|选择默认选项 7 天。 |

    在完成时选择“下一步:**事件源”** 。

   [![新的时序见解环境配置。](media/v2-update-provision/tsi-environment-configuration.png)](media/v2-update-provision/tsi-environment-configuration.png#lightbox)

   [![配置环境的时序 ID。](media/v2-update-provision/tsi-time-series-id-selection.png)](media/v2-update-provision/tsi-time-series-id-selection.png#lightbox)

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
   | **IoT 中心使用者组** | 选择“新建”，输入唯一名称，然后选择“+ 添加”   。 在 Azure 时序见解预览版中，使用者组必须是唯一值。 |
   | **时间戳属性** | 此值用于标识传入遥测数据中的**时间戳**属性。 在本教程中，请将此框留空。 此模拟器使用 IoT 中心的传入时间戳，时序见解默认使用该时间戳。 |

   选择“查看 + 创建”  。

   [![将创建的 IoT 中心配置为事件源。](media/v2-update-provision/tsi-configure-event-source.png)](media/v2-update-provision/tsi-configure-event-source.png#lightbox)

1. 选择“创建”  。

    [![包含“创建”按钮的“查看 + 创建”页。](media/v2-update-provision/tsi-environment-confirmation.png)](media/v2-update-provision/tsi-environment-confirmation.png#lightbox)

    你可以查看部署的状态：

    [![指出部署已完成的通知。](media/v2-update-provision/tsi-deployment-notification.png)](media/v2-update-provision/tsi-deployment-notification.png#lightbox)

1. 如果你是 Azure 订阅的所有者，则默认情况下可以访问 Azure 时序见解预览版环境。 验证你是否具有访问权限：

   1. 搜索资源组，然后选择新创建的 Azure 时序见解预览版环境。 

      [![选择并查看环境。](media/v2-update-provision/verify-tsi-resource-in-group.png)](media/v2-update-provision/verify-tsi-resource-in-group.png#lightbox)

   1. 在“Azure 时序见解预览版”页上，选择“数据访问策略”  ：

      [![验证数据访问策略。](media/v2-update-provision/tsi-data-access-panel.png)](media/v2-update-provision/tsi-data-access-panel.png#lightbox)

   1. 验证凭据是否已列出：

      如果未列出你的凭据，则必须通过选择“添加”并搜索自己的凭据来授予自己访问该环境的权限。 若要详细了解如何设置权限，请参阅[授予数据访问权限](./time-series-insights-data-access.md)。

## <a name="stream-data"></a>流式传输数据

现已部署了时序见解环境，请将数据流式传输以进行分析。

1. 返回到[解决方案加速器仪表板](https://www.azureiotsolutions.com/Accelerators#dashboard)。 根据需要，使用本教程所用的同一 Azure 帐户重新登录。 选择“设备解决方案”，然后选择“转到解决方案加速器”  以启动部署的解决方案。

   [![解决方案加速器仪表板。](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. 设备模拟 Web 应用首先会提示你授予该 Web 应用程序“登录并读取你的个人资料”  的权限。 此权限使得应用程序能够检索所需的用户个人资料信息来支持运行应用程序。

   [![设备模拟 Web 应用程序许可。](media/v2-update-provision/sawa-signin-consent.png)](media/v2-update-provision/sawa-signin-consent.png#lightbox)

1. 选择“+ 新建模拟”  。 在“模拟设置”页加载后，输入所需的参数。 

    | 参数 | 操作 |
    | --- | --- |
    | **名称** | 为模拟器输入唯一名称。 |
    | **说明** | 输入定义。 |
    | **模拟持续时间** | 设置为“无限期运行”。  |
    | **设备型号** | 单击 +“添加设备类型”  <br />**Name**：输入“电梯”  。 <br />**数量**：输入 **3**。 <br /> 保留其余默认值 |
    | **目标 IoT 中心** | 设置为“使用预配的 IoT 中心”。  |

    [![配置参数和启动。](media/v2-update-provision/tsi-launch-solution-accelerator.png)](media/v2-update-provision/tsi-launch-solution-accelerator.png#lightbox)

    选择“开始模拟”  。

    在设备模拟仪表板中，将显示“活动设备数”和“总消息数”   。

    [![Azure IoT 模拟仪表板。](media/v2-update-provision/tsi-see-active-devices-and-messages.png)](media/v2-update-provision/tsi-see-active-devices-and-messages.png#lightbox)

## <a name="analyze-data"></a>分析数据

在此部分，使用 [Azure 时序见解预览资源管理器](./time-series-insights-update-explorer.md)对时序数据进行基本的分析。

1. 在 [Azure 门户](https://portal.azure.com/)中的资源页上选择相应的 URL，转到 Azure 时序见解预览版资源管理器。

    [![时序见解预览版资源管理器 URL。](media/v2-update-provision/tsi-select-explorer-url.png)](media/v2-update-provision/tsi-select-explorer-url.png#lightbox)

1. 在时序见解资源管理器中，将显示横跨屏幕顶部的一个栏。 这是可用性选取器。 确保至少选择了两个 2m，并在需要时通过选中选取器控点并将其向左和向右拖动来扩展期限。

1. **时序实例**将显示在左侧。

    [![无父级实例的列表。](media/v2-update-provision/tsi-explorer-unparented-instances.png)](media/v2-update-provision/tsi-explorer-unparented-instances.png#lightbox)

1. 选择第一个时序实例。 然后选择“显示温度”  。

    [![所选的时序实例，带有的菜单命令可用于显示平均温度。](media/v2-update-provision/select-instance-and-temperature.png)](media/v2-update-provision/select-instance-and-temperature.png#lightbox)

    此时会显示时序图。 将“间隔”更改为 30s   。

1. 重复上一步骤和其他两个时序实例，以便查看全部三个时序实例，如以下图表所示：

    [![所有时序的图表。](media/v2-update-provision/tsi-explorer-add-three-instances.png)](media/v2-update-provision/tsi-explorer-add-three-instances.png#lightbox)

1. 选择右上角的时间跨度选取器。 你可以在此处选择特定的开始时间和结束时间（最小可选择毫秒），或从预先配置的选项中进行选择（例如，近 30 分钟）  。 也可以更改默认时区。

    [![将时间范围设置为近 30 分钟。](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png)](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png#lightbox)

    解决方案加速器的“近 30 分钟”进度现在显示在时序见解资源管理器中  。

## <a name="define-and-apply-a-model"></a>定义并应用模型

在本部分，你将应用一个模型来构造数据。 若要完成该模型，需要定义类型、层次结构和实例。 若要详细了解数据建模，请阅读[时序模型](./time-series-insights-update-tsm.md)。

1. 在资源管理器中，选择“模型”  选项卡：

   [![查看资源管理器中的“模型”选项卡。](media/v2-update-provision/tsi-select-model-view.png)](media/v2-update-provision/tsi-select-model-view.png#lightbox)

   在“类型”选项卡中，选择“+ 添加”   。

1. 输入以下参数：

    | 参数 | 操作 |
    | --- | ---|
    | **名称** | 输入“电梯”  |
    | **说明** | 输入“这是电梯的类型定义”  |

1. 接下来，选择“变量”选项卡  。 

   选择“+ 添加变量”  并为电梯类型的第一个变量填写以下值。 总共创作三个变量。

    | 参数 | 操作 |
    | --- | --- |
    | **名称** | 输入“平均温度”。  |
    | **种类** | 选择“数值”  |
    | **值** | 从预设中选择：选择“温度(双精度型)”  。 <br /> 注意：在 Azure 时序见解预览版开始接收事件之后，可能需要等待几分钟才会自动填充“值”。 |
    | **聚合操作** | 展开“高级选项”  。 <br /> 选择“AVG”。  |

    选择“应用”。  然后，再次选择“+ 添加变量”  ，并设置以下值：

    | 参数 | 操作 |
    | --- | --- |
    | **名称** | 输入“平均振动”  。 |
    | **种类** | 选择“数值”  |
    | **值** | 从预设中选择：选择“振动(双精度)”  。 <br /> 注意：在 Azure 时序见解预览版开始接收事件之后，可能需要等待几分钟才会自动填充“值”。 |
    | **聚合操作** | 展开“高级选项”  。 <br /> 选择“AVG”。  |

    选择“应用”。  然后，再次选择“+ 添加变量”，并为第三个和最后一个变量设置以下值  ：

    | 参数 | 操作 |
    | --- | --- |
    | **名称** | 输入“楼层”  。 |
    | **种类** | 选择“分类”  |
    | **值** | 从预设中选择：选择“楼层(双精度)”  。 <br /> 注意：在 Azure 时序见解预览版开始接收事件之后，可能需要等待几分钟才会自动填充“值”。 |
    | **类别** | <span style="text-decoration: underline">标记 </span>  - <span style="text-decoration: underline">值</span> <br /> 较低：1、2、3、4 <br /> 中层：5、6、7、8、9 <br /> 较高：10、11、12、13、14、15 |
    | **默认类别** | 输入“未知”  |

    [![添加类型变量。](media/v2-update-provision/tsi-add-type-variables.png)](media/v2-update-provision/tsi-add-type-variables.png#lightbox)

    选择“应用”。 

1. 选择“保存”。  此时将创建并显示三个变量。

    [![添加类型后，可在“模型”视图中查看它。](media/v2-update-provision/tsi-add-type-and-view.png)](media/v2-update-provision/tsi-add-type-and-view.png#lightbox)

1. 选择“层次结构”  选项卡。然后选择“+ 添加”  。
   
   在“编辑层次结构”  窗格中，设置以下参数：

   | 参数 | 操作 |
   | --- | ---|
   | **名称** | 输入“位置层次结构”。  |
   |**级别**| 输入“国家/地区”  作为第一级的名称 <br> 选择“+ 添加级别”  <br> 输入“城市”作为第二级  ，然后选择“+ 添加级别”  <br> 输入“建筑”  作为第三级和最后一个级别的名称 |

   选择“保存”。 

   [![在“模型”视图中显示新的层次结构。](media/v2-update-provision/tsi-add-hierarchy-and-view.png)](media/v2-update-provision/tsi-add-hierarchy-and-view.png#lightbox)

1. 导航到“实例”  。 在最右侧的“操作”  下，选择铅笔图标以编辑具有以下值的第一个实例：

    | 参数 | 操作 |
    | --- | --- |
    | 类型  | 选择“电梯”  。 |
    | **名称** | 输入“电梯 1” |
    | **说明** | 输入“电梯 1 的实例”  |

    导航到“实例字段”并输入以下值  ：

    | 参数 | 操作 |
    | --- | --- |
    | **层次结构** | 选择“位置层次结构”  |
    | **国家/地区** | 输入“美国”  |
    | **城市** | 输入“西雅图”  |
    | **建筑物** | 输入“太空针塔”  |

    选择“保存”。 

1. 使用以下值重复上一步骤和其他两个实例：

    **对于电梯 2：**

    | 参数 | 操作 |
    | --- | --- |
    | 类型  | 选择“电梯”  。 |
    | **名称** | 输入“电梯 2” |
    | **说明** | 输入“电梯 2 的实例”  |
    | **层次结构** | 选择“位置层次结构”  |
    | **国家/地区** | 输入“美国”  |
    | **城市** | 输入“西雅图”  |
    | **建筑物** | 输入“太平洋科学中心”  |

    **对于电梯 3：**

    | 参数 | 操作 |
    | --- | --- |
    | 类型  | 选择“电梯”  。 |
    | **名称** | 输入“电梯 3” |
    | **说明** | 输入“电梯 3 的实例”  |
    | **层次结构** | 选择“位置层次结构”  |
    | **国家/地区** | 输入“美国”  |
    | **城市** | 输入“纽约”  |
    | **建筑物** | 输入“帝国大厦”  |

    [![查看已更新的实例。](media/v2-update-provision/iot-solution-accelerator-instances.png)](media/v2-update-provision/iot-solution-accelerator-instances.png#lightbox)

1. 导航回到“分析”  选项卡以查看图表窗格。 在“位置层次结构”下展开所有层次结构级别，显示时序实例： 

    [![在图表视图中查看所有层次结构。](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png)](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png#lightbox)

1. 在“太平洋科学中心”下选择时序实例“电梯 2”，然后选择“显示平均温度”    。

1. 对于同一实例“电梯 2”  ，选择“显示楼层”  。

    借助分类变量，可以确定电梯在较高、较低和中层楼层耗费的时间。

    [![使用层次结构和数据可视化电梯 2。](media/v2-update-provision/iot-solution-accelerator-elevator-two.png)](media/v2-update-provision/iot-solution-accelerator-elevator-two.png#lightbox)

## <a name="clean-up-resources"></a>清理资源

现在你已完成本教程，请清除已创建的资源：

1. 从 [Azure 门户](https://portal.azure.com)的左侧菜单中，选择“所有资源”  ，找到“Azure 时序见解”资源组。
1. 通过选择“删除”  来删除整个资源组（以及其中包含的所有资源），或者单独删除每个资源。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：  

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
