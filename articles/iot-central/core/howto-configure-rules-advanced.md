---
title: 使用工作流将 Azure IoT Central 应用程序与其他云服务相集成 | Microsoft Docs
description: 本操作指南文章为构建人员介绍如何配置用于将 IoT Central 应用程序与其他云服务相集成的规则和操作。 若要创建高级规则，请在 Power Automate 或 Azure 逻辑应用中使用 IoT Central 连接器。
author: dominicbetts
ms.author: dobett
ms.date: 05/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: e2018f4d6f8e0813892a43c66975961356333bff
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83663745"
---
# <a name="use-workflows-to-integrate-your-azure-iot-central-application-with-other-cloud-services"></a>使用工作流将 Azure IoT Central 应用程序与其他云服务相集成

本文适用于解决方案构建人员。

可以在 IoT Central 中创建规则，用于触发操作（例如发送电子邮件）来响应基于遥测数据的条件（例如设备温度超过阈值）。

适用于 Power Automate 和 Azure 逻辑应用的 IoT Central 连接器可让你在 IoT Central 中创建更高级的规则来自动执行操作：

- 当在 Azure IoT Central 应用中触发了某个规则时，该规则可以触发 Power Automate 或 Azure 逻辑应用中的工作流。 这些工作流可以运行其他云服务（例如 Office 365）或第三方服务中的操作。
- 另一云服务（例如 Office 365）中的事件可以触发 Power Automate 或 Azure 逻辑应用中的工作流。 这些工作流可以从你的 IoT Central 应用程序中运行操作或检索数据。

## <a name="trigger-a-workflow-from-a-rule"></a>从规则触发工作流

需要先在 IoT Central 应用程序中配置一个规则，然后才能触发 Power Automate 或 Azure 逻辑应用中的工作流。 有关详细信息，请参阅[在 Azure IoT Central 中配置规则和操作](./howto-configure-rules.md)。

若要将“Azure IoT Central - 预览版”连接器添加为 Power Automate 中的触发器，请执行以下操作：

1. 在 Power Automate 中选择“+ 创建”，然后选择“自定义”选项卡。 
1. 搜索 *IoT Central*，然后选择“Azure IoT Central - 预览版”连接器。
1. 在触发器列表中，选择“触发规则时(预览版)”。
1. 在“触发规则时”步骤中，选择你的 IoT Central 应用程序以及所用的规则。

若要将“Azure IoT Central - 预览版”连接器添加为 Azure 逻辑应用中的触发器，请执行以下操作：

1. 在“逻辑应用设计器”中，选择“空白逻辑应用”模板。 
1. 在设计器中，选择“自定义”选项卡。
1. 搜索 *IoT Central*，然后选择“Azure IoT Central - 预览版”连接器。
1. 在触发器列表中，选择“触发规则时(预览版)”。
1. 在“触发规则时”步骤中，选择你的 IoT Central 应用程序以及所用的规则。

:::image type="content" source="./media/howto-configure-rules-advanced/triggers.png" alt-text="找到“Azure IoT Central - 预览版”连接器并选择触发器":::

现在，可以向工作流添加更多步骤来构建集成方案。

## <a name="run-an-action"></a>运行操作

可以通过 Power Automate 和 Azure 逻辑应用工作流运行 IoT Central 应用程序中的操作。 首先，请创建工作流，并使用连接器来定义用于启动该工作流的触发器。 然后，将“Azure IoT Central - 预览版”连接器用作操作。

若要将“Azure IoT Central - 预览版”连接器添加为 Power Automate 中的操作，请执行以下操作：

1. 在 Power Automate 的“选择操作”面板中，选择“自定义”选项卡。 
1. 搜索 *IoT Central*，然后选择“Azure IoT Central - 预览版”连接器。
1. 在操作列表中，选择要使用的 IoT Central 操作。
1. 在操作步骤中，完成所选操作的配置。 再选择“保存”。

若要将“Azure IoT Central - 预览版”连接器添加为 Azure 逻辑应用中的操作，请执行以下操作：

1. 在“逻辑应用设计器”的“选择操作”面板中，选择“自定义”选项卡。  
1. 搜索 *IoT Central*，然后选择“Azure IoT Central - 预览版”连接器。
1. 在操作列表中，选择要使用的 IoT Central 操作。
1. 在操作步骤中，完成所选操作的配置。 再选择“保存”。

:::image type="content" source="./media/howto-configure-rules-advanced/actions.png" alt-text="找到“Azure IoT Central - 预览版”连接器并选择操作":::

## <a name="list-of-actions"></a>操作列表

以下列表显示了“Azure IoT Central - 预览版”连接器中的所有可用 IoT Central 操作及其配置选项。 许多字段可以包含动态生成的内容。 例如，前一步骤可能确定了当前步骤处理的设备 ID。

### <a name="create-or-update-a-device"></a>创建或更新设备

使用此操作可在 IoT Central 应用程序中创建或更新设备。

| 字段 | 说明 |
| ----- | ----------- |
| 应用程序 | 从 IoT Central 应用程序列表中选择。 |
| 设备 | 要创建或更新的设备的唯一 ID。 |
| 已批准 | 选择是否已批准该设备连接到 IoT Central。 |
| 设备说明 | 设备的详细说明。 |
| 设备名称 | 设备的显示名称。 |
| 设备模板 | 从 IoT Central 应用程序的设备模板列表中选择。 |
| 模拟 | 选择设备是否为模拟设备。 |

### <a name="delete-a-device"></a>删除设备

使用此操作可从 IoT Central 应用程序中删除设备。

| 字段 | 说明 |
| ----- | ----------- |
| 应用程序 | 从 IoT Central 应用程序列表中选择。 |
| 设备 | 要删除的设备的唯一 ID。 |

### <a name="execute-a-device-command"></a>执行设备命令

使用此操作可执行某个设备接口中定义的命令。

| 字段 | 说明 |
| ----- | ----------- |
| 应用程序 | 从 IoT Central 应用程序列表中选择。 |
| 设备 | 要删除的设备的唯一 ID。 |
| 设备组件 | 设备模板中包含命令的接口。 |
| 设备命令 | 选择所选接口中的命令之一。 |
| 设备模板 | 从 IoT Central 应用程序的设备模板列表中选择。 |
| 设备命令请求有效负载 | 如果命令需要请求有效负载，请在此处添加。  |

> [!NOTE]
> 只有在选择设备模板之后，才能选择设备组件。

### <a name="get-a-device-by-id"></a>按 ID 获取设备

使用此操作可检索设备的详细信息。

| 字段 | 说明 |
| ----- | ----------- |
| 应用程序 | 从 IoT Central 应用程序列表中选择。 |
| 设备 | 要删除的设备的唯一 ID。 |

可在其他操作的动态表达式中使用返回的详细信息。 返回的设备详细信息包括：“已批准”、“主体”、“设备说明”、“设备名称”、“设备模板”、“已预配”和“模拟”。      

### <a name="get-device-cloud-properties"></a>获取设备云属性

使用此操作可检索特定设备的云属性值。

| 字段 | 说明 |
| ----- | ----------- |
| 应用程序 | 从 IoT Central 应用程序列表中选择。 |
| 设备 | 要删除的设备的唯一 ID。 |
| 设备模板 | 从 IoT Central 应用程序的设备模板列表中选择。 |

可以在其他操作的动态表达式中使用返回的云属性值。

### <a name="get-device-properties"></a>获取设备属性

使用此操作可检索特定设备的属性值。

| 字段 | 说明 |
| ----- | ----------- |
| 应用程序 | 从 IoT Central 应用程序列表中选择。 |
| 设备 | 要删除的设备的唯一 ID。 |
| 设备模板 | 从 IoT Central 应用程序的设备模板列表中选择。 |

可以在其他操作的动态表达式中使用返回的属性值。

### <a name="get-device-telemetry-value"></a>获取设备遥测值

使用此操作可检索特定设备的遥测值。

| 字段 | 说明 |
| ----- | ----------- |
| 应用程序 | 从 IoT Central 应用程序列表中选择。 |
| 设备 | 要删除的设备的唯一 ID。 |
| 设备模板 | 从 IoT Central 应用程序的设备模板列表中选择。 |

可以在其他操作的动态表达式中使用返回的遥测值。

### <a name="update-device-cloud-properties"></a>更新设备云属性

使用此操作可更新特定设备的云属性值。

| 字段 | 说明 |
| ----- | ----------- |
| 应用程序 | 从 IoT Central 应用程序列表中选择。 |
| 设备 | 要删除的设备的唯一 ID。 |
| 设备模板 | 从 IoT Central 应用程序的设备模板列表中选择。 |
| 云属性 | 选择设备模板之后，将为模板中定义的每个云属性添加一个字段。 |

### <a name="update-device-properties"></a>更新设备属性

使用此操作可更新特定设备的可写属性值。

| 字段 | 说明 |
| ----- | ----------- |
| 应用程序 | 从 IoT Central 应用程序列表中选择。 |
| 设备 | 要删除的设备的唯一 ID。 |
| 设备模板 | 从 IoT Central 应用程序的设备模板列表中选择。 |
| 可写属性 | 选择设备模板之后，将为模板中定义的每个可写属性添加一个字段。 |

## <a name="next-steps"></a>后续步骤

现在你已了解如何在 Azure IoT Central 应用程序中创建高级规则，接下来可以了解如何[在 Azure IoT Central 应用程序中分析设备数据](howto-create-analytics.md)
