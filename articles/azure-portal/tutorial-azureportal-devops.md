---
title: "教程：使用 Azure 门户进行开发运营 | Microsoft Docs"
description: "了解 Azure 门户中的各种 DevOps 工作流。"
services: azure-portal
documentationcenter: 
author: mlearned
manager: douge
editor: mlearned
ms.assetid: 4f1c5bc1-c732-4d35-b5df-0fd68e547d38
ms.service: azure-portal
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/05/2016
ms.author: mlearned
ms.openlocfilehash: d2c059fe415b743e4b2265d6dda492878307be93
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-devops-with-the-azure-portal"></a>教程：使用 Azure 门户进行开发运营
Azure 平台提供各式各样的灵活 DevOps（开发运营）工作流。 在本教程中，将了解如何利用 Azure 门户功能来开发、测试、部署、监视和管理正在运行的应用程序以及对其进行故障排除。 本教程着重于以下内容：

1. 创建 Web 应用程序和启用连续部署
2. 开发和测试应用
3. 对应用进行监视和故障排除
4. 常规应用程序管理任务

## <a name="creating-a-web-app-and-enabling-continuous-deployment"></a>创建 Web 应用程序和启用连续部署
创建一个要在本教程余下部分中使用的、包含 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)的 Web 应用。 首先，要启用从源代码存储库到正在运行的 Azure 环境的连续部署。

1. 登录到 Azure 门户
2. 依次选择“应用服务”&gt;“添加图标”并输入名称、选择订阅，并创建要用作该服务容器的新资源组。
   
   可以通过资源组来管理解决方案的各个方面，例如通过 [Azure 资源管理器](../azure-resource-manager/resource-group-overview.md)将所有资源以单个组的方式进行计费、部署和监视。
   
   ![image1][image1]
3. 片刻之后即可创建应用服务。 请花几分钟时间在门户中了解服务的各个菜单选项。
   
   ![image2][image2]    
4. 单击 URL。 请注意工具和存储库的各个可用选项。 也可以使用自己选择的语言和框架，包括 .NET、Java 和 Ruby。
   
   ![image3][image3]    
5. Azure 门户能使连续部署变成只需执行几个简单步骤的轻松过程。 在 Azure 门户中，通过刚刚创建的应用服务对应的图标选择设置。
   
   ![Image4][image4]
   
   在右侧打开的边栏选项卡中，滚动到发布部分。
   
   ![image5][image5]
6. 接下来，将某些设置配置为允许对应用进行持续部署。 单击“部署源”，并单击“选择源”。 请注意针对存储库源提供的各个选项。
   
   ![image6][image6]
7. 对于本示例，请选择“GitHub”。 （可选）选择所需的存储库，并设置授权凭据。
   
   ![image7][image7]
8. 在授予存储库权限之后，可以选择想要部署的项目和分支。 下面列出了几个虚构的示例。
   
   ![image8][image8]
9. 选择项目和分支后，单击“确定”。 应会看到部署通知。
   
   ![image9][image9]
10. 浏览回到 GitHub，以查看为了将源代码管理存储库与 Azure 集成而创建的 Webhook。 只需执行一些简单的步骤，即可通过 Azure 门户与 GitHub 集成。
    
    ![image10][image10]
11. 为了演示连续部署，请将一些内容快速添加到存储库。 为了创建一个简单示例，请将一个示例文本文件添加到 GitHub 存储库。 可以任意将 .NET、Ruby、Python 或其他某种类型的应用程序与应用服务配合使用。 在所选的存储库中任意添加文本文件、ASP.NET MVC、Java 或 Ruby 应用程序。
    
    ![image11][image11]
12. 将更改提交到存储库之后，会在门户的通知区域中看到新部署已启动。 提交到存储库之后，如果未马上看到更改，请单击“同步”。
    
    ![image12][image12]
13. 此时，如果尝试加载应用服务页面，可能会收到 403 错误。 在本示例中，这是因为页面（例如 index.htm 或 default.html 之类的文件）没有典型的默认文档设置。 可以使用 Azure 门户中的工具快速解决此问题。  在 Azure 门户中，选择“设置”&gt;“应用程序设置”。
    
     ![image13][image13]
14. 此时会打开应用程序设置边栏选项卡。 输入“SamplePage.html”页面的名称，并单击“保存”。 花几分钟时间了解其他设置。
    
    ![image14][image14]
15. （可选）刷新浏览器 URL，以确保看到预期的更改。 在本例中，页面上填充了一些简单的文本。 每次对存储库进行额外的更改都会导致进行新的自动部署。
    
    ![image15][image15]
    
    通过 Azure 门户启用连续部署是很轻松的体验。 还可以构建更复杂的发布管道，并配合现有源代码管理和连续集成系统使用其他许多技术来部署到 Azure，例如利用自动化构建和发布管理系统。

## <a name="develop-and-test-an-app"></a>开发和测试应用
接下来，对代码基进行一些更改，并快速部署这些更改。 此外，针对 Web 应用设置一些性能测试。

1. 在 Azure 门户中，从导航窗格选择“应用程序服务”，并找到应用服务。
   
   ![image16][image16]
2. 单击“工具”
   
   ![image17][image17]
3. 注意“工具”下面的“开发”类别。 此处提供了一些有用的工具，可让我们在不退出 Azure 门户的情况下使用应用。 单击“控制台”。
   
   ![image18][image18]
4. 在控制台窗口中，可针对应用发出实时命令。 键入 dir 命令，并按 Enter。 请注意，需要提升权限的命令无法运行。
   
   ![image19][image19]
5. 返回到“开发”类别，并选择“Visual Studio Online”。 注意：Visual Studio Online 现已改名为 Visual Studio Team Services。
   
   ![image20][image20]
6. 打开应用的浏览器内编辑体验。
   
   ![image21][image21]
7. 将为应用安装一个 Web 扩展。 扩展可快速轻松地将功能添加到 Azure 中的应用。 在以下屏幕截图中，可以看到其他一些可用的扩展类型。
   
   ![image22][image22]
8. 安装 Visual Studio Online 扩展后，单击“执行”。
   
   ![image23][image23]
9. 此时会打开浏览器选项卡，让你直接在浏览器中查看开发 IDE。 请注意，以下体验在 Chrome 中进行。
   
   ![image24][image24]
10. 可以执行多种活动，例如编辑文件、添加文件和文件夹，以及从实时站点下载内容。 对 SamplePage.html 文件进行快速编辑。
    
    ![image25][image25]
11. 几分钟后，会自动保存更改。 如果导航回到页面，就可以看到更改。 请记住，此类实时编辑很可能不适用于生产环境。 但是，使用这些工具可以快速轻松地更改开发和测试环境。
    
    ![image26][image26]
    
    ![image27][image27]
12. 返回到工具边栏选项卡，并单击“开发”类别下的“性能测试”。
    
    ![image28][image28]
13. 需要设置 Team Services 帐户。 有关详细信息，请参阅 [Create a Team Services Account](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)
14. 单击“新建”以创建性能测试。
    
    ![image29][image29]
    
    设置不同的值，并单击对话框底部的“运行测试”以启动性能测试。
    
    ![image30][image30]
    
    ![image31][image31]
15. 开始运行测试后，可以监视状态。
    
    ![image32][image32]
    
    测试完成后，请单击结果以显示更多详细信息。
    
    ![image33][image33]
16. 在本示例中，创建了一个小型测试运行，因此可供分析的数据有限，但可以查看各项指标，以及从此视图重新运行测试。 在 Azure 门户中可以轻松创建、运行和分析 Web 性能测试。 以下屏幕截图显示了性能数据。
    
    ![image34][image34]
    
    ![image35][image35]
    
    ![image36][image36]

## <a name="monitoring-and-troubleshooting-an-app"></a>对应用进行监视和故障排除
Azure 提供了许多功能用于对正在运行的应用程序进行监视和故障排除。

1. 在 Web 应用的 Azure 门户中选择“工具”。
   
   ![image37][image37]
2. 在“故障排除”类别下可以看到多种选项，使我们能够使用工具针对运行中应用程序的潜在问题进行故障排除。 可以执行各种操作，例如，监视实时 HTTP 流量、启用自我修复、查看日志，等等。
   
   ![image38][image38]
3. 选择“站点指标”可以快速查看某些 HTTP 代码。
   
   ![image39][image39]
4. 选择“诊断即服务”。 选择应用程序类型，并选择“运行”。
   
   ![image40][image40]
   
   随即开始收集数据。
   
   ![image41][image41]
5. 可以选择相应的日志来诊断潜在问题。 必须启用日志记录功能才能查看所有可用的数据选项，例如 HTTP 日志。
   
   ![image42][image42]
   
   单击“内存转储文件”可以下载并分析 DebugDiag 分析报告，以帮助找出潜在问题。
   
   ![image43][image43]
6. 若要查看更多数据，需要启用其他日志记录功能。 在 Azure 门户中，导航到 Web 应用并选择“设置”。
   
   ![image44][image44]
7. 向下滚动到“功能”类别，并选择“诊断日志”。
   
      ![image45][image45]
8. 请注意日志记录的各个选项。 打开“Web 服务器日志记录”，并单击“保存”。
   
   ![image46][image46]
9. 返回到应用的工具区域，选择“诊断即服务”，并单击“运行”以重新运行数据收集。
   
   ![image47][image47]
10. 启用 HTTP 日志记录设置后，现在会看到针对 HTTP 日志收集的数据。
    
    ![image48][image48]
11. 单击 HTML 文件日志生成丰富格式的基于浏览器的报告，以便进一步调查。
    
    ![image49][image49]
12. 在应用的 Azure 门户中返回到工具部分。 滚动到“工具”部分，并选择“进程资源管理器”。
    
    ![image50][image50]
13. 选择“进程资源管理器”可以查看有关正在运行的进程的详细信息。 请注意，可以深入到各个进程，甚至可以删除进程，所有这些操作都可通过 Azure 门户完成。
    
    ![image51][image51]
    
    ![image52][image52]
14. 返回到左侧的“设置”边栏选项卡。 单击“新建支持请求”。
    
    ![image53][image53]
15. 在右侧的边栏选项卡中，可以填写问题详细信息、输入联系信息，甚至上传诊断数据。 可以通过 Azure 门户获得与 Microsoft 支持部门协作的无缝体验。
    
    ![image54][image54]
    
    ![image55][image55]
    
    Azure 门户提供强大且熟悉的工具体验，有助于监视和排查所运行应用程序的问题。 还可以通过执行各种任务（例如回收进程、启用和禁用各种数据收集，甚至与 Microsoft 专家支持相集成）来快速采取措施。

## <a name="general-application-management"></a>常规应用程序管理
在管理应用程序时，经常需要执行各种活动，例如配置备份策略、实施和管理标识提供者，以及配置基于角色的访问控制。 与其他 DevOps 体验一样，Azure 平台将这些任务直接集成到门户中。

1. 为了确保 Web 应用的安全且不丢失数据，需要配置备份。 请导航到 Web 应用的“设置”区域。
   
   ![image56][image56]
2. 在右侧的边栏选项卡中，向下滚动到“功能”类别。
   
    ![image57][image57]
3. 选择“备份”；一个边栏选项卡随即在右侧打开。
   
   ![image58][image58]
4. 单击“配置”，并在右侧边栏选项卡中选择存储帐户。
   
   ![image59][image59]
5. 现在，请创建并选择一个存储容器来保存备份。 单击边栏选项卡底部的“创建”。 然后选择容器。
   
   ![image60][image60]
6. 选择容器后，可以配置计划以及设置数据库的备份。 对于本方案，请单击“保存”图标。
   
    ![image61][image61]
7. 保存后，滚动回到左侧边栏选项卡中的“备份”。 单击“立即备份”以备份应用程序。
   
    ![image62][image62]
8. 几分钟后，即可看到创建了备份。 请注意以下屏幕截图中的“立即还原”选项。
   
    ![image63][image63]
9. 单击“立即还原”，并查看右侧边栏选项卡中的选项。 可以根据需要选择适当的备份，并轻松还原到以前的状态。 Azure 门户已帮助我们轻松为应用启用简单的灾难恢复策略。
   
    ![image64][image64]
10. 返回到左侧的“设置”边栏选项卡，并选择“功能”下面的“身份验证/授权”。
    
     ![image65][image65]
11. 在右侧边栏选项卡中，选择“应用服务身份验证”。 请注意可以在热门提供程序中配置的各种选项。
    
     ![image66][image66]
12. 选择所需的提供程序，并注意范围选项。 可以提供“应用 ID”和“应用机密”，并快速为应用启用 Facebook 身份验证。 在 Azure 门户中，可以使用身份验证作为应用的周全解决方案。
    
     ![image67][image67]
13. 返回到“设置”边栏选项卡，并选择“资源管理”类别下面的“用户”。
    
     ![image68][image68]
14. 在右侧边栏选项卡中，检查用于添加角色和用户的各种选项。 可以通过Azure 门户轻松地为应用程序控制 RBAC（基于角色的访问控制）。
    
     ![image69][image69]

## <a name="summary"></a>摘要
本教程演示了 Azure 平台的某些强大功能，包括快速启用 Web 应用的连续部署、执行各种开发和测试活动、对 Live App 进行监视和故障排除，以及管理密钥策略（例如灾难恢复、标识和基于角色的访问控制）。 Azure 平台支持对这些 DevOps 工作流使用集成体验，既可以高效地工作，同时又不会超出手头任务的范围。

## <a name="next-steps"></a>后续步骤
* Azure 资源管理器对于在 Azure 平台中启用 DevOps 至关重要。  有关详细信息，请访问 [Azure 资源管理器概述](../azure-resource-manager/resource-group-overview.md)。
* 有关 Azure 应用服务部署的详细信息，请访问 [将应用部署到 Azure 应用服务](../app-service/app-service-deploy-local-git.md)

[image1]: ./media/tutorial-azureportal-devops/image1.png
[image2]: ./media/tutorial-azureportal-devops/image2.png
[image3]: ./media/tutorial-azureportal-devops/image3.png
[image4]: ./media/tutorial-azureportal-devops/image4.png
[image5]: ./media/tutorial-azureportal-devops/image5.png
[image6]: ./media/tutorial-azureportal-devops/image6.png
[image7]: ./media/tutorial-azureportal-devops/image7.png
[image8]: ./media/tutorial-azureportal-devops/image8.png
[image9]: ./media/tutorial-azureportal-devops/image9.png
[image10]: ./media/tutorial-azureportal-devops/image10.png
[image11]: ./media/tutorial-azureportal-devops/image11.png
[image12]: ./media/tutorial-azureportal-devops/image12.png
[image13]: ./media/tutorial-azureportal-devops/image13.png
[image14]: ./media/tutorial-azureportal-devops/image14.png
[image15]: ./media/tutorial-azureportal-devops/image15.png
[image16]: ./media/tutorial-azureportal-devops/image16.png
[image17]: ./media/tutorial-azureportal-devops/image17.png
[image18]: ./media/tutorial-azureportal-devops/image18.png
[image19]: ./media/tutorial-azureportal-devops/image19.png
[image20]: ./media/tutorial-azureportal-devops/image20.png
[image21]: ./media/tutorial-azureportal-devops/image21.png
[image22]: ./media/tutorial-azureportal-devops/image22.png
[image23]: ./media/tutorial-azureportal-devops/image23.png
[image24]: ./media/tutorial-azureportal-devops/image24.png
[image25]: ./media/tutorial-azureportal-devops/image25.png
[image26]: ./media/tutorial-azureportal-devops/image26.png
[image27]: ./media/tutorial-azureportal-devops/image27.png
[image28]: ./media/tutorial-azureportal-devops/image28.png
[image29]: ./media/tutorial-azureportal-devops/image29.png
[image30]: ./media/tutorial-azureportal-devops/image30.png
[image31]: ./media/tutorial-azureportal-devops/image31.png
[image32]: ./media/tutorial-azureportal-devops/image32.png
[image33]: ./media/tutorial-azureportal-devops/image33.png
[image34]: ./media/tutorial-azureportal-devops/image34.png
[image35]: ./media/tutorial-azureportal-devops/image35.png
[image36]: ./media/tutorial-azureportal-devops/image36.png
[image37]: ./media/tutorial-azureportal-devops/image37.png
[image38]: ./media/tutorial-azureportal-devops/image38.png
[image39]: ./media/tutorial-azureportal-devops/image39.png
[image40]: ./media/tutorial-azureportal-devops/image40.png
[image41]: ./media/tutorial-azureportal-devops/image41.png
[image42]: ./media/tutorial-azureportal-devops/image42.png
[image43]: ./media/tutorial-azureportal-devops/image43.png
[image44]: ./media/tutorial-azureportal-devops/image44.png
[image45]: ./media/tutorial-azureportal-devops/image45.png
[image46]: ./media/tutorial-azureportal-devops/image46.png
[image47]: ./media/tutorial-azureportal-devops/image47.png
[image48]: ./media/tutorial-azureportal-devops/image48.png
[image49]: ./media/tutorial-azureportal-devops/image49.png
[image50]: ./media/tutorial-azureportal-devops/image50.png
[image51]: ./media/tutorial-azureportal-devops/image51.png
[image52]: ./media/tutorial-azureportal-devops/image52.png
[image53]: ./media/tutorial-azureportal-devops/image53.png
[image54]: ./media/tutorial-azureportal-devops/image54.png
[image55]: ./media/tutorial-azureportal-devops/image55.png
[image56]: ./media/tutorial-azureportal-devops/image56.png
[image57]: ./media/tutorial-azureportal-devops/image57.png
[image58]: ./media/tutorial-azureportal-devops/image58.png
[image59]: ./media/tutorial-azureportal-devops/image59.png
[image60]: ./media/tutorial-azureportal-devops/image60.png
[image61]: ./media/tutorial-azureportal-devops/image61.png
[image62]: ./media/tutorial-azureportal-devops/image62.png
[image63]: ./media/tutorial-azureportal-devops/image63.png
[image64]: ./media/tutorial-azureportal-devops/image64.png
[image65]: ./media/tutorial-azureportal-devops/image65.png
[image66]: ./media/tutorial-azureportal-devops/image66.png
[image67]: ./media/tutorial-azureportal-devops/image67.png
[image68]: ./media/tutorial-azureportal-devops/image68.png
[image69]: ./media/tutorial-azureportal-devops/image69.png
