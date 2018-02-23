---
title: "将你的应用程序部署到 Azure 和 Azure 堆栈 |Microsoft 文档"
description: "了解如何使用混合 CI/CD 管道将应用部署到 Azure 和 Azure 堆栈。"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: 
ms.custom: mvc
ms.openlocfilehash: 6c073376db196b7d6c73c38d6a0a7b2c24949528
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2018
---
# <a name="deploy-apps-to-azure-and-azure-stack"></a>将应用部署到 Azure 和 Azure 堆栈
*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

混合[持续集成](https://www.visualstudio.com/learn/what-is-continuous-integration/)/[持续交付](https://www.visualstudio.com/learn/what-is-continuous-delivery/)(CI/CD) 管道使您能够生成、 测试和应用部署到多个云。  在本教程中，你可以生成示例环境，若要了解如何混合 CI/CD 管道可以帮助你：
 
> [!div class="checklist"]
> * 启动新的生成基于代码提交到 Visual Studio Team Services (VSTS) 存储库。
> * 自动在新生成的代码向 Azure 部署为用户验收测试。
> * 一旦你的代码经过测试之后，自动将部署到 Azure 堆栈。 


## <a name="prerequisites"></a>必备组件
了几个组件需要生成混合 CI/CD 管道，并且可能需要一些时间来准备。  如果你已有的某些这些组件，请确保它们符合在开始之前的要求。

本主题还假定你具有 Azure 和 Azure 堆栈的一些知识。 如果你想要了解详细信息在继续之前，请务必阅读以下主题：

- [Azure 简介](https://docs.microsoft.com/azure/fundamentals-introduction-to-azure)
- [Azure 堆栈的关键概念](../azure-stack-key-features.md)

### <a name="azure"></a>Azure
 - 如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
 - 创建[Web 应用](../../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md)，并将其用于配置[FTP 发布](../../app-service/app-service-deploy-ftp.md)。  记下新的 Web 应用 URL，因为它用更高版本。


### <a name="azure-stack"></a>Azure Stack
 - [部署 Azure 堆栈](../azure-stack-run-powershell-script.md)。  安装通常需要几个小时才能完成，因此请相应地计划。
 - 部署[App Service](../azure-stack-app-service-deploy.md) PaaS 服务添加到 Azure 堆栈。
 - 创建 Web 应用并为其配置[FTP 发布](../azure-stack-app-service-enable-ftp.md)。  记下新的 Web 应用 URL，因为它用更高版本。  

### <a name="developer-tools"></a>开发人员工具
 - 创建[VSTS 工作区](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)。  注册过程将创建名为"MyFirstProject。"的项目  
 - [安装 Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio)和[登录到 VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services#connect-and-share-code-from-visual-studio)
 - 连接到项目和[本地克隆](https://www.visualstudio.com/docs/git/gitquickstart)。
 - 创建[代理池](https://www.visualstudio.com/docs/build/concepts/agents/pools-queues#creating-agent-pools-and-queues)VSTS 中。
 - 安装 Visual Studio 和部署[VSTS 生成代理](https://www.visualstudio.com/docs/build/actions/agents/v2-windows)到 Azure 堆栈上的虚拟机。 
 

## <a name="create-app--push-to-vsts"></a>创建应用程序 （&) 将推送到 VSTS

### <a name="create-application"></a>创建应用程序
在本部分中，你创建一个简单的 ASP.NET 应用程序并将其推送到 VSTS。  这些步骤表示正常的开发人员工作流，并可能适用于开发人员工具和语言。 

1.  打开 Visual Studio。
2.  从团队资源管理器空间和**解决方案...**区域中，单击**新建**。
3.  选择**Visual C#** > **Web** > **ASP.NET Web 应用程序 (.NET Framework)**。
4.  提供的名称的应用程序和单击**确定**。
5.  在下一个屏幕上，保留默认值 （Web 窗体），然后单击**确定**。

### <a name="commit-and-push-changes-to-vsts"></a>提交并将更改推送到 VSTS
1.  使用 Visual Studio 中的团队资源管理器，选择下拉菜单，然后单击**更改**。
2.  提供提交消息，并选择**提交所有**。 可能会提示你保存解决方案文件，请单击是以保存所有。
3.  一旦提交，Visual Studio 主动同步到你的项目的更改。 选择“同步”。

    ![显示提交屏幕完成提交后的图像](./media/azure-stack-solution-pipeline/image1.png)

4.  在同步选项卡下*传出*，查看新提交。  选择**推送**同步对 VSTS 的更改。

    ![图像显示同步步骤](./media/azure-stack-solution-pipeline/image2.png)

### <a name="review-code-in-vsts"></a>查看 VSTS 中的代码
一旦已提交更改并推送到 VSTS，检查 VSTS 门户中的代码。  选择**代码**，，然后**文件**从下拉菜单。  你可以看到创建的解决方案。

## <a name="create-build-definition"></a>创建生成定义
生成过程定义你的应用程序生成和打包以便在每次提交的代码更改的部署方式。 在本示例中，我们使用包含的模板配置生成过程的 ASP.NET 应用程序，但此配置无法进行适配化具体取决于你的应用程序。

1.  从 web 浏览器登录到你的 VSTS 工作区。
2.  从标记中，选择**生成和发行**然后**生成**。
3.  单击**+ 新定义**。
4.  从模板列表中，选择**ASP.NET （预览版）**和选择**应用**。
5.  修改*MSBuild 参数*字段*生成解决方案*单步执行到：

    `/p:DeployOnBuild=True /p:WebPublishMethod=FileSystem /p:DeployDefaultTarget=WebPublish /p:publishUrl="$(build.artifactstagingdirectory)\\"`

6.  选择**选项**选项卡，并选择您已部署到 Azure 堆栈上的虚拟机的生成代理的代理队列。 
7.  选择**触发器**选项卡，并启用**持续集成**。
7.  单击**保存并队列**，然后选择**保存**从下拉列表。 

## <a name="create-release-definition"></a>创建发布定义
发布过程定义从前面的步骤的生成部署到环境的方式。  在本教程中，我们将使用 FTP 我们 ASP.NET 的应用程序发布到 Azure Web 应用。 若要配置到 Azure 的版本，请使用以下步骤：

1.  从 VSTS 标题中，选择**生成和发行**然后**版本**。
2.  单击绿色**+ 新定义**。
3.  选择**空**单击**下一步**。
4.  选中对应的框*连续部署*，然后单击**创建**。

现在，创建一个空的版本定义并绑定到生成后，我们添加 Azure 环境的步骤：

1.  单击绿色 **+** 添加任务。
2.  选择**所有**，然后从列表中，添加**FTP 上载**和选择**关闭**。
3.  选择**FTP 上载**任务您刚刚添加，并配置以下参数：
    
    | 参数 | 值 |
    | ----- | ----- |
    |身份验证方法| 输入凭据|
    |服务器 URL | 从 Azure 门户检索到的 web 应用的 FTP URL |
    |用户名 | 为 Web 应用创建 FTP 凭据时配置的用户名 |
    |密码 | 创建时建立的 Web 应用的 FTP 凭据的密码|
    |源目录 | $(System.DefaultWorkingDirectory)\**\ |
    |远程目录 | /site/wwwroot/ |
    |保留文件路径 | 启用 （已选中）|

4.  单击“保存”

最后，你配置要使用包含使用以下步骤部署代理的代理池的版本定义：
1.  选择版本定义，然后单击**编辑**。
2.  选择**在代理上运行**从中间列。  在右列中，选择包含在 Azure 堆栈上运行的生成代理的代理队列。  
    ![若要使用特定的队列的版本定义的映像显示配置](./media/azure-stack-solution-pipeline/image3.png)


## <a name="deploy-your-app-to-azure"></a>将你的应用程序部署到 Azure
此步骤使用新生成的 CI/CD 管道将 ASP.NET 应用部署到 Web 应用在 Azure 上。 

1.  从 VSTS 中标题中，选择**生成和发行**，然后选择**生成**。
2.  单击**...**以前创建的并选择在生成定义**新生成进行排队**。
3.  接受默认值，然后单击**确定**。  生成开始，并显示进度。
4.  完成生成后，你可以通过选择跟踪状态**生成和发行**并选择**版本**。
5.  完成生成后，访问网站，使用在创建 Web 应用程序时记下的 URL。    


## <a name="add-azure-stack-to-pipeline"></a>添加 Azure 堆栈管线传输
现在，你已通过向 Azure 部署测试 CI/CD 管道，它是时候将 Azure 堆栈添加到管道。  在以下步骤中，你创建新环境，并添加一个 FTP 上载任务，以将你的应用程序部署到 Azure 堆栈。  你还可以添加版本审批者，它可作为一种方法，以模拟""在登录到 Azure 堆栈的代码版本。  

1.  在版本定义中，选择**+ 添加环境**和**创建新环境**。
2.  选择**空**，单击**下一步**。
3.  选择**特定用户**并指定你的帐户。  选择“创建”。
4.  对环境重命名通过选择现有的名称，并键入*Azure 堆栈*。
5.  现在，选择 Azure 堆栈环境中，然后选择**添加任务**。
6.  选择**FTP 上载**任务并选择**添加**，然后选择**关闭**。


### <a name="configure-ftp-task"></a>配置 FTP 任务
现在，你已创建发布，你将配置用于发布到 Azure 堆栈上的 Web 应用所需的步骤。  就像你为 Azure 配置 FTP 上载任务，你为 Azure 堆栈配置任务：

1.  选择**FTP 上载**任务您刚刚添加，并配置以下参数：
    
    | 参数 | 值 |
    | -----     | ----- |
    |身份验证方法| 输入凭据|
    |服务器 URL | 从 Azure 堆栈门户检索到的 web 应用的 FTP URL |
    |用户名 | 为 Web 应用创建 FTP 凭据时配置的用户名 |
    |密码 | 创建时建立的 Web 应用的 FTP 凭据的密码|
    |源目录 | $(System.DefaultWorkingDirectory)\**\ |
    |远程目录 | /site/wwwroot/|
    |保留文件路径 | 启用 （已选中）|

2.  单击“保存”

最后，将版本定义要使用包含使用以下步骤部署代理的代理池配置：
1.  选择版本定义，然后单击**编辑**
2.  选择**在代理上运行**从中间列。 在右列中，选择包含在 Azure 堆栈上运行的生成代理的代理队列。  
    ![若要使用特定的队列的版本定义的映像显示配置](./media/azure-stack-solution-pipeline/image3.png)

## <a name="deploy-new-code"></a>部署新代码
现在，你可以测试混合 CI/CD 管道，与发布到 Azure 堆栈的最后一步。  在本部分中，你修改站点的页脚，并启动通过管道的部署。  完成后，你将看到所做的更改部署到 Azure，供查看，然后后批准发布，在发布到 Azure 堆栈。

1. 在 Visual Studio 中，打开*site.master*文件并将更改以下行：
    
    `
        <p>&copy; <%: DateTime.Now.Year %> - My ASP.NET Application</p>
    `

    更改为：

    `
        <p>&copy; <%: DateTime.Now.Year %> - My ASP.NET Application delivered by VSTS, Azure, and Azure Stack</p>
    `
3.  提交所做的更改并同步到 VSTS。  
4.  从 VSTS 工作区中，检查生成状态通过选择**生成和发行** > **生成**
5.  你将看到正在进行的生成。  双击状态，并可以监视生成进度。  一旦您在控制台中看到"已完成的生成"，将移动检查中的版本**生成和发行** > **释放**。  双击版本。
6.  你将收到通知发布需要查看。 检查 Web 应用 URL，并验证新的更改会呈现。  批准 VSTS 中的版本。
    ![若要使用特定的队列的版本定义的映像显示配置](./media/azure-stack-solution-pipeline/image4.png)
    
7.  验证通过访问网站使用创建 Web 应用程序时记下的 URL 发布到 Azure 堆栈已完成。
    ![显示 ASP 的图像。NEt 应用程序与更改的页脚](./media/azure-stack-solution-pipeline/image5.png)


有关其他混合云模式，现在可以使用新的混合 CI/CD 管道为构建基块。

## <a name="next-steps"></a>后续步骤
在本教程中，您学习了如何构建混合 CI/CD 管道：

> [!div class="checklist"]
> * 启动新的生成基于代码提交到你的 Visual Studio Team Services (VSTS) 存储库中。
> * 自动将新生成的代码到 Azure 部署为用户验收测试。
> * 一旦你的代码经过测试之后，自动将部署到 Azure 堆栈。 

现在，你有混合 CI/CD 管道，继续通过了解如何开发 Azure 堆栈的应用。

> [!div class="nextstepaction"]
> [Azure Stack 开发](azure-stack-developer.md)


