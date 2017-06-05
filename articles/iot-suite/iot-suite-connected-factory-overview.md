---
title: "Azure IoT 套件连接工厂概述 | Microsoft Docs"
description: "介绍 Azure IoT 套件连接工厂预配置解决方案。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-suite
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/24/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 97da0f4dc5d5528bfb803eefe9037651939a8efd
ms.contentlocale: zh-cn
ms.lasthandoff: 05/26/2017


---
# <a name="get-started-with-the-connected-factory-preconfigured-solution"></a>连接工厂预配置解决方案入门

## <a name="introduction"></a>介绍

Azure IoT 套件[预配置解决方案][lnk-preconfigured-solutions]结合了多项 Azure IoT 服务，提供可实现常见 IoT 商业应用场景的端到端解决方案。 连接工厂预配置解决方案可连接并监视工业设备。 可以使用此解决方案分析来自设备的数据流并提高运营效率和盈利率。

本教程演示如何预配连接工厂预配置解决方案。 此外还将逐步讲解预配置解决方案的基本功能。 可以通过随预配置解决方案一起部署的解决方案*仪表板* 来访问其中的多项功能：

![连接工厂预配置解决方案仪表板][img-cf-home]

若要完成本教程，需要一个有效的 Azure 订阅。

> [!NOTE]
> 如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用][lnk_free_trial]。
> 
> 

## <a name="provision-the-solution"></a>预配解决方案

1. 使用 Azure 帐户凭据登录 azureiotsuite.com，然后单击“+”创建解决方案。
2. 在“连接工厂”磁贴上单击“选择”。
3. 为连接工厂预配置解决方案输入**解决方案名称**。
4. 选择要用于预配解决方案的“订阅”和“区域”。
5. 单击“创建解决方案”  开始预配过程。 此过程通常需要数分钟的运行时间。

### <a name="while-you-wait-for-the-provisioning-process-to-complete"></a>在等待预配过程完成时，请执行以下操作

1. 单击状态为“正在预配”的解决方案磁贴。
2. 请在 Azure 服务部署到 Azure 订阅中时，注意“预配状态”  。
3. 预配完成之后，状态将更改为“就绪” 。
4. 单击磁贴，会在右边窗格中看到解决方案的详细信息。

> [!NOTE]
> 如果在部署预配置解决方案时遇到问题，请查看 [azureiotsuite.com 站点上的权限][lnk-permissions]和[常见问题][lnk-faq]。 如果问题仍然存在，请在[门户][lnk-portal]中创建服务票证。

是否有你预期会看到但没有列出的解决方案详细信息？ 请在 [用户之声](https://feedback.azure.com/forums/321918-azure-iot)中向我们提供功能建议。

## <a name="scenario-overview"></a>方案概述

部署连接工厂预配置解决方案时，可以通过预先填充在其中的资源来逐步执行常见的工业方案。 在本方案中，数个连接到解决方案的工厂将报告计算设备综合效率 (OEE) 和关键性能指标 (KPI) 所需的数据值。 以下部分介绍了如何：

* 监视工厂、生产线、装配站 OEE 和 KPI 值
* 使用 Azure 时序见解分析从这些设备生成的遥测数据
* 根据警报解决问题

此方案的一大功能是，用户可以从解决方案仪表板远程执行所有这些操作。 不需对设备进行物理访问。

## <a name="view-the-solution-dashboard"></a>查看解决方案仪表板

该解决方案仪表板可让你管理部署的解决方案。 它以分层方式表示全局工厂配置。 例如，你可以查看 OEE 和 KPI，并针对遥测和操作警报发布新节点。

1. 当预配完成且预配置解决方案的磁贴指示“就绪”时，请选择“启动”，在新的选项卡中打开连接工厂解决方案门户。

    ![启动预配置解决方案][img-launch-solution]

1. 默认情况下，此解决方案门户会显示*仪表板*。 可以通过页面左侧的菜单导航到门户的其他区域。

    ![连接工厂预配置解决方案仪表板][cf-img-menu]

仪表板将显示以下信息：

* 一个“工厂列表”面板，显示解决方案中的状态、位置和当前生产配置。 第一次运行解决方案时有多个模拟设备。 进行生产线模拟时，每条生产线有三个真实的 OPC UA 服务器，执行模拟任务并共享数据。 有关 OPC UA 的详细信息，请参阅[常见问题解答][lnk-faq]。
* 一个**地图**，用于显示每个连接到解决方案的设备的位置。 该解决方案可以使用必应地图 API，在地图上根据信息来绘图。 如果订阅启用了必应地图企业版 API，则会自动使用此功能。 否则，请参阅[常见问题][lnk-faq]，了解如何将地图变成动态地图。
* 一个“警报”面板，显示在遥测或 OEE/KPI 值超出特定阈值时生成的警报。
* 一个“设备综合效率”面板，显示整个企业的 OEE 值，或者显示所查看的工厂/生产线/装配站。 该值是从装配站视图聚合到企业级别的聚合值。 可以进一步分析 OEE 图及其构成元素。
* 一个“关键性能指标”面板，显示已生产的设备数，以及所查看的整个企业或工厂/生产线/装配站的能耗。 这些值是从装配站视图聚合到企业级别的聚合值。

## <a name="view-factories"></a>查看工厂

“工厂”面板显示解决方案中所有工厂的地理位置、状态以及当前的生产配置。 可以从位置列表导航到解决方案层次结构中的其他级别。 列表中的行是超链接，链接该位置的生产线的详细信息。 然后，可以通过这些超链接深入了解生产线详细信息，最深可达装配站级别视图。 也可对列表进行筛选。

![连接工厂预配置解决方案工厂][cf-img-factories] 

1. “工厂面板”显示此解决方案的工厂列表。

2. 工厂列表最初显示预配过程创建的六个工厂。 可向解决方案添加更多的模拟设备和物理设备。

3. 若要查看工厂的详细信息，请单击工厂列表中的行。

4. 若要查看生产线的详细信息，请单击列表中的行。

5. 若要查看生产线上某个装配站的已发布 OPC UA 节点，请单击列表中的行。

6. 若要查看装配站中特定节点的详细信息，请单击列表中的行。 此操作会启动带时序见解可视化效果的上下文面板。 单击这些图即可在时序见解资源管理器环境中执行进一步的分析。

## <a name="view-map"></a>查看地图

如果订阅可以访问必应地图 API，则“工厂”视图会显示解决方案中所有工厂的地理位置和状态。 单击地图上显示的位置即可了解位置详细信息。

![连接工厂预配置解决方案地图][cf-img-map]

## <a name="view-alerts"></a>查看警报

“警报”历史记录面板显示因报告的值或计算的 OEE/KPI 值超出所配置的阈值而生成的警报。 此面板显示层次结构中每个级别的警报，从装配站级别视图到全局视图，均可显示。 警报包含对警报内容、日期、时间、位置以及出现次数的说明。 可以根据时序见解数据获取对引发警报的数据的见解。 适用情况下，时序见解数据会在警报中实现可视化。 如果你是管理员，则可对警报执行默认操作，例如：

* 关闭警报。
* 确认警报。

（可选）可以执行更复杂的操作。 例如，对于装配站的压力 OPC UA 节点，可执行以下操作：

* 在新的浏览器窗口中加载网页，以便显示支持信息。
* 在设备上调用 OPC UA 方法，以便消除警报原因。
* 阻止使用默认操作。

    ![连接工厂预配置解决方案警报][cf-img-alerts]

> [!NOTE]
> 这些警报是根据规则生成的，而这些规则是在预配置解决方案的配置文件中指定的。 这些规则可以在 OEE 或 KPI 数据或 OPC UA 节点值超出其配置的阈值时生成警报。

1. “警报”面板显示在此解决方案中生成的警报。

2. 若要查看警报的详细信息，请单击警报面板中的警报。

3. 若要进一步分析警报数据，请单击警报面板中的图，打开时序见解资源管理器环境。

4. 若要解决警报问题，可在警报面板中执行多项操作。 请选择相应的选项，然后单击执行操作命令按钮。

## <a name="view-overall-equipment-efficiency"></a>查看设备综合效率

OEE 使用与生产相关的关键运营参数来评价制造过程的效率。 OEE 是一项工业标准度量指标，其计算方式是将可用率、性能等级和成品率相乘：OEE = 可用率 x 性能等级 x 成品率。

![连接工厂预配置解决方案 OEE][cf-img-oee]

1. 若要查看层次结构中任意级别的 OEE，请导航到所需的特定视图。 该视图的 OEE 以及组成 OEE 百分比的每个元素将显示在面板中。

2. 若要进一步分析层次结构数据中任意级别的 OEE，请单击 OEE 百分比、可用性百分比、性能百分比或成品百分比。 此时会出现一个带时序见解可视化效果的上下文面板，其中会显示过去 1 小时、过去 24 小时以及过去 7 天的数据。

    ![连接工厂预配置解决方案 TSI 可视化效果][cf-img-tsi-visualization]

3. 若要进一步分析警报数据，请单击警报面板中的图。 此操作会打开时序见解资源管理器环境。

    ![连接工厂预配置解决方案 TSI 资源管理器][cf-img-tsi-explorer]

## <a name="view-key-performance-indicators"></a>查看关键性能指标

此解决方案提供两个关键性能指标：“每小时生产的设备数”和“能耗（千瓦时）”。

![连接工厂预配置解决方案 KPI][cf-img-kpi]

1. 若要查看层次结构中任意级别的“每小时生产的设备数”或“能耗”，请导航到所需的特定视图。 在面板中显示的“每小时生产的设备数”和“能耗”。

2. 若要进一步分析层次结构数据中任意级别的“每小时生产的设备数”或“能耗”，请单击“关键性能指标”面板中的已生产设备仪表或能耗仪表。 此时会出现一个带时序见解可视化效果的上下文面板，用于查看过去 1 小时、过去 24 小时以及过去 7 天的数据。

## <a name="scenario-review"></a>方案审核

在本方案中，你在仪表板中对工厂 OEE 和 KPI 值进行了监视。 然后，你使用了时序见解来获取详细信息，以便进一步分析 OEE 和 KPI 的遥测数据，从而检测异常情况。 此外，你还使用了警报面板来查看工厂的问题，并使用了可用操作来解决警报问题。

## <a name="other-features"></a>其他功能

以下部分介绍了连接工厂预配置解决方案的一些其他功能，这些功能未在前面的方案中介绍。

## <a name="apply-filters"></a>应用筛选器

1. 单击 **V 形图标**，在工厂位置面板或警报面板中显示可用筛选器的列表。

2. 此时会显示筛选器面板。 

    ![连接工厂预配置解决方案筛选器][cf-img-alert-filter]

3. 选择所需筛选器，也可根据需要在筛选器字段中键入自定义文本。

4. 然后应用筛选器。 筛选器状态也通过漏斗图显示在仪表板中，漏斗图在工厂和警报表中显示。

    ![连接工厂预配置解决方案筛选器][cf-img-alert-filter-funnel]

    > [!NOTE]
    > 活动筛选器不影响显示的 OEE 和 KPI 值，仅筛选列表内容。

5. 若要清除筛选器，请单击漏斗图，然后单击筛选器上下文面板中的筛选器。 工厂和警报表中会显示文本“全部”。

## <a name="browse-an-opc-ua-server"></a>浏览 OPC UA 服务器

部署预配置的解决方案时，将会自动预配模拟 OPC UA 服务器，此类服务器可以通过解决方案浏览器进行浏览。 这些服务器是模拟 OPC UA 服务器。 模拟服务器可让你试验预配置解决方案，而不需要部署实际的物理服务器。 若要将实际 OPC UA 服务器连接到解决方案，请参阅[将 OPC UA 设备连接到连接工厂预配置解决方案][lnk-connect-cf]教程。

1. 单击仪表板导航栏中的**工厂图标**。

    ![连接工厂预配置解决方案服务器浏览器][cf-img-server-browser]

2. 从预配置的列表中选择一台服务器。 该列表显示在预配置解决方案中为你部署的服务器。

    ![连接工厂预配置解决方案服务器选择][cf-img-server-choice]

3. 单击“连接”，此时会显示一个安全对话框。 就此模拟来说，可以安全地单击“继续”

4. 单击服务器树中的任意节点将其展开。 正在发布遥测的节点旁边有一个对勾标记。

    ![连接工厂预配置解决方案服务器树][cf-img-server-tree]

5. 右键单击某个项，对该节点执行读取、写入、发布或调用操作。 能够使用哪些操作取决于你的权限和节点的属性。 读取选项会显示一个上下文面板，其中显示特定节点的值。 写入选项会显示一个上下文面板，可以在其中输入新值。 调用选项会显示一个节点，可以在其中输入调用的参数。

## <a name="publish-a-node"></a>发布节点

浏览“模拟 OPC UA 服务器”时，还可选择发布新节点。 可以在解决方案中分析这些节点的遥测。 这些模拟 OPC UA 服务器可让你试验预配置解决方案，而不需要部署实际的物理设备。

1. 浏览到要发布的 OPC UA 服务器浏览器树中的某个节点。

2. 右键单击该节点。

3. 选择“发布”。

    ![连接工厂发布节点][cf-img-publish-node]

4. 此时会显示一个上下文面板，告知你发布成功。 节点显示在装配站级别视图中，旁边有一个复选标记。

    ![连接工厂预配置解决方案发布成功][cf-img-publish-success]

## <a name="command-and-control"></a>命令和控制

连接工厂允许你直接从云对工业设备发出命令并进行控制。 可以通过此功能来响应设备生成的警报。 例如，可以从云向设备发送命令。 可以在 OPC UA 服务器浏览器树的 **StationCommands** 节点中查找可用命令。 在本方案中，你要打开慕尼黑生产线装配站的泄压阀。 若要使用命令和控制功能，必须通过“管理员”角色完成预配置解决方案的部署。

1. 浏览到 OPC UA 服务器浏览器树中的 **StationCommands** 节点。

2. 选择要使用的命令。

3. 右键单击该节点。

4. 选择“调用”。

    ![连接工厂预配置解决方案调用命令][cf-img-call-command]

5. 此时会显示一个上下文面板，告知你要调用的具体方法，以及适用的参数详细信息。

6. 选择“调用”。

    ![连接工厂预配置解决方案调用上下文][cf-img-call-context]

7. 上下文面板会进行更新，告知你该方法调用成功。 可以通过读取压力节点的值（在调用后已更新）来验证调用是否成功。

    ![连接工厂预配置解决方案调用成功][cf-img-call-success]


## <a name="behind-the-scenes"></a>幕后

当你部署预配置解决方案时，部署过程会在你选择的 Azure 订阅中创建多个资源。 可以在 Azure [门户][lnk-portal]中查看这些资源。 部署过程会创建一个 **资源组** ，其名称基于你为预配置解决方案选择的名称：

![Azure 门户中的预配置解决方案][img-cf-portal]

你可以查看每个资源的设置，方法是在资源组中的资源列表中选择该资源。

你也可以查看预配置解决方案的源代码。 连接工厂预配置解决方案源代码位于 [azure-iot-connected-factory][lnk-cfgithub] GitHub 存储库中：

完成后，可在 [azureiotsuite.com][lnk-azureiotsuite] 站点的 Azure 订阅中删除预配置解决方案。 利用该站点，可以轻松地删除在创建预配置解决方案时预配的所有资源。

> [!NOTE]
> 若要确保删除与预配置解决方案相关的所有内容，请在 [azureiotsuite.com][lnk-azureiotsuite] 站点中将其删除。 不要删除门户中的资源组。

## <a name="next-steps"></a>后续步骤

部署正常工作的预配置解决方案后，可以继续通过阅读以下文章开始使用 IoT 套件：

* [连接工厂预配置解决方案演练][lnk-rm-walkthrough]
* [将设备连接到连接工厂预配置解决方案][lnk-connect-cf]
* [azureiotsuite.com 站点权限][lnk-permissions]

[img-cf-home]:media/iot-suite-connected-factory-overview/cf-dashboard.png
[img-launch-solution]: media/iot-suite-connected-factory-overview/launch-cf.png
[cf-img-menu]: media/iot-suite-connected-factory-overview/cf-dashboard-menu.png
[cf-img-factories]:media/iot-suite-connected-factory-overview/cf-dashboard-factory.png
[cf-img-map]:media/iot-suite-connected-factory-overview/cf-dashboard-map.png
[cf-img-alerts]:media/iot-suite-connected-factory-overview/cf-dashboard-alerts.png
[cf-img-oee]:media/iot-suite-connected-factory-overview/cf-dashboard-oee.png
[cf-img-kpi]:media/iot-suite-connected-factory-overview/cf-dashboard-kpi.png
[cf-img-tsi-visualization]:media/iot-suite-connected-factory-overview/cf-dashboard-tsi.png
[cf-img-tsi-explorer]:media/iot-suite-connected-factory-overview/tsi-explorer.png
[cf-img-server-browser]: media/iot-suite-connected-factory-overview/cf-dashboard-browser.png
[cf-img-server-choice]: media/iot-suite-connected-factory-overview/cf-select-server.png
[cf-img-server-tree]:media/iot-suite-connected-factory-overview/cf-server-tree.png
[cf-img-publish-node]:media/iot-suite-connected-factory-overview/cf-publish-node1.png
[cf-img-publish-success]:media/iot-suite-connected-factory-overview/cf-publish-success.png
[cf-img-call-command]:media/iot-suite-connected-factory-overview/cf-command-and-control-call.png
[cf-img-call-context]:media/iot-suite-connected-factory-overview/cf-command-and-control-call-button.png
[cf-img-call-success]:media/iot-suite-connected-factory-overview/cf-command-and-control-succeed.png
[img-cf-portal]:media/iot-suite-connected-factory-overview/cf-resource-group.png
[cf-img-alert-filter]:media/iot-suite-connected-factory-overview/cf-filter.png
[cf-img-alert-filter-funnel]:media/iot-suite-connected-factory-overview/cf-filter-funnel.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-portal]: http://portal.azure.com/
[lnk-cfgithub]: https://github.com/Azure/azure-iot-connected-factory
[lnk-rm-walkthrough]: iot-suite-connected-factory-sample-walkthrough.md
[lnk-connect-cf]: iot-suite-connected-factory-gateway-deployment.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-faq]: iot-suite-faq.md
