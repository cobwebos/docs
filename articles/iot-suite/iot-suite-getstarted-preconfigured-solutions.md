<properties
	pageTitle="预配置解决方案入门 | Azure"
	description="遵循本教程，了解如何部署 Azure IoT 套件预配置解决方案。"
	services=""
    suite="iot-suite"
	documentationCenter=""
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-suite"
     ms.date="03/02/2016"
     wacn.date="05/17/2016"/>

# 教程：预配置解决方案入门

## 介绍

Azure IoT 套件[预配置解决方案][lnk-preconfigured-solutions]结合了多项 Azure IoT 服务，以提供可实现常见 IoT 商业应用场景的端到端解决方案。

本教程演示如何预配“远程监视”预配置解决方案。此外还将逐步解说远程监视预配置解决方案的基本功能。

若要完成本教程，你需要一个有效的 Azure 订阅。

> [AZURE.NOTE]  如果没有帐户，可以创建一个试用帐户，只需几分钟即可完成。有关详细信息，请参阅[试用][1rmb-trial]。

## 预配远程监视预配置解决方案

1.  使用 Azure 帐户凭据登录 [azureiotsuite.com][lnk-azureiotsuite]，然后单击 **+** 创建新的解决方案。

    > [AZURE.NOTE] 如果在获取预配解决方案所需的权限时遇到问题，请参阅 [azureiotsuite.com 站点上的权限][lnk-permissions]以获得相关指导。

2.  单击“远程监视”磁贴上的“选择”。

3.  为远程监视预配置解决方案输入“解决方案名称”。

4.  选择要用于预配解决方案的“区域”和“订阅”。

5.  单击“创建解决方案”开始预配过程。这通常需要数分钟的运行时间。

## 等待预配过程完成

1. 单击状态为“正在预配”的解决方案的磁贴。
 
2. 在 Azure 订阅中部署 Azure 服务时，请注意**预配状态**。

3. 预配完成之后，状态会更改为“就绪”。

4. 单击磁贴，随即会在右边窗格中看到解决方案的详细信息。

> [AZURE.NOTE] 如果在部署预配置解决方案时遇到问题，请参阅 [azureiotsuite.com 站点上的权限][lnk-permissions]和[常见问题解答][lnk-faq]。如果问题仍然存在，请在[门户][lnk-portal]中创建服务票证。

是否有你预期会看到但没有列出的解决方案详细信息？ 请在[用户心声](https://feedback.azure.com/forums/321918-azure-iot)中向我们提供功能建议。

## 查看远程监视解决方案仪表板

该解决方案仪表板可让你管理部署的解决方案。例如，你可以查看遥测数据、添加设备以及配置规则。

1.  当预配完成且预配置解决方案的磁贴指示“就绪”时，单击“启动”以在新的选项卡中打开远程监视解决方案门户。

    ![][img-launch-solution]

2.  默认情况下，此解决方案门户会显示“解决方案仪表板”。你可以使用左侧菜单选择其他视图。

    ![][img-dashboard]

仪表板将显示以下信息：

- 地图显示每个设备连接到解决方案的位置。第一次运行解决方案时有四个模拟设备。模拟设备作为 Azure WebJobs 实现，而解决方案使用必应地图 API 在地图上绘制信息。
- “遥测历史记录”面板从所选的设备绘制近乎实时的湿度和温度遥测数据，并显示聚合数据，例如最大、最小和平均湿度。
- “警报历史记录”面板在遥测值超过阈值时显示最近的警报事件。除了预配置解决方案所创建的示例之外，还可以定义自己的警报。

## 查看解决方案设备列表

设备列表会显示解决方案中所有已注册的设备。你可以查看和编辑设备元数据、添加或删除设备，以及向设备发送命令。

1.  单击左侧菜单中的“设备”，以显示此解决方案的“设备列表”。

    ![][img-devicelist]

2.  此设备列表显示预配过程所创建的四个模拟设备。

3.  单击设备列表中的设备，以查看设备详细信息。

    ![][img-devicedetails]

“设备详细信息”面板包含三个部分：

- “操作”部分列出可以在设备上执行的操作。如果禁用设备，设备将不再能够发送遥测数据或接收命令。如果禁用设备，之后可以重新启用它。你可以添加与设备关联的规则，以便在遥测值超出阈值时触发警报。也可以向设备发送命令。当设备第一次连接时，它会告诉解决方案可以响应的命令。
- “设备属性”部分列出设备元数据。此元数据某些来自设备本身（例如制造商），某些由解决方案生成（例如创建时间）。你可以从此处编辑设备元数据。
- “身份验证密钥”部分列出设备可用来向解决方案进行身份验证的密钥。

## 向设备发送命令

设备详细信息窗格显示特定设备支持的所有命令，并且可让你向设备发送命令。设备在第一次启动时，会向解决方案发送其支持的命令的相关信息。

1.  单击所选设备的设备详细信息窗格中的“命令”。

    ![][img-devicecommands]

2.  从命令列表中选择“PingDevice”。

3.  单击“发送命令”。

4.  你可以在命令历史记录中查看命令的状态。

    ![][img-pingcommand]

解决方案会跟踪其发送的每个命令的状态。结果最初为“挂起”。当设备报告它已执行命令时，结果会设置为“成功”。

## 添加新的模拟设备

1.  导航回到设备列表。

2.  单击左下角的“+ 添加设备”以添加新设备。

    ![][img-adddevice]

3.  单击“模拟设备”磁贴上的“新增”。

    ![][img-addnew]
    
    如果选择创建“自定义设备”，则除了创建新的模拟设备，也可以添加物理设备。若要了解详细信息，请参阅[将设备连接到 IoT 套件远程监视预配置方解决案][lnk-connecting-devices]。

4.  选择“自行定义设备 ID”，然后输入唯一的设备 ID 名称，例如 **mydevice\_01**。

5.  单击“创建”。

    ![][img-definedevice]

6. 在“添加模拟设备”的步骤 3 中，单击“完成”以返回到设备列表。

7. 可以在设备列表中查看“正在运行”的设备。

    ![][img-runningnew]

8. 也可以在仪表板上查看来自新设备的模拟遥测：

    ![][img-runningnew-2]

## 编辑设备元数据

1.  导航回到设备列表。

2.  在“设备列表”中选择新设备，然后单击“编辑”以编辑“设备属性”：

    ![][img-editdevice]

3. 向下滚动并对纬度和经度值进行更改。然后单击“将更改保存到设备注册表”。

    ![][img-editdevice2]

4. 导航回到仪表板，地图上的设备位置已更改：

    ![][img-editdevice3]

## 为新设备添加规则

刚才添加的新设备没有规则。在本节中，你将添加一个规则，以便在新设备所报告的温度超过 47 度时触发警报。在开始之前，请注意仪表板上新设备的遥测历史记录显示设备温度绝不会超过 45 度。

1.  导航回到设备列表。

2.  在“设备列表”中选择新设备，然后单击“添加规则”以便为设备添加新规则。

3. 创建一个规则，该规则使用“温度”作为数据字段，使用“AlarmTemp”作为温度超过 47 度时的输出：

    ![][img-adddevicerule]

4. 单击“保存并查看规则”以保存更改。

5.  单击新设备的设备详细信息窗格中的“命令”。

    ![][img-adddevicerule2]

6.  从命令列表中选择“ChangeSetPointTemp”并将“SetPointTemp”设置为 45。然后单击“发送命令”：

    ![][img-adddevicerule3]

7.  导航回到解决方案仪表板。一小段时间之后，你会在新设备所报告的温度超过 47 度阈值时，在“警报历史记录”窗格中看到新的条目：

    ![][img-adddevicerule4]

8. 可以在仪表板的“规则”页上查看和编辑所有规则：

    ![][img-rules]

9. 可以在仪表板的“操作”页上查看和编辑为了响应规则而可采取的所有操作：

    ![][img-actions]

> [AZURE.NOTE] 可以定义一些这样的操作：可发送电子邮件或 SMS 以响应规则，或通过[逻辑应用][lnk-logic-apps]与业务线系统集成。

## 幕后

当你部署预配置解决方案时，部署过程会在你选择的 Azure 订阅中创建多个资源。你可以在 Azure [门户][lnk-portal]中查看这些资源。部署过程会创建一个**资源组**，其名称基于你为预配置解决方案选择的名称：

![][img-portal]

你可以查看每个资源的设置，方法是在资源组中的资源列表中选择该资源。上方屏幕截图显示预配置解决方案中所用的 IoT 中心设置。

你也可以查看预配置解决方案的源代码。远程监视预配置解决方案源代码位于 [azure-iot-remote-monitoring][lnk-rmgithub] 中：

- **DeviceAdministration** 文件夹包含仪表板的源代码。
- **Simulator** 文件夹包含模拟设备的源代码。
- **EventProcessor** 文件夹包含后端进程的源代码，可用于处理传入遥测。

完成后，你可以在 [azureiotsuite.com][lnk-azureiotsuite] 站点上删除 Azure 订阅中的预配置解决方案 — 这可让你轻松地删除在创建预配置解决方案时预配的所有资源。

> [AZURE.NOTE] 若要确保删除与预配置解决方案相关的所有项目，请从 [azureiotsuite.com][lnk-azureiotsuite] 进行删除，而不只是删除门户中的资源组。

## 后续步骤

你已经生成一个可运行的预配置解决方案，现在可以继续进行以下演练：

-   [预配置解决方案自定义指南][lnk-customize]
-   [预见性维护预配置解决方案概述][lnk-predictive]

[img-launch-solution]: ./media/iot-suite-getstarted-preconfigured-solutions/launch.png
[img-dashboard]: ./media/iot-suite-getstarted-preconfigured-solutions/dashboard.png
[img-devicelist]: ./media/iot-suite-getstarted-preconfigured-solutions/devicelist.png
[img-devicedetails]: ./media/iot-suite-getstarted-preconfigured-solutions/devicedetails.png
[img-devicecommands]: ./media/iot-suite-getstarted-preconfigured-solutions/devicecommands.png
[img-pingcommand]: ./media/iot-suite-getstarted-preconfigured-solutions/pingcommand.png
[img-adddevice]: ./media/iot-suite-getstarted-preconfigured-solutions/adddevice.png
[img-addnew]: ./media/iot-suite-getstarted-preconfigured-solutions/addnew.png
[img-definedevice]: ./media/iot-suite-getstarted-preconfigured-solutions/definedevice.png
[img-runningnew]: ./media/iot-suite-getstarted-preconfigured-solutions/runningnew.png
[img-runningnew-2]: ./media/iot-suite-getstarted-preconfigured-solutions/runningnew2.png
[img-rules]: ./media/iot-suite-getstarted-preconfigured-solutions/rules.png
[img-editdevice]: ./media/iot-suite-getstarted-preconfigured-solutions/editdevice.png
[img-editdevice2]: ./media/iot-suite-getstarted-preconfigured-solutions/editdevice2.png
[img-editdevice3]: ./media/iot-suite-getstarted-preconfigured-solutions/editdevice3.png
[img-adddevicerule]: ./media/iot-suite-getstarted-preconfigured-solutions/addrule.png
[img-adddevicerule2]: ./media/iot-suite-getstarted-preconfigured-solutions/addrule2.png
[img-adddevicerule3]: ./media/iot-suite-getstarted-preconfigured-solutions/addrule3.png
[img-adddevicerule4]: ./media/iot-suite-getstarted-preconfigured-solutions/addrule4.png
[img-actions]: ./media/iot-suite-getstarted-preconfigured-solutions/actions.png
[img-portal]: ./media/iot-suite-getstarted-preconfigured-solutions/portal.png

[1rmb-trial]: /pricing/1rmb-trial/
[lnk-preconfigured-solutions]: /documentation/articles/iot-suite-what-are-preconfigured-solutions
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-customize]: /documentation/articles/iot-suite-guidance-on-customizing-preconfigured-solutions
[lnk-predictive]: /documentation/articles/iot-suite-predictive-overview
[lnk-connecting-devices]: /documentation/articles/iot-suite-connecting-devices
[lnk-permissions]: /documentation/articles/iot-suite-permissions
[lnk-logic-apps]: /documentation/services/app-service/logic/
[lnk-portal]: http://portal.azure.cn/
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-faq]: /documentation/articles/iot-suite-faq

<!---HONumber=Mooncake_0523_2016-->