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
ms.date: 05/15/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 41e6f64ada7c95674cc2573048eef8afc83e4385
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604346"
---
# <a name="tutorial-deploy-apps-to-azure-and-azure-stack"></a>教程： 将应用部署到 Azure 和 Azure 堆栈

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

了解如何应用程序部署到 Azure 和 Azure 堆栈使用混合连续集成/continuous 传递 (CI/CD) 管道。

在本教程中，你将创建示例环境到：

> [!div class="checklist"]
> * 根据 Visual Studio Team Services (VSTS) 存储库的代码提交启动新的生成。
> * 自动将你的应用程序部署到为用户验收测试的全球 Azure。
> * 如果你的代码通过测试，自动将应用程序部署到 Azure 堆栈。

## <a name="benefits-of-the-hybrid-delivery-build-pipe"></a>混合传递的好处生成管道

连续性、 安全性和可靠性个的应用程序部署的关键元素。 这些元素是必需的你的组织和关键到你的开发团队。 混合 CI/CD 管道，您可以在你的本地环境和公有云之间整合生成管道。 混合交付模型还可以更改而无需更改你的应用程序的部署位置。

使用这种混合方法的其他好处包括：

* 你可以在你的本地 Azure 堆栈环境和 Azure 公有云中维护一组一致的开发工具。  一组通用工具便于实现 CI/CD 模式和实践。
* 应用程序和服务部署在 Azure 或 Azure 堆栈中是可互换和相同的代码可以在任一位置中运行。 可以利用本地以及公有云特性和功能。

若要了解更多有关 CI 和 CD:

* [什么是持续集成？](https://www.visualstudio.com/learn/what-is-continuous-integration/)
* [什么是持续交付？](https://www.visualstudio.com/learn/what-is-continuous-delivery/)

## <a name="prerequisites"></a>必备组件

你需要具有用来生成混合 CI/CD 管道的组件。 以下组件将花点时间准备：

* Azure 的 OEM 硬件合作伙伴可以部署生产 Azure 堆栈。 所有用户就可以都部署 Azure 堆栈开发工具包 (ASDK)。
* Azure 堆栈运算符还必须： 部署 App Service、 创建计划和产品/服务、 创建租户订阅，和添加 Windows Server 2016 映像。

>[!NOTE]
>如果你已有一些部署这些组件，请确保它们满足所有要求，然后在开始本教程。

本教程假定你具有 Azure 和 Azure 堆栈的一些基本知识。 若要了解详细信息在开始本教程之前，请阅读以下文章：

* [Azure 简介](https://azure.microsoft.com/overview/what-is-azure/)
* [Azure Stack 的重要概念](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure-requirements"></a>Azure 要求

* 如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 在 Azure 中创建 [Web 应用](https://docs.microsoft.com/azure/app-service/app-service-web-overview)。 请记下的 Web 应用 URL，你需要在本教程中使用它。

### <a name="azure-stack-requirements"></a>Azure 堆栈要求

* 使用集成的 Azure 堆栈系统或部署 Azure 堆栈开发工具包 (ASDK)。 部署 ASDK:
    * [教程： 部署使用安装程序 ASDK](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy)提供详细的部署说明。
    * 使用[ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 ) PowerShell 脚本自动执行 ASDK 后期部署步骤。

    > [!Note]
    > ASDK 安装需要大约七个小时才能完成，因此请相应地计划。

 * 将[应用服务](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) PaaS 服务部署到 Azure Stack。
 * 创建[计划/提供](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview)Azure 堆栈中。
 * 创建[租户订阅](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm)Azure 堆栈中。
 * 在租户订阅中创建 Web 应用。 记下新 Web 应用的 URL，供稍后使用。
 * 租户订阅中部署 VSTS 虚拟机。
* 虚拟机 (VM)，使用.NET 3.5 中提供 Windows Server 2016 的映像。 此 VM 将生成 Azure 堆栈上，为私有的生成代理。

### <a name="developer-tool-requirements"></a>开发人员工具要求

* 创建 [VSTS 工作区](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)。 注册过程将创建名为 **MyFirstProject** 的项目。
* [安装 Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) 并[登录到 VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services)。
* 连接到你的项目和[本地克隆](https://www.visualstudio.com/docs/git/gitquickstart)。

 > [!Note]
 > 你的 Azure 堆栈环境需要正确的图像联合运行 Windows Server 和 SQL Server。 它必须还具有部署的应用程序服务。

## <a name="prepare-the-private-build-and-release-agent-for-visual-studio-team-services-integration"></a>准备用于 Visual Studio Team Services 集成的专用生成和发布代理

### <a name="prerequisites"></a>必备组件

Visual Studio Team Services (VSTS) 使用服务主体对 Azure 资源管理器进行身份验证。 VSTS 必须具有**参与者**设置订阅中的资源 Azure 堆栈的角色。

以下步骤描述所需配置身份验证：

1. 创建服务主体，或使用现有的服务主体。
2. 创建身份验证密钥为服务主体。
3. 验证 Azure 堆栈订阅通过基于角色的访问控制，以允许服务主体名称 (SPN) 的参与者角色的一部分。
4. 在使用 Azure 堆栈终结点和 SPN 的信息的 VSTS 中创建新的服务定义。

### <a name="create-a-service-principal"></a>创建服务主体

请参阅[服务主体创建](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)说明进行操作以创建服务主体，然后选择**Web 应用程序/API**的应用程序类型。

### <a name="create-an-access-key"></a>创建访问键

服务主体进行身份验证需要密钥。 使用以下步骤来生成密钥。

1. 从 Azure Active Directory 中的“应用注册”，选择应用程序。

    ![选择的应用程序](media\azure-stack-solution-hybrid-pipeline\000_01.png)

2. 记下“应用程序 ID”的值。 在 VSTS 中配置服务终结点时，将要使用该值。

    ![应用程序 ID](media\azure-stack-solution-hybrid-pipeline\000_02.png)

3. 若要生成身份验证密钥，请选择“设置”。

    ![编辑应用程序设置](media\azure-stack-solution-hybrid-pipeline\000_03.png)

4. 若要生成身份验证密钥，请选择“密钥”。

    ![配置密钥设置](media\azure-stack-solution-hybrid-pipeline\000_04.png)

5. 提供的说明的键，并设置密钥的持续时间。 完成后，选择“保存”。

    ![密钥的说明和持续时间](media\azure-stack-solution-hybrid-pipeline\000_05.png)

    保存密钥后，将显示密钥**值**。 复制此值，因为你无法获取此值。 提供用于登录到应用程序的**密钥值**和应用程序 ID。 将密钥值存储在应用程序可检索的位置。

    ![密钥值](media\azure-stack-solution-hybrid-pipeline\000_06.png)

### <a name="get-the-tenant-id"></a>获取租户 ID

VSTS 需要作为服务终结点配置的一部分，**租户 ID**相对应的 Azure 堆栈戳部署到 AAD 目录。 使用以下步骤获取租户 id。

1. 选择“Azure Active Directory”。

    ![Azure Active Directory 租户](media\azure-stack-solution-hybrid-pipeline\000_07.png)

2. 若要获取租户 ID，请选择 Azure AD 租户的“属性”。

    ![查看租户属性](media\azure-stack-solution-hybrid-pipeline\000_08.png)

3. 复制“目录 ID”。 此值即为租户 ID。

    ![Directory ID](media\azure-stack-solution-hybrid-pipeline\000_09.png)

### <a name="grant-the-service-principal-rights-to-deploy-resources-in-the-azure-stack-subscription"></a>授予在 Azure Stack 订阅中部署资源的服务主体权限

要访问订阅中的资源，必须将应用程序分配到角色。 决定哪个角色表示应用程序的最佳权限。 若要了解有关可用角色的信息，请参阅 [RBAC：内置角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)。

可将作用域设置为订阅、资源组或资源级别。 较低级别的作用域将继承权限。 例如，应用程序添加到资源组的读者角色意味着它可以读取资源组和任何其资源。

1. 导航到要将应用程序分配到的作用域级别。 例如，若要在订阅范围内分配角色，选择“订阅”。

    ![选择订阅](media\azure-stack-solution-hybrid-pipeline\000_10.png)

2. 在**订阅**，选择 Visual Studio Enterprise。

    ![Visual Studio Enterprise](media\azure-stack-solution-hybrid-pipeline\000_11.png)

3. 在 Visual Studio Enterprise 中，选择**访问控制 (IAM)**。

    ![访问控制 (IAM)](media\azure-stack-solution-hybrid-pipeline\000_12.png)

4. 选择 **添加** 。

    ![添加](media\azure-stack-solution-hybrid-pipeline\000_13.png)

5. 在**添加权限**，选择的角色你想要分配给应用程序。 在此示例中，**所有者**角色。

    ![所有者角色](media\azure-stack-solution-hybrid-pipeline\000_14.png)

6. 默认情况下，可用选项中不显示 Azure Active Directory 应用程序。 若要查找你的应用程序，你必须提供在其名称**选择**字段对其进行搜索。 选择应用。

    ![应用程序搜索结果](media\azure-stack-solution-hybrid-pipeline\000_16.png)

7. 选择“保存”完成角色分配。 该应用程序会显示在分配到该范围角色的用户列表中。

### <a name="role-based-access-control"></a>基于角色的访问控制

Azure 基于角色的访问控制 (RBAC) 提供了 Azure 的精细访问权限管理。 通过使用 RBAC，你可以控制用户需要执行其作业的访问的级别。 有关基于角色的访问控制的详细信息，请参阅[管理对 Azure 订阅资源的访问](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json)。

### <a name="vsts-agent-pools"></a>VSTS 代理池

而不是单独管理每个代理，也可以将代理到代理池。 代理池定义该池中所有代理的共享边界。 VSTS 中, 代理池的范围限于 VSTS 帐户，这意味着你可以跨团队项目共享代理池。 若要了解有关代理池的详细信息，请参阅[创建代理池和队列](https://docs.microsoft.com/vsts/build-release/concepts/agents/pools-queues?view=vsts)。

### <a name="add-a-personal-access-token-pat-for-azure-stack"></a>为 Azure 堆栈添加个人访问令牌 (PAT)

创建个人访问令牌来访问 VSTS。

1. 登录到 VSTS 帐户，并选择帐户配置文件名称。
2. 选择“管理安全性”以访问令牌创建页。

    ![用户登录](media\azure-stack-solution-hybrid-pipeline\000_17.png)

    ![选择团队项目](media\azure-stack-solution-hybrid-pipeline\000_18.png)

    ![添加个人访问令牌](media\azure-stack-solution-hybrid-pipeline\000_18a.png)

    ![创建令牌](media\azure-stack-solution-hybrid-pipeline\000_18b.png)

3. 复制令牌。

    > [!Note]
    > 保存该令牌的信息。 此信息不会存储，将不会再次将保留网页上时。

    ![个人访问令牌](media\azure-stack-solution-hybrid-pipeline\000_19.png)

### <a name="install-the-vsts-build-agent-on-the-azure-stack-hosted-build-server"></a>在 Azure Stack 托管的生成服务器上安装 VSTS 生成代理

1. 连接到 Azure Stack 主机上部署的生成服务器。
2. 下载生成代理，并使用个人访问令牌 (PAT) 将其部署为服务，然后以 VM 管理员帐户运行。

    ![下载生成代理](media\azure-stack-solution-hybrid-pipeline\010_downloadagent.png)

3. 导航到的文件夹的提取的生成代理。 从特权提升的命令提示符运行 **run.cmd** 文件。

    ![提取的生成代理](media\azure-stack-solution-hybrid-pipeline\000_20.png)

    ![注册生成代理](media\azure-stack-solution-hybrid-pipeline\000_21.png)

4. Run.cmd 完成后，生成代理文件夹将更新为其他文件。 文件夹提取的内容应如下所示：

    ![生成文件夹更新的代理](media\azure-stack-solution-hybrid-pipeline\009_token_file.png)

    你可以看到 VSTS 文件夹中的代理。

## <a name="endpoint-creation-permissions"></a>终结点创建权限

通过创建终结点，Visual Studio Online (VSTO) 生成可以将 Azure 服务应用程序部署到 Azure 堆栈。 VSTS 连接到连接到 Azure 堆栈的生成代理。

![在 VSTO NorthwindCloud 示例应用程序](media\azure-stack-solution-hybrid-pipeline\012_securityendpoints.png)

1. 登录到 VSTO 并导航到应用设置页。
2. 上**设置**，选择**安全**。
3. 在**VSTS 组**，选择**终结点创建者**。

    ![NorthwindCloud 终结点创建者](media\azure-stack-solution-hybrid-pipeline\013_endpoint_creators.png)

4. 上**成员**选项卡上，选择**添加**。

    ![添加成员](media\azure-stack-solution-hybrid-pipeline\014_members_tab.png)

5. 在**添加用户和组**、 输入用户名和从用户的列表中选择该用户。
6. 选择“保存更改”。
7. 在**VSTS 组**列表中，选择**终结点管理员**。

    ![NorthwindCloud 终结点管理员](media\azure-stack-solution-hybrid-pipeline\015_save_endpoint.png)

8. 上**成员**选项卡上，选择**添加**。
9. 在**添加用户和组**、 输入用户名和从用户的列表中选择该用户。
10. 选择“保存更改”。

现在，终结点信息存在，则 Azure 堆栈连接到 VSTS 已准备好使用。 Azure 堆栈中的生成代理从 VSTS，获取说明和代理然后传达与 Azure 堆栈进行通信的终结点信息。

![生成代理](media\azure-stack-solution-hybrid-pipeline\016_save_changes.png)

## <a name="develop-your-application-build"></a>开发你的应用程序生成

在本教程的此部分中，你将：

* 将代码添加到 VSTS 项目。
* 创建自包含的 web 应用程序部署。
* 配置连续部署过程

> [!Note]
 > 你的 Azure 堆栈环境需要正确的图像联合运行 Windows Server 和 SQL Server。 它必须还具有部署的应用程序服务。 查看应用服务文档的“先决条件”部分，了解 Azure Stack 操作员的要求。

混合 CI/CD 可同时应用到应用程序代码和基础结构代码。 使用[Azure 资源管理器模板，如 web](https://azure.microsoft.com/resources/templates/)从 VSTS 将部署到这两个云应用程序代码。

### <a name="add-code-to-a-vsts-project"></a>将代码添加到 VSTS 项目

1. 使用具有 Azure 堆栈的项目创建权限的帐户登录到 VSTS。 下一步的屏幕截图显示如何连接到 HybridCICD 项目。

    ![连接到项目](media\azure-stack-solution-hybrid-pipeline\017_connect_to_project.png)

2. 创建并打开默认 Web 应用以**克隆存储库**。

    ![克隆存储库](media\azure-stack-solution-hybrid-pipeline\018_link_arm.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>为这两个云中的应用服务创建独立的 Web 应用部署

1. 编辑**WebApplication.csproj**文件： 选择**Runtimeidentifier** ，然后添加`win10-x64.`详细信息，请参阅[独立的部署](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd)文档。

    ![配置 Runtimeidentifier](media\azure-stack-solution-hybrid-pipeline\019_runtimeidentifer.png)

2. 团队资源管理器用于将代码签入 VSTS。

3. 确认应用程序代码已签入到 Visual Studio Team Services。

### <a name="create-the-build-definition"></a>创建生成定义

1. 可以创建生成定义的帐户登录到 VSTS。
2. 导航到**生成 Web 应用程序**项目页。

3. 在**参数**，添加 **-r win10 x64**代码。 这必需触发自包含的部署使用.NET Core。

    ![添加的自变量生成定义](media\azure-stack-solution-hybrid-pipeline\020_publish_additions.png)

4. 运行生成。 [独立部署生成](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd)过程将发布可在 Azure 和 Azure Stack 上运行的项目。

### <a name="use-an-azure-hosted-build-agent"></a>使用 Azure 托管生成代理

使用在 VSTS 中托管的生成代理是一个方便的选项，用于生成和部署 web 应用。 启用连续和不间断地开发周期的 Microsoft Azure 自动执行代理维护和升级。

### <a name="configure-the-continuous-deployment-cd-process"></a>配置 (CD) 进行连续部署过程

Visual Studio Team Services (VSTS) 和 Team Foundation Server (TFS) 提供高度可配置和可管理管道发行版本中多个环境开发，如暂存，质量保证 (QA) 和生产。 此过程可能包括在应用程序生命周期的特定阶段都需要审批。

### <a name="create-release-definition"></a>创建发布定义

创建版本定义是在你的应用程序中的最后一步生成过程。 此版本定义用于创建发布和部署的版本。

1. 登录到 VSTS 并导航到**生成和发布**为你的项目。
2. 上**版本**选项卡上，选择 **\[ +]** ，然后挑选**创建版本定义**。

   ![创建发布定义](media\azure-stack-solution-hybrid-pipeline\021a_releasedef.png)

3. 上**选择模板**，选择**Azure 应用程序服务部署**，然后选择**应用**。

    ![应用模板](media\azure-stack-solution-hybrid-pipeline\102.png)

4. 上**添加项目**，从**源 （生成定义）** 下拉菜单，选择 Azure 云生成应用程序。

    ![添加项目](media\azure-stack-solution-hybrid-pipeline\103.png)

5. 上**管道**选项卡上，选择**1 阶段**， **1 个任务**链接到**查看环境任务**。

    ![管道视图任务](media\azure-stack-solution-hybrid-pipeline\104.png)

6. 上**任务**选项卡上，输入 Azure 为**环境名称**并选择从 AzureCloud Traders Web EP **Azure 订阅**下拉列表。

    ![设置环境变量。](media\azure-stack-solution-hybrid-pipeline\105.png)

7. 输入**Azure app service 名称**，即"northwindtraders"下一步的屏幕截图中。

    ![应用程序服务名称](media\azure-stack-solution-hybrid-pipeline\106.png)

8. 代理阶段中，选择**托管 VS2017**从**代理队列**下拉列表。

    ![承载的代理](media\azure-stack-solution-hybrid-pipeline\107.png)

9. 在**部署 Azure App Service**，选择有效**包或文件夹**环境。

    ![选择包或文件夹](media\azure-stack-solution-hybrid-pipeline\108.png)

10. 在**选择文件或文件夹**，选择**确定**到**位置**。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\109.png)

11. 保存所有更改并返回到**管道**。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\110.png)

12. 上**管道**选项卡上，选择**添加项目**，然后选择**NorthwindCloud Traders 容器**从**源 （生成的定义）** 下拉列表。

    ![添加新项目](media\azure-stack-solution-hybrid-pipeline\111.png)

13. 上**选择模板**，添加另一个环境。 选取**Azure 应用程序服务部署**，然后选择**应用**。

    ![选择模板](media\azure-stack-solution-hybrid-pipeline\112.png)

14. 输入"Azure 堆栈"作为**环境名称**。

    ![环境名称](media\azure-stack-solution-hybrid-pipeline\113.png)

15. 上**任务**选项卡上，查找并选择 Azure 堆栈。

    ![Azure 堆栈环境](media\azure-stack-solution-hybrid-pipeline\114.png)

16. 从**Azure 订阅**下拉列表中，选择"AzureStack Traders 容器 EP"Azure 堆栈终结点。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\115.png)

17. 输入 Azure 堆栈 web 应用程序名称作为**App service 名称**。

    ![应用程序服务名称](media\azure-stack-solution-hybrid-pipeline\116.png)

18. 下**代理选择**，从选取"AzureStack bDouglas Fir"**代理队列**下拉列表。

    ![选择代理](media\azure-stack-solution-hybrid-pipeline\117.png)

19. 有关**部署 Azure App Service**，选择有效**包或文件夹**环境。 上**选择文件或文件夹**，选择**确定**文件夹**位置**。

    ![选取包或文件夹](media\azure-stack-solution-hybrid-pipeline\118.png)

    ![批准位置](media\azure-stack-solution-hybrid-pipeline\119.png)

20. 上**变量**选项卡上，查找命名的变量**VSTS_ARM_REST_IGNORE_SSL_ERRORS**。 将变量的值设置为**true**，并将其范围设置为**Azure 堆栈**。

    ![配置变量](media\azure-stack-solution-hybrid-pipeline\120.png)

21. 上**管道**选项卡上，选择**连续部署触发器**NorthwindCloud Traders Web 项目并设置图标**连续部署触发器**到**启用**。  执行的"容器上 NorthwindCloud Traders"项目相同的操作。

    ![设置连续部署触发器](media\azure-stack-solution-hybrid-pipeline\121.png)

22. 对于 Azure 堆栈环境中，选择**预部署条件**图标将该触发器设置为**发布后**。

    ![设置预部署条件触发器](media\azure-stack-solution-hybrid-pipeline\122.png)

23. 保存所有更改。

> [!Note]
> 释放任务的某些设置可能自动已定义为[环境变量](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables)从模板创建的版本定义时。 任务设置，无法修改这些设置。 但是，你可以编辑父环境项目中的这些设置。

## <a name="create-a-release"></a>创建发布

完成对发布定义的修改后，可以开始部署。 为此，请从发布定义创建发布。 可以自动创建发布；例如，发布定义中已设置持续部署触发器。 这意味着，修改源代码会启动新的生成，继而生成新的发布。 但在本部分，我们将手动创建新发布。

1. 上**管道**选项卡上，打开**释放**下拉列表并选择**创建版本**。

    ![创建发布](media\azure-stack-solution-hybrid-pipeline\200.png)

2. 输入发布的说明，请检查是否选择了正确的项目，，然后选择**创建**。 几分钟后标题显示，指示创建新的版本，且发布名称显示为链接。 选择链接以查看发布摘要页。

    ![版本创建横幅](media\azure-stack-solution-hybrid-pipeline\201.png)

3. 发布摘要页显示有关版本的详细信息。 在"版本-2"，以下屏幕截图**环境**部分将显示**部署状态**表示 Azure 为"正在进行"、 和 Azure 堆栈的状态为"成功"。 当 Azure 环境的部署状态更改为"已成功"时，标题将显示，指示发布可供批准。 何时部署处于挂起状态或已失败，蓝色 **(i)** ，则显示信息图标。 将鼠标悬停在该图标可查看包含延迟或失败的原因一个弹出窗口。

    ![发布摘要页](media\azure-stack-solution-hybrid-pipeline\202.png)

其他视图，例如列表释放，还将显示一个图标，指示审批处于挂起状态。 环境名称和与部署相关的更多详细信息，将显示此图标的弹出消息。 有关管理员，请参阅版本和查看哪些版本正等待批准的整个过程很容易。

### <a name="monitor-and-track-deployments"></a>监视和跟踪部署

本部分说明如何监视和跟踪你的所有部署。 部署两个 Azure 应用程序服务网站版本提供了一个很好示例。

1. 在"发行版 2"摘要页上，选择**日志**。 在部署期间，此页从代理显示实时日志。 左窗格中显示每个环境的部署中的每个操作的状态。

    你可以选择在人员图标**操作**供查看哪些人批准 （或拒绝） 部署和它们所提供的消息预部署或部署后审批的列。

2. 部署完成后，在右窗格中显示整个的日志文件。 你可以选择任意**步骤**在左窗格中，若要查看单个的步骤中，如"初始化作业"日志文件。 若要查看单个日志的能力使更轻松地跟踪和调试的总体部署部分。 你还可以**保存**步骤的日志文件或**所有日志都下载为 zip**。

    ![版本日志](media\azure-stack-solution-hybrid-pipeline\203.png)

3. 打开**摘要**选项卡以查看有关发布的常规信息。 此视图显示有关生成、 已部署到环境、 部署状态和版本的其他信息的详细信息。

4. 选择环境链接 (**Azure**或**Azure 堆栈**) 若要查看有关现有和挂起的部署到特定环境的信息。 这些视图可用作验证的相同内部版本已部署到这两个环境的快速方法。

5. 打开**部署生产应用**在浏览器中。 例如，为 Azure 应用程序服务网站中，打开 URL `http://[your-app-name].azurewebsites.net`。

## <a name="next-steps"></a>后续步骤

* 若要了解有关 Azure 云模式的详细信息，请参阅[云设计模式](https://docs.microsoft.com/azure/architecture/patterns)。
