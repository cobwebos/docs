---
title: 在 Azure IoT Central 应用程序中创建并运行作业 | Microsoft Docs
description: Azure IoT Central 作业允许使用大容量设备管理功能，例如更新属性或运行命令。
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 06/08/2020
ms.topic: how-to
ms.openlocfilehash: 406881a9131aae35b91dcab248745bb426cecf0e
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88797830"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>在 Azure IoT Central 应用程序中创建并运行作业

你可以使用 Microsoft Azure IoT Central 通过作业大规模管理连接设备。 使用这些作业可以批量更新设备属性并运行命令。 本文介绍如何在自己的应用程序中开始使用作业。

## <a name="create-and-run-a-job"></a>创建并运行作业

本部分介绍如何以设置逻辑网关设备组的光源阈值的形式创建和运行作业。

1. 在左侧窗格中，选择 " **作业**"。

2. 选择“+ 新建”。

   ![显示用于创建作业的选项的屏幕截图。](./media/howto-run-a-job/create-new-job.png)

3. 输入名称和描述以标识要创建的作业。

4. 选择要将作业应用到的目标设备组。 可以在 " **摘要** " 部分中查看作业配置适用的设备数。

5. 选择 " **云属性**"、" **属性**" 或 " **命令** " 作为要配置的作业类型。 

   若要设置 **属性** 作业配置，请选择属性并设置其新值。 若要设置 **命令** 作业配置，请选择要运行的命令。 属性作业可以设置多个属性。

   ![显示用于创建名为 "设置光源阈值" 的属性作业的选项的屏幕截图。](./media/howto-run-a-job/configure-job.png)

6. 选择 " **运行** " 或 " **保存**"。 该作业随即显示在“作业”主页上。**** 在此页上，可以看到当前正在运行的作业，以及以前运行的或保存的作业的历史记录。 你可以随时重新打开保存的作业，以便继续编辑或运行它。

   ![显示已创建作业的名称、状态和说明的屏幕截图。](./media/howto-run-a-job/view-job.png)

   > [!NOTE]
   > 你可以查看以前运行的作业的30天历史记录。

7. 选择保存的作业并通过选择 " **运行** " 按钮运行它。 

   此时将显示 " **运行你的作业？** " 对话框。 通过选择 " **运行作业** " 按钮进行确认。 

   ![确认你要运行作业的对话框屏幕截图。](./media/howto-run-a-job/run-job.png)

8. 作业经历了挂起、运行和完成的各个阶段。 作业执行详细信息包含结果指标、持续时间详细信息和设备列表网格。 

   从此概述中，你还可以选择 " **结果日志** " 以下载作业详细信息的 CSV 文件，包括设备及其状态值。 这些信息可用于故障排除。

   ![显示设备状态的屏幕截图。](./media/howto-run-a-job/download-details.png)

## <a name="manage-jobs"></a>管理作业

若要停止正在运行的作业，请将其打开，然后选择 " **停止**"。 作业状态将更改，以反映作业已停止。 " **摘要** " 部分显示已完成、已失败或仍处于挂起状态的设备。

![显示正在运行的作业和停止作业的按钮的屏幕截图。](./media/howto-run-a-job/manage-job.png)

作业处于停止状态后，你可以选择 " **继续** " 以继续运行作业。 作业状态将更改，以反映作业现在正在运行。 " **摘要** " 部分继续更新为最新进度。

![显示已停止的作业和用于继续作业的按钮的屏幕截图。](./media/howto-run-a-job/stopped-job.png)

## <a name="copy-a-job"></a>复制作业

若要复制某个现有作业，请在 " **作业** " 页上选择该作业，然后选择 " **作业详细信息**"。 此时将显示 " **作业详细信息** " 页。 

![显示作业详细信息页的屏幕截图。](./media/howto-run-a-job/job-details.png)

选择“复制”。****

![显示 "复制" 按钮的屏幕截图。](./media/howto-run-a-job/job-details-copy.png)

此时会打开一个作业配置副本供你编辑，并且 **复制** 将追加到作业名称。 可以保存或运行新作业。

![显示作业配置副本的屏幕截图。](./media/howto-run-a-job/copy-job.png)

## <a name="view-job-status"></a>查看作业状态

创建作业后，" **状态** " 列将更新为该作业的最新状态消息。 下表列出了可能的作业状态值：

| 状态消息       | 状态的含义                                          |
| -------------------- | ------------------------------------------------------- |
| 已完成            | 此作业在所有设备上运行。              |
| 失败               | 此作业失败，并且未在设备上完全运行。  |
| 挂起的              | 此作业尚未开始在设备上运行。         |
| 运行              | 此作业当前正在设备上运行。             |
| 已停止              | 用户已手动停止此作业。           |

状态消息后面是作业中设备的概述。 下表列出了可能的设备状态值：

| 状态消息       | 状态的含义                                                     |
| -------------------- | ------------------------------------------------------------------ |
| 已成功            | 已成功运行作业的设备数。       |
| 失败               | 作业无法在其上运行的设备的数量。       |

若要查看作业和所有受影响的设备的状态，请打开 "作业"。 每个设备名称的旁边显示了以下状态消息之一：

| 状态消息       | 状态的含义                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| 已完成            | 此设备上运行的作业。                                     |
| 失败               | 作业无法在此设备上运行。 错误消息中显示了更多信息。  |
| 挂起的              | 作业尚未在此设备上运行。                                   |

若要下载包含作业详细信息和设备列表及其状态值的 CSV 文件，请选择 " **下载**"。

## <a name="filter-the-device-list"></a>筛选设备列表

您可以通过选择 "筛选器" 图标来筛选 " **作业详细信息** " 页上的设备列表。 可以在 " **设备 ID** " 或 " **状态** " 字段中进行筛选。

:::image type="content" source="media/howto-run-a-job/filter.png" alt-text="显示用于筛选设备列表的选项的屏幕截图。":::

## <a name="customize-columns-in-the-device-list"></a>自定义设备列表中的列

可以通过选择 "列选项" 图标，选择要在设备列表中显示的其他列。

:::image type="content" source="media/howto-run-a-job/column-options.png" alt-text="显示列选项图标的屏幕截图。":::

利用对话框，您可以选择要显示在设备列表中的列。 选择要显示的列，选择向右箭头，然后选择 **"确定"**。 若要选择所有可用列，请选中 " **全选**"。

:::image type="content" source="media/howto-run-a-job/column-picker-popup.png" alt-text="显示用于选择要显示的列的对话框的屏幕截图。":::

选定的列将显示在设备列表中。

:::image type="content" source="media/howto-run-a-job/column-picker-column-selected.png" alt-text="在设备列表中显示所选列的屏幕截图。":::

选定的列将在用户会话中或在有权访问应用程序的用户会话之间保持。

## <a name="rerun-jobs"></a>重新运行作业

你可以重新运行具有失败设备的作业。 **失败时选择 "重新运行"**。

:::image type="content" source="media/howto-run-a-job/rerun.png" alt-text="屏幕截图，显示用于在失败设备上重新运行作业的按钮。":::

输入 "作业名称" 和 "说明"，然后选择 " **重新运行作业**"。 提交新作业，以重试对失败设备的操作。

:::image type="content" source="media/howto-run-a-job/rerun-failed.png" alt-text="显示重新运行失败的设备的对话框的屏幕截图。":::

> [!NOTE]
> 你不能从 Azure IoT Central 应用程序同时运行五个以上的作业。
>
> 当作业完成并且删除作业的设备列表中的设备时，设备名称中的设备条目将显示为 "已删除"。 "详细信息" 链接对已删除的设备不可用。

## <a name="next-steps"></a>后续步骤

了解如何在 Azure IoT Central 应用程序中创建作业后，接下来请学习：

- [管理设备](howto-manage-devices.md)
- [对设备模板进行版本控制](howto-version-device-template.md)
