---
title: 从合作伙伴获取传感器数据
description: 本文介绍如何从合作伙伴获取传感器数据。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 113ab07af8ada16c0779da510c5f5b1f1f5a290b
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398235"
---
# <a name="get-sensor-data-from-sensor-partners"></a>从传感器合作伙伴获取传感器数据

Azure FarmBeats 可帮助您将来自 IoT 设备和传感器的流式处理数据引入数据集。 目前，支持以下传感器设备合作伙伴。

  ![农场节拍合作伙伴](./media/get-sensor-data-from-sensor-partner/partner-information-2.png)

将设备数据与 Azure FarmBeats 集成可帮助您从服务器场中部署的 IoT 传感器获取地面数据到数据中心。 数据一旦可用，可以通过 FarmBeats 加速器进行可视化。 这些数据可用于数据融合和机器学习/人工智能 （ML/AI） 模型构建，使用 FarmBeats。

要启动传感器数据流，请确保：

-  您在 Azure 应用商店中安装了服务器场节拍。
-  您决定在服务器场上安装的传感器和设备。
-  如果您计划使用土壤水分传感器，请使用 FarmBeats 土壤水分传感器放置图，获取有关传感器数量以及传感器的确切位置的建议。 有关详细信息，请参阅[生成地图](generate-maps-in-azure-farmbeats.md)。
- 您可以在服务器场中从设备合作伙伴购买和部署设备或传感器。 确保您可以通过设备合作伙伴的解决方案访问传感器数据。

## <a name="enable-device-integration-with-farmbeats"></a>启用与服务器场节拍的设备集成

开始传感器数据流后，可以开始将数据输入 FarmBeats 系统的过程。 向设备提供商提供以下信息，以启用与 FarmBeats 的集成：

 - API 终结点
 - 租户 ID
 - 客户端 ID
 - 客户端机密
 - 事件Hub连接字符串

按照以下步骤生成上述信息：

> [!NOTE]
> 这些步骤需要在 Azure 上完成才能访问部署服务器场节拍的 Azure 订阅。

1. 登录 https://portal.azure.com/。

2. **如果您在 FarmBeats 版本 1.2.7 或更高版本中，请跳过步骤 a、b 和 c，然后转到步骤 3。** 您可以通过选择 FarmBeats UI 右上角的 **"设置"** 图标来检查服务器场节拍版本。

      a.  转到**Azure 活动目录** > **应用注册**

      b. 选择作为服务器场节拍部署的一部分创建**的应用注册**。 它将具有与服务器场Beats数据库相同的名称。

      c. 选择 **"公开 API>** 选择 **"添加客户端应用程序**"并输入**04b07795-8ddb-461a-bbee-02f9e1bf7b46**并选中**授权范围**。 这将授予 Azure CLI（云外壳）以执行以下步骤的访问权限：

3. 打开 Cloud Shell。 此选项在 Azure 门户右上角的工具栏上可用。

    ![Azure 门户工具栏](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

4. 确保环境设置为**PowerShell**。 默认情况下，它设置为 Bash。

    ![PowerShell 工具栏设置](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

5. 转到主目录。

    ```azurepowershell-interactive 
    cd  
    ```

6. 运行以下命令。 这将将脚本下载到您的主目录。

    ```azurepowershell-interactive 

    wget –q https://aka.ms/farmbeatspartnerscriptv3 -O ./generatePartnerCredentials.ps1

    ```

7. 运行以下脚本。 该脚本要求提供租户 ID，可以从**Azure 活动目录** > **概述**页获取该 ID。

    ```azurepowershell-interactive 

    ./generatePartnerCredentials.ps1   

    ```

8. 按照屏幕上的说明捕获**API 终结点**、**租户 ID、****客户端 ID、****客户端密钥**和**事件Hub连接字符串**的值。

### <a name="integrate-device-data-by-using-the-generated-credentials"></a>使用生成的凭据集成设备数据

现在，您从上一节生成了以下信息。
 - API 终结点
 - 事件Hub连接字符串
 - 客户端 ID
 - 客户端机密
 - 租户 ID

您需要向设备合作伙伴提供此功能以链接 FarmBeats。 转到设备合作伙伴门户执行相同的操作。 例如，如果您使用的是戴维斯仪器、太溶或佩斯尔仪器（Metos.at）的设备，则按照以下说明访问相应的页面：

1. [戴维斯仪器](https://weatherlink.github.io/azure-farmbeats/setup)

2. [太溶](https://app.teralytic.com/)

3. [佩斯尔仪器](https://ng.fieldclimate.com/user-api-services)

设备提供程序确认集成成功。 确认后，您可以查看 Azure FarmBeats 上的所有设备和传感器。

## <a name="view-devices-and-sensors"></a>查看设备和传感器

使用以下部分查看服务器场的设备和传感器。

### <a name="view-devices"></a>查看设备

目前，FarmBeats 支持以下设备：

- **节点**：一个或多个传感器连接到的设备。
- **网关**：一个或多个节点连接到的设备。

执行以下步骤:

1. 在主页上，从菜单中选择 **"设备**"。
  "**设备"** 页显示设备类型、型号、状态、放置在其中的服务器场以及元数据的最后更新日期。 默认情况下，服务器场列设置为*NULL*。 您可以选择将设备分配给服务器场。 有关详细信息，请参阅[分配设备](#assign-devices)。
2. 选择设备以查看连接到设备的设备属性、遥测和子设备。

    ![“设备”页](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>查看传感器

执行以下步骤:

1. 在主页上，从菜单中选择 **"传感器**"。
  "**传感器"** 页显示有关传感器类型、所连接的服务器场、父设备、端口名称、端口类型和上次更新状态的详细信息。
2. 选择传感器以查看传感器属性、活动警报和传感器的遥测数据。

    ![传感器页面](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>分配设备  

传感器数据流入后，您可以将其分配给部署传感器的服务器场。

1. 在主页上，从菜单中选择 **"农场**"。 将显示 **"服务器场**"列表页。
2. 选择要为其分配设备的服务器场，然后选择 **"添加设备**"。
3. 将显示"**添加设备"** 窗口。 选择要分配给服务器场的设备。

    ![添加设备窗口](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. 选择 **"添加设备**"。 或者，转到 **"设备"** 菜单，选择要分配给服务器场的设备，然后选择 **"关联设备**"。
5. 在 **"关联设备"** 窗口中，从下拉列表中选择服务器场，然后选择"**全部应用**"将服务器场关联到所有选定的设备。

    ![关联设备窗口](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. 要将每个设备关联到不同的服务器场，请选择"**分配给服务器场"** 列中的下拉箭头，然后为每个设备行选择一个服务器场。

7. 选择 **"分配"** 以完成设备分配。

### <a name="visualize-sensor-data"></a>可视化传感器数据

执行以下步骤:

1. 在主页上，从菜单中选择 **"服务器场**"以查看 **"服务器场"** 页。
2. 选择要查看传感器数据的**服务器场**。
3. 在 **"服务器场"** 仪表板上，您可以查看遥测数据。 您可以查看实时遥测或使用**自定义范围**查看特定日期范围。

    ![服务器场仪表板](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-a-sensor"></a>删除传感器

执行以下步骤:

1. 在主页上，从菜单中选择 **"传感器**"以查看 **"传感器"** 页面。
2. 选择要删除的设备，并在确认窗口中选择 **"删除**"。

    ![“删除”按钮](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

确认消息显示传感器已成功删除。

## <a name="delete-devices"></a>删除设备

执行以下步骤:

1. 在主页上，从菜单中选择 **"设备**"以查看 **"设备"** 页。
2. 选择要删除的设备，并在确认窗口中选择 **"删除**"。

    ![“删除”按钮](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>后续步骤

现在，传感器数据会流入 Azure FarmBeats 实例。 现在，了解如何为服务器场[生成地图](generate-maps-in-azure-farmbeats.md#generate-maps)。
