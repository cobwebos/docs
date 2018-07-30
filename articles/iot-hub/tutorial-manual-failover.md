---
title: Azure IoT 中心的手动故障转移 | Microsoft Docs
description: 介绍如何为 Azure IoT 中心执行手动故障转移
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: f0e8bf922f142b795dd1a2ded4b3ec265c43481a
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2018
ms.locfileid: "39249913"
---
# <a name="tutorial-perform-manual-failover-for-an-iot-hub-public-preview"></a>教程：为 IoT 中心执行手动故障转移（公共预览版）

手动故障转移是 IoT 中心服务的一项功能，允许客户将其中心的操作从主要区域[故障转移](https://en.wikipedia.org/wiki/Failover)到相应的 Azure 异地配对区域。 在出现区域性灾难或者服务中断时间延长的情况下，可以进行手动故障转移。 也可通过执行计划内故障转移来测试灾难恢复功能，虽然我们建议使用测试性 IoT 中心而不是在生产环境中运行的 IoT 中心。 提供给客户的手动故障转移功能不另外收费。

将在本教程中执行以下任务：

> [!div class="checklist"]
> * 使用 Azure 门户创建 IoT 中心。 
> * 执行故障转移。 
> * 查看在辅助位置运行的中心。
> * 执行故障回复，让 IoT 中心的操作返回到主位置。 
> * 确认中心在正确的位置正确运行。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-an-iot-hub"></a>创建 IoT 中心

1. 登录到 [Azure 门户](https://portal.azure.com)。 

2. 单击“+ 创建资源”，选择“物联网”，然后选择“IoT 中心”。

   ![显示正在创建 IoT 中心的屏幕截图](./media/tutorial-manual-failover/create-hub-01.png)

3. 选择“基本信息”选项卡。在以下字段中进行填充。

    **订阅**：选择要使用的 Azure 订阅。

    **资源组**：单击“新建”，指定“ManlFailRG”作为资源组名称。

    **区域**：选择离你近的属于预览版一部分的区域。 本教程使用 `westus2`。 只能在 Azure 异地配对区域之间执行故障转移。 与 westus2 异地配对的区域是 WestCentralUS。
    
   > [!NOTE]
   > 手动故障转移目前为公共预览版，在以下 Azure 区域不可用：美国东部、美国西部、北欧、西欧、巴西南部、美国中南部。

   **Iot 中心名称**：指定 IoT 中心的名称。 该中心名称必须全局唯一。 

   ![显示用于创建 IoT 中心的“基本信息”窗格的屏幕截图](./media/tutorial-manual-failover/create-hub-02-basics.png)

   单击“查看 + 创建”。 （它使用大小和规模的默认值。） 

4. 查看信息，然后单击“创建”以创建 IoT 中心。 

   ![显示用于创建 IoT 中心的最后步骤的屏幕截图](./media/tutorial-manual-failover/create-hub-03-create.png)

## <a name="perform-a-manual-failover"></a>执行手动故障转移

请注意，一个 IoT 中心每天的限制是两次故障转移和两次故障回复。

1. 单击“资源组”，然后选择资源组“ManlFailRG”。 在资源列表中单击你的中心。 

2. 在“IoT 中心”窗格的“复原”下，单击“手动故障转移(预览)”。 请注意，如果中心不是在有效区域中设置的，则会禁用手动故障转移选项。

   ![显示 IoT 中心属性窗格的屏幕截图](./media/tutorial-manual-failover/trigger-failover-01.png)

3. 在“手动故障转移”窗格中，可以看到“IoT 中心主位置”和“IoT 中心辅助位置”。 主位置一开始设置为你在创建 IoT 中心时指定的位置，并且始终表示中心当前处于活动状态时的位置。 辅助位置是标准的 [Azure 异地配对区域](../best-practices-availability-paired-regions.md)，与主位置配对。 不能更改位置值。 在本教程中，主位置为 `westus2`，辅助位置为 `WestCentralUS`。

   ![显示“手动故障转移”窗格的屏幕截图](./media/tutorial-manual-failover/trigger-failover-02.png)

3. 在“手动故障转移”窗格顶部单击“启动故障转移”。 此时会看到“确认手动故障转移”窗格。 填充 IoT 中心的名称，确认它是需要进行故障转移的。 然后，若要启动故障转移，请单击“确定”。

   执行手动故障转移所需时间与中心的已注册设备数成正比。 例如，如果有 1 百万台设备，可能需要 15 分钟，但如果有 5 百万台设备，则可能需要 1 小时或更长的时间。

4. 在“确认手动故障转移”窗格中填充 IoT 中心的名称，确认它是需要进行故障转移的。 然后，若要启动故障转移，请单击“确定”。 

   ![显示“手动故障转移”窗格的屏幕截图](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   当手动故障转移过程正在运行时，“手动故障转移”窗格中会有一个横幅，告知你手动故障转移正在进行。 

   ![显示手动故障转移正在进行的屏幕截图](./media/tutorial-manual-failover/trigger-failover-04-in-progress.png)

   如果在关闭“IoT 中心”窗格后又在“资源组”窗格中通过单击来打开它，则会看到一个横幅，指出该中心未处于活动状态。 

   ![显示 IoT 中心处于非活动状态的屏幕截图](./media/tutorial-manual-failover/trigger-failover-05-hub-inactive.png)

   完成后，“手动故障转移”页上的主要区域和次要区域会互换，中心又处于活动状态。 在本示例中，主位置现在为 `WestCentralUS`，辅助位置现在为 `westus2`。 

   ![显示故障转移已完成的屏幕截图](./media/tutorial-manual-failover/trigger-failover-06-finished.png)

## <a name="perform-a-failback"></a>执行故障回复 

执行手动故障转移以后，可以将中心的操作切换回原始的主要区域 -- 这称为故障回复。 如果刚执行故障转移，则需等待大约一小时，然后才能请求故障回复。 如果尝试在比这更短的时间内执行故障回复，则会显示错误消息。

故障回复的执行方式与手动故障转移一样。 下面是步骤： 

1. 若要执行故障回复，请返回到 Iot 中心的“Iot 中心”窗格。

2. 在“IoT 中心”窗格的“复原”下，单击“手动故障转移(预览)”。 

3. 在“手动故障转移”窗格顶部单击“启动故障转移”。 此时会看到“确认手动故障转移”窗格。 

4. 在“确认手动故障转移”窗格中填充 IoT 中心的名称，确认它是需要进行故障回复的。 然后，若要启动故障回复，请单击“确定”。 

   ![手动故障回复请求的屏幕截图](./media/tutorial-manual-failover/trigger-failback-01-regions.png)

   横幅会如[执行故障转移](#perform-a-failover)部分所述一样显示。 故障回复完成之后，它会将 `westus2` 显示为主位置，将 `WestCentralUS` 显示为辅助位置，就像初始设置的一样。

## <a name="clean-up-resources"></a>清理资源 

若要删除为本教程创建的资源，请删除资源组。 此操作会一并删除组中包含的所有资源。 在这种情况下，它会删除 IoT 中心和资源组本身。 

1. 单击“资源组”。 

2. 找到并选择资源组“ManlFailRG”。 单击可将其打开。 

3. 单击“删除资源组”。 系统提示时，请输入资源组的名称，然后单击“删除”进行确认。 

## <a name="next-steps"></a>后续步骤

本教程介绍了如何执行以下任务，以便配置并执行手动故障转移以及请求故障回复：

> [!div class="checklist"]
> * 使用 Azure 门户创建 IoT 中心。 
> * 执行故障转移。 
> * 查看在辅助位置运行的中心。
> * 执行故障回复，让 IoT 中心的操作返回到主位置。 
> * 确认中心在正确的位置正确运行。

转到下一教程，了解如何管理 IoT 设备的状态。 

> [!div class="nextstepaction"]
[管理 IoT 设备的状态](tutorial-device-twins.md)