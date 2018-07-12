---
title: 使用 Power BI 可视化远程监视数据 - Azure | Microsoft Docs
description: 本教程使用 Power BI Desktop 和 Cosmos DB 将来自远程监视解决方案的数据集成到自定义的可视化效果。 这样，用户便可以生成其自己的自定义仪表板，并将其与解决方案外部的用户共享。
author: asdonald
manager: hegate
ms.author: asdonald
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: ae039573cf202059114f23cca86207c117a35ead
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970393"
---
# <a name="visualize-remote-monitoring-data-using-power-bi"></a>使用 Power BI 可视化远程监视数据

本教程逐步介绍如何将来自 CosmosDB 的远程监视解决方案数据插入 Power BI。 建立此连接后，可以创建自己的自定义仪表板，并将其添加回到远程监视解决方案仪表板。 此工作流程可用于创建更专业的图形，此外还提供现成的图形。 然后，可以使用本教程来集成其他数据流，或生成可在外部远程监视解决方案外部使用的自定义仪表板。 在 Power BI 中生成仪表板意味着，在选择特定的部件时，还能让面板彼此交互。 例如，可以使用一个筛选器来仅显示有关模拟卡车的信息，仪表板的每个部件将会交互，以仅显示模拟卡车的信息。 如果想要使用 Power BI 以外的工具，则还可以扩展这些步骤，以使用所选的可视化工具并挂接到 Cosmos 数据库或自定义数据库（如果已设置）。 

## <a name="prerequisites"></a>先决条件

- 当前必须有一个远程监视解决方案正在运行
- 必须对 [Azure 门户](https://portal.azure.com)以及运行 IoT 中心和解决方案的订阅拥有访问权限
- 必须安装 [Power BI Desktop](https://powerbi.microsoft.com)（任何版本都可以）


## <a name="information-needed-from-azure-portal"></a>Azure 门户中的所需信息

1. 导航到 [Azure 门户](https://portal.azure.com)并根据需要登录

2. 在左侧面板中单击“资源组”

    ![边侧面板导航](./media/iot-accelerators-integrate-data-powerbi/side_panel.png)

3. 导航运行 IoT 解决方案的资源组，并单击它以转到该资源组的“概述”页。 

4. 在该概述页上单击类型为“Azure Cosmos DB 帐户的”项，随后会转到该 IoT 解决方案的 Cosmos DB 流的概述页。

    ![资源组](./media/iot-accelerators-integrate-data-powerbi/resource_groups.png)

5. 在左侧面板中，单击“密钥”部分并记下以下值，以便在 Power BI 中使用：

    - URI
    - 主密钥

    ![密钥](./media/iot-accelerators-integrate-data-powerbi/keys.png)

## <a name="setting-up-the-stream-in-power-bi"></a>在 Power BI 中设置流
  
1. 打开 Power BI Desktop 应用，单击左上角的“获取数据”。 

    ![获取数据](./media/iot-accelerators-integrate-data-powerbi/get_data.png)

2. 系统要求输入数据时，选择搜索“Azure Cosmos DB”并选择此连接器。 此连接器实质上是从 Azure IoT 解决方案的 Cosmos 数据库直接提取数据
  
    ![Cosmos DB](./media/iot-accelerators-integrate-data-powerbi/cosmos_db.png)
  
3. 输入前面记下的信息：

    * URI
    * 主密钥

4. 选择将所有表导入 Power BI。 此操作会启动数据加载。 解决方案运行的时间越长，加载数据所需的时间也就越长（最长达到几个小时）。 

    ![导入表](./media/iot-accelerators-integrate-data-powerbi/import_tables.png)

5. 完成数据加载后，单击 Power BI 顶部行中的“编辑查询”，然后单击每个表对应的黄色栏中的箭头展开所有表。 实质上是展开后显示所有列。 可以看到，湿度、速度、时间等属性的数据类型不正确。

    ![新列](./media/iot-accelerators-integrate-data-powerbi/new_column.png)
  
    例如，传入 Power BI 的数据在流过连接器时，已更改为 UNIX 时间。 若要调整此转换，可以继续创建新列，并使用以下公式将其转换为日期时间格式： 

    ```text
    #datetime(1970, 1, 1, 0, 0, 0) + #duration(0, 0, 0, [Document.device.msg.received]/1000)
    ```

    ![更新的表](./media/iot-accelerators-integrate-data-powerbi/updated_table.png)
  
    Document.device.msg.received 只是采用 UNIX 格式的一个列，可以使用其他需要转换的列将其替换。 
  
    在适当的情况下，可以使用上述相同的步骤将已转换为 String 类型的其他数据点更改为 Doubles 或 Int。

## <a name="creating-a-dashboard"></a>创建仪表板

连接流后，可以开始创建个性化仪表板！ 以下示例仪表板接收模拟设备发出的遥测数据，并围绕这些数据显示不同的透视图，例如： 

* 地图上的设备位置（右侧）
* 设备及其状态和严重性。 （左上侧）
* 实施了规则的设备，以及是否针对这些设备发出了任何警报（左下侧）

![Power BI 可视化](./media/iot-accelerators-integrate-data-powerbi/visual_data.png)

## <a name="publishing-the-dashboard-and-refreshing-the-data"></a>发布仪表板并刷新数据

成功创建仪表板后，我们建议[发布 Power BI 仪表板](https://docs.microsoft.com/power-bi/desktop-upload-desktop-files)，以便与他人共享。

还需要在发布的仪表板上[刷新数据](https://docs.microsoft.com/power-bi/refresh-data)，确保获得最新的数据集。

## <a name="next-steps"></a>后续步骤

本文已介绍如何使用 Power BI 可视化远程监视数据。

有关自定义远程监视解决方案的详细信息，请参阅：

* [自定义远程监视解决方案 UI](iot-accelerators-remote-monitoring-customize.md)
* [开发人员参考指南](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [开发人员故障排除指南](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

