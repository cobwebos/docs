---
title: 将应用部署到 Azure 和 Azure Stack | Microsoft Docs
description: 了解如何使用混合 CI/CD 管道将应用部署到 Azure 和 Azure Stack。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 49a80805c976e5584bb158965583a03eda68cc46
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-deploy-apps-to-azure-and-azure-stack"></a>教程： 将应用部署到 Azure 和 Azure 堆栈

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

混合连续集成/continuous 传递 (CI/CD) 管道，可生成、 测试和应用部署到多个云。  在本教程中，你将生成到示例环境：
 
> [!div class="checklist"]
> * 根据 Visual Studio Team Services (VSTS) 存储库的代码提交启动新的生成。
> * 自动将你新生成的代码部署到为用户验收测试的全球 Azure。
> * 代码通过测试后，可自动部署到 Azure Stack。

### <a name="about-the-hybrid-delivery-build-pipe"></a>有关混合传递生成管道

应用程序部署连续性、 安全性和可靠性是基本到你的组织和你的开发团队的关键。 与混合 CI/CD 管道，你可以在你的本地环境和公有云合并管道。 无需切换你的应用程序，可以更改位置。

此方法还允许您保持一致的开发工具集。 在 Azure 公有云中和本地 Azure 堆栈环境之间一致的工具意味着得更轻松地实现 CI/CD 开发做法。 应用程序和服务部署在 Azure 或 Azure 堆栈中是可互换和相同的代码可以运行在任一位置中利用本地以及公有云特性和功能。

了解有关以下方面的详细信息：
 - [持续集成是什么？](https://www.visualstudio.com/learn/what-is-continuous-integration/)
 - [什么是持续交付？](https://www.visualstudio.com/learn/what-is-continuous-delivery/)


## <a name="prerequisites"></a>必备组件

你将需要几个组件的位置，以生成混合 CI/CD 管道。 它们可能需要一些时间来准备。
 
 - Azure 的 OEM 硬件合作伙伴可以部署生产 Azure 堆栈和所有用户可以都部署 Azure 堆栈开发工具包 (ASDK)。 
 - Azure 堆栈运算符还必须部署 App Service、 创建计划，并提供、 创建租户订阅，并添加 Windows Server 2016 映像。

如果已准备好其中的某些组件，请确保它们符合要求，然后开始操作。

本主题还假设你对 Azure 和 Azure Stack 有一定的了解。 若要在了解详情之后再继续，请务必从以下主题着手：


本教程还假定你具有 Azure 和 Azure 堆栈的一些知识。 

如果你想要了解详细信息在继续之前，你可以阅读以下主题：
 - [Azure 简介](https://azure.microsoft.com/overview/what-is-azure/)
 - [Azure Stack 的重要概念](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure

 - 如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

 - 创建[Web 应用](https://docs.microsoft.com/azure/app-service/app-service-web-overview)在 Azure 中。 记下新 Web 应用的 URL，因为稍后需要用到。

Azure Stack
 - 使用集成的 Azure 堆栈系统或部署 Azure 堆栈开发工具包 (ASDK) 按以下链接：
    - 你可以找到有关部署在 ASDK 的详细的说明"[教程： 部署使用安装程序 ASDK](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy)"
    - 你可以自动执行的许多使用以下 PowerShell 脚本，你 ASDK 后期部署步骤[ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 )。

    > [!note]  
    > ASDK 安装需要七个小时才能完成，因此请相应地计划。

 - 将[应用服务](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) PaaS 服务部署到 Azure Stack。 
 - 创建[计划/提供](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview)Azure 堆栈环境中。 
 - 创建[租户订阅](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm)Azure 堆栈环境中。 
 - 创建租户订阅中 Web 应用。 请记下的新的 Web 应用 URL 的更高版本使用。
 - 仍在租户订阅中部署 VSTS 虚拟机。
 - 使用所需的.NET 3.5 的 Windows Server 2016 VM。 此 VM 将生成 Azure 堆栈上，为私有的生成代理。 

### <a name="developer-tools"></a>开发人员工具

 - 创建 [VSTS 工作区](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)。 注册过程将创建一个名为项目**MyFirstProject**。
 - [安装 Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio)和[登录到 VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services)。
 - 连接到项目并在[本地克隆](https://www.visualstudio.com/docs/git/gitquickstart)。
 
 > [!note]  
 > 你需要 Azure 堆栈使用联合运行 （Windows Server 和 SQL） 并让部署应用程序服务的正确图像。
 
## <a name="prepare-the-private-build-and-release-agent-for-visual-studio-team-services-integration"></a>准备 Visual Studio Team Services 集成的专用生成和版本的代理

### <a name="prerequisites"></a>必备组件

Visual Studio Team Services (VSTS) 进行身份验证对 Azure 资源管理器使用服务主体。 VSTS 能够设置 Azure 堆栈订阅中的资源，需要参与者状态。

需要配置才能启用此类身份验证的高级步骤如下：

1. 可能使用一个现有或应创建服务主体。
2. 身份验证密钥需要创建服务主体。
3. Azure 堆栈订阅需要验证通过基于角色的访问控制，以允许 SPN 是参与者角色的一部分。
4. 必须使用 Azure 堆栈终结点以及 SPN 信息创建新的服务定义 VSTS 中。

### <a name="service-principal-creation"></a>服务主体创建

请参阅[服务主体创建](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)说明创建服务主体，并为应用程序类型中选择 Web 应用程序/API。

### <a name="access-key-creation"></a>访问密钥创建

服务主体进行身份验证需要密钥，请按照此部分以生成密钥中的步骤。


1. 从 Azure Active Directory 中的“应用注册”，选择应用程序。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\000_01.png)

2.  请记下的值的**应用程序 ID**。 VSTS 中配置的服务终结点时，你将使用该值。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\000_02.png)

3. 若要生成身份验证密钥，请选择“设置”。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\000_03.png)

4. 若要生成身份验证密钥，请选择“密钥”。
 
    ![替换文字](media\azure-stack-solution-hybrid-pipeline\000_04.png)

5. 提供密钥说明和密钥持续时间。 完成后，选择“保存”。
 
    ![替换文字](media\azure-stack-solution-hybrid-pipeline\000_05.png)

    保存密钥后, 会显示密钥的值。 复制此值，因为稍后不能检索密钥。 你提供**键值**替换为与应用程序日志的应用程序 ID。 将密钥值存储在应用程序可检索的位置。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\000_06.png)

### <a name="get-tenant-id"></a>获取租户 ID

VSTS 需要作为服务终结点配置的一部分，**租户 ID**对应于您的 Azure 堆栈图章已部署到 AAD 目录。 按照要收集租户 id。 此部分中的步骤

1. 选择“Azure Active Directory”。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\000_07.png)

2. 若要获取租户 ID，请选择 Azure AD 租户的“属性”。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\000_08.png)
 
3. 复制“目录 ID”。 此值即为租户 ID。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\000_09.png)

### <a name="grant-the-service-principal-rights-to-deploy-resources-in-the-azure-stack-subscription"></a>授予部署 Azure 堆栈订阅中的资源的服务主体权限 

要访问订阅中的资源，必须将应用程序分配到角色。 决定哪个角色表示应用程序的相应权限。 若要了解有关可用角色的信息，请参阅 [RBAC：内置角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)。

可将作用域设置为订阅、资源组或资源级别。 较低级别的作用域将继承权限。 例如，将某个应用程序添加到资源组的“读取者”角色意味着该应用程序可以读取该资源组及其包含的所有资源。

1. 导航到要将应用程序分配到的作用域级别。 例如，若要在订阅范围内分配角色，选择“订阅”。 可改为选择资源组或资源。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\000_10.png)

2. 选择**订阅**（资源组或资源），将分配到应用程序。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\000_11.png)

3. 选择“访问控制 (IAM)”。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\000_12.png)

4. 选择 **添加** 。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\000_13.png)

5. 选择要分配到应用程序的角色。 下图显示**所有者**角色。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\000_14.png)

6. 默认情况下，可用选项中不显示 Azure Active Directory 应用程序。 若要查找你的应用程序，你必须**提供名称**搜索字段中。 选择它。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\000_16.png)

7. 选择“保存”完成角色分配。 该应用程序会显示在分配到该范围角色的用户列表中。

### <a name="role-based-access-control"></a>基于角色的访问控制

Azure 基于角色的访问控制 (RBAC) 启用 Azure 的精细访问权限管理。 使用 RBAC，可以仅授予用户执行其作业所需的访问次数。 有关基于角色的访问控制的详细信息，请参阅[管理对 Azure 订阅资源的访问](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json)。

### <a name="vsts-agent-pools"></a>VSTS 代理池

而不是单独管理每个代理，你将代理添加到代理池。 代理池该池中的所有代理定义的共享的边界。 VSTS 中, 代理池的作用域为 VSTS 本地帐户。因此，你可以跨团队项目共享代理池。 有关详细信息和如何创建池的 VSTS 代理的教程，请参阅[创建代理池和队列](https://docs.microsoft.com/vsts/build-release/concepts/agents/pools-queues?view=vsts)。

### <a name="add-a-personal-access-token-pat-for-azure-stack"></a>为 Azure 堆栈添加个人访问令牌 (PAT)

1. 登录到你的 VSTS 帐户，然后选择你的帐户配置文件名称。
2. 选择**管理安全性**到访问令牌创建页。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\000_17.png)

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\000_18.png)

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\000_18a.png)

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\000_18b.png)

3. 将复制该令牌。
    
    > [!note]  
    > 获取令牌的信息。 它不会显示在离开此屏幕后再次。 
    
    ![替换文字](media\azure-stack-solution-hybrid-pipeline\000_19.png)
    

### <a name="install-the-vsts-build-agent-on-the-azure-stack-hosted-build-server"></a>VSTS Azure 堆栈生成代理的安装托管生成服务器

1.  连接到你部署 Azure 堆栈的主机的生成服务器。

2.  下载和部署的生成代理作为服务使用你的个人访问令牌 (PAT)，并且 VM 管理员帐户下运行。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\010_downloadagent.png)

3. 转到提取的生成代理文件夹。 运行**run.cmd**文件从提升的命令提示符。 

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\000_20.png)

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\000_21.png)

4.  Run.cmd 完成后文件夹提取的内容应如下所示：

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\009_token_file.png)

    你现在可以看到 VSTS 文件夹中的代理。

## <a name="endpoint-creation-permissions"></a>终结点创建权限

用户可以创建终结点，以便 VSTO 生成可以将 Azure 服务应用程序部署到堆栈。 VSTS 连接到生成代理，然后将与 Azure 堆栈的连接。 

![替换文字](media\azure-stack-solution-hybrid-pipeline\012_securityendpoints.png)

1. 上**设置**菜单上，选择**安全**。
2. 在**VSTS 组**在左侧，选择列表**终结点创建者**。 

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\013_endpoint_creators.png)

3. 上**成员**选项卡上，选择 **+ 添加**。 

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\014_members_tab.png)

4. 键入用户名，然后从列表中选择该用户。
5. 单击“保存更改”。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\015_save_endpoint.png)

6. 在**VSTS 组**在左侧，选择列表**终结点管理员**。
7. 上**成员**选项卡上，选择 **+ 添加**。
8. 键入用户名，然后从列表中选择该用户。
9. 单击“保存更改”。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\016_save_changes.png)

    Azure 堆栈中的生成代理获得从 VSTS，然后传达与 Azure 堆栈进行通信的终结点信息的说明。 
    
    到 Azure 堆栈连接 VSTS 现在已准备好。

## <a name="develop-your-application"></a>开发应用程序

设置混合 CI/CD 来将 Web 应用部署到 Azure 和 Azure 堆栈，并自动将更改推送到这两个云。

> [!note]  
> 你需要 Azure 堆栈使用联合运行 （Windows Server 和 SQL） 并让部署应用程序服务的正确图像。 查看有关 Azure 堆栈运算符要求的 App Service 文档"先决条件"部分。

### <a name="add-code-to-vsts-project"></a>将代码添加到 VSTS 项目

1. 使用具有 Azure 堆栈的项目创建权限的帐户登录到 Visual Studio。

    混合 CI/CD 可以将应用于应用程序代码和基础结构代码。 使用[Azure 资源管理器模板，如 web](https://azure.microsoft.com/resources/templates/)从 VSTS 对这两个云的应用程序代码。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\017_connect_to_project.png)

2. **克隆存储库**通过创建并打开默认的 web 应用。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\018_link_arm.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>在这两个云中创建自包含的 web 应用程序部署的应用程序服务

1. 编辑**WebApplication.csproj**文件： 选择**Runtimeidentifier**并添加`win10-x64.`详细信息，请参阅[独立的部署](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd)文档.

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\019_runtimeidentifer.png)

2. 将代码签入 VSTS 使用团队资源管理器。

3. 确认已到 Visual Studio Team Services 中检查应用程序代码。 

### <a name="create-the-build-definition"></a>创建生成定义

1. 登录到 VSTS 以确认能够创建生成定义。

2. 添加 **-r win10 x64**代码。 这是需触发自包含的部署使用.Net 核心。 

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\020_publish_additions.png)

3. 运行生成。 [自包含的部署生成](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd)过程将发布可以运行在 Azure 和 Azure 堆栈的项目。

### <a name="using-an-azure-hosted-agent"></a>使用 Azure 托管的代理

使用在 VSTS 中托管的代理是一个方便的选项生成和部署 web 应用。 Microsoft Azure，从而启用持续的、 不间断的开发、 测试和部署自动执行维护和升级。

### <a name="manage-and-configure-the-continuous-deployment-cd-process"></a>管理和配置连续部署 (CD) 进程

Visual Studio Team Services (VSTS) 和 Team Foundation Server (TFS) 提供高度可配置和可管理管道发行版本开发，如多个环境暂存，QA 和生产环境;包括在特定阶段都需要审批。

### <a name="create-release-definition"></a>创建发布定义

![替换文字](media\azure-stack-solution-hybrid-pipeline\021a_releasedef.png)

1. 选择 **\[ +]** 添加新的发行版下**版本选项卡**VSO 的生成和发布页中。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\102.png)

2. 应用**Azure 应用程序服务部署**模板。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\103.png)

3. 添加项目下拉列表菜单下,**添加项目**为 Azure 云生成应用程序。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\104.png)

4. 在管道选项卡上，选择**阶段**，**任务**环境的链接并将 Azure 云设置环境值。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\105.png)

5. 设置**环境名称**和选择 Azure**订阅**Azure 云终结点。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\106.png)

6. 在环境名称下设置所需**Azure app service 名称**。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\107.png)

7. 输入**托管 VS2017**下托管的 Azure 云环境的代理队列。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\108.png)

8. 在部署 Azure App Service 菜单中，选择有效**包或文件夹**环境。 选择**确定**到**文件夹位置**。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\109.png)

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\110.png)

9. 保存所有更改并返回到**发行管道**。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\111.png)

10. 添加**新项目**选择的生成进行 Azure 堆栈应用。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\112.png)

11. 添加一个详细环境应用**Azure 应用程序服务部署**。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\113.png)

12. 命名新环境**Azure 堆栈**。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\114.png)

13. 查找 Azure 堆栈环境下的**任务**选项卡。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\115.png)

14. 选择**订阅**Azure 堆栈终结点。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\116.png)

15. 设置 Azure 堆栈 web 应用名称作为**App service 名称**。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\117.png)

16. 选择**Azure 堆栈代理**。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\118.png)

17. 在部署 Azure App Service 部分，选择有效**包或文件夹**环境。 选择确定以**文件夹位置**。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\119.png)

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\120.png)

18. 变量的选项卡下添加一个名为变量**VSTS_ARM_REST_IGNORE_SSL_ERRORS**，设置其值作为**true**，并确定范围到**Azure 堆栈**。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\121.png)

19. 选择**连续**部署触发这两个项目中的图标和启用仍继续部署触发器。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\122.png)

20. 选择**预部署**在 azure 中的条件图标堆栈环境，并将该触发器设置为**发布后**。

21. 保存所有更改。

> [!note]  
> 任务的某些设置可能自动已定义为[环境变量](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables)从模板创建的版本定义时。 任务设置;，无法修改这些设置而是必须选择要编辑这些设置的父环境项。

## <a name="create-a-release"></a>创建版本

现在，你已完成对版本定义的修改，就可以开始部署。 若要执行此操作，你创建从版本定义的版本。 可能自动; 创建发布例如，版本定义中设置连续部署触发器。 这意味着修改源代码将启动新的生成和从，新版本。 但是，在本部分中您将新的发行版手动创建。

1. 打开**释放**下拉列表并选择**创建版本**。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\200.png)
 
2. 输入发布的说明，请检查正确的项目已选中，且然后选择**创建**。 几分钟后标题显示，指示已创建新的版本。 选择链接 （该版本的名称）。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\201.png)
 
3. 解除摘要页将打开显示详细信息的版本。 在**环境**部分中，你将看到"QA"环境的部署状态从"正在进行的"更改为"SUCCEEDED"，并在该点，显示横幅，该值指示版本现在正在等待批准。 当部署到环境处于挂起状态或已失败的蓝色 (i) 将显示信息图标。 指向此事件可查看包含原因一个弹出窗口。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\202.png)

其他视图，例如的版本中，列表还显示一个图标，指示审批处于挂起状态。 图标显示一个弹出窗口，当你指向它包含环境名称和更多详细信息。 这样，可轻松管理员，了解哪些版本正在等待批准，以及所有版本的总体进度。

### <a name="monitor-and-track-deployments"></a>监视和跟踪部署

在本部分中，你将了解如何监视和跟踪部署-在此示例中为两个 Azure 应用程序服务网站-从你在上一节中创建的版本。

1. 在发布摘要页中，选择**日志**链接。 虽然部署正在进行，此页将显示实时日志，从代理，并且，在左窗格中，在部署过程中为每个环境的每个操作的状态的指示。

    选择中的图标**操作**列预部署或部署后的审批以查看详细信息的人批准 （或拒绝） 的部署和消息提供该用户。

2. 部署完成后，在右窗格中显示整个的日志文件。 选择任一**处理步骤**在左窗格中显示仅日志文件内容以该步骤。 这使得更轻松地跟踪和调试的总体部署的各个部分。 或者，单独的日志文件中，从或下载的所有日志文件，zip 的图标和在页中的链接。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\203.png)
 
3. 打开**摘要**选项卡以查看发布的总体详细信息。 它显示生成，并将它部署到-以及部署状态和版本的其他信息的环境的详细的信息。

4. 选择的每个**环境链接**若要查看有关现有和挂起的部署到该特定环境的更多详细信息。 这些页可用于验证的相同内部版本已部署到这两个环境。

5. 打开**部署生产应用**在你浏览。 例如，对于一个 Azure 应用程序服务的网站，从 URL `http://[your-app-name].azurewebsites.net`。

## <a name="next-steps"></a>后续步骤

- 若要了解有关 Azure 云模式的详细信息，请参阅[云设计模式](https://docs.microsoft.com/azure/architecture/patterns)。
