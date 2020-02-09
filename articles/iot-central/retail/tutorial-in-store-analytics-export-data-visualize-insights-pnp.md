---
title: 教程 - 在 Azure IoT Central 中导出数据并直观呈现见解
description: 在本教程中，了解如何从 IoT Central 导出数据并在 Power BI 仪表板中可视化见解。
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: dobett
author: dominicbetts
ms.date: 11/12/2019
ms.openlocfilehash: 9dcb185ab8375d46c75a12e6adaeeae2358c13ac
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022080"
---
# <a name="tutorial-export-data-from-azure-iot-central-and-visualize-insights-in-power-bi"></a>教程：从 Azure IoT Central 导出数据，并在 Power BI 中可视化见解



在前面的两个教程中，你使用“店内分析 - 结帐”  应用程序模板创建并自定义了 IoT Central 应用程序。 本教程将介绍如何配置 IoT Central 应用程序以导出从设备收集的遥测数据。 然后，可以使用 Power BI 创建用于存储管理器的自定义仪表板，以可视化从遥测数据派生的见解。

在本教程中，您将学习如何执行以下操作：
> [!div class="checklist"]
> * 配置 IoT Central 应用程序以将遥测数据导出到事件中心。
> * 使用逻辑应用将数据从事件中心发送到 Power BI 流数据集。
> * 创建一个 Power BI 仪表板，用于可视化流数据集中的数据。

## <a name="prerequisites"></a>必备条件

要完成本教程，需要：

* 若要完成之前的两个教程，请[在 Azure IoT Central 中创建店内分析应用程序](./tutorial-in-store-analytics-create-app-pnp.md)并[在 Azure IoT Central 中自定义操作员仪表板和管理设备](./tutorial-in-store-analytics-customize-dashboard-pnp.md)。
* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 一个 Power BI 帐户。 如果没有 Power BI 帐户，请在开始之前注册 [Power BI Pro 免费试用版](https://app.powerbi.com/signupredirect?pbi_source=web)。

## <a name="create-a-resource-group"></a>创建资源组

在创建事件中心和逻辑应用之前，需要创建一个资源组来对其进行管理。 资源组应与“店内分析 - 结帐”  IoT Central 应用程序的位置相同。 创建资源组：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航栏中，选择“资源组”  。 然后选择“添加”  。
1. 对于“订阅”  ，请选择用于创建 IoT Central 应用程序的 Azure 订阅的名称。
1. 对于“资源组”  名称，请输入“retail-store-analysis”  *。
1. 对于“区域”  ，选择为 IoT Central 应用程序所选的同一区域。
1. 选择“查看 + 创建”  。
1. 在“查看 + 创建”页上，选择“创建”。  

在订阅中，你现在已有一个名为“retail-store-analysis”  的资源组。

## <a name="create-an-event-hub"></a>创建事件中心

在将零售监视应用程序配置为导出遥测数据之前，需要创建一个事件中心来接收导出的数据。 下列步骤展示了如何创建事件中心：

1. 在 Azure 门户中，选择屏幕左上角的“创建资源”  。
1. 在“搜索市场”  中，输入“事件中心”  ，然后按 Enter  。
1. 在“事件中心”  页上，选择“创建”  。
1. 在“创建命名空间”  页上执行以下步骤：
    * 输入命名空间的唯一名称，例如“yourname”  。 系统会检查该名称是否可用。
    * 选择“基本”  定价层。
    * 对于“订阅”  ，请选择用于创建 IoT Central 应用程序的订阅的名称。
    * 选择“retail-store-analysis”  资源组。
    * 请选择用于创建 IoT Central 应用程序的位置。
    * 选择“创建”  。 可能需要等待几分钟让系统预配资源。
1. 在门户中，导航到“retail-store-analysis”  资源组。 等待部署完成。 可能需要选择“刷新”  才能更新部署状态。 你还可以在“通知”  中检查事件中心命名空间创建的状态。
1. 在“retail-store-analysis”  资源组中，选择“事件中心命名空间”  。 门户中会显示“事件中心命名空间”  的主页。

现在，你已有事件中心命名空间  ，你可以创建事件中心  以用于 IoT Central 应用程序：

1. 在门户中“事件中心命名空间”  的主页上，选择“+ 事件中心”  。
1. 在“创建事件中心”  页上，输入“store-telemetry”  作为名称，然后选择“创建”  。

现在，你有了一个事件中心，可以在从 IoT Central 应用程序配置数据导出时使用它：

![事件中心](./media/tutorial-in-store-analytics-visualize-insights-pnp/event-hub.png)

## <a name="configure-data-export"></a>配置数据导出

现在，你有了一个事件中心，可以配置“店内分析 - 结帐”  应用程序，以从连接的设备导出遥测数据。 以下步骤展示了如何配置导出：

1. 登录到“店内分析 - 结帐”  IoT Central 应用程序。
1. 在左窗格中选择“数据导出”  。
1. 选择“新建”>“Azure 事件中心”  。
1. 对于“显示名称”  ，输入“Telemetry export”  。
1. 选择你的事件中心命名空间  。
1. 选择“store-telemetry”  事件中心。
1. 在“要导出的数据”  部分中，关闭“设备”  和“设备模板”  。
1. 选择“保存”。 

数据导出可能需要几分钟才能开始向事件中心发送遥测数据。 可以在“数据导出”  页上查看导出的状态：

![连续数据导出配置](./media/tutorial-in-store-analytics-visualize-insights-pnp/export-configuration.png)

## <a name="create-the-power-bi-datasets"></a>创建 Power BI 数据集

Power BI 仪表板将显示零售监视应用程序中的数据。 在此解决方案中，将使用 Power BI 流数据集作为 Power BI 仪表板的数据源。 在本部分中，将定义流数据集的架构，以便逻辑应用可以从事件中心转发数据。 以下步骤展示了如何为环境传感器创建两个流数据集，并为占用传感器创建一个流数据集：

1. 登录到 **Power BI** 帐户。
1. 选择“工作区”  ，然后选择“创建工作区”  。
1. 在“创建工作区”  页上的“工作区名称”  中，输入“In-store analytics - checkout”  。
1. 滚动到“欢迎使用店内分析 - 结帐工作区”  页的底部，然后选择”跳过”  。
1. 在工作区页上，选择“创建”>“流数据集”  。
1. 在“新建流数据集”  页上，选择“API”  ，然后选择“下一步”  "。
1. 对于“数据集名称”  ，输入“Zone 1 sensor”  。
1. 在下表中输入三个“流中的值”  ：

    | 值名称  | 值类型 |
    | ----------- | ---------- |
    | 时间戳   | DateTime   |
    | 湿度    | Number     |
    | 温度 | Number     |

1. 启用“历史数据分析”  。
1. 选择“创建”  ，然后选择“完成”  。
1. 创建另一个名为“Zone 2 sensor”  的流数据集，其架构和设置与“Zone 1 sensor”  流数据集相同。

现在有两个流数据集。 逻辑应用将从连接到“店内分析 - 结帐”  应用程序的两个环境传感器将遥测数据路由到以下两个数据集：

![区域数据集](./media/tutorial-in-store-analytics-visualize-insights-pnp/dataset-1.png)

此解决方案对每个传感器使用一个流数据集，因为无法在 Power BI 中将筛选器应用于流数据。

还需要一个用于占有遥测数据的流数据集：

1. 在工作区页上，选择“创建”>“流数据集”  。
1. 在“新建流数据集”  页上，选择“API”  ，然后选择“下一步”  "。
1. 对于“数据集名称”  ，输入“Occupancy sensor”  。
1. 在下表中输入五个“流中的值”  ：

    | 值名称     | 值类型 |
    | -------------- | ---------- |
    | 时间戳      | DateTime   |
    | 队列长度 1 | Number     |
    | 队列长度 2 | Number     |
    | 停留时间 1   | Number     |
    | 停留时间 2   | Number     |

1. 启用“历史数据分析”  。
1. 选择“创建”  ，然后选择“完成”  。

现在，你有了第三个流数据集，它可存储模拟占用传感器中的值。 此传感器报告商店中两个结帐之间的队列长度，以及客户在这些队列中等待的时长：

![占用数据集](./media/tutorial-in-store-analytics-visualize-insights-pnp/dataset-2.png)

## <a name="create-a-logic-app"></a>创建逻辑应用

在此解决方案中，逻辑应用从事件中心读取遥测数据，分析数据，然后将数据发送到创建的 Power BI 流数据集。

创建逻辑应用之前，需要连接到 IoT Central 应用程序（位于[在 Azure IoT Central 中创建店内分析应用程序](./tutorial-in-store-analytics-create-app-pnp.md)教程中）的两个 RuuviTag 传感器的设备 ID：

1. 登录到“店内分析 - 结帐”  IoT Central 应用程序。
1. 在左窗格中，选择“设备”  。 然后选择“RuuviTag”  。
1. 记下“设备 ID”  。 在以下屏幕截图中，ID 是 f5dcf4ac32e8  和 e29ffc8d5326  ：

    ![设备 ID](./media/tutorial-in-store-analytics-visualize-insights-pnp/device-ids.png)

以下步骤展示了如何在 Azure 门户中创建逻辑应用：

1. 登录到 [Azure 门户](https://portal.azure.com)中，选择屏幕左上角的“创建资源”  。
1. 在“搜索市场”  中，输入“逻辑应用”  ，然后按 Enter  。
1. 在“逻辑应用”  页上，选择“创建”  。
1. 在“逻辑应用”  的“创建”页上：
    * 输入逻辑应用的唯一名称，例如“yourname-retail-store-analysis”  。
    * 对于“订阅”  ，请选择用于创建 IoT Central 应用程序的订阅的名称。
    * 选择“retail-store-analysis”  资源组。
    * 请选择用于创建 IoT Central 应用程序的位置。
    * 选择“创建”  。 可能需要等待几分钟让系统预配资源。
1. 在 Azure 门户中，导航到新的逻辑应用。
1. 在“逻辑应用设计器”  页上，向下滚动并选择“空白逻辑应用”  。
1. 在“搜索连接器和触发器”  中，输入“事件中心”  。
1. 在“触发器”  中，选择“当事件在事件中心可用时”  。
1. 对于“连接名称”  ，输入“Store telemetry”  ，并选择“事件中心命名空间”  。
1. 选择“RootManageSharedAccess”  策略，然后选择“创建”  。
1. 在“当事件在事件中心可用时”  操作中：
    * 在“事件中心名称”  中，选择“store-telemetry”  。
    * 在“内容类型”  中，选择“应用程序/json”  。
    * 将“间隔”  设置为 3，将“频率”  设置为秒
1. 选择“保存”  以保存逻辑应用。

若要将逻辑添加到逻辑应用设计，请选择“代码视图”  ：

1. 将 `"actions": {},` 替换为以下 JSON。 将两个占位符 `[YOUR RUUVITAG DEVICE ID 1]` 和 `[YOUR RUUVITAG DEVICE ID 2]` 替换为你记下的两个 RuuviTag 设备的 ID：

    ```json
    "actions": {
        "Initialize_Device_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "DeviceID",
                        "type": "String"
                    }
                ]
            },
            "runAfter": {},
            "type": "InitializeVariable"
        },
        "Initialize_Interface_ID_variable": {
            "inputs": {
                "variables": [
                    {
                        "name": "InterfaceID",
                        "type": "String",
                        "value": "Other"
                    }
                ]
            },
            "runAfter": {
                "Initialize_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "InitializeVariable"
        },
        "Parse_Properties": {
            "inputs": {
                "content": "@triggerBody()?['Properties']",
                "schema": {
                    "properties": {
                        "iothub-connection-auth-generation-id": {
                            "type": "string"
                        },
                        "iothub-connection-auth-method": {
                            "type": "string"
                        },
                        "iothub-connection-device-id": {
                            "type": "string"
                        },
                        "iothub-enqueuedtime": {
                            "type": "string"
                        },
                        "iothub-interface-name": {
                            "type": "string"
                        },
                        "iothub-message-source": {
                            "type": "string"
                        },
                        "x-opt-enqueued-time": {
                            "type": "string"
                        },
                        "x-opt-offset": {
                            "type": "string"
                        },
                        "x-opt-sequence-number": {
                            "type": "integer"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Parse_Telemetry": {
            "inputs": {
                "content": "@triggerBody()?['ContentData']",
                "schema": {
                    "properties": {
                        "DwellTime1": {
                            "type": "number"
                        },
                        "DwellTime2": {
                            "type": "number"
                        },
                        "count1": {
                            "type": "number"
                        },
                        "count2": {
                            "type": "number"
                        },
                        "humidity": {
                            "type": "number"
                        },
                        "temperature": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                }
            },
            "runAfter": {
                "Initialize_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "ParseJson"
        },
        "Set_Device_ID_variable": {
            "inputs": {
                "name": "DeviceID",
                "value": "@body('Parse_Properties')?['iothub-connection-device-id']"
            },
            "runAfter": {
                "Parse_Properties": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Set_Interface_ID_variable": {
            "inputs": {
                "name": "InterfaceID",
                "value": "@body('Parse_Properties')?['iothub-interface-name']"
            },
            "runAfter": {
                "Set_Device_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "SetVariable"
        },
        "Switch_by_DeviceID": {
            "cases": {
                "Occupancy": {
                    "actions": {
                        "Switch_by_InterfaceID": {
                            "cases": {
                                "Dwell_Time_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_v2_1l0"
                                },
                                "People_Count_interface": {
                                    "actions": {},
                                    "case": "RS40_Occupancy_Sensor_iv"
                                }
                            },
                            "default": {
                                "actions": {}
                            },
                            "expression": "@variables('InterfaceID')",
                            "runAfter": {},
                            "type": "Switch"
                        }
                    },
                    "case": "Occupancy"
                },
                "Zone 2 environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 2]"
                },
                "Zone_1_environment": {
                    "actions": {},
                    "case": "[YOUR RUUVITAG DEVICE ID 1]"
                }
            },
            "default": {
                "actions": {}
            },
            "expression": "@variables('DeviceID')",
            "runAfter": {
                "Parse_Telemetry": [
                    "Succeeded"
                ],
                "Set_Interface_ID_variable": [
                    "Succeeded"
                ]
            },
            "type": "Switch"
        }
    },
    ```

1. 选择“保存”  ，然后选择“设计器”  以查看所添加逻辑的视觉版本：

    ![逻辑应用设计](./media/tutorial-in-store-analytics-visualize-insights-pnp/logic-app.png)

1. 选择“按设备 ID 切换”  以展开操作。 然后选择“Zone 1 environment”  ，并选择“添加操作”  。
1. 在“搜索连接器和操作”  中，输入“Power BI”  ，然后按 Enter  。
1. 选择“向数据集添加行(预览)”  操作。
1. 选择“登录”  并按照提示登录到 Power BI 帐户。
1. 完成登录过程后，在“向数据集添加行”  操作中：
    * 选择“店内分析 - 结帐”  作为工作区。
    * 选择“Zone 1 sensor”  作为数据集。
    * 选择“RealTimeData”  作为表。
    * 选择“添加新参数”  ，然后选择“时间戳”  、“湿度”  和“温度”  字段。
    * 选择“时间戳”  字段，然后从“动态内容”  列表中选择“x-opt-enqueuedtime”  。
    * 选择“湿度”  字段，然后选择“分析遥测”  旁边的“查看更多”  。 然后选择“湿度”  。
    * 选择“温度”  字段，然后选择“分析遥测”  旁边的“查看更多”  。 然后选择“温度”  。
    * 选择“保存”  以保存更改。 **Zone 1 environment**操作如以下屏幕截图所示：![Zone 1 environment](./media/tutorial-in-store-analytics-visualize-insights-pnp/zone-1-action.png)
1. 选择“Zone 2 environment”  操作，然后选择“添加操作”  。
1. 在“搜索连接器和操作”  中，输入“Power BI”  ，然后按 Enter  。
1. 选择“向数据集添加行(预览)”  操作。
1. 在“向数据集添加行 2”  操作中：
    * 选择“店内分析 - 结帐”  作为工作区。
    * 选择“Zone 2 sensor”  作为数据集。
    * 选择“RealTimeData”  作为表。
    * 选择“添加新参数”  ，然后选择“时间戳”  、“湿度”  和“温度”  字段。
    * 选择“时间戳”  字段，然后从“动态内容”  列表中选择“x-opt-enqueuedtime”  。
    * 选择“湿度”  字段，然后选择“分析遥测”  旁边的“查看更多”  。 然后选择“湿度”  。
    * 选择“温度”  字段，然后选择“分析遥测”  旁边的“查看更多”  。 然后选择“温度”  。
    选择“保存”  以保存更改。  **Zone 2 environment**操作如以下屏幕截图所示：![Zone 2 environment](./media/tutorial-in-store-analytics-visualize-insights-pnp/zone-2-action.png)
1. 选择“占用”  操作，然后选择“按接口 ID 切换”  操作。
1. 选择“停留时间接口”  操作，然后选择“添加操作”  。
1. 在“搜索连接器和操作”  中，输入“Power BI”  ，然后按 Enter  。
1. 选择“向数据集添加行(预览)”  操作。
1. 在“向数据集添加行”  操作中：
    * 选择“店内分析 - 结帐”  作为工作区。
    * 选择“占用传感器”  作为数据集。
    * 选择“RealTimeData”  作为表。
    * 选择“添加新参数”  然，后选择“时间戳”  “停留时间 1”  和“停留时间 2”  字段。
    * 选择“时间戳”  字段，然后从“动态内容”  列表中选择“x-opt-enqueuedtime”  。
    * 选择“停留时间 1”  字段，然后选择“分析遥测”  旁边的“查看更多”  。 然后选择“DwellTime1”  。
    * 选择“停留时间 2”  字段，然后选择“分析遥测”  旁边的“查看更多”  。 然后选择“DwellTime2”  。
    * 选择“保存”  以保存更改。 **停留时间接口**操作如以下屏幕截图所示：![占用操作](./media/tutorial-in-store-analytics-visualize-insights-pnp/occupancy-action-1.png)
1. 选择“人数接口”  操作，然后选择“添加操作”  。
1. 在“搜索连接器和操作”  中，输入“Power BI”  ，然后按 Enter  。
1. 选择“向数据集添加行(预览)”  操作。
1. 在“向数据集添加行”  操作中：
    * 选择“店内分析 - 结帐”  作为工作区。
    * 选择“占用传感器”  作为数据集。
    * 选择“RealTimeData”  作为表。
    * 选择“添加新参数”  ，然后选择“时间戳”  、“队列长度 1”  和“队列长度 2”  字段。
    * 选择“时间戳”  字段，然后从“动态内容”  列表中选择“x-opt-enqueuedtime”  。
    * 选择“队列长度 1”  字段，然后选择“分析遥测”  旁边的“查看更多”  。 然后，选择“count1”  。
    * 选择“队列长度 2”  字段，然后选择“分析遥测”  旁边的“查看更多”  。 然后，选择“count2”  。
    * 选择“保存”  以保存更改。 **人数接口**操作如以下屏幕截图所示：![占用操作](./media/tutorial-in-store-analytics-visualize-insights-pnp/occupancy-action-2.png)

逻辑应用会自动运行。 若要查看每个运行的状态，请导航到 Azure 门户中逻辑应用的“概述”  页：

## <a name="create-a-power-bi-dashboard"></a>创建 Power BI 仪表板

现在，你已通过事件中心从 IoT Central 应用程序流动了遥测数据。 然后，逻辑应用会分析事件中心消息并将其添加到 Power BI 流数据集。 现在，可以创建一个 Power BI 仪表板来可视化遥测数据：

1. 登录到 **Power BI** 帐户。
1. 选择“工作区”>“店内分析 - 结帐”  。
1. 选择“创建”>“仪表板”  。
1. 输入“Store analytics”  作为仪表板名称，然后选择“创建”  。

### <a name="add-line-charts"></a>添加折线图

添加四个折线图磁贴，以显示两个环境传感器的温度和湿度。 使用下表中的信息创建磁贴。 若要添加每个磁贴，请先选择“...(更多选项)”>“添加磁贴”  。 依次选择“自定义流数据”  和“下一步”  ：

| 设置 | 图表 #1 | 图表 #2 | 图表 #3 | 图表 #4 |
| ------- | -------- | -------- | -------- | -------- |
| 数据集 | Zone 1 sensor | Zone 1 sensor | Zone 2 sensor | Zone 2 sensor |
| 可视化类型 | 折线图 | 折线图 | 折线图 | 折线图 |
| 轴 | 时间戳 | 时间戳 | 时间戳 | 时间戳 |
| 值 | 温度 | 湿度 | 温度 | 湿度 |
| 时间范围 | 60 分钟 | 60 分钟 | 60 分钟 | 60 分钟 |
| 标题 | 温度（1小时） | 湿度（1小时） | 温度（1小时） | 湿度（1小时） |
| 副标题 | 区域 1 | 区域 1 | 区域 2 | 区域 2 |

以下屏幕截图显示了第一个图表的设置：

![折线图设置](./media/tutorial-in-store-analytics-visualize-insights-pnp/line-chart.png)

### <a name="add-cards-to-show-environmental-data"></a>添加卡以显示环境数据

添加四个卡磁贴，以显示两个环境传感器的温度和湿度值。 使用下表中的信息创建磁贴。 若要添加每个磁贴，请先选择“...(更多选项)”>“添加磁贴”  。 依次选择“自定义流数据”  和“下一步”  ：

| 设置 | 卡 #1 | 卡 #2 | 卡 #3 | 卡 #4 |
| ------- | ------- | ------- | ------- | ------- |
| 数据集 | Zone 1 sensor | Zone 1 sensor | Zone 2 sensor | Zone 2 sensor |
| 可视化类型 | 卡片 | 卡片 | 卡片 | 卡片 |
| 字段 | 温度 | 湿度 | 温度 | 湿度 |
| 标题 | 温度 (F) | 湿度 (%) | 温度 (F) | 湿度 (%) |
| 副标题 | 区域 1 | 区域 1 | 区域 2 | 区域 2 |

以下屏幕截图显示了第一个卡的设置：

![卡设置](./media/tutorial-in-store-analytics-visualize-insights-pnp/card-settings.png)

### <a name="add-tiles-to-show-checkout-occupancy-data"></a>添加磁贴以显示结帐占用数据

添加四个卡磁贴，以显示店内两个结帐之间的队列长度和停留时间。 使用下表中的信息创建磁贴。 若要添加每个磁贴，请先选择“...(更多选项)”>“添加磁贴”  。 依次选择“自定义流数据”  和“下一步”  ：

| 设置 | 卡 #1 | 卡 #2 | 卡 #3 | 卡 #4 |
| ------- | ------- | ------- | ------- | ------- |
| 数据集 | 占用传感器 | 占用传感器 | 占用传感器 | 占用传感器 |
| 可视化类型 | 簇状柱形图 | 簇状柱形图 | 仪表 | 仪表 |
| 轴    | 时间戳 | 时间戳 | 空值 | 空值 |
| 值 | 停留时间 1 | 停留时间 2 | 队列长度 1 | 队列长度 2 |
| 时间范围 | 60 分钟 | 60 分钟 |  空值 | 空值 |
| 标题 | 停留时间 | 停留时间 | 队列长度 | 队列长度 |
| 副标题 | 结帐 1 | 结帐 2 | 结帐 1 | 结帐 2 |

在仪表板上调整磁贴的大小并重新排列，如以下屏幕截图所示：

![Power BI 仪表板](./media/tutorial-in-store-analytics-visualize-insights-pnp/pbi-dashboard.png)

可以添加一些其他图形资源来进一步自定义仪表板：

![Power BI 仪表板](./media/tutorial-in-store-analytics-visualize-insights-pnp/pbi-dashboard-graphics.png)

## <a name="clean-up-resources"></a>清理资源

如果已完成 IoT Central 应用程序，可以通过登录到应用程序并导航到“管理”  部分中的“应用程序设置”  页来删除它。

如果要保留应用程序，但降低与之相关的成本，请禁用将遥测数据发送到事件中心的数据导出。

可以通过删除名为“retail-store-analysis”  的资源组来删除 Azure 门户中的事件中心和逻辑应用。

通过从工作区的 Power BI 设置页中删除工作区，可以删除你的 Power BI 数据集和仪表板。

## <a name="next-steps"></a>后续步骤

这三个教程已向你展示了一个端到端解决方案，该解决方案使用“店内分析 - 结帐”  IoT Central 应用程序模板。 已将设备连接到应用程序，已使用 IoT Central 监视设备，并已使用 Power BI 生成仪表板，以便从设备遥测数据查看见解。 建议下一步是浏览其他 IoT Central 应用程序模板之一：

> [!div class="nextstepaction"]
> * [使用 IoT Central 生成能源用解决方案](../energy/overview-iot-central-energy.md)
> * [使用 IoT Central 生成政府解决方案](../government/overview-iot-central-government.md)
> * [使用 IoT Central 生成医疗保健解决方案](../healthcare/overview-iot-central-healthcare.md)
