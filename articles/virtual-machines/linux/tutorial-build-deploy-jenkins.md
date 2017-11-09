---
title: "使用 VSTS 通过 Jenkins 实现到 Azure VM 的 CI/CD | Microsoft Docs"
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
ms.date: 10/19/2017
ms.author: ahomer
ms.custom: mvc
ms.openlocfilehash: d5c15d6ab36a8454d1c69bac498be89b990c7e33
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2017
---
# <a name="deploy-your-app-to-linux-vms-using-jenkins-and-vsts"></a>使用 Jenkins 和 VSTS 将应用部署到 Linux VM

持续集成 (CI) 和持续部署 (CD) 是一个管道，可以通过它生成、发布和部署代码。 Visual Studio Team Services (VSTS) 针对到 Azure 的部署提供了一组完整的功能完备的 CI/CD 自动化工具。 Jenkins 是一个流行的基于 CI/CD 服务器的第三方工具，也提供 CI/CD 自动化功能。 可以组合使用以上两者来自定义如何提供云应用或服务。

本教程使用 Jenkins 生成一个 Node.js Web 应用，并使用 VSTS 或 Team Foundation Server (TFS) 将其部署到包含 Linux 虚拟机的[部署组](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/)。

将能够：

> [!div class="checklist"]
> * 获取示例应用
> * 配置 Jenkins 插件
> * 为 Node.js 配置 Jenkins 自由风格项目
> * 为 VSTS 集成配置 Jenkins
> * 创建 Jenkins 服务终结点
> * 为 Azure 虚拟机创建部署组
> * 创建 VSTS 发布定义
> * 执行手动部署和由 CI 触发的部署

## <a name="before-you-begin"></a>开始之前

* 需要具有对 Jenkins 服务器的访问权限。 如果尚未创建 Jenkins 服务器，请参阅[在 Azure 虚拟机上创建 Jenkins 主机](https://docs.microsoft.com/en-us/azure/jenkins/install-jenkins-solution-template)。 

* 登录到 VSTS 帐户 (`https://{youraccount}.visualstudio.com`)。 
  可以获取[免费 VSTS 帐户](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308)。

  > [!NOTE]
  > 有关详细信息，请参阅[连接到 VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services)。

*  部署目标需用到 Linux 虚拟机。  有关详细信息，请参阅[使用 Azure CLI 创建和管理 Linux VM](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/tutorial-manage-vm)。

*  为虚拟机开启入站端口 80。  有关详细信息，请参阅[使用 Azure 门户创建网络安全组](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-create-nsg-arm-pportal)。

## <a name="get-the-sample-app"></a>获取示例应用

你需要部署 Git 存储库中存储的一个应用。
对于本教程，建议使用 [GitHub 中提供的此示例应用](https://github.com/azooinmyluggage/fabrikam-node)。  本教程包含用于安装 Node.js 和应用程序的示例脚本。  要使用自己的存储库，请配置一个类似的示例。

1. 创建此应用的一个分支并记下位置 (URL) 以便在本教程的后续步骤中使用。  有关详细信息，请参阅 [Fork a repo](https://help.github.com/articles/fork-a-repo/)（为存储库创建分支）    

> [!NOTE]
> 此应用是使用 [Yeoman](http://yeoman.io/learning/index.html) 构建的；它使用了 **Express**、**bower** 和 **grunt**；它以某些 **npm** 程序包作为依赖项。
> 示例还包含一个用来设置 Nginx 并部署应用的脚本。 它在虚拟机上执行。 具体而言，该脚本将安装 Node、Nginx 和 PM2，配置 Nginx 和 PM2，然后启动 Node 应用。

## <a name="configure-jenkins-plugins"></a>配置 Jenkins 插件

首先，必须为 NodeJS 和 VS Team Services 持续部署配置两个 Jenkins 插件。

1. 打开 Jenkins 帐户并选择“管理 Jenkins”。
2. 在“管理 Jenkins”页中，选择“管理插件”。
3. 对列表进行筛选以找到“NodeJS”插件，然后选择“安装且不重启”选项。
    ![将 NodeJS 插件添加到 Jenkins](media/tutorial-build-deploy-jenkins/jenkins-nodejs-plugin.png)
4. 对列表进行筛选以找到“VS Team Services 持续部署”插件，然后选择“安装且不重启”选项。
5. 导航返回 Jenkins 仪表板并选择“管理 Jenkins”。
6. 选择“全局工具配置”。  查找“NodeJS”并单击“NodeJS 安装”。
7. 启用“自动安装”选项，然后输入“名称”值。
8. 单击“保存” 。

## <a name="configure-a-jenkins-freestyle-project-for-nodejs"></a>为 Node.js 配置 Jenkins 自由风格项目

1. 单击“新建项”。  输入“项目名称”。
2. 选择“自由风格项目”。  单击 **“确定”**。
3. 在“源代码管理”选项卡中，选择“Git”并输入包含你的应用代码的存储库和分支的详细信息。    
    ![将存储库添加到生成](media/tutorial-build-deploy-jenkins/jenkins-git.png)
4. 在“生成触发器”选项卡中，选择“轮询 SCM”并输入计划 `H/03 * * * *` 以便每三分钟轮询一次 Git 存储库来查询更改。 
5. 在“生成环境”选项卡中，选择“提供节点和 npm bin/ 文件夹路径”并选择“Node JS 安装”值。 将“npmrc 文件”的设置保留为“使用系统默认值”。
6. 在“生成”选项卡中，选择“执行 shell”，然后输入命令 `npm install` 以确保更新所有依赖项。


## <a name="configure-jenkins-for-vsts-integration"></a>为 VSTS 集成配置 Jenkins

  > [!NOTE]
  请确保在下列步骤中使用的个人访问令牌 (PAT) 包含 VSTS 中的发布（读取、写入、执行和管理）权限。
 
1.  在 VSTS 帐户中创建 PAT（如果还没有 PAT）。 Jenkins 需要使用此信息来访问 VSTS 帐户。  在本部分中，请确保已为接下来的步骤存储了令牌信息。
  请参阅[如何创建用于 VSTS 和 TFS 的个人访问令牌](https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate)，了解如何生成该令牌。
2. 在“生成后操作”选项卡上，单击“添加生成后操作”。 选择“存档项目”。
3. 对于“要存档的文件”，输入 `**/*` 以包括所有文件。
4. 要创建另一操作，请单击“添加生成后操作”。
5. 选择“TFS/Team Services 中的触发器版本”，输入 VSTS 帐户的 URI，如：`https://{your-account-name}.visualstudio.com`。
6. 输入“团队项目”名称。
7. 为“发布定义”选择一个名称（可稍后在 VSTS 中创建此发布定义）。
8. 选择凭据以连接到 VSTS 或 TFS 环境。  如果使用 VSTS，请将“用户名”保留空白。
   如果使用 TFS 的本地版本，请输入“用户名和密码”。    
    ![配置 Jenkins 生成后操作](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)
5. 保存 Jenkins 项目。

## <a name="create-a-jenkins-service-endpoint"></a>创建 Jenkins 服务终结点

服务终结点允许 VSTS 连接到 Jenkins。

1. 在 VSTS 中打开“服务”页面，打开“新建服务终结点”列表，然后选择“Jenkins”。
    ![添加 Jenkins 终结点](media/tutorial-build-deploy-jenkins/add-jenkins-endpoint.png)
2. 输入连接的名称。
3. 输入 Jenkins 服务器的 URL，并选中“接受不受信任的 SSL 证书”选项。  示例 URL：`http://{YourJenkinsURL}.westcentralus.cloudapp.azure.com`
4. 输入 Jenkins 帐户的用户名和密码。
5. 选择“验证连接”来检查信息是否正确。
6. 选择“确定”以创建服务终结点。

## <a name="create-a-deployment-group-for-azure-virtual-machines"></a>为 Azure 虚拟机创建部署组

需要使用[部署组](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/)注册 VSTS 代理，才能将发布定义部署到虚拟机。  对于部署，可利用部署组轻松定义目标计算机的逻辑组，还可在各计算机上安装所需的代理。

   > [!NOTE]
   > 在以下步骤中，请确保安装了必备组件，并避免使用 sudo 特权执行脚本。

1. 打开“生成和发布”中心的“发布”选项卡，然后打开“部署组”并选择“+ 新建”。
2. 为部署组输入名称和可选说明。
   选择“创建”。
3. 为部署目标虚拟机选择操作系统。  例如，选择“Ubuntu 16.04+”。
4. 勾选“使用脚本中的个人访问令牌进行身份验证”。
5. 选择“系统必备组件”链接。  为操作系统安装必备组件。
6. 选择“将脚本复制到剪贴板”以复制脚本。
7. 登录到部署目标虚拟机，并执行脚本。  请勿使用 sudo 特权运行脚本。
8. 完成安装后，系统将提示你部署组标记。  接受默认值。
9. 在 VSTS 中，在“部署组”的“目标”下，检查新注册的虚拟机。

## <a name="create-a-vsts-release-definition"></a>创建 VSTS 发布定义

发布定义指定 VSTS 在部署应用时执行的流程。  在此示例中，你将执行一个 shell 脚本。

要在 VSTS 中创建发布定义，请执行以下操作：

1. 打开“生成和发布”中心的“发布”选项卡，并选择“创建发布定义”。 
2. 首先通过选择“空进程”选择“空”模板。
3. 在“项目”部分，单击“+ 添加项目”，并为“源类型”选择“Jenkins”。 选择你的 Jenkins 服务终结点连接。 然后，选择 Jenkins 源作业并选择“添加”。
4.  单击“环境 1”旁边的省略号。  单击“添加部署组阶段”。
5.  选择你的“部署组”。
5. 单击 + 将任务添加到“部署组阶段”。
6. 选择“shell 脚本”任务并单击“添加”。    
    **Shell 脚本**任务用来为在每台服务器上安装 Node.js 并启动该应用的脚本提供配置。 请如下所述对其进行配置：
8. **脚本路径**：     
    `$(System.DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh`
9.  单击“高级”，然后启用“指定工作目录”。
10.  **工作目录**：`$(System.DefaultWorkingDirectory)/Fabrikam-Node`
11. 将发布定义的名称编辑为你在 Jenkins 中在生成的“生成后操作”选项卡中指定的名称。 必须提供此名称，Jenkins 才能在源项目更新时触发新的发布。
12. 单击“保存”，然后单击“确定”以保存发布定义。

## <a name="execute-manual-and-ci-triggered-deployments"></a>执行手动部署和由 CI 触发的部署

1. 选择“+ 发布”并选择“创建发布”。
2. 在突出显示的下拉列表中选择你已完成的生成，然后选择“队列”。
3. 选择弹出消息中的发布链接。 例如：“发布 **Release-1** 已创建。”
4. 打开“日志”选项卡以查看发布控制台输出。
5. 在浏览器中，打开已添加到部署组的服务器之一的 URL。 例如，输入 `http://{your-server-ip-address}`
6. 转到源 Git 存储库，使用某些已更改的文本来修改文件 app/views/index.jade 中 h1 标题的内容。
7. 提交更改。
8. 几分钟后，可以在 VSTS 或 TFS 的“发布”页面中看到新创建的发布。 打开此发布可以看到部署正在进行。 祝贺你！

## <a name="next-steps"></a>后续步骤

在本教程中，你已使用 Jenkins 生成和用于发布的 VSTS 实现了到 Azure 的应用部署自动化。 你已了解如何：

> [!div class="checklist"]
> * 采用 Jenkins 生成应用
> * 为 VSTS 集成配置 Jenkins
> * 为 Azure 虚拟机创建部署组
> * 创建对 VM 进行配置并部署应用的发布定义

转到下一教程，详细了解如何部署 LAMP（Linux、Apache、MySQL 和 PHP）堆栈。

> [!div class="nextstepaction"]
> [部署 LAMP 堆栈](tutorial-lamp-stack.md)