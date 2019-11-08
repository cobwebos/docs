---
title: 获取合作伙伴提供的传感器数据
description: 描述如何获取合作伙伴提供的传感器数据
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 380c67e5aeaba9be60e016f173e4da127e4e5f14
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73798175"
---
# <a name="get-sensor-data-from-sensor-partners"></a>从传感器合作伙伴获取传感器数据

Azure FarmBeats 可帮助你将 IoT 设备和传感器中的流数据引入数据中心。 目前支持以下传感器设备伙伴：

  ![项目场节拍](./media/get-sensor-data-from-sensor-partner/partner-information-1.png)

将设备数据与 Azure FarmBeats 集成可帮助你获取在场中部署到数据中心的 IoT 传感器的基本数据。 可以通过 FarmBeats 加速器直观显示数据，并可将其用于数据合成和使用 FarmBeats 生成的 AI/ML 模型。

若要启动传感器数据流，请确保以下各项：

-  你已从 Azure Marketplace 安装了 FarmBeats。
-  你已决定要在你的场中安装的传感器和设备。
-  如果打算使用 "土料湿气传感器"，则可以使用 FarmBeats 土潮湿传感器放置图来获取传感器数量的建议，以及确切应放置传感器的位置。 有关详细信息，请参阅[生成映射](generate-maps.md)。

- 在服务器场中购买和部署设备合作伙伴的设备/传感器。 请确保可以通过设备合作伙伴的解决方案访问传感器数据。

### <a name="enable-device-integration-with-farmbeats"></a>启用设备与 FarmBeats 的集成   

启动传感器数据的流式处理后，便可以开始将数据导入到 FarmBeats 系统的过程。 你需要向设备提供商提供以下信息以启用与 FarmBeats 的集成：  

 - API 终结点  
 - 租户 ID  
 - 客户端 ID  
 - 客户端机密  
 - EventHub 连接字符串

系统集成商提供以上信息。 对于启用设备集成时遇到的任何问题，请与系统集成商联系。

或者，你可以通过从 Azure Cloud Shell 运行此脚本来生成凭据。 请按照以下步骤操作：

1. 下载[ZIP 文件](https://aka.ms/farmbeatspartnerscript)并将其解压缩到本地驱动器。 你将在 ZIP 文件中找到两个文件。
2. 登录到 https://portal.azure.com/ 并打开 Cloud Shell （该选项在门户的右上栏中提供）  

    ![项目场节拍](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. 确保将环境设置为**PowerShell** -默认情况下，它设置为 Bash。

    ![项目场节拍](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

4. 将 Cloud Shell 中的两个文件（从上面的步骤1中）上传。

    ![项目场节拍](./media/get-sensor-data-from-sensor-partner/power-shell-two-1.png)

5. 中转到上载文件的目录（默认情况下，会将文件上传到主目录 > 用户名）。
6. 运行以下脚本：

    ```azurepowershell-interactive 

    PS> ./generateCredentials.ps1   

    ```
7. 按照屏幕上的说明来捕获值。 （API 终结点、租户 ID、客户端 ID、客户端密钥和 EventHub 连接字符串）。

**使用生成的凭据集成设备数据**

访问设备合作伙伴门户，使用上一节中生成的一组凭据链接 FarmBeats。

 - API 终结点  
 - EventHub 连接字符串  
 - 客户端 ID  
 - 客户端机密  
 - 租户 ID  

 设备提供程序确认集成成功。 确认后，可以在 Azure FarmBeats 上查看所有设备和传感器。

## <a name="view-devices-and-sensors"></a>查看设备和传感器

使用以下部分查看服务器场中的设备和传感器。

### <a name="view-devices"></a>查看设备

当前 FarmBeats 支持以下设备：

- **Node**：一个或多个传感器连接到的设备。
- **网关**：一个或多个传感器连接到的设备。

请执行以下步骤：

1. 在主页上，从菜单中选择 "**设备**"。
  "设备" 页显示设备类型、型号、状态、它所在的场和元数据的上次更新日期。 默认情况下，场列设置为 NULL。 你可以选择将设备分配到场。 有关详细信息，请参阅[分配设备](#assign-devices)。
2. 选择设备以查看连接到设备的设备属性、遥测和子设备。  

    ![项目场节拍](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>查看传感器

请执行以下步骤：

1. 在主页上，从菜单中选择 "**传感器**"。
  "传感器" 页显示有关传感器类型、其连接到的服务器、父设备、端口名称、端口类型和上次更新状态的详细信息。
2. 选择传感器以查看传感器的传感器属性、活动警报和遥测数据。

    ![项目场节拍](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>分配设备  

传感器数据流动后，可以将其分配给部署了传感器的场。

1. 在主页上，从菜单中选择 "**服务器场**"，随即会显示 "**服务器场**" 列表页。  
2. 选择要向其分配设备的场，然后选择 "**添加设备**"。  
3. 将显示 "**添加设备**" 窗口。 选择要分配给场的设备。

    ![项目场节拍](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. 选择 "**添加设备**"。 或者，在 "**设备**" 菜单中，选择要分配给场的设备，然后选择 "**关联设备**"。  
5. 在 "**关联设备**" 窗口中，从下拉选项中选择 "服务器场"，然后选择 "**应用到所有**" 以将场关联到所有选定设备。

    ![项目场节拍](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. 若要将每个设备关联到不同的场，请在 "**分配给场**" 列中选择下拉列表，并为每个设备行选择场。  
7. 选择 "**分配**" 完成设备分配。

### <a name="visualize-sensor-data"></a>直观显示传感器数据

请执行以下步骤：

1. 在主页上，从菜单中选择 "**服务器场**" 以查看 "**服务器场**" 页。  
2. 选择要查看其传感器数据的**场**。  
3. 在**场**仪表板上，可以查看遥测数据。 您可以选择查看实时遥测数据，也可以使用**自定义范围**在特定日期范围内查看。

    ![项目场节拍](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-sensor"></a>删除传感器

执行以下步骤:

1. 在 "主页" 页上，从菜单中选择 "**传感器**" 以查看**传感器**页面。  
2. 选择要删除的设备，然后从确认窗口中选择 "**删除**"。

    ![项目场节拍](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

确认消息显示传感器已成功删除。  

## <a name="delete-devices"></a>删除设备

执行以下步骤:

1. 在主页上，从菜单中选择 "**设备**" 以查看 "设备" 页。  
2. 选择要删除的设备，然后从确认窗口中选择 "**删除**"。

    ![项目场节拍](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>后续步骤

现在，传感器数据流入 Azure FarmBeats 实例。 现在，了解如何为你的场[生成地图](generate-maps.md#generate-maps)。
