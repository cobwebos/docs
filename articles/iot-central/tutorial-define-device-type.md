---
title: 在 Azure IoT Central 中定义新设备类型 | Microsoft Docs
description: 本教程向构建人员介绍如何在 Azure IoT Central 应用程序中定义新设备类型。 其中介绍了如何为类型定义遥测、状态、属性和设置。
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: b085911f760693a774d443ca055944268b20f055
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2018
ms.locfileid: "37869554"
---
# <a name="tutorial-define-a-new-device-type-in-your-azure-iot-central-application"></a>教程：在 Azure IoT Central 应用程序中定义新设备类型

本教程向构建人员介绍如何使用设备模板在 Microsoft Azure IoT Central 应用程序中定义新的设备类型。 设备模板定义设备类型的遥测、状态、属性和设置。

为了让你在连接实际设备之前测试应用程序，Azure IoT Central 会在创建设备模板时基于该模板创建一个模拟设备。

本教程将创建“连接的空调”设备模板。 连接的空调设备：

* 发送温度和湿度等遥测数据。
* 报告状态，例如，该设备是已打开还是已关闭。
* 具有属性，例如固件版本和序列号。
* 具有设置，例如目标温度和风扇速度。

本教程介绍如何执行以下操作：

> [!div class="checklist"]
> * 创建新设备模板
> * 将遥测功能添加到设备
> * 查看模拟遥测数据
> * 定义事件度量
> * 查看模拟事件
> * 定义状态度量
> * 查看模拟状态
> * 使用设备属性
> * 使用设备设置
> * 使用命令

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要一个 Azure IoT Central 应用程序。 如果已完成[创建 Azure IoT Central 应用程序](quick-deploy-iot-central.md)快速入门，则可以重复使用此快速入门中创建的应用程序。 否则，请完成以下步骤，以创建一个空的 Azure IoT Central 应用程序：

1. 导航到 Azure IoT Central 的 [应用程序管理器](https://aka.ms/iotcentral) 页。

2. 输入用于访问 Azure 订阅的电子邮件地址和密码：

   ![输入组织帐户](./media/tutorial-define-device-type/sign-in.png)

3. 若要开始创建新的 Azure IoT Central 应用程序，请选择“新建应用程序”：

    ![Azure IoT Central 的“应用程序管理器”页](./media/tutorial-define-device-type/iotcentralhome.png)

4. 若要创建新的 Azure IoT Central 应用程序：

    * 选择一个友好的应用程序名称，例如 **Contoso Air Conditioners**。 Azure IoT Central 将会生成唯一的 URL 前缀。 可将此 URL 前缀更改为更容易记住的内容。
    
    * 选择要使用的 Azure Active Directory 和 Azure 订阅。 有关目录和订阅的详细信息，请参阅[创建 Azure IoT Central 应用程序](howto-create-application.md)。
    
    * 选择现有的资源组，或使用所选的名称创建新的资源组。 例如 **contoso-rg**。
    
    * 选择最靠近自己的地理区域。
    
    * 选择“自定义应用程序”应用程序模板。
    
    * 选择“免费 30 天试用版应用程序”付款计划。
    
    * 选择“创建”。

    ![Azure IoT Central 的“创建应用程序”页](./media/tutorial-define-device-type/iotcentralcreate.png)

有关详细信息，请参阅[如何创建 Azure IoT Central 应用程序](howto-create-application.md)。

## <a name="create-a-new-custom-device-template"></a>创建新的自定义设备模板

构建人员可在应用程序中创建和编辑设备模板。 创建设备模板时，Azure IoT Central 将会基于该模板生成模拟设备。 模拟设备生成遥测数据，使你能够在连接物理设备之前测试应用程序的行为。

若要将新设备模板添加到应用程序，需要转到“应用程序生成器”页。 为此，请在左侧导航菜单中选择“应用程序生成器”。

![应用程序构建人员页](./media/tutorial-define-device-type/builderhome.png)

## <a name="add-a-device-and-define-telemetry"></a>添加设备并定义遥测

以下步骤说明如何针对向应用程序发送温度遥测数据的设备创建新的“连接的空调”设备模板：

1. 在“应用程序生成器”页上，选择“创建设备模板”：

    ![“应用程序生成器”页，“创建设备模板”](./media/tutorial-define-device-type/builderhomedevices.png)

2. 在“设备模板”页上，选择“自定义”。 使用“自定义”设备模板可以定义连接的空调的所有特征和行为：

    ![设备](./media/tutorial-define-device-type/builderhomedevicescustom.png)

3. 在“新建设备模板”页上，输入“连接的空调”作为设备的名称，然后选择“创建”。 还可以上传设备的图像，使操作员能够在 Device Explorer 中看到它：

    ![自定义设备](./media/tutorial-define-device-type/createcustomdevice.png)

4. 在“连接的空调”设备模板中，确保在定义遥测的“度量”页中操作。 定义的每个设备模板包含单独的页面用于执行以下操作：

    * 指定度量，例如设备发送的遥测数据、事件和状态。
    
    * 定义用于控制设备的设置。
    
    * 定义用于记录有关设备的信息的属性。
    
    * 定义与设备关联的规则。
    
    * 为操作员自定义设备仪表板。

    ![空调度量](./media/tutorial-define-device-type/airconmeasurements.png)

    > [!NOTE]
    > 若要更改设备或设备模板的名称，请单击页面顶部的文本。

5. 若要添加温度遥测度量，请选择“新建度量”。 然后选择“遥测”作为度量类型：

    ![连接的空调度量](./media/tutorial-define-device-type/airconmeasurementsnew.png)

6. 为设备模板定义的每种遥测都包括一些[配置选项](howto-set-up-template.md)，例如：

    * 显示选项。

    * 遥测详细信息。

    * 模拟参数。

    若要配置“温度”遥测，请使用下表中的信息：

    | 设置              | 值         |
    | -------------------- | -----------   |
    | 显示名称         | 温度   |
    | 字段名称           | 温度   |
    | 单位                | F             |
    | Min                  | 60            |
    | Max                  | 110           |
    | 小数位数       | 0             |

    还可以选择遥测数据的显示颜色。 若要保存遥测定义，请选择“保存”：

    ![配置温度模拟](./media/tutorial-define-device-type/temperaturesimulation.png)

7. 片刻之后，“度量”页会显示模拟的连接空调设备发来的温度遥测数据图表。 使用控件可以管理可见性、聚合，或编辑遥测定义：

    ![查看温度模拟数据](./media/tutorial-define-device-type/viewsimulation.png)

8. 还可以使用“折线图”、“堆积图”和“编辑时间范围”控件来自定义图表：

    ![自定义图表](./media/tutorial-define-device-type/customizechart.png)

## <a name="define-event-measurement"></a>定义事件度量

可以使用“事件”来定义设备发送的时间点数据，以指明某个重要的事件，例如，发生错误或组件故障。 与处理遥测度量一样，Azure IoT Central 可以模拟设备事件，使你能够在连接物理设备之前测试应用程序的行为。 在“度量”视图中为设备类型定义事件度量。

1. 若要添加“风扇电机错误”事件度量，请选择“新建度量”。 然后选择“事件”作为度量类型：

    ![连接的空调度量](./media/tutorial-define-device-type/eventnew.png)

2. 为设备模板定义的每种事件都包括一些[配置选项](howto-set-up-template.md)，例如：

   * 显示名称。

   * 字段名称。

   * 严重性。

    若要配置“风扇电机错误”事件，请使用下表中的信息：

    | 设置              | 值             |
    | -------------------- | -----------       |
    | 显示名称         | 风扇电机错误   |
    | 字段名称           | fanmotorerr       |
    | Severity             | 错误             |

    若要保存事件定义，请选择“保存”：

    ![配置事件度量](./media/tutorial-define-device-type/eventconfiguration.png)

3. 片刻之后，“度量”页会显示模拟的连接空调设备随机生成的事件图表。 使用控件可以管理可见性，或编辑事件定义：

    ![查看事件模拟数据](./media/tutorial-define-device-type/eventview.png)

1. 若要查看有关事件的更多详细信息，请在图表中单击该事件：

    ![查看事件详细信息](./media/tutorial-define-device-type/eventviewdetail.png)

## <a name="define-state-measurement"></a>定义状态度量

可以使用“状态”来定义和可视化设备或其组件在一段时间内的状态。 与处理遥测度量一样，Azure IoT Central 可以模拟设备状态，使你能够在连接物理设备之前测试应用程序的行为。 在“度量”视图中为设备类型定义状态度量。

1. 若要添加“风扇模式”度量，请选择“新建度量”。 然后选择“状态”作为度量类型：

    ![连接的空调状态度量](./media/tutorial-define-device-type/statenew.png)

2. 为设备模板定义的每种状态都包括一些[配置选项](howto-set-up-template.md)，例如：

   * 显示名称。

   * 字段名称。

   * 带有显示标签（可选）的值。

   * 每个值的颜色。

    若要配置“风扇模式”状态，请使用下表中的信息：

    | 设置              | 值             |
    | -------------------- | -----------       |
    | 显示名称         | 风扇模式          |
    | 字段名称           | fanmode           |
    | 值                | 1                 |
    | 显示标签        | 正在运行         |
    | 值                | 0                 |
    | 显示标签        | 已停止           |

    若要保存状态度量定义，请选择“保存”：

    ![配置状态度量](./media/tutorial-define-device-type/stateconfiguration.png)

3. 片刻之后，“度量”页会显示模拟的连接空调设备随机生成的状态图表。 使用控件可以管理可见性，或编辑状态定义：

    ![查看状态模拟数据](./media/tutorial-define-device-type/stateview.png)

4. 如果设备在短时间内发送了过多的数据点，状态度量值会以不同的视觉效果显示，如下所示。 如果单击图表，该时间段内的所有数据点将按时间顺序显示。 还可以缩小时间范围，以查看图表上绘制的度量值。

    ![查看状态详细信息](./media/tutorial-define-device-type/stateviewdetail.png)

## <a name="settings-properties-and-commands"></a>设置、属性和命令

设置、属性和设备属性以及命令是设备模板中定义的、与每个设备关联的不同值：

* 使用“设置”可从应用程序向设备发送配置数据。 例如，操作员可以使用某项设置将设备的遥测间隔从 2 秒更改为 5 秒。 当操作员更改某项设置时，该设置将在 UI 中标记为挂起，直到设备确认已处理设置更改为止。

* 使用“属性”可以记录有关应用程序中设备的信息。 例如，可以使用属性来记录设备的序列号，或设备制造商的电话号码。 属性存储在应用程序中，不会与设备保持同步。 操作员可以向属性赋值。

* 使用“设备属性”可让设备向应用程序发送属性值。 这些属性只能由设备更改。 对于操作员而言，设备属性是只读的。

* 请使用命令通过应用程序远程管理设备。 可以通过云直接在设备上运行命令，以便控制设备。 例如，操作员可以运行重启之类的命令来即时重启设备。

## <a name="use-settings"></a>使用设置

操作员可以使用“设置”向设备发送配置数据。 在本部分，我们要将一项设置添加到“连接的空调”设备模板，使操作员能够设置连接的空调的目标温度。

1. 导航到“连接的空调”设备模板的“设置”页：

    ![准备添加设置](./media/tutorial-define-device-type/deviceaddsetting.png)

    可以创建不同类型（例如数字或文本）的设置。

2. 选择“数字”可将数字设置添加到设备。

3. 若要配置“设置温度”设置，请使用下表中的信息：

    | 字段                | 值           |
    | -------------------- | -----------     |
    | 显示名称         | 设置温度 |
    | 字段名称           | setTemperature  |
    | 度量单位  | F               |
    | 小数位数       | 1               |
    | 最小值        | 20              |
    | 最大值        | 200             |
    | 初始值        | 80              |
    | 说明          | 设置空调的目标温度 |

    然后选择“保存”：

    ![配置“设置温度”设置](./media/tutorial-define-device-type/configuresetting.png)

    > [!NOTE]
    > 当设备确认设置更改时，该设置的状态将更改为“已同步”。

4. 可以通过移动设置磁贴或调整其大小，来自定义“设置”页的布局：

    ![自定义设置布局](./media/tutorial-define-device-type/settingslayout.png)

## <a name="use-properties--device-properties"></a>使用属性/设备属性

使用“属性”可以存储有关应用程序中设备的信息。 在本部分，请将设备属性添加到“连接的空调”设备模板，以便存储设备序列号和固件版本。 请注意，这两个都是设备报告的只读属性 -- 不能向其分配值。 可以使用的并且能够向其分配值的属性包括：设备的位置、所有权信息、设备的上次服务日期/时间。
 
1. 导航到“连接的空调”设备模板的“属性”页：

    ![准备添加属性](./media/tutorial-define-device-type/deviceaddproperty.png)

    可以创建不同类型（例如数字或文本）的设备属性。 若要将序列号属性添加到设备模板，请选择“文本”。

2. 若要配置序列号属性，请使用下表中的信息：

    | 字段                | 值                |
    | -------------------- | -------------------- |
    | 显示名称         | 序列号        |
    | 字段名称           | serialNumber         |
    | 初始值        | cac00001             |
    | 说明          | 设备序列号 |

    在其他字段中保留默认值。

    ![配置设备属性](./media/tutorial-define-device-type/configureproperties.png)

    选择“保存”。

3. 若要将固件版本添加到设备模板的设备属性，请选择“文本”。

4. 若要配置固件版本设备属性，请使用下表中的信息：

    | 字段                | 值                   |
    | -------------------- | ----------------------- |
    | 显示名称         | 固件版本        |
    | 字段名称           | firmwareVersion         |
    | 初始值        | 0.1                     |
    | 说明          | 设备固件版本 |

    ![配置设备属性](./media/tutorial-define-device-type/configureproperties2.png)

    选择“保存”。

5. 可以通过移动属性磁贴或调整其大小，来自定义“属性”页的布局：

    ![自定义属性布局](./media/tutorial-define-device-type/propertieslayout.png)


## <a name="use-commands"></a>使用命令

若要允许操作员直接在设备上运行命令，请使用“命令”功能。 在本部分，请将一项命令添加到“连接的空调”设备模板，使操作员能够回显已连接空调显示器上的特定消息（这适用于 MxChip 示例代码）。

1. 导航到“连接的空调”设备模板的“命令”页：

    ![准备添加设置](media/tutorial-define-device-type/commandsecho.png)

    可以根据需要创建不同类型的命令。 

1. 单击“新建命令”，向设备添加命令。

1. 若要配置新命令，请使用下表中的信息：

    | 字段                | 值           |
    | -------------------- | -----------     |
    | 显示名称         | 回显命令    |
    | 字段名称           | echo            |
    | 默认超时      | 30              |
    | 显示名称         | 显示文本    |
    | 显示类型         | text            |  

可以向命令添加其他输入，方法是单击“+”进行输入。

2. 选择“保存”。

3. 可以通过移动命令磁贴或调整其大小，来自定义“命令”页的布局：

    ![自定义设置布局](media/tutorial-define-device-type/commandstileresize.png)

## <a name="view-your-simulated-device"></a>查看模拟设备

定义“连接的空调”设备模板后，可以自定义其“仪表板”，以包含定义的度量、设置和属性。 然后，可用操作员的身份预览仪表板：

1. 选择“连接的空调”设备模板的“仪表板”页：

    ![连接的空调仪表板](./media/tutorial-define-device-type/aircondashboards.png)

2. 选择“折线图”，将组成部分拖放到“仪表板”：

    ![仪表板组成部分](./media/tutorial-define-device-type/dashboardcomponents1.png)

3. 使用下表中的信息配置“折线图”组成部分：

    | 设置      | 值       |
    | ------------ | ----------- |
    | 标题        | 温度 |
    | 时间范围   | 过去 30 分钟 |
    | 度量 | 温度（选择“温度”旁边的“可见性”） |

    ![折线图设置](./media/tutorial-define-device-type/linechartsettings.png)

    然后选择“保存”。

4. 使用下表中的信息配置“事件图表”组成部分：

    | 设置      | 值       |
    | ------------ | ----------- |
    | 标题        | 活动 |
    | 时间范围   | 过去 30 分钟 |
    | 度量 | 风扇电机错误（选择“风扇电机错误”旁边的“可见性”） |

    ![折线图设置](./media/tutorial-define-device-type/dashboardeventchartsetting.png)

    然后选择“保存”。

5. 使用下表中的信息配置“状态图表”组成部分：

    | 设置      | 值       |
    | ------------ | ----------- |
    | 标题        | 风扇模式 |
    | 时间范围   | 过去 30 分钟 |
    | 度量 | 风扇模式（选择“风扇模式”旁边的“可见性”） |

    ![折线图设置](./media/tutorial-define-device-type/dashboardstatechartsetting.png)

    然后选择“保存”。

6. 若要将“设置温度”设置添加到仪表板，请选择“设置和属性”：

    ![仪表板组成部分](./media/tutorial-define-device-type/dashboardcomponents4.png)

7. 使用下表中的信息配置“设置和属性”组成部分：

    | 设置                 | 值         |
    | ----------------------- | ------------- |
    | 标题                   | 设置目标温度 |
    | 设置和属性 | 设置温度 |

    ![序列号属性设置](./media/tutorial-define-device-type/propertysettings3.png)

    然后选择“保存”。

8. 若要将设备序列号添加到仪表板，请选择“设置和属性”：

    ![仪表板组成部分](./media/tutorial-define-device-type/dashboardcomponents3.png)

9. 使用下表中的信息配置“设置和属性”组成部分：

    | 设置                 | 值         |
    | ----------------------- | ------------- |
    | 标题                   | 序列号 |
    | 设置和属性 | 序列号 |

    ![序列号属性设置](./media/tutorial-define-device-type/propertysettings1.png)

    然后选择“保存”。

10. 若要将设备固件版本添加到仪表板，请选择“设置和属性”：

    ![仪表板组成部分](./media/tutorial-define-device-type/dashboardcomponents4.png)

11. 使用下表中的信息配置“设置和属性”组成部分：

    | 设置                 | 值            |
    | ----------------------- | ---------------- |
    | 标题                   | 固件版本 |
    | 设置和属性 | 固件版本 |

    ![序列号属性设置](./media/tutorial-define-device-type/propertysettings2.png)

    然后选择“保存”。

12. 若要以操作员身份查看仪表板，请在页面右上角关闭“设计模式”。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * 创建新设备模板
> * 将遥测功能添加到设备
> * 查看模拟遥测数据
> * 定义设备事件
> * 查看模拟事件
> * 定义状态
> * 查看模拟状态
> * 使用设备属性
> * 使用设备设置

在 Azure IoT Central 应用程序内定义设备模板后，建议接下来执行以下后续步骤：

* [为设备配置规则和操作](tutorial-configure-rules.md)
* [自定义操作员的视图](tutorial-customize-operator.md)