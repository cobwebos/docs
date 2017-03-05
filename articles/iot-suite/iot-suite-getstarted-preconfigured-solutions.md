---
title: "预配置解决方案入门 | Microsoft Docs"
description: "遵循本教程，了解如何部署 Azure IoT 套件预配置解决方案。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 6ab38d1a-b564-469e-8a87-e597aa51d0f7
ms.service: iot-suite
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 9e1bcba086a9f70c689a5d7d7713a8ecdc764492
ms.openlocfilehash: 8248e0a02cb0775a87f0c8130e53b98f8bcfe581
ms.lasthandoff: 02/27/2017


---
# <a name="tutorial-get-started-with-the-preconfigured-solutions"></a>教程：预配置解决方案入门
## <a name="introduction"></a>介绍
Azure IoT 套件[预配置解决方案][lnk-preconfigured-solutions]结合了多项 Azure IoT 服务，提供可实现常见 IoT 商业应用场景的端到端解决方案。 *远程监视* 预配置解决方案将连接并监视设备。 可以利用该解决方案分析设备发出的数据流，并通过让流程自动响应该数据流提升业务绩效。

本教程演示如何预配远程监视预配置解决方案。 此外还将逐步讲解预配置解决方案的基本功能。 可以通过随预配置解决方案一起部署的解决方案仪表板来访问其中的多项功能。

![远程监控预配置解决方案仪表板][img-dashboard]

若要完成本教程，需要一个有效的 Azure 订阅。

> [!NOTE]
> 如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用][lnk_free_trial]。
> 
> 

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## <a name="view-the-solution-dashboard"></a>查看解决方案仪表板
该解决方案仪表板可让你管理部署的解决方案。 例如，你可以查看遥测数据、添加设备以及配置规则。

1. 当预配完成且预配置解决方案的磁贴指示“就绪”时，单击“启动”以在新的选项卡中打开远程监视解决方案门户。
   
   ![启动预配置解决方案][img-launch-solution]
2. 默认情况下，此解决方案门户会显示 *解决方案仪表板*。 你可以使用左侧菜单选择其他视图。
   
   ![远程监控预配置解决方案仪表板][img-dashboard]

仪表板将显示以下信息：

* 地图显示每个设备连接到解决方案的位置。 第一次运行解决方案时有四个模拟设备。 模拟设备作为 Azure WebJobs 实现，而解决方案使用必应地图 API 在地图上绘制信息。
* “遥测历史记录”面板从所选的设备绘制准实时的湿度和温度遥测数据，并显示聚合数据，例如最大、最小和平均湿度。
* “警报历史记录”面板在遥测值超过阈值时显示最近的警报事件。 除了预配置解决方案所创建的示例之外，还可以定义自己的警报。
* “作业”面板显示有关计划作业的信息。 可在“管理作业”页上计划自己的作业。

## <a name="view-the-device-list"></a>查看设备列表
“设备列表”显示解决方案中所有已注册的设备。 在设备列表中，可以查看和编辑设备元数据、添加或删除设备，以及在设备上调用方法。

1. 单击左侧菜单中的“设备”可显示此解决方案的设备列表。
   
   ![仪表板中的设备列表][img-devicelist]
2. 设备列表最初显示预配过程创建的四个模拟设备。 可向解决方案添加更多的模拟设备和物理设备。
3. 单击“列编辑器”可自定义设备列表中显示的信息。 可以添加和删除用于显示报告的属性和标记值的列。 此外，可以将列重新排序和重命名：
   
   ![仪表板中的列编辑器][img-columneditor]
4. 若要查看设备详细信息，请在设备列表中单击该设备。
   
   ![仪表板中的设备详细信息][img-devicedetails]

“设备详细信息”面板包含六个部分：

* 这是一系列的链接，可让你自定义设备图标、禁用设备、添加规则、调用方法或者发送命令。 有关命令（设备到云的消息）和方法（直接方法）的比较，请参阅[云到设备的通信指南][lnk-c2d-guidance]。
* 在“设备孪生 - 标记”部分中可以编辑设备的标记值。 可在设备列表中显示标记值，并使用标记值来筛选设备列表。
* 在“设备孪生 - 所需属性”部分中可以设置要发送到设备的属性值。
* “设备孪生 - 报告的属性”部分显示设备发送的属性值。
* “设备属性”部分显示标识注册表中的信息，例如设备 ID 和身份验证密钥。
* “最近的作业”部分显示有关最近针对此设备执行的任何作业的信息。

## <a name="customize-the-device-icon"></a>自定义设备图标

可按如下所述，通过“设备详细信息”面板自定义设备列表中显示的设备图标：

1. 单击铅笔图标打开设备的“编辑图像”面板：
   
   ![打开设备图像编辑器][img-startimageedit]
2. 上载新图像或使用现有的某个图像，然后单击“保存”：
   
   ![编辑设备图像编辑器][img-imageedit]
3. 选择的图像随即显示在设备的“图标”列中。

> [!NOTE]
> 该图像将存储在 Blob 存储中。 设备孪生中的标记包含 Blob 存储中的图像的链接。
> 
> 

## <a name="invoke-a-method-on-a-device"></a>在设备上调用方法
通过“设备详细信息”面板可在设备上调用方法。 设备在首次启动时，会将它支持的方法的相关信息发送到解决方案。

1. 在所选设备的“设备详细信息”窗格中单击“方法”：
   
   ![仪表板中的设备方法][img-devicemethods]
2. 在方法列表中选择“重新启动”。
3. 单击“调用方法”。
4. 可在方法历史记录中看到方法调用的状态。
   
   ![仪表板中的方法状态][img-pingmethod]

解决方案将跟踪它调用的每个方法的状态。 设备完成该方法后，方法历史记录表中会显示一个新项。

某些方法在设备上启动异步作业。 例如，**InitiateFirmwareUpdate** 方法会启动一个异步任务来执行更新。 设备使用报告的属性来报告正在进行的固件更新的状态。

## <a name="send-a-command-to-a-device"></a>向设备发送命令
通过“设备详细信息”面板可向设备发送命令。 设备在第一次启动时，会向解决方案发送其支持的命令的相关信息。

1. 在所选设备的“设备详细信息”窗格中单击“命令”：
   
   ![仪表板中的设备命令][img-devicecommands]
2. 从命令列表中选择“PingDevice”。
3. 单击左下角的“+ 添加设备” 。
4. 你可以在命令历史记录中查看命令的状态。
   
   ![仪表板中的命令状态][img-pingcommand]

解决方案会跟踪其发送的每个命令的状态。 结果最初为“挂起”。 当设备报告它已执行命令时，结果会设置为“成功”。

## <a name="add-a-new-simulated-device"></a>添加新的模拟设备
部署预配置解决方案时，会自动预配在设备列表中可见的四个示例设备。 这些设备是在 Azure Web 作业中运行的 *模拟设备* 。 模拟设备可让你试验预配置解决方案，而不需要部署实际的物理设备。 若要将实际设备连接到解决方案，请参阅[将设备连接到远程监视预配置解决方案][lnk-connect-rm]教程。

以下步骤说明如何将模拟设备添加到解决方案：

1. 导航回到设备列表。
2. 若要添加设备，请单击左下角的“+ 添加设备”。
   
   ![将设备添加到预配置解决方案][img-adddevice]
3. 单击“模拟设备”磁贴上的“新增”。
   
   ![在仪表板中设置新设备详细信息][img-addnew]
   
   如果选择创建“自定义设备”，则除了创建新的模拟设备，也可以添加物理设备。 若要深入了解如何将物理设备连接到解决方案，请参阅[将设备连接到 IoT 套件远程监视预配置解决方案][lnk-connect-rm]。
4. 选择“自行定义设备 ID”，然后输入唯一的设备 ID 名称，例如 **mydevice_01**。
5. 单击“创建” 。
   
   ![保存新设备][img-definedevice]
6. 在“添加模拟设备”的步骤 3 中，单击“完成”回到设备列表。
7. 可以在设备列表中查看“正在运行”的设备。
   
    ![查看设备列表中的新设备][img-runningnew]
8. 也可以在仪表板上查看来自新设备的模拟遥测：
   
    ![查看新设备发出的遥测数据][img-runningnew-2]

## <a name="device-properties"></a>设备属性
远程监视预配置解决方案使用[设备孪生][lnk-device-twin]来同步设备与解决方案后端之间的设备元数据。 设备孪生是存储在 IoT 中心的一个 JSON 文档，其中存储了各个设备的属性值。 设备会定期将元数据以*报告的属性*的形式发送到解决方案后端，以便将其存储在设备孪生中。 解决方案后端可在设备孪生中设置*所需的属性*，以便将元数据更新发送到设备。 报告的属性显示设备最近发送的元数据值。 有关详细信息，请参阅[设备标识注册表、设备孪生和 DocumentDB][lnk-devicemetadata]。

> [!NOTE]
> 解决方案还使用 DocumentDB 数据库来存储与命令和方法相关的特定于设备的数据。
> 
> 

1. 导航回到设备列表。
2. 在“设备列表”中选择新设备，然后单击“编辑”来编辑“设备孪生 - 所需的属性”：
   
   ![编辑设备所需的属性][img-editdevice]
3. 将“所需的属性名称”设置为“纬度”，将值设置为 **47.639521**。 然后单击“将更改保存到设备注册表”：
   
    ![编辑设备所需的属性][img-editdevice2]
4. 在“设备详细信息”面板中，新纬度值最初显示为所需的属性，旧纬度值显示为报告的属性：
   
    ![查看报告的属性][img-editdevice3]
5. 目前，预配置解决方案中的模拟设备只能处理所需的属性 **Desired.Config.TemperatureMeanValue** 和 **Desired.Config.TelemetryInterval**。 实际设备应该从 IoT 中心读取所有所需的属性、对其配置进行更改，以及以报告的属性的形式向中心报告新值。

在“设备详细信息”面板中，还可以像编辑“设备孪生 - 所需的属性”一样编辑“设备孪生 - 标记”。 但是，与所需的属性不同，标记不会与设备同步。 标记只会出现在 IoT 中心的设备孪生中。 使用标记可在设备列表中生成自定义筛选器。

## <a name="sort-the-device-list"></a>为设备列表排序

在列标题的旁边单击可为设备列表排序。 第一次单击会按升序排序，第二个单击会按降序排序：

![为设备列表排序][img-sortdevices]

## <a name="filter-the-device-list"></a>筛选设备列表

在设备列表中，可以创建、保存和重新加载筛选器，以显示与中心连接的设备的自定义列表。 若要创建筛选器，请执行以下操作：

1. 单击设备列表上面的“编辑筛选器”图标：
   
   ![打开筛选器编辑器][img-editfiltericon]
2. 在“筛选器编辑器”中，添加字段、运算符和值用于筛选设备列表。 可以添加多个子句来细化筛选器。 单击“筛选器”应用该筛选器：
   
   ![创建筛选器][img-filtereditor]
3. 在本示例中，已按制造商和型号筛选列表：
   
   ![筛选的列表][img-filterelist]
4. 若要使用自定义名称保存筛选器，请单击“另存为”图标：
   
   ![保存筛选器][img-savefilter]
5. 若要重新应用以前保存的筛选器，请单击“打开已保存的筛选器”图标：
   
   ![打开筛选器][img-openfilter]

可以基于设备 ID、设备状态、所需的属性、报告的属性和标记创建筛选器。

> [!NOTE]
> 在“筛选器编辑器”中，可以使用“高级视图”直接编辑查询文本。
> 
> 

## <a name="add-a-rule-for-the-new-device"></a>为新设备添加规则
刚才添加的新设备没有规则。 在本节中，将添加一个规则，以便在新设备所报告的温度超过 47 度时触发警报。 在开始之前，请注意仪表板上新设备的遥测历史记录显示设备温度绝不会超过 45 度。

1. 导航回到设备列表。
2. 若要添加设备规则，请在“设备列表”中选择新设备，然后单击“添加规则”。
3. 创建一个规则，该规则使用“温度”作为数据字段，使用“AlarmTemp”作为温度超过 47 度时的输出：
   
    ![添加设备规则][img-adddevicerule]
4. 若要保存更改，请单击“保存并查看规则”。
5. 单击新设备的设备详细信息窗格中的“命令”。
   
   ![添加设备规则][img-adddevicerule2]
6. 从命令列表中选择“ChangeSetPointTemp”并将“SetPointTemp”设置为 45。 Then click **Send Command**:
   
   ![添加设备规则][img-adddevicerule3]
7. 导航回到解决方案仪表板。 一小段时间之后，将在新设备所报告的温度超过 47 度阈值时，在“警报历史记录”  窗格中看到新的条目：
   
   ![添加设备规则][img-adddevicerule4]
8. 可以在仪表板的“规则”页上查看和编辑所有规则： 
   
    ![列出设备规则][img-rules]
9. 可以在仪表板的“操作”页上查看和编辑为了响应规则而可采取的所有操作： 
   
    ![列出设备操作][img-actions]

> [!NOTE]
> 可以定义一些这样的操作：发送电子邮件或短信以响应规则，或通过[逻辑应用][lnk-logic-apps]与业务线系统集成。 有关详细信息，请参阅[将逻辑应用连接到 Azure IoT 套件远程监视预配置解决方案][lnk-logicapptutorial]。
> 
> 

## <a name="disable-and-remove-devices"></a>禁用和删除设备
可以禁用设备，并且在已禁用之后删除：

![禁用并删除设备][img-disable]

## <a name="run-jobs"></a>运行作业
可以计划作业，在设备上执行批量操作。 为设备列表创建作业。 此列表可以包含所有设备，也可以是在设备列表中使用[筛选工具](#filter-the-device-list)创建的筛选列表。 作业可在列表中的每个设备上调用方法，或者更新设备列表中每个设备的设备孪生。

### <a name="create-a-job-to-invoke-a-method"></a>创建作业来调用方法

以下步骤说明如何创建一个可在列表中每个设备上调用固件更新方法的作业。 只会在支持该方法的设备上调用该方法：

1. 在设备列表中使用筛选器工具创建一个要接收固件更新的设备列表：
   
   ![打开筛选器编辑器][img-editfiltericon]
2. 在筛选的列表中，单击“作业计划程序”：
   
   ![打开作业计划程序][img-clickjobscheduler]
3. 在“计划作业”面板中，单击“调用方法”。
4. 在“调用方法”页中，输入要调用的方法的详细信息，然后单击“计划”：
   
   ![配置方法作业][img-invokemethodjob]

**InitiateFirmwareUpdate** 方法在设备上以异步方式启动一个任务并立即返回。 然后，固件更新过程使用报告的属性来报告正在运行的更新过程。

### <a name="create-a-job-to-edit-the-device-twin"></a>创建作业来编辑设备孪生

以下步骤说明如何创建一个可在列表中每个设备上编辑设备孪生的作业：

1. 在设备列表中使用筛选器工具创建一个要进行设备孪生编辑的设备列表：
   
   ![打开筛选器编辑器][img-editfiltericon]
2. 在筛选的列表中，单击“作业计划程序”：
   
   ![打开作业计划程序][img-clickjobscheduler]
3. 在“计划作业”面板中，单击“编辑设备孪生”。
4. 在“编辑设备孪生”页中，输入要编辑的“所需的属性”和“标记”的详细信息，然后单击“计划”：
   
   ![配置方法作业][img-edittwinjob]

### <a name="monitor-the-job"></a>监视作业
可在“管理作业”页中监视计划的作业的状态。 “作业详细信息”面板显示有关所选作业的信息：
   
   ![查看作业状态][img-jobstatus]

也可以在“仪表板”中查看有关作业的信息：
   
   ![在仪表板中查看作业][img-jobdashboard]


## <a name="behind-the-scenes"></a>幕后
当你部署预配置解决方案时，部署过程会在你选择的 Azure 订阅中创建多个资源。 可以在 Azure [门户][lnk-portal]中查看这些资源。 部署过程会创建一个 **资源组** ，其名称基于你为预配置解决方案选择的名称：

![Azure 门户中的预配置解决方案][img-portal]

你可以查看每个资源的设置，方法是在资源组中的资源列表中选择该资源。

你也可以查看预配置解决方案的源代码。 远程监视预配置解决方案源代码位于 [azure-iot-remote-monitoring][lnk-rmgithub] GitHub 存储库中：

* **DeviceAdministration** 文件夹包含仪表板的源代码。
* **Simulator** 文件夹包含模拟设备的源代码。
* **EventProcessor** 文件夹包含后端进程的源代码，可用于处理传入遥测。

完成后，可在 [azureiotsuite.com][lnk-azureiotsuite] 站点的 Azure 订阅中删除预配置解决方案。 利用该站点，可以轻松地删除在创建预配置解决方案时预配的所有资源。

> [!NOTE]
> 若要确保删除与预配置解决方案相关的所有内容，请在 [azureiotsuite.com][lnk-azureiotsuite] 站点中删除这些内容，而不是删除门户中的资源组。
> 
> 

## <a name="next-steps"></a>后续步骤
部署正常工作的预配置解决方案后，可以继续通过阅读以下文章开始使用 IoT 套件：

* [远程监视预配置解决方案演练][lnk-rm-walkthrough]
* [将设备连接到远程监视预配置解决方案][lnk-connect-rm]
* [azureiotsuite.com 站点权限][lnk-permissions]

[img-launch-solution]: media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-devicelist]: media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-devicedetails]: media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-devicemethods]: media/iot-suite-getstarted-preconfigured-solutions/devicemethods.png
[img-pingcommand]: media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
[img-pingmethod]: media/iot-suite-getstarted-preconfigured-solutions/pingmethod.png
[img-adddevice]: media/iot-suite-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-getstarted-preconfigured-solutions/runningnew.png
[img-runningnew-2]: media/iot-suite-getstarted-preconfigured-solutions/runningnew2.png
[img-rules]: media/iot-suite-getstarted-preconfigured-solutions/rules.png
[img-editdevice]: media/iot-suite-getstarted-preconfigured-solutions/editdevice.png
[img-editdevice2]: media/iot-suite-getstarted-preconfigured-solutions/editdevice2.png
[img-editdevice3]: media/iot-suite-getstarted-preconfigured-solutions/editdevice3.png
[img-adddevicerule]: media/iot-suite-getstarted-preconfigured-solutions/addrule.png
[img-adddevicerule2]: media/iot-suite-getstarted-preconfigured-solutions/addrule2.png
[img-adddevicerule3]: media/iot-suite-getstarted-preconfigured-solutions/addrule3.png
[img-adddevicerule4]: media/iot-suite-getstarted-preconfigured-solutions/addrule4.png
[img-actions]: media/iot-suite-getstarted-preconfigured-solutions/actions.png
[img-portal]: media/iot-suite-getstarted-preconfigured-solutions/portal.png
[img-disable]: media/iot-suite-getstarted-preconfigured-solutions/solutionportal_08.png
[img-columneditor]: media/iot-suite-getstarted-preconfigured-solutions/columneditor.png
[img-startimageedit]: media/iot-suite-getstarted-preconfigured-solutions/imagedit1.png
[img-imageedit]: media/iot-suite-getstarted-preconfigured-solutions/imagedit2.png
[img-sortdevices]: media/iot-suite-getstarted-preconfigured-solutions/sortdevices.png
[img-editfiltericon]: media/iot-suite-getstarted-preconfigured-solutions/editfiltericon.png
[img-filtereditor]: media/iot-suite-getstarted-preconfigured-solutions/filtereditor.png
[img-filterelist]: media/iot-suite-getstarted-preconfigured-solutions/filteredlist.png
[img-savefilter]: media/iot-suite-getstarted-preconfigured-solutions/savefilter.png
[img-openfilter]:  media/iot-suite-getstarted-preconfigured-solutions/openfilter.png
[img-clickjobscheduler]: media/iot-suite-getstarted-preconfigured-solutions/clickscheduler.png
[img-invokemethodjob]: media/iot-suite-getstarted-preconfigured-solutions/invokemethodjob.png
[img-edittwinjob]: media/iot-suite-getstarted-preconfigured-solutions/edittwinjob.png
[img-jobstatus]: media/iot-suite-getstarted-preconfigured-solutions/jobstatus.png
[img-jobdashboard]: media/iot-suite-getstarted-preconfigured-solutions/jobdashboard.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devicemetadata]: iot-suite-what-are-preconfigured-solutions.md#device-identity-registry-device-twin-and-documentdb
[lnk-logicapptutorial]: iot-suite-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
