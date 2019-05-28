---
title: 教程：设置 Azure 时序见解预览版环境 | Microsoft Docs
description: 了解如何在 Azure 时序见解预览中设置环境。
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 04/25/2019
ms.custom: seodec18
ms.openlocfilehash: f83063a88207f51f9d481447923fd8a8498692a2
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2019
ms.locfileid: "64713912"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>教程：设置 Azure 时序见解预览环境

本教程将逐步引导你创建一个 Azure 时序见解预览版即付即用 (PAYG) 环境。 本教程介绍如何执行下列操作：

* 创建 Azure 时序见解预览环境。
* 将 Azure 时序见解预览环境连接到“Azure 事件中心”中的事件中心。
* 运行解决方案加速器示例，以将数据流式传输到 Azure 时序见解预览版环境。
* 对数据进行基本的分析。
* 定义时序模型类型和层次结构，并将其与实例相关联。

# <a name="create-a-device-simulation"></a>创建设备模拟

在本部分，你将创建三个模拟设备，用于将数据发送到 IoT 中心。

1. 转到 [Azure IoT 解决方案加速器页](https://www.azureiotsolutions.com/Accelerators)。 该页显示了多个预生成的示例。 使用 Azure 帐户登录。 然后，选择“设备模拟”  。

   [![Azure IoT 解决方案加速器页](media/v2-update-provision/device-one-accelerator.png)](media/v2-update-provision/device-one-accelerator.png#lightbox)

   选择“立即试用”。 

1. 在“创建设备模拟解决方案”页上输入所需的参数： 

   | 参数 | 说明 |
   | --- | --- |
   | **解决方案名称** |    输入用于创建新资源组的唯一值。 将会创建列出的 Azure 资源并将其分配到资源组。 |
   | **订阅** | 指定用于创建时序见解环境的同一订阅。 |
   | **区域** |   指定用于创建时序见解环境的同一区域。 |
   | **部署可选 Azure 资源**    | 将“IoT 中心”保留选中状态，因为模拟设备将使用它来建立连接和流式传输数据。 |

   然后选择“创建解决方案”  。 等待 10-15 分钟，让解决方案部署完成。

   [![“创建设备模拟解决方案”页](media/v2-update-provision/device-two-create.png)](media/v2-update-provision/device-two-create.png#lightbox)

1. 在解决方案加速器仪表板中，选择“启动”按钮  ：

   [![启动设备模拟解决方案](media/v2-update-provision/device-three-launch.png)](media/v2-update-provision/device-three-launch.png#lightbox)

1. 随后你将重定向到“Microsoft Azure IoT 设备模拟”页  。 在页面的右上方选择“+ 新建模拟”。 

   [![Azure IoT 模拟页](media/v2-update-provision/device-four-iot-sim-page.png)](media/v2-update-provision/device-four-iot-sim-page.png#lightbox)

1. 填写所需参数，如下所示：

    [![要填写的参数](media/v2-update-provision/device-five-params.png)](media/v2-update-provision/device-five-params.png#lightbox)

    |||
    | --- | --- |
    | **名称** | 为模拟器输入唯一名称。 |
    | **说明** | 输入定义。 |
    | **模拟持续时间** | 设置为“无限期运行”。  |
    | **设备型号** | **名称**：输入 `Chiller` 。 </br>**数量**：输入 `3` 。 |
    | **目标 IoT 中心** | 设置为“使用预配的 IoT 中心”。  |

    然后选择“开始模拟”  。

1. 在设备模拟仪表板中，查看“活动设备数”和“每秒消息数”   。

    [![Azure IoT 模拟仪表板](media/v2-update-provision/device-seven-dashboard.png)](media/v2-update-provision/device-seven-dashboard.png#lightbox)

## <a name="list-device-simulation-properties"></a>列出设备模拟属性

在创建 Azure 时序见解环境之前，需要获取 IoT 中心、订阅和资源组的名称。

1. 转到解决方案加速器仪表板，使用同一 Azure 订阅帐户登录。 找到在上一步骤中创建的设备模拟。

1. 选择你的设备模拟器，然后选择“启动”  。 在右侧选择“Azure 管理门户”链接。 

    [![模拟器列表](media/v2-update-provision/device-six-listings.png)](media/v2-update-provision/device-six-listings.png#lightbox)

1. 记下 IoT 中心、订阅和资源组的名称。

    [![Azure 门户](media/v2-update-provision/device-eight-portal.png)](media/v2-update-provision/device-eight-portal.png#lightbox)

## <a name="create-a-time-series-insights-preview-payg-environment"></a>创建时序见解预览 PAYG 环境

此部分介绍如何使用 [Azure 门户](https://portal.azure.com/)创建 Azure 时序见解预览环境。

1. 使用 Azure 订阅帐户登录到 Azure 门户。

1. 选择“创建资源”。 

1. 依次选择“物联网”类别、“时序见解”   。

   [![依次选择“物联网”、“时序见解”](media/v2-update-provision/payg-one-azure.png)](media/v2-update-provision/payg-one-azure.png#lightbox)

1. 按如下所示填写页面上的字段：

   | | |
   | --- | ---|
   | **环境名称** | 为 Azure 时序见解预览环境选择唯一名称。 |
   | **订阅** | 输入想要在其中创建 Azure 时序见解预览环境的订阅。 最佳做法是使用与设备模拟器创建的其他 IoT 资源相同的订阅。 |
   | **资源组** | 资源组是 Azure 资源的容器。 为 Azure 时序见解环境资源选择现有的资源组或创建新的资源组。 最佳做法是使用与设备模拟器创建的其他 IoT 资源相同的资源组。 |
   | **位置** | 为 Azure 时序见解预览版环境选择数据中心区域。 为了避免带宽成本和延迟提高，最好是将 Azure 时序见解环境与其他 IoT 资源保留在同一区域。 |
   | **层** |  选择“PAYG”（表示即用即付）。  这是 Azure 时序见解预览版产品的 SKU。 |
   | **属性 ID** | 输入用于唯一标识时序的内容。 请注意，此字段不可变，以后不可更改。 在本教程中，使用 `iothub-connection-device-id`。 若要详细了解时序 ID，请阅读[如何选择时序 ID](./time-series-insights-update-how-to-id.md)。 |
   | **存储帐户名称** | 为要创建的新存储帐户输入全局唯一名称。 |

   然后选择“下一步:**事件源”  。

   [![用于创建时序见解环境的页面](media/v2-update-provision/payg-two-create.png)](media/v2-update-provision/payg-two-create.png#lightbox)

1. 在事件源的页面上，按如下所示填写字段：

   | | |
   | --- | --- |
   | **创建事件源?** | 输入 `Yes` 。|
   | **名称** | 输入用于命名事件源的唯一值。|
   | **源类型** | 选择“IoT 中心”  。 |
   | **选择中心?** | 选择“选择现有项”  。 |
   | **订阅** | 选择用于设备模拟器的订阅。 |
   | **IoT 中心名称** | 选择为设备模拟器创建的 IoT 中心名称。 |
   | **Iot 中心访问策略** | 选择“iothubowner”。  |
   | **IoT 中心使用者组** | 需要为 Azure 时序见解预览版提供唯一的使用者组。 选择“新建”，输入唯一名称，然后选择“添加”。   |
   | **时间戳属性** | 此字段用于标识传入遥测数据中的时间戳属性。 对于本教程，无需填写该字段。 此模拟器使用 IoT 中心的传入时间戳，时序见解默认使用该时间戳。|

   然后选择“复查 + 创建”  。

   [![配置事件源](media/v2-update-provision/payg-five-event-source.png)](media/v2-update-provision/payg-five-event-source.png#lightbox)

1. 检查复查页上的所有字段，然后选择“创建”  。

   [![包含“创建”按钮的“查看 + 创建”页](media/v2-update-provision/payg-six-review.png)](media/v2-update-provision/payg-six-review.png#lightbox)

1. 可以看到部署状态。

   [![指出部署已完成的通知](media/v2-update-provision/payg-seven-deploy.png)](media/v2-update-provision/payg-seven-deploy.png#lightbox)

1. 如果你是租户所有者，应可访问自己的 Azure 时序见解预览版环境。 确保具有以下访问权限：

   a. 搜索资源组，并选择自己的 Azure 时序见解预览版环境：

      [![所选环境](media/v2-update-provision/payg-eight-environment.png)](media/v2-update-provision/payg-eight-environment.png#lightbox)

   b. 在“Azure 时序见解预览版”页上，转到“数据访问策略”  。

     [![数据访问策略](media/v2-update-provision/payg-nine-data-access.png)](media/v2-update-provision/payg-nine-data-access.png#lightbox)

   c. 验证列出的凭据。

     [![列出的凭据](media/v2-update-provision/payg-ten-verify.png)](media/v2-update-provision/payg-ten-verify.png#lightbox)

   如果未列出你的凭据，则必须授予自己访问该环境的权限。 若要详细了解如何设置权限，请参阅[授予数据访问权限](./time-series-insights-data-access.md)。

## <a name="analyze-data-in-your-environment"></a>在环境中分析数据

在此部分，使用 [Azure 时序见解预览资源管理器](./time-series-insights-update-explorer.md)对时序数据进行基本的分析。

1. 在 [Azure 门户](https://portal.azure.com/)中的资源页上选择相应的 URL，转到 Azure 时序见解预览版资源管理器。

   [![时序见解预览版资源管理器 URL](media/v2-update-provision/analyze-one-portal.png)](media/v2-update-provision/analyze-one-portal.png#lightbox)

1. 在资源管理器中，选择“时序见解实例”节点，以查看环境中的所有 Azure 时序见解预览版实例  。

   [![无父级实例的列表](media/v2-update-provision/analyze-two-unparented.png)](media/v2-update-provision/analyze-two-unparented.png#lightbox)

1. 在显示的时序中选择第一个实例。 然后选择“显示平均压力”。 

   [![所选的带有用于显示平均压力的菜单命令的实例](media/v2-update-provision/analyze-three-show-pressure.png)](media/v2-update-provision/analyze-three-show-pressure.png#lightbox)

   时序图应显示在右侧。 将“间隔”调整为 `15s`  。

   [![时序图](media/v2-update-provision/analyze-four-chart.png)](media/v2-update-provision/analyze-four-chart.png#lightbox)

1. 使用另外两个时序重复“步骤 3”  。 然后可以查看所有时序，在以下图表中所示：

   [![所有时序的图表](media/v2-update-provision/analyze-five-chart.png)](media/v2-update-provision/analyze-five-chart.png#lightbox)

1. 修改时间范围以查看过去一小时的时序趋势。

   a. 选择“期限”选项框  ：

      [![将时间范围设置为小时](media/v2-update-provision/analyze-six-time.png)](media/v2-update-provision/analyze-six-time.png#lightbox)

## <a name="define-and-apply-a-model"></a>定义并应用模型

在本部分，你将应用一个模型来构造数据。 若要完成该模型，需要定义类型、层次结构和实例。 若要详细了解数据建模，请转到[时序模型](./time-series-insights-update-tsm.md)。

1. 在资源管理器中，选择“模型”  选项卡：

   [![资源管理器中的“模型”选项卡](media/v2-update-provision/define-one-model.png)](media/v2-update-provision/define-one-model.png#lightbox)

1. 选择“+ 添加”以添加类型。  右侧将会打开一个类型编辑器。

   [![类型的“添加”按钮](media/v2-update-provision/define-two-add.png)](media/v2-update-provision/define-two-add.png#lightbox)

1. 为类型定义三个变量：压力、温度和湿度。 输入以下信息：

   | | |
   | --- | ---|
   | **名称** | 输入 `Chiller` 。 |
   | **说明** | 输入 `This is a type definition of Chiller` 。 |

   * 使用三个变量定义压力：

      | | |
      | --- | ---|
      | **名称** | 输入 `Avg Pressure` 。 |
      | **值** | 选择“压力(双精度型)”  。 请注意，在 Azure 时序见解预览版开始接收事件之后，可能需要等待几分钟才能填充此字段。 |
      | **聚合操作** | 选择“AVG”。  |

      [![用于定义压力的选项](media/v2-update-provision/define-three-variable.png)](media/v2-update-provision/define-three-variable.png#lightbox)

      单击“+ 添加变量”以添加下一个变量  。

   * 定义温度：

      | | |
      | --- | ---|
      | **名称** | 输入 `Avg Temperature` 。 |
      | **值** | 选择“温度(双精度型)”  。 请注意，在 Azure 时序见解预览版开始接收事件之后，可能需要等待几分钟才能填充此字段。 |
      | **聚合操作** | 选择“AVG”。 |

      [![用于定义温度的选项](media/v2-update-provision/define-four-avg.png)](media/v2-update-provision/define-four-avg.png#lightbox)

   * 定义湿度：

      | | |
      | --- | ---|
      | **名称** | 输入 `Max Humidity` |
      | **值** | 选择“湿度(双精度型)”  。 请注意，在 Azure 时序见解预览版开始接收事件之后，可能需要等待几分钟才能填充此字段。 |
      | **聚合操作** | 选择“MAX”。 |

      [![用于定义温度的选项](media/v2-update-provision/define-five-humidity.png)](media/v2-update-provision/define-five-humidity.png#lightbox)

   然后选择“创建”  。

1. 可以看到添加的类型：

   [![有关添加类型的信息](media/v2-update-provision/define-six-type.png)](media/v2-update-provision/define-six-type.png#lightbox)

1. 下一步是添加层次结构。 在“层次结构”部分，选择“+ 添加”：  

   [![包含“添加”按钮的“层次结构”选项卡](media/v2-update-provision/define-seven-hierarchy.png)](media/v2-update-provision/define-seven-hierarchy.png#lightbox)

1. 定义层次结构。 按如下所示填写字段：

   | | |
   | --- | ---|
   | **名称** | 输入 `Location Hierarchy` 。 |
   | **级别 1** | 输入 `Country` 。 |
   | **级别 2** | 输入 `City` 。 |
   | **级别 3** | 输入 `Building` 。 |

   然后选择“创建”  。

   [![包含“创建”按钮的“层次结构”字段](media/v2-update-provision/define-eight-add-hierarchy.png)](media/v2-update-provision/define-eight-add-hierarchy.png#lightbox)

1. 可以看到创建的层次结构：

   [![有关层次结构的信息](media/v2-update-provision/define-nine-created.png)](media/v2-update-provision/define-nine-created.png#lightbox)

1. 在左侧选择“实例”。  实例显示后，选择第一个实例，然后选择“编辑”  ：

   [![选择实例对应的“编辑”按钮](media/v2-update-provision/define-ten-edit.png)](media/v2-update-provision/define-ten-edit.png#lightbox)

1. 随后会在右侧打开一个文本编辑器。 添加以下信息：

   | | |
   | --- | --- |
   | 类型  | 选择“冷却器”。  |
   | **说明** | 输入 `Instance for Chiller-01.1` 。 |
   | **层次结构** | 选择“位置层次结构”  。 |
   | **国家/地区** | 输入 `USA` 。 |
   | **城市** | 输入 `Seattle` 。 |
   | **建筑物** | 输入 `Space Needle` 。 |

    然后选择“保存”  。

   [![包含“保存”按钮的“实例”字段](media/v2-update-provision/define-eleven-chiller.png)](media/v2-update-provision/define-eleven-chiller.png#lightbox)

1. 针对其他传感器重复上面的步骤。 使用以下字段：

   * 对于冷却器 01.2：

     | | |
     | --- | --- |
     | 类型  | 选择“冷却器”。  |
     | **说明** | 输入 `Instance for Chiller-01.2` 。 |
     | **层次结构** | 选择“位置层次结构”  。 |
     | **国家/地区** | 输入 `USA` 。 |
     | **城市** | 输入 `Seattle` 。 |
     | **建筑物** | 输入 `Pacific Science Center` 。 |

   * 对于冷却器 01.3：

     | | |
     | --- | --- |
     | 类型  | 选择“冷却器”。  |
     | **说明** | 输入 `Instance for Chiller-01.3` 。 |
     | **层次结构** | 选择“位置层次结构”  。 |
     | **国家/地区** | 输入 `USA` 。 |
     | **城市** | 输入 `New York` 。 |
     | **建筑物** | 输入 `Empire State Building` 。 |

1. 转到“分析”选项卡并刷新页面  。 展开所有层次结构级别，以查找时序。

   [![“分析”选项卡](media/v2-update-provision/define-twelve.png)](media/v2-update-provision/define-twelve.png#lightbox)

1. 若要浏览过去一小时的时序，请将“QuickTime”更改为“过去一小时”：  

    [![“QuickTime”框，其中已选择“过去一小时”](media/v2-update-provision/define-thirteen-explore.png)](media/v2-update-provision/define-thirteen-explore.png#lightbox)

1. 选择“太平洋科学馆”下的时序，然后选择“显示最大湿度”   。

    [![选择的时序，以及“显示最大湿度”菜单选项](media/v2-update-provision/define-fourteen-show-max.png)](media/v2-update-provision/define-fourteen-show-max.png#lightbox)

1. 此时会打开间隔大小为 1 分钟的“最大湿度”时序   。 选择某个区域以筛选范围。 然后，单击右键并选择“缩放”，以分析该时间范围内的事件： 

   [![选择的范围，以及快捷菜单上的“缩放”命令](media/v2-update-provision/define-fifteen-filter.png)](media/v2-update-provision/define-fifteen-filter.png#lightbox)

1. 还可以选择某个区域并单击右键来查看事件详细信息：

   [![事件的详细列表](media/v2-update-provision/define-eighteen.png)](media/v2-update-provision/define-eighteen.png#lightbox)

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：  

> [!div class="checklist"]
> * 创建并使用设备模拟加速器。
> * 创建 Azure 时序见解预览 PAYG 环境。
> * 将 Azure 时序见解预览环境连接到事件中心。
> * 运行解决方案加速器示例，以将数据流式传输到 Azure 时序见解预览版环境。
> * 对数据进行基本的分析。
> * 定义时序模型类型和层次结构，并将其与实例相关联。

知道如何创建自己的 Azure 时序见解预览环境后，现在来了解更多关于 Azure 时序见解的关键概念。

了解 Azure 时序见解存储配置：

> [!div class="nextstepaction"]
> [Azure 时序见解预览版存储和入口](./time-series-insights-update-storage-ingress.md)

详细了解时序模型：

> [!div class="nextstepaction"]
> [Azure 时序见解预览数据模型](./time-series-insights-update-tsm.md)