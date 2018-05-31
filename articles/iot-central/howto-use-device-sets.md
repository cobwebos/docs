---
title: 在 Azure IoT Central 应用程序中使用设备集 | Microsoft Docs
description: 如何以操作员的身份在 Azure IoT Central 应用程序中使用设备集。
services: iot-central
author: ellenfosborne
ms.author: elfarber
ms.date: 01/21/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 932c8315b5af22c3adf18de50cf03deaf6b2a53e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201143"
---
# <a name="use-device-sets-in-your-azure-iot-central-application"></a>在 Azure IoT Central 应用程序中使用设备集

本文介绍如何以操作员的身份在 Microsoft Azure IoT Central 应用程序中使用设备集。

设备集是由于与某个指定的条件匹配，而全部分组到一起的一系列设备。 设备集通过将设备分组到较小的逻辑组，来帮助大规模管理、可视化和分析设备。 例如，可以针对西雅图的所有空调设备创建一个列表，使西雅图的技术人员能够找到他们所要负责的所有设备。 本文介绍如何创建和配置设备集。

## <a name="create-a-device-set"></a>创建设备集

若要创建设备集：

1. 在左侧导航菜单中选择“设备集”。

1. 单击“+ 新建”。

    ![新建设备集](media/howto-use-device-sets/image1.png)

1. 为设备集指定一个在整个应用程序中唯一的名称。 还可以添加说明。 设备集只能包含单个设备模板中的设备。 选择用于此集的设备模板。

1. 通过选择属性、比较运算符和值，创建查询来识别设备集的设备。 可以添加满足设备集中指定的**所有**条件的多个查询和设备。 有权访问该应用程序的任何人都可以访问创建的设备集，因此，任何人都可以查看、修改或删除该设备集。

    ![设备集查询](media/howto-use-device-sets/image2.png)

    > [!NOTE]
    > 设备集是动态查询。 每当查看设备列表时，列表中都可能显示不同的设备。 列表内容取决于哪些设备当前满足查询的条件。

1. 选择“保存”。

## <a name="configure-the-dashboard-for-your-device-set"></a>配置设备集的仪表板

创建设备集后，可以配置其**仪表板**。 **仪表板**是一个主页，可在其中放置图像和链接。 还可以添加网格，用于列出设备集中的设备。

1. 在左侧导航菜单中选择“设备集”。

1. 选择“仪表板”选项卡。

1. 打开“设计模式”。

    ![设计模式已打开](media/howto-use-device-sets/image3.png)

1. 有关添加图像的详细信息，请参阅[准备图像并将其上传到 Azure IoT Central 应用程序](howto-prepare-images.md)。

1. 添加链接磁贴：
    1. 在右窗格中选择“链接”。

        ![选择链接](media/howto-use-device-sets/image6.png)

    1. 为链接指定**标题**。
    1. 选择单击该链接时要打开的 URL。
    1. 为链接提供说明，该说明将显示在**标题**下面。
    1. 选择“保存”。

        ![保存链接](media/howto-use-device-sets/image7.png)

    1. 可以在**仪表板**上移动链接磁贴并并调整其大小。

1. 添加网格。 网格是设备集中的设备表格，其中包含所选的列。
    1. 在右窗格中选择“网格”。

        ![选择网格](media/howto-use-device-sets/image8.png)

    1. 为网格指定**标题**。
    1. 选择按下设置按钮时要显示的列。 在弹出的面板中，选择要显示的列，并使用右箭头将其选中。
    1. 选择“确定”。
    1. 选择“保存”。

        ![保存网格](media/howto-use-device-sets/image9.png)

    1. 将网格拖放到**仪表板**。

    > [!NOTE]
    > 可以添加多个图像、链接和网格。

1. 关闭“设计模式”。

    ![设计模式已关闭](media/howto-use-device-sets/image10.png)

## <a name="configure-the-list-for-your-device-set"></a>配置设备集的列表

创建设备集后，可以配置**列表**。 **列表**在包含所选列的表格中显示设备集中的所有设备。

1. 在左侧导航菜单中选择“设备集”。

1. 选择“列表”选项卡。

1. 选择“列选项”。

    ![列选项](media/howto-use-device-sets/image11.png)

1. 选择要显示的列，并使用右箭头将其选中。

    ![选择列](media/howto-use-device-sets/image12.png)

1. 选择“确定”。

## <a name="analytics"></a>分析

设备集中的分析功能与左侧导航菜单中的主要分析选项卡相同。 可以在有关[如何创建分析](howto-create-analytics.md)的文章中详细了解分析。

## <a name="next-steps"></a>后续步骤

了解如何在 Azure IoT Central 应用程序中使用设备集后，建议接下来执行以下步骤：

> [!div class="nextstepaction"]
> [如何创建遥测规则](howto-create-telemetry-rules.md)
