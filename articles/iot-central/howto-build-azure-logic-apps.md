---
title: 使用 Azure 逻辑应用中的 IoT Central 连接器生成工作流 | Microsoft Docs
description: 使用 Azure 逻辑应用中的 IoT Central 连接器触发工作流并在工作流中创建、更新和删除设备。
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 1/3/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 971c01ec8002e73dbc6abfb66a0ede26f90a4d9c
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2019
ms.locfileid: "54453204"
---
# <a name="build-workflows-with-the-iot-central-connector-in-azure-logic-apps"></a>使用 Azure 逻辑应用中的 IoT Central 连接器生成工作流

本主题适用于构建者和管理员。

使用 Azure 逻辑应用生成可缩放的自动化工作流，以便跨云服务和本地系统集成应用和数据。 Azure 逻辑应用在许多 Azure 服务、Microsoft 服务和其他服务型软件 (SaaS) 产品中有许多连接器。 如果使用 Azure 逻辑应用中的 IoT Central 连接器，则当规则在 IoT Central 中触发时，就可以触发工作流。 也可以使用 IoT Central 连接器中的操作来创建设备、更新设备的属性和设置，或者删除设备。 

可以使用 Microsoft Flow 中的 IoT Central 连接器。 Azure 逻辑应用和 Microsoft Flow 都是设计器优先的集成服务，但目标受众略有不同。 Flow 允许任何办公室工作人员生成他们需要的业务工作流。 逻辑应用则允许 IT 专业人员生成连接数据所需的集成。 [此处](https://docs.microsoft.com/azure/azure-functions/functions-compare-logic-apps-ms-flow-webjobs)对 Flow 和逻辑应用进行了比较。 可从[此处](howto-add-microsoft-flow.md)了解如何使用 Microsoft Flow 中的 IoT Central 连接器生成工作流。 

## <a name="prerequisites"></a>先决条件

- 即用即付应用程序
- Azure 帐户和订阅，用于创建和管理逻辑应用

## <a name="trigger-a-workflow-when-a-rule-is-triggered"></a>在触发某个规则时触发一个工作流

本部分介绍如何在触发规则时向 Microsoft Teams 发布消息。 可以将工作流配置为使用其他连接器执行诸如向事件中心发送事件、创建新的 Azure DevOps 工作项或在 SQL Server 中插入新行等操作。

1. 首先[在 IoT Central 中创建规则](howto-create-telemetry-rules.md)。 保存规则条件后，单击“Azure 逻辑应用”磁贴作为新操作。 单击“在 Azure 门户中创建”。 随即转到 Azure 门户以创建新的逻辑应用。 你可能需要登录 Azure 帐户。

1. 输入创建新逻辑应用所需的信息。 可以选择要将新逻辑应用预配到的 Azure 订阅。 它不必是创建 IoT Central 应用时所在的订阅。 单击“创建”。

    ![在 Azure 门户创建逻辑应用](./media/howto-build-azure-logic-apps/createinazureportal.PNG)

1. 成功创建逻辑应用后，将自动导航到逻辑应用设计器。 单击“空白逻辑应用”。 

    ![创建空白逻辑应用](./media/howto-build-azure-logic-apps/blanklogicapp.PNG)

1. 在设计器中，搜索“iot central”，并选择“在触发规则时”触发器。 使用登录 IoT Central 应用时所用的帐户登录连接器。 

    ![登录 IoT Central 连接器](./media/howto-build-azure-logic-apps/addtrigger.PNG)

1. 登录成功后，应该会出现一些字段。 从下拉列表中选择“应用程序”和“规则”。

    ![选择应用程序和规则](./media/howto-build-azure-logic-apps/pickappandrule.PNG)

1. 添加新操作。 搜索“发布消息团队”，并从 Microsoft Teams 连接器中选择“发布消息”。 使用在 Microsoft Teams 中所用的帐户登录连接器。 

1. 在操作中，选择“团队”和“通道”。 在“消息”字段中填写希望每条消息所要表述的内容。 可以包括 IoT Central 规则提供的动态内容，将设备名称和时间戳等重要信息传递给通知。
    > [!NOTE]
    > 单击“动态内容”窗口中的“查看更多”文本，获取触发了规则的度量和属性值。

    ![逻辑应用编辑操作，此时动态窗格处于打开状态](./media/howto-build-azure-logic-apps/buildworkflow.PNG)

1. 将操作编辑完以后，请单击“保存”。

1. 如果回到 IoT Central 应用，则会看到此规则在“操作”区域下有一个 Azure 逻辑应用操作。

随时可以在 Azure 门户中使用逻辑应用中的 IoT Central 连接器开始生成工作流。 然后可以选择要连接到的 IoT Central 应用和规则，它仍将以相同的方式工作。 无需每次都从 IoT Central 规则页面开始。

## <a name="create-update-and-delete-a-device-in-a-workflow"></a>在工作流中创建、更新和删除设备

在逻辑应用中生成工作流时，可以使用 IoT Central 连接器添加操作。 你将找到“创建设备”、“更新设备”和“删除设备”。
> [!NOTE]
> 对于“更新设备”和“删除设备”，需要提供要更新或删除的现有设备的 ID。 可以在浏览器 URL 中获得 IoT Central 设备的 ID。 这与资源管理器页面上列出的设备 ID **不**同。

![IoT Central Device Explorer 设备 ID](./media/howto-build-azure-logic-apps/iotcdeviceid.PNG)
  

## <a name="next-steps"></a>后续步骤
了解如何使用 Microsoft Flow 来生成工作流以后，建议完成下一步：[管理设备](howto-manage-devices.md)。
