---
title: 在 Power BI 仪表板中可视化 Azure IoT Central 数据 | Microsoft Docs
description: 使用 Azure IoT Central 的 Power BI 解决方案来可视化和分析 IoT Central 数据。
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 10/4/2019
ms.topic: conceptual
ms.openlocfilehash: f996bb2d5126ef038ca872aee1f1893979a5229b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87080992"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>在 Power BI 仪表板中可视化和分析 Azure IoT Central 数据

*本主题适用于管理员和解决方案开发人员。*

:::image type="content" source="media/howto-connect-powerbi/iot-continuous-data-export.png" alt-text="Power BI 解决方案管道":::

使用适用于 Azure IoT Central V3 的 Power BI 解决方案来创建功能强大的 Power BI 仪表板来监视 IoT 设备的性能。 在 Power BI 仪表板中，可以：

- 跟踪设备在不同时间段发送的数据量
- 在不同遥测流之间比较数据卷
- 筛选到特定设备发送的数据
- 查看表中的最新遥测数据

此解决方案设置一个管道，该管道从你的 [连续数据导出](howto-export-data-blob-storage.md) Azure Blob 存储帐户读取数据。 管道使用 Azure Functions、Azure 数据工厂和 Azure SQL 数据库来处理和转换数据。 可以可视化和分析作为 .PBIX 文件下载的 Power BI 报表中的数据。 所有资源都是在 Azure 订阅中创建的，因此，你可以自定义每个组件以满足你的需求。

## <a name="prerequisites"></a>必备条件

完成本操作方法指南中的步骤需要有效的 Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

设置解决方案需要以下资源：

- 版本 3 IoT Central 应用程序。 若要了解如何检查您的应用程序版本，请参阅 [关于您的应用程序](./howto-get-app-info.md)。 若要了解如何创建 IoT Central 应用程序，请参阅 [创建 Azure IoT Central 应用程序](./quick-deploy-iot-central.md)。
- 连续数据导出配置为将遥测、设备和设备模板导出到 Azure Blob 存储。 若要了解详细信息，请参阅 [如何将 IoT 数据导出到 Azure 中的目标](howto-export-data.md)。
  - 请确保只有 IoT Central 的应用程序将数据导出到 blob 容器。
  - 你的 [设备必须发送 JSON 编码的消息](../../iot-hub/iot-hub-devguide-messages-d2c.md)。 设备必须 `contentType:application/JSON` `contentEncoding:utf-8` `contentEncoding:utf-16` `contentEncoding:utf-32` 在消息系统属性中指定、或或。
- Power BI Desktop (最新版本) 。 请参阅 [Power BI 下载](https://powerbi.microsoft.com/downloads/)。
- 如果要与其他人共享仪表板) ，则 Power BI Pro (。

> [!NOTE]
> 如果使用版本 2 IoT Central 应用程序，请参阅之前版本的文档站点上的 [Power BI 仪表板中的可视化和分析 Azure IoT Central 数据](https://docs.microsoft.com/previous-versions/azure/iot-central/core/howto-connect-powerbi) 。

## <a name="install"></a>安装

若要设置管道，请导航到**Microsoft AppSource**站点上[适用于 Azure IoT Central V3 页面的 Power BI 解决方案](https://appsource.microsoft.com/product/web-apps/iot-central.power-bi-solution-iot-central)。 选择 " **立即获取**"，然后按照说明进行操作。

打开 .PBIX 文件时，请确保阅读并按照封面上的说明进行操作。 这些说明介绍了如何将报表连接到 SQL 数据库。

## <a name="report"></a>报告

.PBIX 文件包含 **设备和遥测** 报表显示设备发送的遥测数据的历史视图。 它提供不同类型的遥测的细目，还显示设备发送的最新遥测数据。

:::image type="content" source="media/howto-connect-powerbi/report.png" alt-text="Power BI 解决方案管道":::

## <a name="pipeline-resources"></a>管道资源

可以访问在 Azure 门户中构成管道的所有 Azure 资源。 所有资源都在设置管道时创建的资源组中。

:::image type="content" source="media/howto-connect-powerbi/azure-deployment.png" alt-text="Power BI 解决方案管道":::

以下列表描述了管道中每个资源的角色：

### <a name="azure-functions"></a>Azure Functions

每次向 Blob 存储 IoT Central 写入新文件时，都将触发 Azure Function app。 函数从遥测、设备和设备模板 blob 中提取数据，以填充 Azure 数据工厂使用的中间 SQL 表。

### <a name="azure-data-factory"></a>Azure 数据工厂

Azure 数据工厂连接到 SQL 数据库作为链接服务。 它运行存储过程来处理数据，并将其存储在分析表中。

每隔15分钟运行一次 Azure 数据工厂，转换最新的批数据以加载到 SQL 表中， (是 **翻转窗口触发器**) 的最小数目。

### <a name="azure-sql-database"></a>Azure SQL Database

Azure 数据工厂为 Power BI 生成一组分析表。 你可以在 Power BI 中浏览这些架构，并使用它们来构建你自己的可视化效果。

## <a name="estimated-costs"></a>估计成本

Microsoft AppSource 站点上 [适用于 Azure IoT Central V3 页面的 Power BI 解决方案](https://appsource.microsoft.com/product/web-apps/iot-central.power-bi-solution-iot-central) 包括一个指向部署资源的成本估计器的链接。

## <a name="next-steps"></a>后续步骤

现在，你已了解如何在 Power BI 中对数据进行可视化，接下来建议的下一步是了解 [如何管理设备](howto-manage-devices.md)。
