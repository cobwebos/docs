---
title: 在 Azure IoT Central 应用程序中创建并运行作业 | Microsoft Docs
description: Azure IoT Central 作业允许批量设备管理功能，例如更新属性或执行命令。
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 06/08/2020
ms.topic: how-to
ms.openlocfilehash: dec9abc38bc0354ef3d22994a7988bfb006f5769
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84609664"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>在 Azure IoT Central 应用程序中创建并运行作业

可以使用 Microsoft Azure IoT Central 通过作业大规模地管理连接的设备。 使用这些作业可以批量更新设备属性并运行命令。 本文介绍如何在自己的应用程序中开始使用作业。

## <a name="create-and-run-a-job"></a>创建并运行作业

此部分介绍如何创建并运行作业。 它介绍了如何为一组逻辑网关设备设置光阈。

1. 从左窗格导航到“作业”。 

2. 选择“+ 新建”以创建新作业： 

    ![新建作业](./media/howto-run-a-job/create-new-job.png)

3. 输入用于标识所要创建的作业的名称和说明。

4. 选择要将作业应用到的目标设备组。 可以在“摘要”  部分查看作业配置适用的设备的数量。

5. 接下来，选择 "**云属性**"、"**属性**" 或 "**命令**" 作为要配置的作业的类型。 若要设置“属性”  作业配置，请选择一个属性并设置其新值。 若要设置一个“命令”  ，请选择要运行的命令。 一个属性作业可以设置多个属性：

    ![配置作业](./media/howto-run-a-job/configure-job.png)

6. 创建作业后，请选择“运行”或“保存”。   该作业随即显示在“作业”主页上。  在此页面上，可以看到当前正在运行的作业，以及以往运行或保存的任意作业的历史记录。 随时可以再次打开已保存的作业，以继续编辑或运行它：

    ![查看作业](./media/howto-run-a-job/view-job.png)

    > [!NOTE]
    > 对于以前运行的作业，可以查看长达 30 天的历史记录。

7. 若要获取作业的概述，请从列表中选择要查看的作业。 此概述包含作业详细信息、设备和设备状态值。 在此概览中，还可以选择“下载作业详细信息”以下载作业详细信息 CSV 文件，其中包含设备及其状态值。  这些信息可用于故障排除：

    ![查看设备状态](./media/howto-run-a-job/download-details.png)

## <a name="manage-jobs"></a>管理作业

若要停止正在运行的作业之一，请将其打开，然后选择“停止”  。 作业状态将会更改，以反映该作业已停止。 “摘要”  部分显示已完成、已失败或仍处于挂起状态的设备。

若要运行当前已停止的作业，请选择该作业，然后选择“运行”  。 作业状态将会更改，以反映该作业现已重新运行。  “摘要”部分会继续更新并获得最新进度。

![管理作业](./media/howto-run-a-job/manage-job.png)

## <a name="copy-a-job"></a>复制作业

若要复制现有作业之一，请在“作业”  页上选择该作业，然后选择“复制”  。 此时会打开一个作业配置副本供你编辑，并会将 **Copy** 追加到作业名称。 可以保存或运行新作业：

![复制作业](./media/howto-run-a-job/copy-job.png)

## <a name="view-job-status"></a>查看作业状态

创建作业后，“状态”列将会更新，包含作业的最新状态消息。  下表列出了可能的状态值：

| 状态消息       | 状态的含义                                          |
| -------------------- | ------------------------------------------------------- |
| 已完成            | 此作业已在所有设备上执行。              |
| 失败               | 此作业失败且未在设备上完全执行。  |
| 挂起的              | 此作业尚未在设备上开始执行。         |
| 正在运行              | 此作业当前正在设备上执行。             |
| 已停止              | 此作业已由用户手动停止。           |

状态消息后面是作业中设备的概述。 下表列出了可能的设备状态值：

| 状态消息       | 状态的含义                                                     |
| -------------------- | ------------------------------------------------------------------ |
| 已成功            | 已成功执行该作业的设备数。       |
| 失败               | 执行作业失败的设备数。       |

### <a name="view-the-device-status-values"></a>查看设备状态值

若要查看作业的状态和所有受影响设备，请打开该作业。 每个设备名称的旁边显示了以下状态消息之一：

| 状态消息       | 状态的含义                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| 完成            | 此设备上执行的作业。                                     |
| 已失败               | 作业无法在此设备上执行。 错误消息中显示了更多信息。  |
| 挂起              | 作业尚未在此设备上执行。                                   |

若要下载包含作业详细信息和设备列表及其状态值的 CSV 文件，请选择 "**下载**"。

### <a name="filter-the-list-of-devices"></a>筛选设备列表

您可以通过选择 "筛选器" 图标来筛选 "作业详细信息" 页上的设备列表。 可以在 "**设备 ID** " 或 "**状态**" 字段上进行筛选：

:::image type="content" source="media/howto-run-a-job/filter.png" alt-text="筛选设备列表":::

### <a name="customize-columns-in-the-device-list"></a>自定义设备列表中的列

可以通过选择 "列选项" 图标，选择要在设备列表中显示的其他列：

:::image type="content" source="media/howto-run-a-job/column-options.png" alt-text="列选项":::

你将看到一个对话框，可通过该对话框选择要在设备列表中显示的列。 选择要显示的列，选择右箭头图标，然后选择 **"确定"**。 若要选择所有可用列，请选中 "**全选**"：

:::image type="content" source="media/howto-run-a-job/column-picker-popup.png" alt-text="列选取器对话框":::

选定的列将显示在设备列表中：

:::image type="content" source="media/howto-run-a-job/column-picker-column-selected.png" alt-text="选择列":::

选定的列将在用户会话中或在有权访问应用程序的用户会话之间保持。

## <a name="rerun-jobs"></a>重新运行作业

你可以重新运行具有失败设备的作业。 选择 "**重新运行**"：

:::image type="content" source="media/howto-run-a-job/rerun.png" alt-text="重新运行作业":::

输入 "作业名称" 和 "说明"，然后选择 "**重新运行作业**"。 提交新作业以重试对失败设备的操作：

:::image type="content" source="media/howto-run-a-job/rerun-failed.png" alt-text="重新运行失败的设备":::

> [!NOTE]
> 不能从 IoT Central 应用程序同时执行五个以上的作业。

> [!NOTE]
> 当作业完成并且您删除作业的设备列表中的设备时，设备名称和设备详细信息链接中的设备条目将显示为 "已删除"。

## <a name="next-steps"></a>后续步骤

了解如何在 Azure IoT Central 应用程序中创建作业后，接下来请学习：

- [管理设备](howto-manage-devices.md)
- [对设备模板进行版本控制](howto-version-device-template.md)
