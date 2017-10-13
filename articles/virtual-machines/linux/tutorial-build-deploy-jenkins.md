---
title: "使用 Team Services 通过 Jenkins 实现到 Azure VM 的 CI/CD | Microsoft Docs"
description: "通过 Visual Studio Team Services (VSTS) 或 Microsoft Team Foundation Server (TFS) 中的 Release Management 使用 Jenkins 设置到 Azure VM 的 Node.js 应用持续集成 (CI) 和持续部署 (CD)。"
author: ahomer
manager: douge
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/15/2017
ms.author: ahomer
ms.custom: mvc
ms.openlocfilehash: feaced0d0784b5724fb1e30be5e66cb7c808d77f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-your-app-to-linux-vms-using-jenkins-and-team-services"></a>使用 Jenkins 和 Team Services 将应用部署到 Linux VM

持续集成 (CI) 和持续部署 (CD) 是一个管道，可以通过它生成、发布和部署代码。 Team Services 针对到 Azure 的部署提供了一组完整的功能完备的 CI/CD 自动化工具。 Jenkins 是一个流行的基于 CI/CD 服务器的第三方工具，也提供 CI/CD 自动化功能。 可以组合使用以上两者来自定义如何提供云应用或服务。

本教程使用 Jenkins 生成一个 **Node.js web 应用**，并使用 Visual Studio Team Services 将其部署到包含 Linux 虚拟机的[部署组](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/)。

你将执行以下任务：

> [!div class="checklist"]
> * 采用 Jenkins 生成应用
> * 为 Team Services 集成配置 Jenkins
> * 为 Azure 虚拟机创建部署组
> * 创建对 VM 进行配置并部署应用的发布定义

## <a name="before-you-begin"></a>开始之前

* 需要具有对 Jenkins 帐户的访问权限。 如果尚未创建 Jenkins 服务器，请参阅 [Jenkins 文档](https://jenkins.io/doc/)。 

* 登录到 Team Services 帐户 (`https://{youraccount}.visualstudio.com`)。 
  可以获取[免费 Team Services 帐户](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308)。

  > [!NOTE]
  > 有关详细信息，请参阅[连接到 Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services)。

* 在 Team Services 帐户中创建个人访问令牌 (PAT)（如果尚没有该令牌）。 Jenkins 需要使用此信息来访问 Team Services 帐户。
  请参阅[如何创建用于 Team Services 和 TFS 的个人访问令牌](https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate)来了解如何生成该令牌。

## <a name="get-the-sample-app"></a>获取示例应用

你需要部署 Git 存储库中存储的一个应用。
对于本教程，建议使用 [GitHub 中提供的此示例应用](https://github.com/azooinmyluggage/fabrikam-node)。

1. 创建此应用的一个分支并记下位置 (URL) 以便在本教程的后续步骤中使用。

1. 使此分支成为**公共**分支，以便简化稍后的 GitHub 连接过程。

> [!NOTE]
> 有关详细信息，请参阅 [Fork a repo](https://help.github.com/articles/fork-a-repo/)（创建存储库分支）和 [Making a private repository public](https://help.github.com/articles/making-a-private-repository-public/)（使专用存储库成为公共存储库）。

> [!NOTE]
> 此应用是使用 [Yeoman](http://yeoman.io/learning/index.html) 构建的；它使用了 **Express**、**bower** 和 **grunt**；它以某些 **npm** 程序包作为依赖项。
> 示例应用包含一组 [Azure 资源管理器模板](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment)，它们用来为 Azure 上的部署动态创建虚拟机。 [Team Services 发布定义](https://www.visualstudio.com/docs/build/actions/work-with-release-definitions)中的任务将使用这些模板。
> 主模板创建一个网络安全组、一台虚拟机和一个虚拟网络。 它分配一个公共 IP 地址并打开入站端口 80。 它还会添加一个标记，部署组将使用该标记来选择要接收部署的计算机。
>
> 示例还包含一个用来设置 Nginx 并部署应用的脚本。 它在每台虚拟机上执行。 具体而言，该脚本将安装 Node、Nginx 和 PM2，配置 Nginx 和 PM2，然后启动 Node 应用。

## <a name="configure-jenkins-plugins"></a>配置 Jenkins 插件

首先，必须为 **NodeJS** 和**与 Team Services 的集成**配置两个 Jenkins 插件。

1. 打开 Jenkins 帐户并选择“管理 Jenkins”。

1. 在“管理 Jenkins”页中，选择“管理插件”。

1. 对列表进行筛选以找到“NodeJS”插件，安装该插件且不重新启动。

   ![将 NodeJS 插件添加到 Jenkins](media/tutorial-build-deploy-jenkins/jenkins-nodejs-plugin.png)

1. 对列表进行筛选以找到“Team Foundation Server”插件并安装它。 （此插件同时适用于 Team Services 和 Team Foundation Server。）不需要重新启动 Jenkins。

## <a name="configure-jenkins-build-for-nodejs"></a>为 Node.js 配置 Jenkins 生成

在 Jenkins 中，创建一个新的生成项目并如下所述对其进行配置：

1. 在“常规”选项卡中，为生成项目输入一个名称。

1. 在“源代码管理”选项卡中，选择“Git”并输入包含你的应用代码的存储库和分支的详细信息。

   ![将存储库添加到生成](media/tutorial-build-deploy-jenkins/jenkins-git.png)

   > [!NOTE]
   > 如果存储库不是公共的，请选择“添加”并提供用于连接到它的凭据。

1. 在“生成触发器”选项卡中，选择“轮询 SCM”并输入计划 `H/03 * * * *` 以便每三分钟轮询一次 Git 存储库来查询更改。 

1. 在“生成环境”选项卡中，选择“提供节点和 npm bin/ 文件路径”并输入 `NodeJS` 作为 Node JS 安装值。 将“npmrc 文件”的设置保留为“使用系统默认值”。

1. 在“生成”选项卡中，输入命令 `npm install` 以确保更新所有依赖项。

## <a name="configure-jenkins-for-team-services-integration"></a>为 Team Services 集成配置 Jenkins

1. 在“生成后操作”选项卡中，对于“要存档的文件”，输入 `**/*` 以包括所有文件。

1. 对于“触发 TFS/Team Services 中发布”，输入帐户的完整 URL（例如 `https://your-account-name.visualstudio.com`）、项目名称、（稍后创建的）发布定义的名称，以及用于连接到帐户的凭据。
   需要使用之前创建的用户名和 PAT。 

   ![配置 Jenkins 生成后操作](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)

1. 保存生成项目。

## <a name="create-a-jenkins-service-endpoint"></a>创建 Jenkins 服务终结点

服务终结点允许 Team Services 连接到 Jenkins。

1. 在 Team Services 中打开“服务”页面，打开“新建服务终结点”列表，然后选择“Jenkins”。

   ![添加 Jenkins 终结点](media/tutorial-build-deploy-jenkins/add-jenkins-endpoint.png)

1. 输入用于引用此连接的名称。

1. 输入 Jenkins 服务器的 URL，并选中“接受不受信任的 SSL 证书”选项。

1. 输入 Jenkins 帐户的用户名和密码。

1. 选择“验证连接”来检查信息是否正确。

1. 选择“确定”以创建服务终结点。

## <a name="create-a-deployment-group"></a>创建部署组

需要一个[部署组](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/)来包含虚拟机。

1. 打开“生成和发布”中心的“发布”选项卡，然后打开“部署组”选项卡并选择“+ 新建”。

1. 为部署组输入名称和可选说明。
   选择“创建”。

Azure 资源组部署任务将在运行时使用 Azure 资源管理器模板创建并注册 VM。
你不需要自己创建并注册虚拟机。

## <a name="create-a-release-definition"></a>创建发布定义

发布定义指定 Team Services 在部署应用时执行的流程。
若要在 Team Services 中创建发布定义，请执行以下操作：

1. 打开“生成和发布”中心的“发布”选项卡，打开发布定义列表中的 **+** 下拉列表，然后选择“创建发布定义”。**&amp;** 

1. 选择“空”模板并选择“下一步”。

1. 在“项目”部分中，单击“链接一个项目”并选择“Jenkins”。 选择你的 Jenkins 服务终结点连接。 然后，选择 Jenkins 源作业并选择“创建”。 

1. 在新的发布定义中，选择“+ 添加任务”并向默认环境中添加一个**Azure 资源组部署**任务。

1. 选择“+ 添加任务”链接旁边的下拉箭头并向定义中添加一个部署组阶段。

   ![添加部署组阶段](media/tutorial-build-deploy-jenkins/deployment-group-phase-in-release-definition.png) 

1. 在任务目录中，打开“实用工具”部分并添加“Shell 脚本”任务的一个实例。

1. Azure 资源组部署任务中使用的参数模板设置用来连接到 VM 的管理员密码。
   你通过变量 **$(adminpassword)** 提供此密码：
   
   - 打开“变量”选项卡，在“变量”部分中，输入名称 `adminpassword`。

   - 输入管理员密码。

   - 选择值文本框旁边的“挂锁”图标来保护密码。 

## <a name="configure-the-azure-resource-group-deployment-task"></a>配置 Azure 资源组部署任务

**Azure 资源组部署任务**用来创建部署组。 请如下所述对其进行配置：

* **Azure 订阅：**从“可用 Azure 服务连接”下的列表中选择一个连接。 
  如果没有显示任何连接，则依次选择“管理”、“新建服务终结点”和“Azure 资源管理器”，并根据提示进行操作。
  返回发布定义，刷新“AzureRM 订阅”列表并选择已创建的连接。

* **资源组**：输入之前创建的资源组的名称。

* **位置**：为部署选择区域。

  ![创建新的资源组](media/tutorial-build-deploy-jenkins/provision-web-server.png)

* **模板位置**：`URL of the file`

* **模板链接**：`{your-git-repo}/ARM-Templates/UbuntuWeb1.json`

* **模板参数链接**：`{your-git-repo}/ARM-Templates/UbuntuWeb1.parameters.json`

* **替代模板参数**：替代值的列表，例如：`-location {location} -virtualMachineName {machine] -virtualMachineSize Standard_DS1_v2 -adminUsername {username} -virtualNetworkName fabrikam-node-rg-vnet -networkInterfaceName fabrikam-node-websvr1 -networkSecurityGroupName fabrikam-node-websvr1-nsg -adminPassword $(adminpassword) -diagnosticsStorageAccountName fabrikamnodewebsvr1 -diagnosticsStorageAccountId Microsoft.Storage/storageAccounts/fabrikamnodewebsvr1 -diagnosticsStorageAccountType Standard_LRS -addressPrefix 172.16.8.0/24 -subnetName default -subnetPrefix 172.16.8.0/24 -publicIpAddressName fabrikam-node-websvr1-ip -publicIpAddressType Dynamic`。<br />请在 {占位符} 处插入自己的具体值。 

* **启用先决条件**：`Configure with Deployment Group agent`

* **TFS/VSTS 终结点**：选择“添加”，然后在“添加新的 Team Foundation Server/Team Services 连接”对话框中，选择“基于令牌的身份验证”。 输入连接名称和你的团队项目的 URL。 然后，生成并输入[个人访问令牌 (PAT)]( https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate) 来对你的项目团队的连接进行身份验证。

  ![创建个人访问令牌](media/tutorial-build-deploy-jenkins/create-a-pat.png)

* **团队项目**：选择你当前的项目。

* **部署组**：输入用于**资源组**参数的同一部署组名称。

Azure 资源组部署任务的默认设置是创建或更新资源，并且以增量方式执行该操作。 该任务在它首次运行时创建 VM，之后只是对它们进行更新。

## <a name="configure-the-shell-script-task"></a>配置 Shell 脚本任务

**Shell 脚本**任务用来为在每台服务器上安装 Node.js 并启动该应用的脚本提供配置。 请如下所述对其进行配置：

* **脚本路径**：`$(System.DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh`

* **指定工作目录**：`Checked`

* **工作目录**：`$(System.DefaultWorkingDirectory)/Fabrikam-Node`
   
## <a name="rename-and-save-the-release-definition"></a>重命名并保存发布定义

1. 将发布定义的名称编辑为你在 Jenkins 中在生成的“生成后操作”选项卡中指定的名称。 必须提供此名称，Jenkins 才能在源项目更新时触发新的发布。

1. （可选）通过单击环境名称对其进行更改。 

1. 选择“保存”，然后选择“确定”。

## <a name="start-a-manual-deployment"></a>启动手动部署

1. 选择“+ 发布”并选择“创建发布”。

1. 在突出显示的下拉列表中选择你已完成的生成，然后选择“创建”。

1. 选择弹出消息中的发布链接。 例如：“发布 **Release-1** 已创建。”

1. 打开“日志”选项卡以查看发布控制台输出。

1. 在浏览器中，打开已添加到部署组的服务器之一的 URL。 例如，输入 `http://{your-server-ip-address}`

## <a name="start-a-cicd-deployment"></a>启动 CI/CD 部署

1. 在发布定义中，在 Azure 资源组部署任务的设置的“控制选项”部分中取消选中“已启用”复选框。
   如果将来要部署到现有部署组，不需要重新执行此任务。

1. 转到源 Git 存储库并修改文件 [app/views/index.jade](https://github.com/azooinmyluggage/fabrikam-node/blob/master/app/views/index.jade) 中 **h1** 标题的内容。

1. 提交更改。

1. 几分钟后，可以在 Team Services 或 TFS 的“发布”页面中看到新创建的发布。 打开此发布可以看到部署正在进行。 祝贺你！

## <a name="next-steps"></a>后续步骤

在本教程中，你已使用 Jenkins 生成和用于发布的 Team Services 实现了到 Azure 的应用部署自动化。 你已了解如何：

> [!div class="checklist"]
> * 采用 Jenkins 生成应用
> * 为 Team Services 集成配置 Jenkins
> * 为 Azure 虚拟机创建部署组
> * 创建对 VM 进行配置并部署应用的发布定义

转到下一教程，详细了解如何部署 LAMP（Linux、Apache、MySQL 和 PHP）堆栈。

> [!div class="nextstepaction"]
> [部署 LAMP 堆栈](tutorial-lamp-stack.md)