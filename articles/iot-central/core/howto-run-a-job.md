---
title: 在 Azure IoT Central 应用程序中创建并运行作业 | Microsoft Docs
description: Azure IoT Central 作业允许使用大容量设备管理功能，例如更新属性或运行命令。
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 09/30/2020
ms.topic: how-to
ms.openlocfilehash: 5b8aba74cb0914cf26382e0d17a8ce2ba6bd4063
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91573936"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>在 Azure IoT Central 应用程序中创建并运行作业

可以使用 Azure IoT Central 通过作业大规模管理连接设备。 作业允许对设备和云属性进行批量更新，并运行命令。 本文介绍如何在自己的应用程序中开始使用作业。

## <a name="create-and-run-a-job"></a>创建并运行作业

下面的示例演示如何创建和运行作业，以便为一组逻辑网关设备设置光源阈值。 使用作业向导创建和运行作业。 你可以保存作业以便稍后运行。

1. 在左侧窗格中，选择 " **作业**"。

1. 选择 " **+ 新建作业**"。

1. 在 " **配置作业** " 页上，输入名称和描述以标识要创建的作业。

1. 选择要将作业应用到的目标设备组。 你可以在 **设备组** 选择下查看作业配置适用于的设备数。

1. 选择 " **云属性**"、" **属性**" 或 " **命令** " 作为 **作业类型**：

    若要配置 **属性** 作业，请选择属性并设置其新值。 若要配置 **命令** 作业，请选择要运行的命令。 属性作业可以设置多个属性。

    :::image type="content" source="media/howto-run-a-job/configure-job.png" alt-text="显示用于创建名为 &quot;设置光源阈值&quot; 的属性作业的选项的屏幕截图&quot;:::

    选择 &quot; **保存并退出** &quot;，将作业添加到 &quot; **作业** &quot; 页上保存的作业列表中。 稍后可以从已保存作业列表返回到作业。

    选择 &quot; **下一步** &quot; 转到 &quot; **传递选项** &quot; 页。 &quot; **传递选项** &quot; 页面允许您为此作业设置传递选项： &quot; **批处理** &quot; 和 &quot; **取消" 阈值**。

    批处理使你可以将大量设备的作业错开。 作业分为多个批处理，每个批处理包含设备的一个子集。 批处理已排队并按顺序运行。

    如果错误数超出设置的限制，则取消阈值允许您自动取消作业。 阈值可应用于作业中的所有设备，或应用于单个批次。

    :::image type="content" source="media/howto-run-a-job/job-wizard-delivery-options.png" alt-text="显示用于创建名为 &quot;设置光源阈值&quot; 的属性作业的选项的屏幕截图&quot;:::

    选择 &quot; **保存并退出** &quot;，将作业添加到 &quot; **作业** &quot; 页上保存的作业列表中。 稍后可以从已保存作业列表返回到作业。

    选择 &quot; **下一步** &quot; 转到 &quot; **传递选项** &quot; 页。 &quot; **传递选项** &quot; 页面允许您为此作业设置传递选项： &quot; **批处理** &quot; 和 &quot; **取消" 以提交作业。

    :::image type="content" source="media/howto-run-a-job/job-wizard-review.png" alt-text="显示用于创建名为 &quot;设置光源阈值&quot; 的属性作业的选项的屏幕截图&quot;:::

    选择 &quot; **保存并退出** &quot;，将作业添加到 &quot; **作业** &quot; 页上保存的作业列表中。 稍后可以从已保存作业列表返回到作业。

    选择 &quot; **下一步** &quot; 转到 &quot; **传递选项** &quot; 页。 &quot; **传递选项** &quot; 页面允许您为此作业设置传递选项： &quot; **批处理** &quot; 和 &quot; **取消" 以下载作业详细信息的 CSV 文件，包括设备及其状态值。 这些信息可用于故障排除。

    :::image type="content" source="media/howto-run-a-job/download-details.png" alt-text="显示用于创建名为 &quot;设置光源阈值&quot; 的属性作业的选项的屏幕截图&quot;:::

    选择 &quot; **保存并退出** &quot;，将作业添加到 &quot; **作业** &quot; 页上保存的作业列表中。 稍后可以从已保存作业列表返回到作业。

    选择 &quot; **下一步** &quot; 转到 &quot; **传递选项** &quot; 页。 &quot; **传递选项** &quot; 页面允许您为此作业设置传递选项： &quot; **批处理** &quot; 和 &quot; **取消" 列表中。 此页显示当前正在运行的作业，以及以前运行的或保存的作业的历史记录。

    > [!NOTE]
    > 你可以查看以前运行的作业的30天历史记录。

## <a name="manage-jobs"></a>管理作业

若要停止正在运行的作业，请将其打开，然后选择 " **停止**"。 作业状态将更改，以反映作业已停止。 " **摘要** " 部分显示已完成、已失败或仍处于挂起状态的设备。

:::image type="content" source="media/howto-run-a-job/manage-job.png" alt-text="显示用于创建名为 &quot;设置光源阈值&quot; 的属性作业的选项的屏幕截图&quot;:::

    选择 &quot; **保存并退出** &quot;，将作业添加到 &quot; **作业** &quot; 页上保存的作业列表中。 稍后可以从已保存作业列表返回到作业。

    选择 &quot; **下一步** &quot; 转到 &quot; **传递选项** &quot; 页。 &quot; **传递选项** &quot; 页面允许您为此作业设置传递选项： &quot; **批处理** &quot; 和 &quot; **取消" 部分继续更新并获得最新进度。

:::image type="content" source="media/howto-run-a-job/stopped-job.png" alt-text="显示用于创建名为 &quot;设置光源阈值&quot; 的属性作业的选项的屏幕截图&quot;:::

    选择 &quot; **保存并退出** &quot;，将作业添加到 &quot; **作业** &quot; 页上保存的作业列表中。 稍后可以从已保存作业列表返回到作业。

    选择 &quot; **下一步** &quot; 转到 &quot; **传递选项** &quot; 页。 &quot; **传递选项** &quot; 页面允许您为此作业设置传递选项： &quot; **批处理** &quot; 和 &quot; **取消":::

## <a name="copy-a-job"></a>复制作业

若要复制现有作业，请选择已执行的作业。 在 "作业结果" 页或 "作业详细信息" 页上选择 " **复制** "：

:::image type="content" source="media/howto-run-a-job/job-details-copy.png" alt-text="显示用于创建名为 &quot;设置光源阈值&quot; 的属性作业的选项的屏幕截图&quot;:::

    选择 &quot; **保存并退出** &quot;，将作业添加到 &quot; **作业** &quot; 页上保存的作业列表中。 稍后可以从已保存作业列表返回到作业。

    选择 &quot; **下一步** &quot; 转到 &quot; **传递选项** &quot; 页。 &quot; **传递选项** &quot; 页面允许您为此作业设置传递选项： &quot; **批处理** &quot; 和 &quot; **取消":::

此时会打开一个作业配置副本供你编辑，并且 **复制** 将追加到作业名称。

## <a name="view-job-status"></a>查看作业状态

创建作业后，" **状态** " 列将使用最新的作业状态消息进行更新。 下表列出了可能的 *作业状态* 值：

| 状态消息       | 状态的含义                                          |
| -------------------- | ------------------------------------------------------- |
| 已完成            | 此作业在所有设备上运行。              |
| Failed               | 此作业失败，并且未在设备上完全运行。  |
| 挂起              | 此作业尚未开始在设备上运行。         |
| 运行              | 此作业当前正在设备上运行。             |
| 已停止              | 用户已手动停止此作业。           |
| 已取消             | 此作业已取消，因为超出了 " **传递选项** " 页面上设置的阈值。 |

状态消息后面是作业中设备的概述。 下表列出了可能的 *设备状态* 值：

| 状态消息       | 状态的含义                                                     |
| -------------------- | ------------------------------------------------------------------ |
| 成功            | 已成功运行作业的设备数。       |
| Failed               | 作业无法在其上运行的设备的数量。       |

若要查看作业和所有受影响的设备的状态，请打开 "作业"。 每个设备名称的旁边显示了以下状态消息之一：

| 状态消息       | 状态的含义                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| 已完成            | 此设备上运行的作业。                                     |
| Failed               | 作业无法在此设备上运行。 错误消息中显示了更多信息。  |
| 挂起              | 作业尚未在此设备上运行。                                   |

若要下载包含作业详细信息和设备列表及其状态值的 CSV 文件，请选择 " **结果日志**"。

## <a name="filter-the-device-list"></a>筛选设备列表

您可以通过选择 "筛选器" 图标来筛选 " **作业详细信息** " 页上的设备列表。 可以在 " **设备 ID** " 或 " **状态** " 字段上进行筛选：

:::image type="content" source="media/howto-run-a-job/filter.png" alt-text="显示用于创建名为 &quot;设置光源阈值&quot; 的属性作业的选项的屏幕截图&quot;:::

    选择 &quot; **保存并退出** &quot;，将作业添加到 &quot; **作业** &quot; 页上保存的作业列表中。 稍后可以从已保存作业列表返回到作业。

    选择 &quot; **下一步** &quot; 转到 &quot; **传递选项** &quot; 页。 &quot; **传递选项** &quot; 页面允许您为此作业设置传递选项： &quot; **批处理** &quot; 和 &quot; **取消":::

## <a name="customize-columns-in-the-device-list"></a>自定义设备列表中的列

可以通过选择 "列选项" 图标，将列添加到设备列表：

:::image type="content" source="media/howto-run-a-job/column-options.png" alt-text="显示用于创建名为 &quot;设置光源阈值&quot; 的属性作业的选项的屏幕截图&quot;:::

    选择 &quot; **保存并退出** &quot;，将作业添加到 &quot; **作业** &quot; 页上保存的作业列表中。 稍后可以从已保存作业列表返回到作业。

    选择 &quot; **下一步** &quot; 转到 &quot; **传递选项** &quot; 页。 &quot; **传递选项** &quot; 页面允许您为此作业设置传递选项： &quot; **批处理** &quot; 和 &quot; **取消" **全选**"。 选定的列将显示在设备列表中。

所选列跨用户会话或有权访问应用程序的用户会话之间保持不变。

## <a name="rerun-jobs"></a>重新运行作业

你可以重新运行具有失败设备的作业。 **失败时选择重新运行**：

:::image type="content" source="media/howto-run-a-job/rerun.png" alt-text="显示用于创建名为 &quot;设置光源阈值&quot; 的属性作业的选项的屏幕截图&quot;:::

    选择 &quot; **保存并退出** &quot;，将作业添加到 &quot; **作业** &quot; 页上保存的作业列表中。 稍后可以从已保存作业列表返回到作业。

    选择 &quot; **下一步** &quot; 转到 &quot; **传递选项** &quot; 页。 &quot; **传递选项** &quot; 页面允许您为此作业设置传递选项： &quot; **批处理** &quot; 和 &quot; **取消" 链接对已删除的设备不可用。

## <a name="next-steps"></a>后续步骤

了解如何在 Azure IoT Central 应用程序中创建作业后，接下来请学习：

- [管理设备](howto-manage-devices.md)
- [对设备模板进行版本控制](howto-version-device-template.md)
