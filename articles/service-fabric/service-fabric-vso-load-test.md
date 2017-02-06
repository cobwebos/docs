---
title: "使用 Visual Studio Team Services 对应用程序进行负载测试 | Microsoft 文档"
description: "了解如何使用 Visual Studio Team Services 对 Azure Service Fabric 应用程序进行压力测试。"
services: service-fabric
documentationcenter: na
author: cawams
manager: timlt
editor: 
ms.assetid: fc743585-0d1b-483f-981d-493f4552ac07
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/18/2016
ms.author: cawa
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: e8e270ce865d4da3ee219958b308db2c1c89b11b


---
# <a name="load-test-your-application-by-using-visual-studio-team-services"></a>使用 Visual Studio Team Services 对应用程序进行负载测试
本文说明如何使用 Microsoft Visual Studio 负载测试功能对应用程序进行压力测试。 其中使用了 Azure Service Fabric 有状态服务后端和无状态服务 Web 前端。 此处使用的示例应用程序是一个飞机定位模拟器。 你需要提供飞机 ID、出发时间和目的地。 应用程序的后端处理请求，前端在地图上显示与条件匹配的飞机。

下图演示了即将测试的 Service Fabric应用程序。

![示例飞机定位应用程序的示意图][0]

## <a name="prerequisites"></a>先决条件
开始之前，需要执行以下操作：

* 获取 Visual Studio Team Services 帐户。 可以访问 [Visual Studio Team Services](https://www.visualstudio.com)，获取一个免费帐户。
* 获取并安装 Visual Studio 2013 或 Visual Studio 2015。 本文使用 Visual Studio 2015 Enterprise 版本，但是 Visual Studio 2013 和其他版本应该也可以正常工作。
* 将应用程序部署到过渡环境。 若要了解如何执行此操作，请参阅[如何使用 Visual Studio 将应用程序部署到远程群集](service-fabric-publish-app-remote-cluster.md)。
* 了解应用程序的使用模式。 这项信息用于模拟负载模式。
* 了解负载测试的目标。 这可帮助你解释和分析负载测试结果。

## <a name="create-and-run-the-web-performance-and-load-test-project"></a>创建并运行 Web 性能与负载测试项目
### <a name="create-a-web-performance-and-load-test-project"></a>创建 Web 性能与负载测试项目
1. 打开 Visual Studio 2015。 在菜单栏上，依次选择“**文件**” > “**新建**” > “**项目**”，打开“**新建项目**”对话框。
2. 展开“**Visual C#**”节点，然后依次选择“**测试**” > “**Web 性能和负载测试项目**”。 命名项目，然后选择“**确定**”按钮。

    ![“新建项目”对话框屏幕截图][1]

    你应会在解决方案资源管理器中看到新的 Web 性能和负载测试项目。

    ![显示新项目的解决方案资源管理器屏幕截图][2]

### <a name="record-a-web-performance-test"></a>录制 Web 性能测试
1. 打开 .webtest 项目。
2. 选择“**添加录制**”图标，开始在浏览器中录制会话。

    ![浏览器中“添加录制”图标的屏幕截图][3]

    ![浏览器中“录制”按钮的屏幕截图][4]
3. 浏览到 Service Fabric应用程序。 录制面板应显示 Web 请求。

    ![录制面板中 Web 请求的屏幕截图][5]
4. 执行用户预期会执行的一系列操作。 这些操作将用作生成负载的模式。
5. 完成后，选择“**停止**”按钮，停止录制。

    ![“停止”按钮的屏幕截图][6]

    Visual Studio 中的 .webtest 项目应该已捕捉到一系列请求。 将自动替换动态参数。 此时，你可以删除不属于测试方案的任何额外的重复依赖性请求。
6. 保存项目，然后选择“**运行测试**”命令，在本地运行 Web 性能测试，确保一切正常运行。

    ![“运行测试”命令的屏幕截图][7]

### <a name="parameterize-the-web-performance-test"></a>参数化 Web 性能测试
你可以将 Web 性能测试转换成 Web 性能测试代码，然后编辑代码，以参数化 Web 性能测试。 或者，可以将 Web 性能测试绑定到数据列表，以便测试循环访问数据。 若要详细了解如何将 Web 性能测试转换成编码测试，请参阅[生成并运行编码 Web 性能测试](https://msdn.microsoft.com/library/ms182552.aspx)。 若要了解如何将数据绑定到 Web 性能测试，请参阅[将数据源添加到 Web 性能测试](https://msdn.microsoft.com/library/ms243142.aspx)。

对于本示例，我们将 Web 性能测试转换成代码测试，就可以使用生成的 GUID 替换飞机 ID，并添加更多请求以将航班发送到不同位置。

### <a name="create-a-load-test-project"></a>创建负载测试项目
负载测试项目由 Web 性能测试和单位测试描述的一个或多个方案以及其他指定的负载测试设置组成。 以下步骤说明如何创建负载测试项目：

1. 在“Web 性能和负载测试”项目的快捷菜单中，依次选择“**添加**” > “**负载测试**”。 在“**负载测试**”向导中，选择“**下一步**”按钮，配置测试设置。
2. 在“**负载模式**”部分中，选择是要执行常量用户负载还是执行分级负载（在一段时间内，开始用户较少，随后用户不断增加）。

    如果你能精确估计用户负载量，并且想要查看当前系统的执行方式，请选择“**常量负载**”。 如果目标是要了解系统在不同负载下的行为是否一致，请选择“**分级负载**”。
3. 在“**测试组合**”部分中，选择“**添加**”按钮，然后选择要包含在负载测试中的测试。 可以使用“**分布**”列，指定每项测试在测试总运行时间中所占的百分比。
4. 在“**运行设置**”部分中，指定负载测试持续期间。

   > [!NOTE]
   > 只有当使用 Visual Studio 在本地运行负载测试时，才能使用“**测试迭代**”选项。
   >
   >
5. 在“**运行设置**”的“**位置**”部分中，指定生成负载测试请求的位置。 该向导可能会提示你登录 Team Services 帐户。 请登录，然后选择一个地理位置。 完成后，选择“**完成**”按钮。
6. 创建负载测试后，打开 .loadtest 项目，然后选择当前运行设置。例如，依次选择“**运行设置**” > “**运行设置 1 [可用]**”。 此时，系统会在“**属性**”窗口中打开运行设置。
7. 在“**运行设置**”属性窗口的“**结果**”部分中，“**计时详细信息存储**”设置的默认值应为“**无**”。 将此值更改为“**所有的详细信息**”可以获取有关负载测试结果的详细信息。 若要详细了解如何连接 Visual Studio Team Services，以及如何运行负载测试，请参阅[负载测试](https://www.visualstudio.com/load-testing.aspx)。

### <a name="run-the-load-test-by-using-visual-studio-team-services"></a>使用 Visual Studio Team Services 运行负载测试
选择“**运行负载测试**”命令，开始运行测试。

![“运行负载测试”命令的屏幕截图][8]

## <a name="view-and-analyze-the-load-test-results"></a>查看和分析负载测试结果
随着负载测试的进行，性能信息将图形化。 你应会看到类似于下面的图形。

![负载测试结果性能图形的屏幕截图][9]

1. 选择页面顶部附近的“**下载报告**”链接。 下载报告后，选择“**查看报告**”按钮。

    在“**图形**”选项卡上，可以查看各种性能计数器的图形。 “**摘要**”选项卡显示整体测试结果。 “**表**”选项卡显示成功和失败的负载测试的总数。
2. 选择“**测试**” > “**失败**”和“**错误**” > “**计数**”列上的数字链接，查看错误详细信息。

    “**详细信息**”选项卡显示失败请求的虚拟用户和测试方案信息。 如果负载测试包含多个方案，此数据相当有用。

若要详细了解如何查看负载测试结果，请参阅[在负载测试分析器的“图形”视图中分析负载测试结果](https://www.visualstudio.com/load-testing.aspx)。

## <a name="automate-your-load-test"></a>自动执行负载测试
Visual Studio Team Services 负载测试提供了 API 来帮助你管理负载测试，并分析 Team Services 帐户中的结果。 有关详细信息，请参阅[云负载测试 Rest API](http://blogs.msdn.com/b/visualstudioalm/archive/2014/11/03/cloud-load-testing-rest-apis-are-here.aspx)。

## <a name="next-steps"></a>后续步骤
* [在本地计算机开发安装过程中监视和诊断服务](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[0]: ./media/service-fabric-vso-load-test/OverviewDiagram.png
[1]: ./media/service-fabric-vso-load-test/NewProjectDialog.png
[2]: ./media/service-fabric-vso-load-test/Project.png
[3]: ./media/service-fabric-vso-load-test/AddRecording.png
[4]: ./media/service-fabric-vso-load-test/AddRecording2.png
[5]: ./media/service-fabric-vso-load-test/ActionSequence.png
[6]: ./media/service-fabric-vso-load-test/StopRecording.png
[7]: ./media/service-fabric-vso-load-test/RunTest.png
[8]: ./media/service-fabric-vso-load-test/RunTest2.png
[9]: ./media/service-fabric-vso-load-test/Graph.png



<!--HONumber=Dec16_HO2-->


