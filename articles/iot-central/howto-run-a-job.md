---
title: 在 Azure IoT Central 应用程序中创建并运行作业 | Microsoft Docs
description: Azure IoT Central 作业允许批量设备管理功能，例如更新设备属性、设置或执行命令。
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 09/15/2018
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: ac5accc72369d811c0d36c4ef64cd8d523a061f3
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2018
ms.locfileid: "52724496"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>在 Azure IoT Central 应用程序中创建并运行作业

可以使用 Microsoft Azure IoT Central 通过作业大规模地管理连接的设备。 作业功能使你能够对设备属性、设置和命令执行批量更新。 本文将引导你了解如何开始在自己的应用程序中使用作业。

## <a name="create-and-run-a-job"></a>创建并运行作业

此部分介绍如何创建并运行作业。 每个步骤都通过一个示例，演示如何为需要提高风扇速度的冷藏自动售货机设备运行作业。

1. 从导航窗格中，导航到“作业”。

1. 单击“+ 新建”以便开始创建新的作业。

    ![新建作业](./media/howto-run-a-job/createnewjob.png)

1. 输入帮助标识要创建的作业的名称和说明。

1. 选择要将作业应用到的设备集。 选择设备集后，将看到右侧填充所选设备集内的设备。 如果选择中断的设备集，则不会显示任何设备，你将看到一条消息，说明设备集已中断。

1. 接下来，选择将定义的作业类型（设置、属性或命令）。 单击所选作业类型旁的 **+** 并添加所需的操作。

    ![配置作业](./media/howto-run-a-job/configurejob.png)

1. 在右侧，选择要运行作业的设备。 通过单击顶部的复选框，可在整个设备集中选择所有设备。 通过单击“名称”旁的复选框，可选择当前页上的所有设备。

1. 一旦选择了所需的设备，请选择“运行”。 该作业现在将显示在主要的“作业”页上。 在此视图上，能够看到当前正在运行的作业和任何以前运行的作业的历史记录。 正在运行的作业始终会显示在列表顶部。

    ![运行作业](./media/howto-run-a-job/runjob.png)

    ![查看作业](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > 你将能够查看最多 30 天以前运行作业的历史记录。

1. 若要获取作业的概述，请从列表中单击要查看的作业名称。 本概述包含作业详细信息、设备和设备状态。

    ![查看设备状态](./media/howto-run-a-job/viewdevicestatus.png)

### <a name="stop-a-running-job"></a>停止正在运行的作业

如果要停止当前正在运行的作业，单击要停止的正在运行的作业名称。 选择面板上的“停止”按钮。 你将看到作业状态已更改以反映作业已停止。

   ![停止作业](./media/howto-run-a-job/stopjob.png)

### <a name="run-a-stopped-job"></a>运行已停止的作业

如果要运行当前已停止的作业，单击要运行的已停止作业的名称。 选择面板上的“运行”按钮。 可看到作业状态已更改以反映作业现在已重新运行。

   ![已恢复的作业](./media/howto-run-a-job/resumejob.png)

## <a name="view-the-job-status"></a>查看作业状态

一旦创建作业后，“状态”列将使用作业的最新状态消息进行更新。 状态消息表示以下含义：

| 状态消息       | 状态的含义                                          |
| -------------------- | ------------------------------------------------------- |
| 已完成            | 此作业已在所有设备上执行。              |
| 已失败               | 此作业失败且未在设备上完全执行。  |
| 挂起的              | 此作业尚未开始在设备上执行。        |
| 正在运行              | 此作业当前正在设备上执行。             |
| 已停止              | 此作业已由用户手动停止。           |

状态消息后面是作业中设备的概述。 这些设备状态表示以下含义：

| 状态消息       | 状态的含义                                                     |
| -------------------- | ------------------------------------------------------------------ |
| 已成功            | 作业已成功执行的设备数。  |
| 已失败               | 作业执行失败的设备数。      |

### <a name="view-the-device-status"></a>查看设备状态

若要查看作业中每个设备的状态，请单击作业名称。 在此，将看到作业及所有属于此特定作业一部分的设备的详细信息。 每个设备名称旁边会看到以下状态消息之一：

| 状态消息       | 状态的含义                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| 已完成            | 作业已在此设备上执行。                                     |
| 已失败               | 作业在此设备上执行失败。 随附的错误消息将显示更多信息。  |
| 挂起的              | 作业尚未在此设备上执行。                                  |

> [!NOTE]
> 如果设备已删除，则将无法选择设备，并且它将显示为已删除，包含设备 ID。

## <a name="next-steps"></a>后续步骤

了解如何在 Azure IoT Central 应用程序中创建作业后，可了解如下后续步骤：

- [使用设备集](howto-use-device-sets.md)
- [管理设备](howto-manage-devices.md)
- [对设备模板进行版本控制](howto-version-devicetemplate.md)
