---
title: 在 Power BI 仪表板中可视化 Azure IoT Central 数据 | Microsoft Docs
description: 使用 Azure IoT Central Analytics Power BI 解决方案模板来可视化和分析 IoT Central 数据。
ms.service: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/16/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: peterpr
ms.openlocfilehash: 85c0432bceef3e94b32fa9b4a2803276b3efee17
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39324317"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>在 Power BI 仪表板中可视化和分析 Azure IoT Central 数据

![Power BI 解决方案模板管道](media/howto-connect-powerbi/iot-continuous-data-export.png)

使用 Azure IoT Central Analytics Power BI 解决方案模板可以创建功能强大的 Power BI 仪表板来监视 IoT 设备的性能。 在 Power BI 仪表板中，可以：
- 跟踪设备在不同时间段发送的数据量
- 比较遥测、状态和事件之间的数据量
- 识别报告大量度量值的设备
- 观察设备测量值的历史趋势
- 识别发送大量关键事件的有问题设备

此解决方案模板会设置一个管道，用于通过[连续数据导出](howto-export-data.md)将数据引入 Azure Blob 存储帐户。 此数据将流经 Azure Functions、Azure 数据工厂和 Azure SQL 数据库，这三个服务会处理并转换数据，以便在 Power BI 报表（可下载其 .PBIX 文件）中进行可视化和分析。 所有这些资源在 Azure 订阅中创建，因此，你可以根据需求自定义每个组件。 此解决方案模板完全是开源的，可以访问 [Github 存储库](https://aka.ms/iotcentralgithubpowerbisolutiontemplate)来详细了解体系结构和扩展该解决方案。

**[从 Microsoft AppSource 获取 Azure IoT Central Analytics 解决方案模板。](https://aka.ms/iotcentralpowerbisolutiontemplate)**

## <a name="prerequisites"></a>先决条件
设置此模板需要以下各项：
- Azure 订阅的访问权限
- 在 IoT Central 应用中使用[连续数据导出](howto-export-data.md)导出的数据。 我们建议启用度量、设备和设备模板流，以充分利用 Power BI 仪表板。
- Power BI Desktop（最新版本）
- Power BI Pro（如果想要与其他人共享仪表板）

## <a name="reports"></a>报告

会自动生成两份报表。 

第一份报表显示设备报告的度量值历史视图，详细划分不同类型的度量值，并详细列出发送了最多度量值的设备。

![Power BI 报表页 1](media/howto-connect-powerbi/template-page1-hasdata.PNG)

第二份报表更深入地分析事件，并显示报告的错误和警告的历史视图。 此外，它还显示哪些设备到目前为止报告了最多的事件，并专门显示错误事件和警告事件。

![Power BI 报表页 2](media/howto-connect-powerbi/template-page2-hasdata.PNG)

## <a name="resources"></a>资源

访问 AppSource 以获取 [Azure IoT Central Analytics 解决方案模板](https://aka.ms/iotcentralpowerbisolutiontemplate)。

访问 [Github 存储库](https://aka.ms/iotcentralgithubpowerbisolutiontemplate)，以详细了解体系结构和扩展解决方案。

## <a name="next-steps"></a>后续步骤

了解如何在 Power BI 中可视化数据后，建议接下来继续学习：

> [!div class="nextstepaction"]
> [如何管理设备](howto-manage-devices.md)
