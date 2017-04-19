---
title: "在 Azure 中使用 Visual Studio Team Services 进行持续交付 | Microsoft Docs"
description: "了解如何将 Visual Studio Team Services 团队项目配置为自动生成并部署到 Azure App Service 或云服务中的 Web 应用功能。"
services: cloud-services
documentationcenter: .net
author: mlearned
manager: douge
editor: 
ms.assetid: 797f67ad-e4d4-4063-ae91-41cbdf154191
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/06/2016
ms.author: mlearned
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: d80ce63eb7ddfd7c45726be887a772f9a7594b28
ms.lasthandoff: 04/06/2017


---
# <a name="continuous-delivery-to-azure-using-visual-studio-team-services"></a>使用 Visual Studio Team Services 向 Azure 持续交付
你可以将 Visual Studio Team Services 团队项目配置为自动生成并部署到 Azure Web 应用或云服务。  （有关如何使用*本地* Team Foundation Server 设置持续生成和部署系统的信息，请参阅[在 Azure 中持续传送云服务](cloud-services-dotnet-continuous-delivery.md)。）

本教程假设你已安装 Visual Studio 2013 和 Azure SDK。 如果你尚未安装 Visual Studio 2013，请在 [www.visualstudio.com](http://www.visualstudio.com) 上选择“**免费试用**”链接以下载该软件。 从[此处](http://go.microsoft.com/fwlink/?LinkId=239540)安装 Azure SDK。

> [!NOTE]
> 需要使用 Visual Studio Team Services 帐户来完成本教程：可以[免费建立一个 Visual Studio Team Services 帐户](http://go.microsoft.com/fwlink/p/?LinkId=512979)。
> 
> 

若要使用 Visual Studio Team Services 将云服务设置为自动生成并部署到 Azure，请执行下列步骤：

## <a name="1-create-a-team-project"></a>1：创建团队项目
按照[此处](http://go.microsoft.com/fwlink/?LinkId=512980)的说明创建团队项目，并将其链接到 Visual Studio。 本演练假设你使用 Team Foundation 版本控制 (TFVC) 作为源代码管理解决方案。 如果你要使用 Git 进行版本控制，请参阅[本演练的 Git 版本](http://go.microsoft.com/fwlink/p/?LinkId=397358)。

## <a name="2-check-in-a-project-to-source-control"></a>2：将项目签入到源控件
1. 在 Visual Studio 中，打开要部署的解决方案或创建一个新的解决方案。
   你可以通过执行本演练中的步骤来部署 Web 应用或云服务（Azure 应用程序）。
   若要创建新的解决方案，请创建新的 Azure 云服务项目或新的 ASP.NET MVC 项目。 确保项目以 .NET Framework 4 或 4.5 为目标。如果你要创建云服务项目，请添加 ASP.NET MVC Web 角色和辅助角色，并为 Web 角色选择 Internet 应用程序。 系统提示时，请选择“**Internet 应用程序**”。
   如果要创建 Web 应用，请选择 ASP.NET Web 应用程序项目模板，然后选择 MVC。 请参阅[在 Azure 应用服务中创建 ASP.NET Web 应用](../app-service-web/app-service-web-get-started-dotnet.md)。
   
   > [!NOTE]
   > Visual Studio Team Services 目前仅支持 Visual Studio Web 应用程序的 CI 部署。 网站项目不在支持范围内。
   > 
   > 
2. 打开解决方案的上下文菜单，然后选择“**将解决方案添加到源控件**”。
   
    ![][5]
3. 接受或更改默认值，然后选择“**确定**”按钮。 完成此过程后，源控件图标就会显示在**解决方案资源管理器**中。
   
    ![][6]
4. 打开解决方案的快捷菜单，然后选择“**签入**”。
   
    ![][7]
5. 在**团队资源管理器**的“**挂起的更改**”区域中，为签入键入注释并选择“**签入**”按钮。
   
    ![][8]
   
    记下在签入时用于包含或排除特定更改的选项。 如果已排除所需的更改，请选择“**全部包含**”链接。
   
    ![][9]

## <a name="3-connect-the-project-to-azure"></a>3：将项目连接到 Azure
1. 此时你已拥有一个包含某些源代码的 VS Team Services 团队项目，可以将该团队项目连接到 Azure。  在 [Azure 经典门户](http://go.microsoft.com/fwlink/?LinkID=213885)中选择你的云服务或 Web 应用，或通过选择左下角的 **+** 图标，再选择“**云服务**”或“**Web 应用**”，然后选择“**快速创建**”来创建新的云服务或 Web 应用。 选择“**使用 Visual Studio Team Services 设置发布**”链接。
   
    ![][10]
2. 在向导中，在文本框中键入 Visual Studio Team Services 帐户的名称，然后单击“**立即授权**”链接。 系统可能会要求你登录。
   
    ![][11]
3. 在“**连接请求**”弹出对话框中，选择“**接受**”按钮以授予 Azure 在 VS Team Services 中配置团队项目的权限。
   
    ![][12]
4. 授权成功后，将显示一个包含一系列 Visual Studio Team Services 团队项目的下拉列表。 选择在之前的步骤中创建的团队项目的名称，然后选择向导的复选标记按钮。
   
    ![][13]
5. 链接你的项目后，你将看到一些有关将更改签入到 Visual Studio Team Services 团队项目中的说明。  下次签入时，Visual Studio Team Services 将生成项目并将其部署到 Azure。  通过单击“**从 Visual Studio 签入**”链接并单击“**启动 Visual Studio**”链接（或位于门户屏幕底部具有等效功能的“**Visual Studio**”按钮）可立即尝试此操作。
   
    ![][14]

## <a name="4-trigger-a-rebuild-and-redeploy-your-project"></a>4：触发重新生成和重新部署项目
1. 在 Visual Studio 的**团队资源管理器**中，选择“**源控件资源管理器**”链接。
   
    ![][15]
2. 导航到解决方案文件并将其打开。
   
    ![][16]
3. 在**解决方案资源管理器**中，打开文件并进行更改。 例如，更改 MVC Web 角色中 Views\\Shared 文件夹下的文件 `_Layout.cshtml`。
   
    ![][17]
4. 编辑站点的徽标，并选择 **Ctrl+S** 来保存文件。
   
    ![][18]
5. 在“**团队资源管理器**”中，选择“**挂起的更改**”链接。
   
    ![][19]
6. 输入注释，然后选择“**签入**”按钮。
   
    ![][20]
7. 选择“**主页**”按钮可返回**团队资源管理器**主页。
   
    ![][21]
8. 选择“**生成**”链接可查看正在进行的生成。
   
    ![][22]
   
    **团队资源管理器**将显示已为你的签入触发生成。
   
    ![][23]
9. 双击正在进行的生成的名称可在生成进行中查看详细日志。
   
    ![][24]
10. 虽然生成正在进行中，但可查看使用向导将 TFS 链接到 Azure 时创建的生成定义。  打开生成定义的快捷菜单，然后选择“**编辑生成定义**”。
    
     ![][25]
    
     在“**触发器**”选项卡中，你将看到生成定义已设置为默认情况下在每次签入时生成。
    
     ![][26]
    
     在“**进程**”选项卡中，你会看到部署环境已设置为云服务或 Web 应用的名称。 如果你正在使用 Web 应用，看到的属性将不同于此处所示。
    
     ![][27]
11. 指定属性的值（如果希望这些值与默认值不同）。 Azure 发布属性在 **Deployment** 节中。
    
     下表显示了 **Deployment** 节中提供的属性：
    
    | 属性 | 默认值 |
    | --- | --- |
    | 允许不受信任的证书 |如果为 false，则 SSL 证书必须由根颁发机构签名。 |
    | 允许升级 |允许部署更新现有的部署，而不允许创建新部署。 保留 IP 地址。 |
    | 不要删除 |如果为 true，则不会覆盖现有的无关部署（允许升级）。 |
    | 部署设置的路径 |Web 应用的 .pubxml 文件的路径，相对于存储库的根文件夹。 对于云服务将忽略该属性。 |
    | Sharepoint 部署环境 |与服务名称相同。 |
    | Azure 部署环境 |Web 应用或云服务的名称 |
12. 如果你在使用多个服务配置（.cscfg 文件），可以在**“生成”>“高级”>“MSBuild 参数”**设置中指定所需的服务配置。 例如，若要使用 ServiceConfiguration.Test.cscfg，请设置 MSBuild 参数行选项 `/p:TargetProfile=Test`。
    
     ![][38]
    
     此时，您的生成应已成功完成。
    
     ![][28]
13. 如果双击生成名称，则 Visual Studio 将显示“**生成摘要**”，其中包括来自关联的单元测试项目的任何测试结果。
    
     ![][29]
14. 在 [Azure 经典门户](http://go.microsoft.com/fwlink/?LinkID=213885)中，可以在选定过渡环境后在“**部署**”选项卡上查看关联的部署。
    
     ![][30]
15. 浏览到你的站点的 URL。 对于 Web 应用，只需单击命令栏上的“**浏览**”按钮。 对于云服务，请在“**仪表板**”页上选择显示云服务过渡环境的“**速览**”部分中的 URL。 默认情况下，来自云服务的持续集成的部署将发布到过渡环境。 可以通过将“**备用云服务环境**”属性设置为“**生产**”来改变此情况。 以下屏幕截图显示了站点 URL 在云服务仪表板页上的位置。
    
    ![][31]
    
    新的浏览器选项卡将打开以显示正在运行的网站。
    
    ![][32]
    
    对于云服务，如果对项目进行其他更改，则将触发更多生成，并且将累积多个部署。 标记为“活动”的最新项目。
    
    ![][33]

## <a name="5-redeploy-an-earlier-build"></a>5：重新部署以前的生成
此步骤适用于云服务，并且是一个可选步骤。 在 Azure 经典门户中，选择以前的部署，然后选择“**重新部署**”按钮可使站点退回以前的签入。  请注意，这将在 TFS 中触发新的生成，并在部署历史记录中创建新的条目。

![][34]

## <a name="6-change-the-production-deployment"></a>6：更改生产部署
此步骤仅适用于云服务而非 Web 应用。 准备就绪后，可以在 Azure 经典门户中选择“**交换**”按钮将过渡环境提升为生产环境。 新部署的过渡环境将提升为生产环境，而之前的生产环境（如果有）将变成过渡环境。 虽然生产环境和过渡环境的活动部署可能会不同，但最新生成的部署历史记录都是相同的，不管是哪一种环境。

![][35]

## <a name="7-run-unit-tests"></a>7：运行单元测试
此步骤仅适用于 Web Apps，而不适用于云服务。 若要对实时部署或过渡部署进行质量把关，你可以运行单元测试，如果测试失败，你可以停止部署。

1. 在 Visual Studio 中添加一个单元测试项目。
   
   ![][39]
2. 添加对你要测试的项目的项目引用。
   
   ![][40]
3. 添加一些单元测试。 若要开始，请先尝试一个始终会通过的虚拟测试。
   
       ```
       using System;
       using Microsoft.VisualStudio.TestTools.UnitTesting;
   
       namespace UnitTestProject1
       {
           [TestClass]
           public class UnitTest1
           {
               [TestMethod]
               [ExpectedException(typeof(NotImplementedException))]
               public void TestMethod1()
               {
                   throw new NotImplementedException();
               }
           }
       }
       ```
4. 编辑生成定义，选择“**进程**”选项卡，然后展开“**测试**”节点。
5. 将“**测试未通过时生成失败**”设置为 True。 这意味着除非通过测试，否则部署将不会进行。
   
   ![][41]
6. 将新的生成排队。
   
   ![][42]
   
   ![][43]
7. 在生成过程中，检查生成的进度。
   
    ![][44]
   
    ![][45]
8. 完成生成后，检查测试结果。
   
    ![][46]
   
    ![][47]
9. 尝试创建一个会失败的测试。 通过复制第一个测试来添加新的测试，将其重命名，注释掉引发 NotImplementedException 的代码行。
   
       ```
       [TestMethod]
       //[ExpectedException(typeof(NotImplementedException))]
       public void TestMethod2()
       {
           throw new NotImplementedException();
       }
       ```
10. 签入更改以将新的生成排队。
    
     ![][48]
11. 查看测试结果，以了解有关失败的详细信息。
    
     ![][49]
    
     ![][50]

## <a name="next-steps"></a>后续步骤
有关 Visual Studio Team Services 中的单元测试的详细信息，请参阅[在内部版本中运行单元测试](http://go.microsoft.com/fwlink/p/?LinkId=510474)。 如果使用的是 Git，请参阅[在 Git 中共享代码](http://www.visualstudio.com/get-started/share-your-code-in-git-vs.aspx)和[持续部署到 Azure 应用服务](../app-service-web/app-service-continuous-deployment.md)。  有关 Visual Studio Team Services 的详细信息，请参阅 [Visual Studio Team Services](http://go.microsoft.com/fwlink/?LinkId=253861)。

[0]: ./media/cloud-services-continuous-delivery-use-vso/tfs0.PNG
[1]: ./media/cloud-services-continuous-delivery-use-vso/tfs1.png
[2]: ./media/cloud-services-continuous-delivery-use-vso/tfs2.png

[5]: ./media/cloud-services-continuous-delivery-use-vso/tfs5.png
[6]: ./media/cloud-services-continuous-delivery-use-vso/tfs6.png
[7]: ./media/cloud-services-continuous-delivery-use-vso/tfs7.png
[8]: ./media/cloud-services-continuous-delivery-use-vso/tfs8.png
[9]: ./media/cloud-services-continuous-delivery-use-vso/tfs9.png
[10]: ./media/cloud-services-continuous-delivery-use-vso/tfs10.png
[11]: ./media/cloud-services-continuous-delivery-use-vso/tfs11.png
[12]: ./media/cloud-services-continuous-delivery-use-vso/tfs12.png
[13]: ./media/cloud-services-continuous-delivery-use-vso/tfs13.png
[14]: ./media/cloud-services-continuous-delivery-use-vso/tfs14.png
[15]: ./media/cloud-services-continuous-delivery-use-vso/tfs15.png
[16]: ./media/cloud-services-continuous-delivery-use-vso/tfs16.png
[17]: ./media/cloud-services-continuous-delivery-use-vso/tfs17.png
[18]: ./media/cloud-services-continuous-delivery-use-vso/tfs18.png
[19]: ./media/cloud-services-continuous-delivery-use-vso/tfs19.png
[20]: ./media/cloud-services-continuous-delivery-use-vso/tfs20.png
[21]: ./media/cloud-services-continuous-delivery-use-vso/tfs21.png
[22]: ./media/cloud-services-continuous-delivery-use-vso/tfs22.png
[23]: ./media/cloud-services-continuous-delivery-use-vso/tfs23.png
[24]: ./media/cloud-services-continuous-delivery-use-vso/tfs24.png
[25]: ./media/cloud-services-continuous-delivery-use-vso/tfs25.png
[26]: ./media/cloud-services-continuous-delivery-use-vso/tfs26.png
[27]: ./media/cloud-services-continuous-delivery-use-vso/tfs27.png
[28]: ./media/cloud-services-continuous-delivery-use-vso/tfs28.png
[29]: ./media/cloud-services-continuous-delivery-use-vso/tfs29.png
[30]: ./media/cloud-services-continuous-delivery-use-vso/tfs30.png
[31]: ./media/cloud-services-continuous-delivery-use-vso/tfs31.png
[32]: ./media/cloud-services-continuous-delivery-use-vso/tfs32.png
[33]: ./media/cloud-services-continuous-delivery-use-vso/tfs33.png
[34]: ./media/cloud-services-continuous-delivery-use-vso/tfs34.png
[35]: ./media/cloud-services-continuous-delivery-use-vso/tfs35.png
[36]: ./media/cloud-services-continuous-delivery-use-vso/tfs36.PNG
[37]: ./media/cloud-services-continuous-delivery-use-vso/tfs37.PNG
[38]: ./media/cloud-services-continuous-delivery-use-vso/AdvancedMSBuildSettings.PNG
[39]: ./media/cloud-services-continuous-delivery-use-vso/AddUnitTestProject.PNG
[40]: ./media/cloud-services-continuous-delivery-use-vso/AddProjectReferences.PNG
[41]: ./media/cloud-services-continuous-delivery-use-vso/EditBuildDefinitionForTest.PNG
[42]: ./media/cloud-services-continuous-delivery-use-vso/QueueNewBuild.PNG
[43]: ./media/cloud-services-continuous-delivery-use-vso/QueueBuildDialog.PNG
[44]: ./media/cloud-services-continuous-delivery-use-vso/BuildInTeamExplorer.PNG
[45]: ./media/cloud-services-continuous-delivery-use-vso/BuildRequestInfo.PNG
[46]: ./media/cloud-services-continuous-delivery-use-vso/BuildSucceeded.PNG
[47]: ./media/cloud-services-continuous-delivery-use-vso/TestResultsPassed.PNG
[48]: ./media/cloud-services-continuous-delivery-use-vso/CheckInChangeToMakeTestsFail.PNG
[49]: ./media/cloud-services-continuous-delivery-use-vso/TestsFailed.PNG
[50]: ./media/cloud-services-continuous-delivery-use-vso/TestsResultsFailed.PNG

