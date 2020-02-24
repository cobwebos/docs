---
title: 教程：使用 Azure IoT Central 创建连接的废弃物管理应用
description: 教程：了解如何使用 Azure IoT Central 应用程序模板创建连接的废弃物管理应用程序。
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 274d725a0e7987c8cb15363ba276aec7ebc541d5
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2020
ms.locfileid: "77426354"
---
# <a name="tutorial-create-a-connected-waste-management-application-in-iot-central"></a>教程：在 IoT Central 中创建连接的废弃物管理应用程序



本教程指导你从 IoT Central 连接的废弃物管理应用程序模板创建 Azure IoT Central 连接的废弃物管理应用程序  。 

在本教程中，您将学习如何执行以下操作： 

> [!div class="checklist"]

> * 使用 Azure IoT Central 连接的废弃物管理模板创建连接的废弃物管理应用程序 
> * 浏览并自定义操作员仪表板 
> * 浏览连接的垃圾箱设备模板
> * 浏览模拟设备
> * 浏览并配置规则
> * 配置作业
> * 使用白色标签自定义应用程序品牌

## <a name="prerequisites"></a>必备条件

要完成本教程，需要：
-  建议使用 Azure 订阅。 可以选择使用 7 天免费试用版。 如果没有 Azure 订阅，则可在 [Azure 注册页](https://aka.ms/createazuresubscription)上创建一个。

## <a name="create-connected-waste-management-app-in-iot-central"></a>在 IoT Central 中创建连接的废弃物管理应用

在本部分中，你将使用 Azure IoT Central 连接的废弃物管理模板在 IoT Central 中创建连接的废弃物管理应用程序  。

若要创建新的 Azure IoT Central 连接的废弃物管理应用程序，请执行以下操作：  

1. 导航到 [Azure IoT Central 主页](https://aka.ms/iotcentral)网站。

    如果你有一个 Azure 订阅，请使用用于访问该订阅的凭据登录，否则请使用 Microsoft 帐户登录：

    ![输入组织帐户](./media/tutorial-connectedwastemanagement/sign-in.png)

2. 单击左侧窗格中的“生成”，然后选择“政府”选项卡   。“政府”窗格显示了几个政府应用程序模板。

    ![生成政府应用模板](./media/tutorial-connectedwastemanagement/iotcentral-government-tab-overview.png)

1. 选择“连接的废弃物管理”应用程序模板  。 此模板包括示例连接的垃圾箱设备模板、模拟设备、操作员仪表板以及预配置的监视规则。    

2. 单击“创建应用”，将打开“新建应用程序”创建表单，其中包含以下字段   ：
    * **应用程序名称**。 默认情况下，应用程序使用“连接的废弃物管理”，后跟 IoT Central 生成的唯一 ID 字符串  。 （可选）选择一个易记的应用程序名称。 稍后也可以更改应用程序名称。
    * **URL** - 你可以选择所需的 URL。 也可以稍后更改 URL。 
    * 如果你有一个 Azure 订阅，请输入目录、Azure 订阅和区域  。 如果你没有订阅，则可以启用 7 天免费试用版并填写所需的联系人信息  。  

    有关目录和订阅的详细信息，请参阅[创建应用程序快速入门](../core/quick-deploy-iot-central.md)。

5. 单击页面底部的“创建”按钮  。 

    ![Azure IoT Central 的“创建连接的废弃物应用程序”页](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement.png)
    
    ![Azure IoT Central 创建连接的废弃物应用程序 - 计费信息](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement-billinginfo.png)

 
6. 现在，你已使用 Azure IoT Central“连接的废弃物管理模板”创建了连接的废弃物管理应用  。 

祝贺你！ 新创建的应用程序附带预配置的以下项：
* 示例操作员仪表板
* 预定义的连接的垃圾箱设备示例模板
* 模拟的联网垃圾箱设备
* 预配置的规则和作业
* 使用白色标签的示例品牌 

这是你的应用程序，你可以随时修改它。 现在，浏览应用程序并进行一些自定义。  

## <a name="explore-and-customize-operator-dashboard"></a>浏览并自定义操作员仪表板 
创建应用程序后，将登录到“Wide Waste 连接的废弃物管理仪表板”  。

   ![连接的废弃物管理仪表板](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard1.png)

生成人员可在仪表板上创建和自定义视图，以供操作员使用。 但请在尝试自定义之前浏览仪表板。 

> [!NOTE]
> 仪表板中显示的所有数据均基于模拟设备数据，这将在下节中进行探讨。 

仪表板包含不同种类的磁贴：

* ***Wide World Waste 废弃物处理厂图像磁贴***：仪表板中的第一个磁贴是虚构的废弃物处理厂“Wide World Waste”的图像磁贴。 可以自定义磁贴并放置自己的图像或将其删除。 

* ***垃圾箱图像磁贴***：可以使用图像和内容磁贴创建正在受监视的设备的可视化表示形式以及描述性文本。 

* ***填充级别 KPI 磁贴***：该磁贴显示垃圾桶中的填充级别传感器报告的值  。 可以远程监控垃圾箱中的填充级别和其他传感器（例如“气味测量仪”或“重量”）    。 操作员可以采取行动，比如调度垃圾收集车。 

*  ***废弃物监视区域地图***：该地图使用 Azure Maps，你可以直接在 Azure IoT Central 中对其进行配置。 地图磁贴显示设备位置。 尝试将鼠标悬停在地图上，然后尝试使用地图上的控件，如放大、缩小或展开。

     ![连接的废弃物管理仪表板地图](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-map.png)


* ***填充、气味、重量级别条形图**：可以在条形图中可视化一个或多个设备遥测数据。 还可以展开条形图。  

  ![连接的废弃物管理仪表板条形图](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-barchart.png)


* **Field Services 内容磁贴**：仪表板包含如何从 Azure IoT Central 应用程序与 Dynamics 365 Field Services 集成的链接。 例如，可以使用 Field Services 创建用于调度垃圾收集服务的票证。 


### <a name="customize-dashboard"></a>自定义仪表板 

生成人员可在仪表板上自定义视图，以供操作员使用。 你可以尝试：
1. 单击“编辑”自定义“Wide World 连接的废弃物管理仪表板”   。 可通过单击“编辑”菜单来自定义仪表板  。 仪表板进入编辑模式后，可添加新的磁贴，也可配置  

    ![编辑仪表板](./media/tutorial-connectedwastemanagement/edit-dashboard.png)

2. 还可以单击“+ 新建”创建新的仪表板并从头开始配置  。 你可能有多个仪表板，可以从仪表板菜单在仪表板之间导航。 

## <a name="explore-connected-waste-bin-device-template"></a>浏览连接的垃圾箱设备模板

Azure IoT Central 中的设备模板定义设备的功能，这些功能可以是遥测、属性或命令。 生成人员可以定义表示要连接的设备功能的设备模板。 
 

“连接的废弃物管理”应用程序附带了一个连接的垃圾箱设备示例模板  。

如需查看设备模板：

1. 在 IoT Central 中应用程序的左窗格中单击“设备模板”  。 

    ![设备模板](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate.png)

2. 在“设备模板”列表中，你将看到“连接的垃圾桶”  。 通过单击名称打开。

3. 熟悉设备模板功能。 你可以看到，它定义了填充级别、气味计、重量、位置等传感器     。

   ![设备模板](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-connectedbin.png)

   

### <a name="customizing-the-device-template"></a>自定义设备模板

尝试自定义以下内容：
1. 从设备模板菜单导航到“自定义” 
2. 找到 `Odor meter` 遥测类型
3. 将 `Odor meter` 的“显示名称”更新为 `Odor level` 
4. 还可以尝试更新度量单位，或设置最小值和最大值  
5. 保存任何更改  

### <a name="add-a-cloud-property"></a>添加云属性 

1. 从设备模板菜单导航到“云属性” 
2. 单击“+ 添加云属性”添加新的云属性  。 在 IoT Central 中，可以添加与设备相关但不应由设备发送的属性。 例如，云属性可以是特定于安装区域、资产信息或维护信息等的警报阈值。 
3. 保存任何更改  
 
### <a name="views"></a>视图 
* 连接的垃圾箱设备模板带有预定义的视图。 浏览视图，并且可以进行更新。 这些视图定义了操作员查看设备数据及输入云属性的方式。 

  ![设备模板视图](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-views.png)

### <a name="publish"></a>发布 

* 如果进行了任何更改，请确保发布设备模板  。 

### <a name="create-a-new-device-template"></a>创建新设备模板 

* 选择“+ 新建”以创建新的设备模板，然后执行创建过程  。 可以从头开始创建自定义设备模板，也可以从 Azure 设备目录中选择设备模板。 

## <a name="explore-simulated-devices"></a>浏览模拟设备

在 IoT Central 中，可以创建模拟设备以测试设备模板和应用程序。 

“连接的废弃物管理”应用程序有两个模拟设备映射到连接的垃圾箱设备模板  。 

### <a name="to-view-the-devices"></a>要查看设备：

1. 从 IoT Central 左窗格导航到“设备”  。 

   ![设备](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices.png)

2. 选择并单击连接的垃圾箱设备。  

     ![设备 1](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices-bin1.png)

3. 导航到“云属性”选项卡，尝试将 `Bin full alert threshold` 值从 `95` 更新为 `100`  。 
* 浏览“设备属性”选项卡和“设备仪表板”选项卡   。 

> [!NOTE]
> 请注意，所有选项卡都是从“设备模板视图”配置的  。

### <a name="add-new-devices"></a>添加新设备

* 可以通过单击“设备”选项卡上的“+ 新建”添加新设备   。 

## <a name="explore-and-configure-rules"></a>浏览并配置规则

在 Azure IoT Central 中，可以创建规则来自动监视设备上的遥测，并在满足一个或多个条件时触发操作。 这些操作可能包括发送电子邮件通知、触发 Microsoft Flow 操作或向其他服务发送数据的 webhook 操作。

“连接的废弃物管理”应用程序有四个示例规则  。

### <a name="to-view-rules"></a>若要查看规则：
1. 从 IoT Central 左窗格导航到“规则” 

   ![规则](./media/tutorial-connectedwastemanagement/connectedwastemanagement-rules.png)

2. 选择“箱满警报” 

     ![箱满警报](./media/tutorial-connectedwastemanagement/connectedwastemanagement-binfullalert.png)

 3. 在满足**条件** `Fill level is greater than or equal to Bin full alert threshold` 时，检查 `Bin full alert`。

    `Bin full alert threshold` 是在 `Connected waste bin` 设备模板中定义的“云属性”  。 

现在，让我们创建电子邮件操作。

### <a name="create-an-email-action"></a>创建电子邮件操作
若要在规则的操作列表中配置电子邮件操作，请执行以下操作：
1. 选择“+ 电子邮件”  。 
2. 输入“高 pH 警报”作为操作的易记显示名称   。
3. 在“收件人”中输入与 IoT Central 帐户关联的电子邮件地址  。 
4. （可选）输入要包含在电子邮件文本中的注释。
5. 选择“完成”  ，完成操作。
6. 选择“保存”  以保存并激活新规则。 

满足配置条件后，应能收到电子邮件  。

> [!NOTE]
> 每次满足条件后，应用程序都会发送电子邮件。 “禁用”该规则会停止从自动规则接收电子邮件  。 
  
若要创建新规则： 
1. 在左窗格的“规则”上选择“+新建”   。

## <a name="configure-jobs"></a>配置作业

在 IoT Central 中，作业允许你在多个设备上触发设备或云属性更新。 除了属性外，还可以使用作业在多个设备上触发设备命令。 IoT Central 将自动执行工作流。 

1. 从左窗格转到“作业”。  
2. 单击“+ 新建”并配置一个或多个作业  。 


## <a name="customize-your-application"></a>自定义应用程序 

构建者可以更改多个设置来自定义应用程序中的用户体验。

### <a name="to-change-the-application-theme"></a>若要更改应用程序主题：

1. 转到“管理”>“自定义应用程序”  。
3. 使用“更改”按钮选择要作为应用程序徽标上传的图像   。
4. 使用“更改”按钮选择将显示在浏览器选项卡上的浏览器图标图像   。
5. 还可以通过添加 HTML 十六进制颜色代码来替换默认“浏览器颜色”  。

   ![Azure IoT Central 自定义应用程序](./media/tutorial-connectedwastemanagement/connectedwastemanagement-customize-your-application.png)

6. 也可以通过转到“管理”>“应用程序设置”和“选择图像”按钮，选择要作为应用程序图像上传的图像来更改应用程序图像   。
7. 最后，还可以通过单击应用程序刊头上的“设置”来更改“主题”   。

  
## <a name="clean-up-resources"></a>清理资源

如果不希望继续使用此应用程序，请删除应用程序，步骤如下：

1. 从 IoT Central 应用程序的左侧窗格打开“管理”选项卡。
2. 选择“应用程序设置”，然后单击页面底部的“删除”按钮。

  

## <a name="next-steps"></a>后续步骤

* 了解有关[连接的废弃物管理概念](./concepts-connectedwastemanagement-architecture.md)的详细信息
