---
title: 预配置解决方案入门 | Microsoft Docs
description: 遵循本教程，了解如何部署 Azure IoT 套件预配置解决方案。
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 6ab38d1a-b564-469e-8a87-e597aa51d0f7
ms.service: iot-suite
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 12eb858f0be4220419300019497070a081e58249
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38723011"
---
# <a name="get-started-with-the-preconfigured-solutions"></a>预配置的解决方案入门

Azure IoT 套件[预配置解决方案][lnk-preconfigured-solutions]结合了多项 Azure IoT 服务，提供可实现常见 IoT 商业应用场景的端到端解决方案。 *远程监视* 预配置解决方案会连接并监视设备。 可以利用该解决方案分析设备发出的数据流，并通过让流程自动响应该数据流提升业务绩效。

本教程演示如何预配远程监视预配置解决方案。 此外还将逐步讲解预配置解决方案的基本功能。 可以通过随预配置解决方案一起部署的解决方案*仪表板* 来访问其中的多项功能：

![远程监控预配置解决方案仪表板][img-dashboard]

若要完成本教程，需要一个有效的 Azure 订阅。

> [!NOTE]
> 如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用][lnk_free_trial]。

[!INCLUDE [iot-suite-v1-provision-remote-monitoring](../../includes/iot-suite-v1-provision-remote-monitoring.md)]

## <a name="scenario-overview"></a>方案概述

部署远程监视预配置解决方案时，可以通过预先填充在其中的资源来逐步执行常见的远程监视方案。 在本方案中，数个连接到解决方案的设备会报告意外的温度值。 以下部分介绍了如何：

* 标识发送意外温度值的设备。
* 将这些设备配置为发送更详细的遥测。
* 通过更新这些设备上的固件来解决问题。
* 验证自己的操作是否已解决问题。

此方案的一大功能是，用户可以从解决方案仪表板远程执行所有这些操作。 不需对设备进行物理访问。

## <a name="view-the-solution-dashboard"></a>查看解决方案仪表板

该解决方案仪表板用于管理部署的解决方案。 例如，可以查看遥测数据、添加设备以及配置规则。

1. 当预配完成且预配置解决方案的磁贴指示“就绪”时，请选择“启动”，在新的选项卡中打开远程监视解决方案门户。

    ![启动预配置解决方案][img-launch-solution]

1. 默认情况下，此解决方案门户会显示*仪表板*。 可以通过页面左侧的菜单导航到解决方案门户的其他区域。

    ![远程监控预配置解决方案仪表板][img-menu]

仪表板会显示以下信息：

* 一个地图，用于显示每个连接到解决方案的设备的位置。 第一次运行解决方案时有 25 个模拟设备。 模拟设备作为 Azure WebJobs 实现，而解决方案使用必应地图 API 在地图上绘制信息。 请参阅[常见问题][lnk-faq]，了解如何将地图变成动态地图。
* 一个“遥测历史记录”面板，用于从所选的设备绘制近实时的湿度和温度遥测数据，并显示聚合数据，例如最大、最小和平均湿度。
* 一个“警报历史记录”面板，用于在遥测值超过阈值时显示最近的警报事件。 除了预配置解决方案所创建的示例之外，还可以定义自己的警报。
* 一个“作业”面板，用于显示有关计划作业的信息。 可在“管理作业”页上计划自己的作业。

## <a name="view-alarms"></a>查看警报

警报历史记录面板显示，五个设备报告的遥测值高于预期。

![解决方案仪表板上的“待办事项警报”历史记录][img-alarms]

> [!NOTE]
> 这些警报是通过预配置解决方案中包含的规则生成的。 当设备发送的温度值超过 60 时，该规则会生成警报。 用户可以通过在左侧菜单中选择[规则](#add-a-rule)和[操作](#add-an-action)，定义自己的规则和操作。

## <a name="view-devices"></a>查看设备

“设备”列表显示解决方案中所有已注册的设备。 在设备列表中，可以查看和编辑设备元数据、添加或删除设备，以及在设备上调用方法。 可以对设备列表中的一系列设备进行筛选和排序。 还可以自定义设备列表中显示的列。

1. 选择“设备”即可显示该解决方案的设备列表。

   ![在解决方案门户中查看设备列表][img-devicelist]

1. 设备列表最初显示预配过程创建的 25 个模拟设备。 可向解决方案添加更多的模拟设备和物理设备。

1. 若要查看设备详细信息，请选择设备列表中的一个设备。

   ![在解决方案门户中查看设备详细信息][img-devicedetails]

“设备详细信息”面板包含六个部分：

* 这是一系列的链接，可让你自定义设备图标、禁用设备、添加规则、调用方法或者发送命令。 有关命令（设备到云的消息）和方法（直接方法）的比较，请参阅[云到设备的通信指南][lnk-c2d-guidance]。
* 在“设备孪生 - 标记”部分中可以编辑设备的标记值。 可在设备列表中显示标记值，并使用标记值来筛选设备列表。
* 在“设备孪生 - 所需属性”部分中可以设置要发送到设备的属性值。
* “设备孪生 - 报告的属性”部分显示设备发送的属性值。
* “设备属性”部分显示标识注册表中的信息，例如设备 ID 和身份验证密钥。
* “最近的作业”部分显示有关最近针对此设备执行的任何作业的信息。

## <a name="filter-the-device-list"></a>筛选设备列表

若只需显示那些发送意外温度值的设备，可使用筛选器。 远程监视预配置解决方案包含一个“不正常设备”筛选器，用于显示平均温度值超过 60 的设备。 用户还可以[创建自己的筛选器](#add-a-filter)。

1. 选择“打开保存的筛选器”以显示可用筛选器的列表。 然后选择“不正常设备”应用该筛选器：

    ![显示筛选器列表][img-unhealthy-filter]

1. 设备列表现在仅显示平均温度值超过 60 的设备。

    ![查看已筛选的显示不正常设备的设备列表][img-filtered-unhealthy-list]

## <a name="update-desired-properties"></a>更新所需属性

用户现在已确定一组可能需要修复的设备。 不过，用户认为 15 秒的数据频率不足以对问题进行明确诊断。 将遥测频率更改为 5 秒可以提供更多的数据点，从而对问题进行更好的诊断。 可以将该配置更改从解决方案门户推送到远程设备。 进行更改以后，即可评估影响，并处理结果。

执行以下步骤，通过运行一项作业更改受影响设备所需的 **TelemetryInterval** 属性。 收到新的 **TelemetryInterval** 属性值以后，设备会更改其配置，每隔 5 秒而非 15 秒发送一次遥测：

1. 在设备列表中显示不正常设备的列表以后，请选择“作业计划程序”，并选择“编辑设备孪生”。

1. 调用作业“更改遥测时间间隔”。

1. 将**所需属性**名称 **desired.Config.TelemetryInterval** 的值更改为 5 秒。

1. 选择“计划”。

    ![将 TelemetryInterval 属性更改为 5 秒][img-change-interval]

1. 可在门户的“管理作业”页中监视作业进度。

> [!NOTE]
> 若要更改单个设备的所需属性值，请使用“设备详细信息”面板中的“所需属性”部分，而不要运行一项作业。

该作业在设备孪生中针对筛选器选择的所有设备设置 **TelemetryInterval** 所需属性的值。 这些设备从设备孪生检索该值并更新其行为。 从设备孪生检索并处理所需属性以后，设备就会设置相应的报告值属性。

## <a name="invoke-methods"></a>调用方法

当作业运行时，用户在不正常设备的列表中注意到，所有这些设备的固件版本都是旧的（低于 1.6 版）。

![查看针对不正常设备报告的固件版本][img-old-firmware]

此固件版本可能是温度值不正常的根本原因，因为已确定其他正常设备最近更新到了 2.0 版。 用户可以使用内置的“旧固件设备”筛选器确定固件版本过旧的设备， 然后在门户中远程更新仍运行旧版固件的所有设备：

1. 选择“打开保存的筛选器”以显示可用筛选器的列表。 然后选择“旧固件设备”应用该筛选器：

    ![显示筛选器列表][img-old-filter]

1. 设备列表现在仅显示固件版本过旧的设备。 该列表包括 5 个通过“不正常设备”筛选器标识的设备，以及 3 个其他设备：

    ![查看已筛选的显示旧设备的设备列表][img-filtered-old-list]

1. 选择“作业计划程序”，并选择“调用方法”。

1. 将“作业名称”设置为“固件更新到版本 2.0”。

1. 选择 **InitiateFirmwareUpdate** 作为**方法**。

1. 将 **FwPackageUri** 参数设置为 **https://iotrmassets.blob.core.windows.net/firmwares/FW20.bin**。

1. 选择“计划”。 默认设置是让作业立即运行。

    ![创建作业，更新所选设备的固件][img-method-update]

> [!NOTE]
> 若要调用单个设备上的方法，请在“设备详细信息”面板中选择“方法”，而不要运行一个作业。

该作业在所有通过筛选器选择的设备上调用 **InitiateFirmwareUpdate** 直接方法。 设备会即时响应 IoT 中心，并异步启动固件更新过程。 设备通过报告的属性值提供有关固件更新过程的状态信息，如以下屏幕截图所示。 请选择“刷新”图标更新设备和作业列表中的信息：

![显示固件更新列表正在运行的作业列表][img-update-1]
![显示固件更新状态的设备列表][img-update-2]
![显示固件更新列表已完成的作业列表][img-update-3]

> [!NOTE]
> 在生产环境中，可以将作业计划为在指定的维护时段运行。

## <a name="scenario-review"></a>方案审核

在此方案中，用户使用仪表板上的警报历史记录和筛选器确定了某些远程设备的一个潜在问题， 然后通过筛选器和作业对设备进行了远程配置，使之能够提供有助于诊断问题的详细信息。 最后，用户通过筛选器和作业在受影响设备上对维护进行了计划。 如果返回到仪表板，则可查看是否不再有任何警报来自解决方案中的设备。 可以使用筛选器来验证固件在解决方案中的所有设备上是否为最新，以及是否不再有不正常设备：

![显示所有设备的固件均为最新的筛选器][img-updated]

![显示所有设备均正常的筛选器][img-healthy]

## <a name="other-features"></a>其他功能

以下部分介绍了远程监视预配置解决方案的一些其他功能，这些功能未在前面的方案中介绍。

### <a name="customize-columns"></a>自定义列

选择“列编辑器”可自定义设备列表中显示的信息。 可以添加和删除用于显示报告的属性和标记值的列。 此外，可以将列重新排序和重命名：

   ![设备列表中的列编辑器][img-columneditor]

### <a name="customize-the-device-icon"></a>自定义设备图标

可按如下所述，通过“设备详细信息”面板自定义设备列表中显示的设备图标：

1. 选择铅笔图标打开设备的“编辑图像”面板：

   ![打开设备图像编辑器][img-startimageedit]

1. 上传新图像或使用现有的某个图像，然后选择“保存”：

   ![编辑设备图像编辑器][img-imageedit]

1. 选择的图像随即显示在设备的“图标”列中。

> [!NOTE]
> 该图像将存储在 Blob 存储中。 设备孪生中的标记包含 Blob 存储中的图像的链接。

### <a name="add-a-device"></a>添加设备

部署预配置解决方案时，会自动预配在设备列表中可见的 25 个示例设备。 这些设备是在 Azure Web 作业中运行的 *模拟设备* 。 模拟设备可让你试验预配置解决方案，而不需要部署实际的物理设备。 要将实际设备连接到解决方案，请参阅[将设备连接到远程监视预配置解决方案][lnk-connect-rm]教程。

以下步骤说明如何将模拟设备添加到解决方案：

1. 导航回到设备列表。

1. 若要添加设备，请选择左下角的“+ 添加设备”。

   ![将设备添加到预配置解决方案][img-adddevice]

1. 选择“模拟设备”磁贴上的“新增”。

   ![在仪表板中设置新设备详细信息][img-addnew]

   如果选择创建“自定义设备”，则除了创建新的模拟设备，也可以添加物理设备。 要深入了解如何将物理设备连接到解决方案，请参阅[将设备连接到 IoT 套件远程监视预配置解决方案][lnk-connect-rm]。

1. 选择“自行定义设备 ID”，并输入唯一的设备 ID 名称，例如 **mydevice_01**。

1. 选择“创建”。

   ![保存新设备][img-definedevice]

1. 在“添加模拟设备”的步骤 3 中，选择“完成”回到设备列表。

1. 可以在设备列表中查看“正在运行”的设备。

    ![查看设备列表中的新设备][img-runningnew]

1. 也可以在仪表板上查看来自新设备的模拟遥测：

    ![查看新设备发出的遥测数据][img-runningnew-2]

### <a name="disable-and-delete-a-device"></a>禁用并删除设备

可以禁用设备，并且在已禁用之后删除：

![禁用并删除设备][img-disable]

### <a name="add-a-rule"></a>添加规则

刚才添加的新设备没有规则。 在本节中，将添加一个规则，以便在新设备所报告的温度超过 47 度时触发警报。 在开始之前，请注意仪表板上新设备的遥测历史记录显示设备温度绝不会超过 45 度。

1. 导航回到设备列表。

1. 要添加设备规则，请在“设备列表”中选择新设备，并选择“添加规则”。

1. 创建一个规则，该规则使用“温度”作为数据字段，使用“AlarmTemp”作为温度超过 47 度时的输出：

    ![添加设备规则][img-adddevicerule]

1. 若要保存更改，请选择“保存并查看规则”。

1. 选择新设备的设备详细信息窗格中的“命令”。

    ![添加设备规则][img-adddevicerule2]

1. 从命令列表中选择“ChangeSetPointTemp”并将“SetPointTemp”设置为 45。 然后选择“发送命令”：

    ![添加设备规则][img-adddevicerule3]

1. 导航回仪表板。 一小段时间之后，会在新设备所报告的温度超过 47 度阈值时，在“警报历史记录”窗格中看到新的条目：

    ![添加设备规则][img-adddevicerule4]

1. 可以在仪表板的“规则”页上查看和编辑所有规则： 

    ![列出设备规则][img-rules]

1. 可以在仪表板的“操作”页上查看和编辑为了响应规则而可采取的所有操作： 

    ![列出设备操作][img-actions]

> [!NOTE]
> 可以定义一些这样的操作：发送电子邮件或短信以响应规则，或通过[逻辑应用][lnk-logic-apps]与业务线系统集成。 有关详细信息，请参阅[将逻辑应用连接到 Azure IoT 套件远程监视预配置解决方案][lnk-logicapptutorial]。

### <a name="manage-filters"></a>管理筛选器

在设备列表中，可以创建、保存和重新加载筛选器，以显示与中心连接的设备的自定义列表。 若要创建筛选器，请执行以下操作：

1. 选择设备列表上面的“编辑筛选器”图标：

    ![打开筛选器编辑器][img-editfiltericon]

1. 在“筛选器编辑器”中，添加字段、运算符和值用于筛选设备列表。 可以添加多个子句来细化筛选器。 选择“筛选器”应用该筛选器：

    ![创建筛选器][img-filtereditor]

1. 在本示例中，已按制造商和型号筛选列表：

    ![筛选的列表][img-filterelist]

1. 若要使用自定义名称保存筛选器，请选择“另存为”图标：

    ![保存筛选器][img-savefilter]

1. 若要重新应用以前保存的筛选器，请选择“打开已保存的筛选器”图标：

    ![打开筛选器][img-openfilter]

可以基于设备 ID、设备状态、所需的属性、报告的属性和标记创建筛选器。 可以在“设备详细信息”面板的“标记”部分向设备添加自己的自定义标记，也可以运行一个作业，更新多个设备上的标记。

> [!NOTE]
> 在“筛选器编辑器”中，可以使用“高级视图”直接编辑查询文本。

### <a name="commands"></a>命令

通过“设备详细信息”面板可向设备发送命令。 设备在第一次启动时，会向解决方案发送其支持的命令的相关信息。 如需命令和方法之间差异的讨论，请参阅 [Azure IoT 中心云到设备选项][lnk-c2d-guidance]。

1. 在所选设备的“设备详细信息”窗格中选择“命令”：

   ![仪表板中的设备命令][img-devicecommands]

1. 从命令列表中选择“PingDevice”。

1. 选择“发送命令”。

1. 可以在命令历史记录中查看命令的状态。

   ![仪表板中的命令状态][img-pingcommand]

解决方案会跟踪其发送的每个命令的状态。 结果最初为“挂起”。 当设备报告它已执行命令时，结果会设置为“成功”。

## <a name="behind-the-scenes"></a>幕后

部署预配置解决方案时，部署过程会在选择的 Azure 订阅中创建多个资源。 可以在 Azure [门户][lnk-portal]中查看这些资源。 部署过程会创建一个 **资源组**，其名称基于为预配置解决方案选择的名称：

![Azure 门户中的预配置解决方案][img-portal]

可以查看每个资源的设置，方法是在资源组中的资源列表中选择该资源。

也可以查看预配置解决方案的源代码。 远程监视预配置解决方案源代码位于 [azure-iot-remote-monitoring][lnk-rmgithub] GitHub 存储库中：

* **DeviceAdministration** 文件夹包含仪表板的源代码。
* **Simulator** 文件夹包含模拟设备的源代码。
* **EventProcessor** 文件夹包含后端进程的源代码，可用于处理传入遥测。

完成后，可在 [azureiotsuite.com][lnk-azureiotsuite] 站点的 Azure 订阅中删除预配置解决方案。 利用该站点，可以轻松地删除在创建预配置解决方案时预配的所有资源。

> [!NOTE]
> 若要确保删除与预配置解决方案相关的所有内容，请在 [azureiotsuite.com][lnk-azureiotsuite] 站点中删除这些内容，而不是删除门户中的资源组。

## <a name="next-steps"></a>后续步骤

部署正常工作的预配置解决方案后，可以继续通过阅读以下文章开始使用 IoT 套件：

* [远程监视预配置解决方案演练][lnk-rm-walkthrough]
* [将设备连接到远程监视预配置解决方案][lnk-connect-rm]
* [azureiotsuite.com 站点权限][lnk-permissions]

[img-launch-solution]: media/iot-suite-v1-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: media/iot-suite-v1-getstarted-preconfigured-solutions/dashboard.png
[img-menu]: media/iot-suite-v1-getstarted-preconfigured-solutions/menu.png
[img-devicelist]: media/iot-suite-v1-getstarted-preconfigured-solutions/devicelist.png
[img-alarms]: media/iot-suite-v1-getstarted-preconfigured-solutions/alarms.png
[img-devicedetails]: media/iot-suite-v1-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: media/iot-suite-v1-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: media/iot-suite-v1-getstarted-preconfigured-solutions/pingcommand.png
[img-adddevice]: media/iot-suite-v1-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: media/iot-suite-v1-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: media/iot-suite-v1-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: media/iot-suite-v1-getstarted-preconfigured-solutions/runningnew.png
[img-runningnew-2]: media/iot-suite-v1-getstarted-preconfigured-solutions/runningnew2.png
[img-rules]: media/iot-suite-v1-getstarted-preconfigured-solutions/rules.png
[img-adddevicerule]: media/iot-suite-v1-getstarted-preconfigured-solutions/addrule.png
[img-adddevicerule2]: media/iot-suite-v1-getstarted-preconfigured-solutions/addrule2.png
[img-adddevicerule3]: media/iot-suite-v1-getstarted-preconfigured-solutions/addrule3.png
[img-adddevicerule4]: media/iot-suite-v1-getstarted-preconfigured-solutions/addrule4.png
[img-actions]: media/iot-suite-v1-getstarted-preconfigured-solutions/actions.png
[img-portal]: media/iot-suite-v1-getstarted-preconfigured-solutions/portal.png
[img-disable]: media/iot-suite-v1-getstarted-preconfigured-solutions/solutionportal_08.png
[img-columneditor]: media/iot-suite-v1-getstarted-preconfigured-solutions/columneditor.png
[img-startimageedit]: media/iot-suite-v1-getstarted-preconfigured-solutions/imagedit1.png
[img-imageedit]: media/iot-suite-v1-getstarted-preconfigured-solutions/imagedit2.png
[img-editfiltericon]: media/iot-suite-v1-getstarted-preconfigured-solutions/editfiltericon.png
[img-filtereditor]: media/iot-suite-v1-getstarted-preconfigured-solutions/filtereditor.png
[img-filterelist]: media/iot-suite-v1-getstarted-preconfigured-solutions/filteredlist.png
[img-savefilter]: media/iot-suite-v1-getstarted-preconfigured-solutions/savefilter.png
[img-openfilter]:  media/iot-suite-v1-getstarted-preconfigured-solutions/openfilter.png
[img-unhealthy-filter]: media/iot-suite-v1-getstarted-preconfigured-solutions/unhealthyfilter.png
[img-filtered-unhealthy-list]: media/iot-suite-v1-getstarted-preconfigured-solutions/unhealthylist.png
[img-change-interval]: media/iot-suite-v1-getstarted-preconfigured-solutions/changeinterval.png
[img-old-firmware]: media/iot-suite-v1-getstarted-preconfigured-solutions/noticeold.png
[img-old-filter]: media/iot-suite-v1-getstarted-preconfigured-solutions/oldfilter.png
[img-filtered-old-list]: media/iot-suite-v1-getstarted-preconfigured-solutions/oldlist.png
[img-method-update]: media/iot-suite-v1-getstarted-preconfigured-solutions/methodupdate.png
[img-update-1]: media/iot-suite-v1-getstarted-preconfigured-solutions/jobupdate1.png
[img-update-2]: media/iot-suite-v1-getstarted-preconfigured-solutions/jobupdate2.png
[img-update-3]: media/iot-suite-v1-getstarted-preconfigured-solutions/jobupdate3.png
[img-updated]: media/iot-suite-v1-getstarted-preconfigured-solutions/updated.png
[img-healthy]: media/iot-suite-v1-getstarted-preconfigured-solutions/healthy.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-v1-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-logic-apps]: https://azure.microsoft.com/documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.com/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-logicapptutorial]: iot-suite-v1-logic-apps-tutorial.md
[lnk-rm-walkthrough]: iot-suite-v1-remote-monitoring-sample-walkthrough.md
[lnk-connect-rm]: iot-suite-v1-connecting-devices.md
[lnk-permissions]: iot-suite-v1-permissions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-faq]: iot-suite-v1-faq.md