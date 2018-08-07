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
ms.date: 06/08/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 3fcede7f813e97885d8fc3d7e0bc04776f2d0d12
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39582132"
---
# <a name="tutorial-deploy-apps-to-azure-and-azure-stack"></a>教程：将应用部署到 Azure 和 Azure Stack

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

了解如何使用混合持续集成/持续交付 (CI/CD) 管道将应用程序部署到 Azure 和 Azure Stack。

在本教程中，需创建一个示例环境来完成以下任务：

> [!div class="checklist"]
> * 根据 Visual Studio Team Services (VSTS) 存储库的代码提交启动新的生成。
> * 自动将应用部署到 Azure 公有云进行用户验收测试。
> * 代码通过测试后，可自动将应用部署到 Azure Stack。

## <a name="benefits-of-the-hybrid-delivery-build-pipe"></a>混合交付生成管道的优势

持续性、安全性和可靠性是应用程序部署的几大要素。 这些元素对于组织和开发团队而言至关重要。 使用混合 CI/CD 管道，可跨本地环境与公有云整合生成管道。 混合交付模型还允许在不更改应用程序的情况下更改部署位置。

使用混合方法的其他优势还有：

* 可以在本地 Azure Stack 环境和 Azure 公有云中使用一组一致的开发工具。  使用通用的工具集可以更轻松地实施 CI/CD 模式和做法。
* 在 Azure 或 Azure Stack 中部署的应用和服务可以互换，相同的代码可在任一位置运行。 可以利用本地和公有云的特性与功能。

若要详细了解 CI 和 CD，请参阅：

* [什么是持续集成？](https://www.visualstudio.com/learn/what-is-continuous-integration/)
* [什么是持续交付？](https://www.visualstudio.com/learn/what-is-continuous-delivery/)

## <a name="prerequisites"></a>必备组件

需要准备好组件才能生成混合 CI/CD 管道。 以下组件的准备需要一定的时间：

* Azure OEM/硬件合作伙伴可以部署生产型 Azure Stack。 所有用户都可以部署 Azure Stack 开发工具包 (ASDK)。
* 此外，Azure Stack 操作员必须部署应用服务、创建计划和套餐、创建租户订阅，并添加 Windows Server 2016 映像。

>[!NOTE]
>如果已部署其中的某些组件，请确保它们符合所有要求，然后开始本教程。

本教程假设你对 Azure 和 Azure Stack 有一些基本的了解。 若要在开始本教程之前了解更多信息，请阅读以下文章：

* [Azure 简介](https://azure.microsoft.com/overview/what-is-azure/)
* [Azure Stack 的重要概念](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure-requirements"></a>Azure 要求

* 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 在 Azure 中创建 [Web 应用](https://docs.microsoft.com/azure/app-service/app-service-web-overview)。 记下需在本教程中使用的 Web 应用 URL。

### <a name="azure-stack-requirements"></a>Azure Stack 要求

* 使用 Azure Stack 集成系统，或部署 Azure Stack 开发工具包 (ASDK)。 若要部署 ASDK，请遵循以下要求：
    * [教程：使用安装程序部署 ASDK](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy) 提供了详细的部署说明。
    * 使用 [ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 ) PowerShell 脚本自动完成 ASDK 部署后步骤。

    > [!Note]
    > ASDK 安装需要约七小时才能完成，因此请相应地做好规划。

 * 将[应用服务](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) PaaS 服务部署到 Azure Stack。
 * 在 Azure Stack 中创建[计划/套餐](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview)。
 * 在 Azure Stack 中创建[租户订阅](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm)。
 * 在租户订阅中创建 Web 应用。 记下新 Web 应用的 URL，供稍后使用。
 * 在租户订阅中部署 VSTS 虚拟机。
* 提供一个 Windows Server 2016 映像，其中包含用于虚拟机 (VM) 的 .NET 3.5。 将在 Azure Stack 上生成此 VM，作为专用的生成代理。

### <a name="developer-tool-requirements"></a>开发人员工具要求

* 创建 [VSTS 工作区](https://docs.microsoft.com/vsts/repos/tfvc/create-work-workspaces)。 注册过程将创建名为 **MyFirstProject** 的项目。
* [安装 Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) 并[登录到 VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services)。
* 连接到项目并[将其克隆到本地](https://www.visualstudio.com/docs/git/gitquickstart)。

 > [!Note]
 > Azure Stack 环境需要联合正确的映像才能运行 Windows Server 和 SQL Server。 它还必须部署应用服务。

## <a name="prepare-the-private-build-and-release-agent-for-visual-studio-team-services-integration"></a>准备用于 Visual Studio Team Services 集成的专用生成和发布代理

### <a name="prerequisites"></a>必备组件

Visual Studio Team Services (VSTS) 使用服务主体对 Azure 资源管理器进行身份验证。 VSTS 必须使用“参与者”角色在 Azure Stack 订阅中预配资源。

以下步骤介绍了配置身份验证的具体要求：

1. 创建一个服务主体，或者使用现有的服务主体。
2. 创建服务主体的身份验证密钥。
3. 通过基于角色的访问控制来验证 Azure Stack 订阅，使服务主体名称 (SPN) 成为“参与者”角色的一部分。
4. 使用 Azure Stack 终结点和 SPN 信息在 VSTS 中创建新的服务定义。

### <a name="create-a-service-principal"></a>创建服务主体

请按照[创建服务主体](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)中的说明来创建服务主体，然后选择“Web 应用/API”作为“应用程序类型”。

### <a name="create-an-access-key"></a>创建访问密钥

服务主体需要使用密钥进行身份验证。 按以下步骤生成密钥。

1. 从 Azure Active Directory 中的“应用注册”，选择应用程序。

    ![选择应用程序](media\azure-stack-solution-hybrid-pipeline\000_01.png)

2. 记下“应用程序 ID”的值。 在 VSTS 中配置服务终结点时，将要使用该值。

    ![应用程序 ID](media\azure-stack-solution-hybrid-pipeline\000_02.png)

3. 若要生成身份验证密钥，请选择“设置”。

    ![编辑应用设置](media\azure-stack-solution-hybrid-pipeline\000_03.png)

4. 若要生成身份验证密钥，请选择“密钥”。

    ![配置密钥设置](media\azure-stack-solution-hybrid-pipeline\000_04.png)

5. 提供密钥说明并设置密钥持续时间。 完成后，选择“保存”。

    ![密钥说明和持续时间](media\azure-stack-solution-hybrid-pipeline\000_05.png)

    保存密钥后，将显示密钥**值**。 请复制此值，因为以后无法获取此值。 请提供**密钥值**及应用程序 ID，以便以应用程序的身份登录。 将密钥值存储在应用程序可检索的位置。

    ![密钥值](media\azure-stack-solution-hybrid-pipeline\000_06.png)

### <a name="get-the-tenant-id"></a>获取租户 ID

在配置服务终结点的过程中，VSTS 要求提供与 Azure Stack 戳所部署到的 AAD 目录相对应的**租户 ID**。 按以下步骤获取租户 ID。

1. 选择“Azure Active Directory”。

    ![租户的 Azure Active Directory](media\azure-stack-solution-hybrid-pipeline\000_07.png)

2. 若要获取租户 ID，请选择 Azure AD 租户的“属性”。

    ![查看租户属性](media\azure-stack-solution-hybrid-pipeline\000_08.png)

3. 复制“目录 ID”。 此值即为租户 ID。

    ![Directory ID](media\azure-stack-solution-hybrid-pipeline\000_09.png)

### <a name="grant-the-service-principal-rights-to-deploy-resources-in-the-azure-stack-subscription"></a>授予在 Azure Stack 订阅中部署资源的服务主体权限

要访问订阅中的资源，必须将应用程序分配到角色。 确定哪个角色体现应用程序的最佳权限。 若要了解有关可用角色的信息，请参阅 [RBAC：内置角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)。

可将作用域设置为订阅、资源组或资源级别。 较低级别的作用域将继承权限。 例如，将某个应用程序添加到资源组的“读取者”角色意味着该应用程序可以读取该资源组及其任意资源。

1. 导航到要将应用程序分配到的作用域级别。 例如，若要在订阅范围内分配角色，选择“订阅”。

    ![选择订阅](media\azure-stack-solution-hybrid-pipeline\000_10.png)

2. 在“订阅”中选择“Visual Studio Enterprise”。

    ![Visual Studio Enterprise](media\azure-stack-solution-hybrid-pipeline\000_11.png)

3. 在 Visual Studio Enterprise 中选择“访问控制(IAM)”。

    ![访问控制 (IAM)](media\azure-stack-solution-hybrid-pipeline\000_12.png)

4. 选择 **添加** 。

    ![添加](media\azure-stack-solution-hybrid-pipeline\000_13.png)

5. 在“添加权限”中，选择需要分配给应用程序的角色。 在此示例中，请选择“所有者”角色。

    ![“所有者”角色](media\azure-stack-solution-hybrid-pipeline\000_14.png)

6. 默认情况下，可用选项中不显示 Azure Active Directory 应用程序。 若要查找应用程序，必须在“选择”字段中提供其名称，以便进行搜索。 选择应用。

    ![应用搜索结果](media\azure-stack-solution-hybrid-pipeline\000_16.png)

7. 选择“保存”完成角色分配。 该应用程序会显示在分配到该范围角色的用户列表中。

### <a name="role-based-access-control"></a>基于角色的访问控制

‎Azure 基于角色的访问控制 (RBAC) 为 Azure 提供精细的访问管理。 使用 RBAC，可以控制用户执行其作业所需的访问权限级别。 有关基于角色的访问控制的详细信息，请参阅[管理对 Azure 订阅资源的访问](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json)。

### <a name="vsts-agent-pools"></a>VSTS 代理池

可在代理池中组织代理，无需单独管理每个代理。 代理池定义该池中所有代理的共享边界。 在 VSTS 中，代理池的范围限定为 VSTS 帐户，这意味着可在团队项目之间共享一个代理池。 若要详细了解代理池，请参阅[创建代理池和队列](https://docs.microsoft.com/vsts/build-release/concepts/agents/pools-queues?view=vsts)。

### <a name="add-a-personal-access-token-pat-for-azure-stack"></a>添加 Azure Stack 的个人访问令牌 (PAT)

创建访问 VSTS 所需的个人访问令牌。

1. 登录到 VSTS 帐户，并选择帐户配置文件名称。
2. 选择“管理安全性”以访问令牌创建页。

    ![用户登录](media\azure-stack-solution-hybrid-pipeline\000_17.png)

    ![选择团队项目](media\azure-stack-solution-hybrid-pipeline\000_18.png)

    ![添加个人访问令牌](media\azure-stack-solution-hybrid-pipeline\000_18a.png)

    ![创建令牌](media\azure-stack-solution-hybrid-pipeline\000_18b.png)

3. 复制令牌。

    > [!Note]
    > 保存令牌信息。 此信息不存储，在你离开网页后不会再次显示。

    ![个人访问令牌](media\azure-stack-solution-hybrid-pipeline\000_19.png)

### <a name="install-the-vsts-build-agent-on-the-azure-stack-hosted-build-server"></a>在 Azure Stack 托管的生成服务器上安装 VSTS 生成代理

1. 连接到 Azure Stack 主机上部署的生成服务器。
2. 下载生成代理，并使用个人访问令牌 (PAT) 将其部署为服务，然后以 VM 管理员帐户运行。

    ![下载生成代理](media\azure-stack-solution-hybrid-pipeline\010_downloadagent.png)

3. 导航到解压缩生成代理的文件夹。 从提升的命令提示符运行 **config.cmd** 文件。

    ![解压缩的生成代理](media\azure-stack-solution-hybrid-pipeline\000_20.png)

    ![注册生成代理](media\azure-stack-solution-hybrid-pipeline\000_21.png)

4. 当 config.cmd 完成以后，更新的生成代理文件夹会有其他文件。 包含提取内容的文件夹应如下所示：

    ![生成代理文件夹更新](media\azure-stack-solution-hybrid-pipeline\009_token_file.png)

    可以在 VSTS 文件夹中看到该代理。

## <a name="endpoint-creation-permissions"></a>终结点创建权限

通过创建终结点，Visual Studio Online (VSTO) 生成可以将 Azure 服务应用部署到 Azure Stack。 VSTS 会连接到生成代理，而后者会连接到 Azure Stack。

![VSTO 中的 NorthwindCloud 示例应用](media\azure-stack-solution-hybrid-pipeline\012_securityendpoints.png)

1. 登录到 VSTO，然后导航到应用设置页。
2. 在“设置”中，选择“安全性”。
3. 在“VSTS 组”中，选择“终结点创建者”。

    ![NorthwindCloud 终结点创建者](media\azure-stack-solution-hybrid-pipeline\013_endpoint_creators.png)

4. 在“成员”选项卡上，选择“添加”。

    ![添加成员](media\azure-stack-solution-hybrid-pipeline\014_members_tab.png)

5. 在“添加用户和组”中输入用户名，然后从用户列表中选择该用户。
6. 选择“保存更改”。
7. 在“VSTS 组”列表中，选择“终结点管理员”。

    ![NorthwindCloud 终结点管理员](media\azure-stack-solution-hybrid-pipeline\015_save_endpoint.png)

8. 在“成员”选项卡上，选择“添加”。
9. 在“添加用户和组”中输入用户名，然后从用户列表中选择该用户。
10. 选择“保存更改”。

有了终结点信息以后，就可以使用 VSTS 到 Azure Stack 的连接了。 Azure Stack 中的生成代理会从 VSTS 获取指令，然后，此代理会传达与 Azure Stack 通信所需的终结点信息。

![生成代理](media\azure-stack-solution-hybrid-pipeline\016_save_changes.png)

## <a name="develop-your-application-build"></a>开发应用程序生成

本教程的此部分介绍以下操作：

* 将代码添加到 VSTS 项目。
* 创建独立的 Web 应用部署。
* 配置持续部署过程

> [!Note]
 > Azure Stack 环境需要联合正确的映像才能运行 Windows Server 和 SQL Server。 它还必须部署应用服务。 查看应用服务文档的“先决条件”部分，了解 Azure Stack 操作员的要求。

混合 CI/CD 可同时应用到应用程序代码和基础结构代码。 使用要部署到两个云的 [Azure 资源管理器模板](https://azure.microsoft.com/resources/templates/)（例如 VSTS 中的 Web 应用代码）。

### <a name="add-code-to-a-vsts-project"></a>将代码添加到 VSTS 项目

1. 使用在 Azure Stack 上拥有项目创建权限的帐户登录到 VSTS。 下一屏幕捕获显示如何连接到 HybridCICD 项目。

    ![连接到项目](media\azure-stack-solution-hybrid-pipeline\017_connect_to_project.png)

2. 创建并打开默认 Web 应用以**克隆存储库**。

    ![克隆存储库](media\azure-stack-solution-hybrid-pipeline\018_link_arm.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>为这两个云中的应用服务创建独立的 Web 应用部署

1. 编辑 **WebApplication.csproj** 文件：选择 **Runtimeidentifier**，然后添加 `win10-x64.`。有关详细信息，请参阅[独立部署](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd)文档。

    ![配置 Runtimeidentifier](media\azure-stack-solution-hybrid-pipeline\019_runtimeidentifer.png)

2. 使用团队资源管理器将代码签入 VSTS。

3. 确认应用程序代码已签入 Visual Studio Team Services。

### <a name="create-the-build-definition"></a>创建生成定义

1. 使用可以创建生成定义的帐户登录到 VSTS。
2. 导航到项目的“生成 Web 应用程序”页。

3. 在“参数”中，添加 **-r win10-x64** 代码。 在 .Net Core 中触发独立部署时需要此代码。

    ![添加参数生成定义](media\azure-stack-solution-hybrid-pipeline\020_publish_additions.png)

4. 运行生成。 [独立部署生成](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd)过程将发布可在 Azure 和 Azure Stack 上运行的项目。

### <a name="use-an-azure-hosted-build-agent"></a>使用 Azure 托管生成代理

在 VSTS 中使用托管生成代理是生成并部署 Web 应用的便捷做法。 Microsoft Azure，从而使连续和不间断地开发周期自动执行代理维护和升级。

### <a name="configure-the-continuous-deployment-cd-process"></a>配置持续部署 (CD) 过程

Visual Studio Team Services (VSTS) 和 Team Foundation Server (TFS) 提供高度可配置、可管理的管道，用于将内容发布到多个环境（例如开发、过渡、质量保证 (QA) 和生产）。 此过程可能包括要求在应用程序生命周期的特定阶段进行审批。

### <a name="create-release-definition"></a>创建发布定义

创建发布定义是应用程序生成过程中的最后一步。 此发布定义用于创建一个发布并部署一个生成。

1. 登录到 VSTS，然后导航到项目的“生成和发布”。
2. 在“发布”选项卡上选择“\[ + ]”，然后单击“创建发布定义”。

   ![创建发布定义](media\azure-stack-solution-hybrid-pipeline\021a_releasedef.png)

3. 在“选择模板”上选择“Azure 应用服务部署”，然后选择“应用”。

    ![应用模板](media\azure-stack-solution-hybrid-pipeline\102.png)

4. 在“添加项目”的“源(生成定义)”下拉菜单中，选择 Azure 云生成应用。

    ![添加项目](media\azure-stack-solution-hybrid-pipeline\103.png)

5. 在“管道”选项卡上选择“1 阶段，1 任务”链接，以便**查看环境任务**。

    ![管道视图任务](media\azure-stack-solution-hybrid-pipeline\104.png)

6. 在“任务”选项卡上输入“Azure”作为“环境名称”，然后从“Azure 订阅”下拉列表中选择“AzureCloud Traders-Web EP”。

    ![设置环境变量。](media\azure-stack-solution-hybrid-pipeline\105.png)

7. 输入 **Azure 应用服务名称**，即下一屏幕捕获中的“northwindtraders”。

    ![应用服务名称](media\azure-stack-solution-hybrid-pipeline\106.png)

8. 从“代理队列”下拉列表中选择“托管 VS2017”作为“代理阶段”。

    ![托管代理](media\azure-stack-solution-hybrid-pipeline\107.png)

9. 在“部署 Azure 应用服务”中，为环境选择有效的**包或文件夹**。

    ![选择包或文件夹](media\azure-stack-solution-hybrid-pipeline\108.png)

10. 确认“选择文件或文件夹”中的“Location”后，选择“确定”。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\109.png)

11. 保存所有更改后，回到“管道”。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\110.png)

12. 在“管道”选项卡上选择“添加项目”，然后从“源(生成定义)”下拉列表中选择“NorthwindCloud Traders-Vessel”。

    ![添加新项目](media\azure-stack-solution-hybrid-pipeline\111.png)

13. 在“选择模板”中添加另一环境。 选取“Azure 应用服务部署”，然后选择“应用”。

    ![选择模板](media\azure-stack-solution-hybrid-pipeline\112.png)

14. 输入“Azure Stack”作为**环境名称**。

    ![环境名称](media\azure-stack-solution-hybrid-pipeline\113.png)

15. 在“任务”选项卡上，找到并选择“Azure Stack”。

    ![Azure Stack 环境](media\azure-stack-solution-hybrid-pipeline\114.png)

16. 从“Azure 订阅”下拉列表中选择“AzureStack Traders-Vessel EP”作为 Azure Stack 终结点。

    ![替换文字](media\azure-stack-solution-hybrid-pipeline\115.png)

17. 输入 Azure Stack Web 应用名称作为**应用服务名称**。

    ![应用服务名称](media\azure-stack-solution-hybrid-pipeline\116.png)

18. 从“代理选择”下的“代理队列”下拉列表中选取“AzureStack -bDouglas Fir”。

    ![选取代理](media\azure-stack-solution-hybrid-pipeline\117.png)

19. 至于“部署 Azure 应用服务”，请为环境选择有效的**包或文件夹**。 确认“选择文件或文件夹”中的“Location”文件夹后，选择“确定”。

    ![选取包或文件夹](media\azure-stack-solution-hybrid-pipeline\118.png)

    ![审批位置](media\azure-stack-solution-hybrid-pipeline\119.png)

20. 在“变量”选项卡上，找到名为 **VSTS_ARM_REST_IGNORE_SSL_ERRORS** 的变量。 将变量值设置为 **true**，将其范围设置为 **Azure Stack**。

    ![配置变量](media\azure-stack-solution-hybrid-pipeline\120.png)

21. 在“管道”选项卡上，选择 NorthwindCloud Traders-Web 项目对应的“持续部署触发器”图标，然后将“持续部署触发器”设置为“启用”。  针对“NorthwindCloud Traders-Vessel”项目执行相同的操作。

    ![设置持续部署触发器](media\azure-stack-solution-hybrid-pipeline\121.png)

22. 至于 Azure Stack 环境，请选择“部署前条件”图标，并将触发器设置为“发布后”。

    ![设置部署前条件触发器](media\azure-stack-solution-hybrid-pipeline\122.png)

23. 保存所有更改。

> [!Note]
> 发布任务的某些设置可能已在从模板创建发布定义时自动定义为[环境变量](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables)。 这些设置不能在任务设置中修改。 但是，可以在父环境项中编辑这些设置。

## <a name="create-a-release"></a>创建发布

完成对发布定义的修改后，可以开始部署。 为此，请从发布定义创建发布。 可以自动创建发布；例如，发布定义中已设置持续部署触发器。 这意味着，修改源代码会启动新的生成，继而生成新的发布。 但在本部分，我们将手动创建新发布。

1. 在“管道”选项卡上打开“发布”下拉列表，然后选择“创建发布”。

    ![创建发布](media\azure-stack-solution-hybrid-pipeline\200.png)

2. 输入发布的说明，查看是否选择了正确的项目，然后选择“创建”。 片刻之后，将会出现一个横幅，指出已创建新的发布，发布名称以链接形式显示。 选择该链接，查看发布摘要页。

    ![创建发布的横幅](media\azure-stack-solution-hybrid-pipeline\201.png)

3. 发布摘要页显示发布详细信息。 在下面的针对“Release-2”的屏幕捕获中，“环境”部分显示 Azure 的“部署状态”为“正在进行”，Azure Stack的状态为“成功”。 当 Azure 环境的部署状态变为“成功”以后，会显示一个横幅，指示可以审批发布了。 如果部署挂起或失败，则会显示一个蓝色的 **(i)** 信息图标。 将鼠标悬停在图标上方即可看到一个弹出窗口，其中包含延迟或失败的原因。

    ![发布摘要页](media\azure-stack-solution-hybrid-pipeline\202.png)

其他视图（例如发布列表）也会显示一个图标，指示正在等待审批。 此图标的弹出窗口会显示环境名称以及与部署相关的更多详细信息。 管理员可以很容易地查看发布的总体进度以及哪些发布正在等待审批。

### <a name="monitor-and-track-deployments"></a>监视和跟踪部署

此部分介绍如何监视和跟踪所有部署。 部署两个 Azure 应用服务网站的发布是一个很好的示例。

1. 在“Release-2”摘要页中选择“日志”。 部署期间，此页显示代理的实时日志。 左窗格显示每个环境的部署过程中每个操作的状态。

    可以选择部署前或部署后审批活动的“操作”列中的人员图标，查看部署批准者（或拒绝者）及其提供的消息。

2. 部署完成后，整个日志文件会显示在右窗格中。 选择左窗格中的任何**步骤**即可查看单个步骤（例如“初始化作业”）的日志文件。 有了查看单个日志的功能，就可以更轻松地跟踪和调试整体部署的部件。 也可**保存**某个步骤的日志文件，或者**以 Zip 格式下载所有日志**。

    ![发布日志](media\azure-stack-solution-hybrid-pipeline\203.png)

3. 打开“摘要”选项卡，查看有关该发布的常规信息。 此视图详细显示了该发布所部署到的生成和环境、部署状态，以及有关该发布的其他信息。

4. 选择环境链接（**Azure** 或 **Azure Stack**），查看部署到特定环境的现有部署和待定部署的相关信息。 可以使用这些视图来快速验证同一个生成是否已部署到这两个环境。

5. 在浏览器中打开**已部署的生产应用**。 例如，对于 Azure 应用服务网站，请打开 URL `http://[your-app-name].azurewebsites.net`。

## <a name="next-steps"></a>后续步骤

* 若要了解有关 Azure 云模式的详细信息，请参阅[云设计模式](https://docs.microsoft.com/azure/architecture/patterns)。
