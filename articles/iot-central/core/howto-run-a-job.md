---
title: 在 Azure IoT Central 应用程序中创建和运行作业 |Microsoft Docs
description: Azure IoT Central 作业允许批量设备管理功能，例如更新设备属性、设置或执行命令。
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 07/08/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 114946fa37ae161aeb2efd5b7cd50444c5df4c2b
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906709"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>在 Azure IoT Central 应用程序中创建并运行作业

你可以使用 Microsoft Azure IoT Central 通过作业大规模管理连接设备。 作业允许你对设备属性和命令进行批量更新。 本文介绍如何在自己的应用程序中开始使用作业。


## <a name="create-and-run-a-job"></a>创建并运行作业

本部分说明如何创建和运行作业。 其中介绍了如何提高多个 refrigerated 自动售货机机的风扇速度。

1. 从导航窗格中导航到 "作业"。

2. 选择 " **+ 新建**" 以创建新作业。

    ![创建新作业](./media/howto-run-a-job/createnewjob.png)

3. 输入名称和描述以标识要创建的作业。

4. 选择要将作业应用到的设备组。 你可以在 "摘要" 部分中看到你的作业配置将应用到的设备的数量。 

5. 接下来，选择要定义的作业类型（属性或命令）。 通过选择属性并设置新值或选择命令来设置作业配置。 一次可以添加多个属性。

    ![配置作业](./media/howto-run-a-job/configurejob.png)

6. 选择设备后，选择 "**运行**" 或 "**保存**"。 作业现在会显示在 "主**作业**" 页上。 在此视图中，可以看到当前正在运行的作业和任何以前运行的作业的历史记录。 正在运行的作业始终显示在列表的顶部。 你保存的作业可以随时打开以继续编辑或运行。

    ![查看作业](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > 你可以查看以前运行的作业的历史记录最多30天。

7. 若要获取作业的概述，请从列表中选择要查看的作业。 此概述包含作业详细信息、设备和设备状态值。 在此概述中，你还可以选择 "**下载作业详细信息**" 以下载作业详细信息的 .csv 文件，包括设备及其状态值。 此信息可用于故障排除。

    ![查看设备状态](./media/howto-run-a-job/downloaddetails.png)

### <a name="stop-a-running-job"></a>停止正在运行的作业

若要停止正在运行的作业，请选择它，然后选择 "**停止**"。 作业状态将更改，以反映作业已停止。

   ![停止作业](./media/howto-run-a-job/stopjob.png)

### <a name="run-a-stopped-job"></a>运行已停止的作业

若要运行当前已停止的作业，请选择已停止的作业。 选择面板上的 "**运行**"。 作业状态将更改，以反映作业现在正在运行。

   ![恢复作业](./media/howto-run-a-job/resumejob.png)

## <a name="copy-a-job"></a>复制作业

若要复制已创建的现有作业，请打开已创建的作业，然后选择 "**复制**"。 此时会打开一个新的作业配置副本供你编辑。 您可以保存或运行新作业。 

   ![复制作业](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>查看作业状态

在创建作业后，"**状态**" 列将更新作业的最新状态消息。 下表列出了可能的状态值：

| 状态消息       | 状态表示                                          |
| -------------------- | ------------------------------------------------------- |
| 已完成            | 已在所有设备上执行此作业。              |
| 已失败               | 此作业已失败，并且未在设备上完全执行。  |
| 未              | 此作业尚未开始在设备上执行。         |
| 正在运行              | 此作业当前正在设备上执行。             |
| 已停止              | 此作业已由用户手动停止。           |

状态消息后跟作业中设备的概述。 下表列出了可能的设备状态值：

| 状态消息       | 状态表示                                                     |
| -------------------- | ------------------------------------------------------------------ |
| 成功            | 成功执行该作业的设备数。       |
| 已失败               | 作业无法在其上执行的设备的数量。       |

### <a name="view-the-device-status"></a>查看设备状态

若要查看作业和所有受影响的设备的状态，请选择该作业。 若要下载包含作业详细信息的 .csv 文件，包括设备列表及其状态值，请选择 "**下载作业详细信息**"。 在每个设备名称旁边，你会看到以下状态消息之一：

| 状态消息       | 状态表示                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| 已完成            | 已在此设备上执行作业。                                     |
| 已失败               | 作业无法在此设备上执行。 错误消息显示了详细信息。  |
| 未              | 作业尚未在此设备上执行。                                   |

> [!NOTE]
> 如果已删除设备，则无法选择该设备，并将其显示为 "已删除"，并显示设备 ID。

## <a name="next-steps"></a>后续步骤

现在，你已了解如何在 Azure IoT Central 应用程序中创建作业，以下是一些后续步骤：

- [使用设备集](howto-use-device-sets.md)
- [管理设备](howto-manage-devices.md)
- [版本设备模板](howto-version-device-template.md)
