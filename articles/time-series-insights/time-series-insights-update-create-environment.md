---
title: Azure 时序见解预览设置 - 设置 Azure 时序见解预览环境教程 | Microsoft Docs
description: 了解如何在 Azure 时序见解预览中设置环境。
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 12/12/2018
ms.custom: seodec18
ms.openlocfilehash: 9ad957d6378b1279f1ca51939eb4802b0ce7d78f
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2018
ms.locfileid: "53322612"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-preview-environment"></a>教程：设置 Azure 时序见解预览环境

本教程指导创建 Azure 时序见解即付即用 (PAYG) 预览环境。 本教程介绍如何执行下列操作：

* 创建 Azure 时序见解预览环境。
* 将 Azure 时序见解预览环境连接到“Azure 事件中心”中的事件中心。
* 运行风力发电厂模拟以将数据流式传输到 Azure 时序见解预览环境。
* 对数据进行基本的分析。
* 定义时序模型类型和层次结构，并将其与实例相关联。

# <a name="create-a-device-simulation"></a>创建设备模拟

在本部分中，将创建三个模拟设备，用于将数据发送到 IoT 中心。

1. 转到 [Azure IoT 解决方案加速器主页](https://www.azureiotsolutions.com/Accelerators)。 Azure IoT 解决方案加速器主页显示几个预先生成的示例。 使用 Azure 帐户登录。 然后，选择“设备模拟”。

   ![Azure IoT 解决方案加速器主页][1]

   最后，单击“立即试用”。

1. 在“创建设备模拟”解决方案页上输入所需的参数：

   | 参数 | Description |
   | --- | --- |
   | 解决方案名称 |    用于创建新资源组的唯一值。 列出的 Azure 资源会 | 在创建后分配给资源组。 |
   | 订阅 | 指定用于创建 TSI 环境的同一订阅 |
   | 区域 |   指定用于创建 TSI 的同一区域。 |
   | 部署可选 Azure 资源    | 将“IoT 中心”保留选中状态，因为模拟设备将使用它来建立连接/流式传输数据。 |

   输入所需参数后，单击“创建解决方案”。 部署解决方案所需时间大约为 10-15 分钟。

   ![创建设备模拟解决方案][2]

1. 在“解决方案加速器仪表板”中，单击“启动”按钮：

   ![启动设备模拟解决方案][3]

1. 你将被重定向到“Microsoft Azure IoT 设备模拟”页面。 单击屏幕右上角的“+ 新建模拟”。

   ![Azure IoT 模拟页][4]

1.  填写所需参数，如下所示：

    ![要填写的参数][5]

    |||
    | --- | --- |
    | **名称** | 为模拟器输入唯一名称 |
    | **说明** | 输入定义 |
    | **模拟持续时间** | 设置为 `Run indefinitely` |
    | **设备型号** | **名称**：输入 `Chiller` 数量：输入 `3` |
    | **目标 IoT 中心** | 设置为 `Use pre-provisioned IoT Hub` |

    填写所需参数后，单击“开始模拟”。

1. 在设备模拟仪表板中，可以看到“活动设备”和“每秒消息数”。

    ![Azure IoT 模拟仪表板][6]

## <a name="list-device-simulation-properties"></a>列出设备模拟属性

需要获取 IoT 中心、订阅和资源组的名称，再创建 Azure 时序见解环境。

1. 转到“解决方案加速器仪表板”，使用同一 Azure 订阅帐户登录。 找到在上一步骤中创建的设备模拟。

1. 单击设备模拟器，然后单击“启动”。 单击显示在右侧的“Azure 管理门户”链接。

    ![模拟器列表][7]

1. 记下 IoT 中心、订阅和资源组的名称。

    ![Azure 门户][8]

## <a name="create-a-time-series-insights-preview-payg-environment"></a>创建时序见解预览 PAYG 环境

此部分介绍如何使用 [Azure 门户](https://portal.azure.com/)创建 Azure 时序见解预览环境。

1. 使用 Azure 订阅帐户登录到 Azure 门户。

1. 选择“创建资源”。

1. 依次选择“物联网”类别、“时序见解”。

   ![依次选择“创建资源”、“物联网”和“时序见解”][9]

1. 填写页面上的字段，如下所示：

   | | |
   | --- | ---|
   | **环境名称** | 为 Azure 时序见解预览环境选择唯一名称。 |
   | **订阅** | 输入想要在其中创建 Azure 时序见解预览环境的订阅。 最佳做法是使用与设备模拟器创建的其他 IoT 资源相同的订阅。 |
   | **资源组** | 资源组是 Azure 资源的容器。 为 Azure 时序见解环境资源选择现有的资源组或创建新的资源组。 最佳做法是使用与设备模拟器创建的其他 IoT 资源相同的资源组。 |
   | **位置** | 为 Azure 时序见解预览环境选择数据中心区域。 为了避免带宽成本和延迟提高，最好是将 Azure 时序见解环境与其他 IoT 资源保留在同一区域。 |
   | **层** |  选择表示即用即付的 `PAYG`。 这是 Azure 时序见解预览产品的 SKU。 |
   | **属性 ID** | 唯一地标识你的时序。 注意，该字段是不可变的，且以后不能更改。 对于本教程，将字段设置为 `iothub-connection-device-id`。 要了解有关时序 ID 的详细信息，请阅读[如何选择时序 ID](./time-series-insights-update-how-to-id.md)。 |
   | **存储帐户名称** | 为要创建的新存储帐户输入全局唯一名称。 |

   填写上述字段后，请单击“下一步:事件源”。

   ![单击“下一步:事件源”][10]

1. 填写该页上的所有字段，如下所示：

   | | |
   | --- | --- |
   | **是否创建事件源？** | 输入 `Yes`|
   | **名称** | 必须输入用于命名事件源的唯一值。|
   | **源类型** | 输入 `IoT Hub` |
   | **选择中心？** | 输入 `Select Existing` |
   | **订阅** | 输入用于设备模拟器的订阅。 |
   | **IoT 中心名称** | 输入为设备模拟器创建的 IoT 中心名称。 |
   | **Iot 中心访问策略** | 输入 `iothubowner` |
   | **IoT 中心使用者组** | 提供唯一的使用者组，以用于 Azure 时序见解预览。 |
   | **Timestamp** | 此字段用于标识传入遥测数据中的时间戳属性。 对于本教程，请勿填写该字段。 此模拟器使用来自 IoT Hub 的传入时间戳，时序见解默认使用该时间戳。|

   创建唯一使用者组：

   1. 单击“IoT 中心使用者组”字段旁边的“新建”：

      ![单击“下一步:事件源”][11]

   1. 为使用者组提供唯一的名称，然后单击“添加”：

      ![单击“添加”][12]

   填写上述字段后，单击“查看 + 创建”。

      ![查看并创建][13]

1. 查看该页面中的所有字段，然后单击“创建”。

   ![创建][14]

1. 可以看到部署状态。

   ![部署完毕][15]

1. 如果你是租户所有者，则应可以访问时序环境。 确保具有以下访问权限：

   * 导航到新建的 Azure 时序见解预览环境。 可以通过搜索资源组执行此操作。 然后，单击时序环境：

      ![部署完毕][16]

   * 在“Azure 时序见解预览”页上，导航到“数据访问策略”。

     ![数据访问策略][17]

   * 验证列出的凭据。

     ![验证凭据][18]

   如果未列出凭据，将必须授予自己访问该环境的权限。 请阅读[授予数据访问权](./time-series-insights-data-access.md)，了解有关设置权限的详细信息。

## <a name="analyze-data-in-your-environment"></a>在环境中分析数据

在此部分，使用 [Azure 时序见解预览资源管理器](./time-series-insights-update-explorer.md)对时序数据进行基本的分析。

1. 转到 Azure 时序见解预览资源管理器，方法是单击 [Azure 门户](https://portal.azure.com/)中资源页中的 URL。

   ![时序见解资源管理器 URL][19]

1. 在资源管理器中，选择“无父级实例”节点，查看环境中的所有 Azure 时序见解预览。

   ![无父级实例的列表][20]

1. 在所示时序中，单击第一个实例。 然后，单击“显示平均压力”。

   ![显示平均压力][21]

1. 时序图应显示在右侧：

   ![时序图][22]

1. 使用另外两个时序重复“步骤 3”。 然后，可以查看所有时序，如下所示：

   ![所有时序图][23]

1. 修改“时间范围”以查看过去一小时内的时序趋势。 选择 **From** 选项框，如下所示：

   ![选择“开始时间”选项][24]

1. 更改  选项框内的时间以显示过去一小时中的事件：

   ![选择“开始时间”选项][25]

1. 然后可以比较过去一个小时中三个设备的压力：

   ![选择“开始时间”选项][26]

## <a name="define-and-apply-a-model"></a>定义并应用模型

在本节中，将应用模型来构造数据。 要完成该模型，将定义类型、层次结构和实例。 要了解有关数据建模的详细信息，请转到[时序模型](./time-series-insights-update-tsm.md)。

1. 在资源管理器中，选择“模型”选项卡：

   ![选择“模型”选项卡][27]

1. 接下来，单击“+ 添加”添加类型。 将在右侧打开类型编辑器。

   ![单击“添加”][28]

1. 下一步，定义三个变量：类型中的压力、温度和湿度。 输入以下字段：

   | | |
   | --- | ---|
   | **名称** | 输入 `Chiller` |
   | **说明** | 输入 `This is a type definition of Chiller` |

   * 现在，使用三个变量定义压力：

      | | |
      | --- | ---|
      | **名称** | 输入 `Avg Pressure` |
      | **值** | 选择“压力(双精度型)”。 注意，Azure 时序见解开始接收事件之后，填充该字段可能需要几分钟 |
      | **聚合操作** | 选择 `AVG` |

      ![添加变量][29]

      单击“+ 变量”添加下一个变量。

   * 现在，定义温度：

      | | |
      | --- | ---|
      | **名称** | 输入 `Avg Temperature` |
      | **值** | 选择“温度(双精度型)”。 注意，Azure 时序见解开始接收事件之后，填充该字段可能需要几分钟 |
      | **聚合操作** | 选择 `AVG`|

      ![定义温度][30]

   * 现在，定义湿度：

      | | |
      | --- | ---|
      | **名称** | 输入 `Max Humidity` |
      | **值** | 选择“湿度(双精度型)”。 注意，Azure 时序见解开始接收事件之后，填充该字段可能需要几分钟 |
      | **聚合操作** | 选择 `MAX`|

      ![定义温度][31]

   定义变量后，单击“创建”。

1. 可以看到所添加的类型：

   ![查看所添加的类型][32]

1. 下一步是添加层次结构。 在“层次结构”部分，选择“+ 添加”，创建新的层次结构：

   ![添加层次结构][33]

1. 定义层次结构。 填写字段，如下所示：

   | | |
   | --- | ---|
   | **名称** | 输入 `Location Hierarchy` |
   | **级别 1** | 输入 `Country` |
   | **级别 2** | 输入 `City` |
   | **级别 3** | 输入 `Building` |

   填写上述字段后，请单击“创建”。

   ![定义层次结构][34]

1. 可以看到所创层次结构：

   ![查看层次结构][35]

1. 定义层次结构之后，单击左侧的“实例”。 实例显示后，单击第一个实例并选择“编辑”：

   ![添加实例][36]

1. 将在右侧打开文本编辑器。 添加以下字段：

   | | |
   | --- | --- |
   | 类型 | 选择 `Chiller` |
   | **说明** | 输入 `Instance for Chiller-01.1` |
   | **层次结构** | 启用 `Location Hierarchy` |
   | **国家/地区** | 输入 `USA` |
   | **城市** | 输入 `Seattle` |
   | **建筑物** | 输入 `Space Needle` |

    填写上述字段后，请单击“保存”。

   ![保存冷却器][37]

1. 针对其他传感器重复上面的步骤。 使用以下字段：

   * 对于冷却器 01.2：

     | | |
     | --- | --- |
     | 类型 | 选择 `Chiller` |
     | **说明** | 输入 `Instance for Chiller-01.2` |
     | **层次结构** | 启用 `Location Hierarchy` |
     | **国家/地区** | 输入 `USA` |
     | **城市** | 输入 `Seattle` |
     | **建筑物** | 输入 `Pacific Science Center` |

   * 对于冷却器 01.3：

     | | |
     | --- | --- |
     | 类型 | 选择 `Chiller` |
     | **说明** | 输入 `Instance for Chiller-01.1` |
     | **层次结构** | 启用 `Location Hierarchy` |
     | **国家/地区** | 输入 `USA` |
     | **城市** | 输入 `New York` |
     | **建筑物** | 输入 `Empire State Building` |

1. 转到“分析”选项卡并刷新页面。 展开所有层次结构级别，以查找时序。

   ![查看分析选项卡][38]

1. 要浏览过去一小时内的时序，请将 QuickTime 更改为“过去一小时”：

   ![浏览过去一小时][39]

1. 单击“太平洋科学中心”下的时序，再单击“显示最大湿度”。

   ![显示最大湿度][40]

1. 将打开间隔大小为 1 分钟的“最大湿度”时序。 单击某个区域，筛选范围。 然后，右键单击并缩放以分析时间帧内的事件：

   ![查看、筛选和缩放][41]

   ![查看、筛选和缩放][42]

1. 还可以单击某个区域，然后右键单击以查看事件细节：

   ![查看、筛选和缩放][43]

   ![查看、筛选和缩放][44]

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：  

* 创建并使用设备模拟加速器。
* 创建 Azure 时序见解预览 PAYG 环境。
* 将 Azure 时序见解预览环境连接到事件中心。
* 运行风力发电厂模拟以将数据流式传输到 Azure 时序见解预览环境。
* 对数据进行基本的分析。
* 定义时序模型类型和层次结构，并将其与实例相关联。

知道如何创建自己的 Azure 时序见解预览环境后，现在来了解更多关于 Azure 时序见解的关键概念。

阅读 Azure 时序见解存储配置：

> [!div class="nextstepaction"]
> [Azure 时序见解预览存储和流入量](./time-series-insights-update-storage-ingress.md)

详细了解时序模型：

> [!div class="nextstepaction"]
> [Azure 时序见解预览数据模型](./time-series-insights-update-tsm.md)

<!-- Images -->
[1]: media/v2-update-provision/device-one-accelerator.png
[2]: media/v2-update-provision/device-two-create.png
[3]: media/v2-update-provision/device-three-launch.png
[4]: media/v2-update-provision/device-four-iot-sim-page.png
[5]: media/v2-update-provision/device-five-params.png
[6]: media/v2-update-provision/device-six-listings.png
[7]: media/v2-update-provision/device-seven-dashboard.png
[8]: media/v2-update-provision/device-eight-portal.png

[9]: media/v2-update-provision/payg-one-azure.png
[10]: media/v2-update-provision/payg-two-create.png
[11]: media/v2-update-provision/payg-three-new.png
[12]: media/v2-update-provision/payg-four-add.png
[13]: media/v2-update-provision/payg-five-event-source.png
[14]: media/v2-update-provision/payg-six-review.png
[15]: media/v2-update-provision/payg-seven-deploy.png
[16]: media/v2-update-provision/payg-eight-environment.png
[17]: media/v2-update-provision/payg-nine-data-access.png
[18]: media/v2-update-provision/payg-ten-verify.png

[19]: media/v2-update-provision/analyze-one-portal.png
[20]: media/v2-update-provision/analyze-two-unparented.png
[21]: media/v2-update-provision/analyze-three-show-pressure.png
[22]: media/v2-update-provision/analyze-four-chart.png
[23]: media/v2-update-provision/analyze-five-chart.png
[24]: media/v2-update-provision/analyze-six-from.png
[25]: media/v2-update-provision/analyze-seven-change-from.png
[26]: media/v2-update-provision/analyze-eight-all.png

[27]: media/v2-update-provision/define-one-model.png
[28]: media/v2-update-provision/define-two-add.png
[29]: media/v2-update-provision/define-three-variable.png
[30]: media/v2-update-provision/define-four-avg.png
[31]: media/v2-update-provision/define-five-humidity.png
[32]: media/v2-update-provision/define-six-type.png
[33]: media/v2-update-provision/define-seven-hierarchy.png
[34]: media/v2-update-provision/define-eight-add-hierarchy.png
[35]: media/v2-update-provision/define-nine-created.png
[36]: media/v2-update-provision/define-ten-edit.png
[37]: media/v2-update-provision/define-eleven-chiller.png
[38]: media/v2-update-provision/define-twelve.png
[39]: media/v2-update-provision/define-thirteen-explore.png
[40]: media/v2-update-provision/define-fourteen-show-max.png
[41]: media/v2-update-provision/define-fifteen-filter.png
[42]: media/v2-update-provision/define-sixteen.png
[43]: media/v2-update-provision/define-seventeen.png
[44]: media/v2-update-provision/define-eighteen.png