---
title: 教程 - 使用 Team Services 通过 Jenkins 实现到 Azure VM 的 CI/CD | Microsoft Docs
description: 本教程介绍如何从 Visual Studio Team Services 或 Microsoft Team Foundation Server 中的 Release Management，通过使用 Jenkins 将 Node.js 应用设置为持续集成 (CI) 和持续部署 (CD) 到 Azure VM
author: ahomer
manager: douge
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/19/2017
ms.author: ahomer
ms.custom: mvc
ms.openlocfilehash: fc301edf13f8e6874f0b77440e2b0dc01b2a55fc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-deploy-your-app-to-linux-virtual-machines-in-azure-with-using-jenkins-and-visual-studio-team-services"></a>教程：使用 Jenkins 和 Visual Studio Team Services 将应用部署到 Azure 中的 Linux 虚拟机

持续集成 (CI) 和持续部署 (CD) 可形成一个管道，用于生成、发布和部署代码。 Visual Studio Team Services 提供一套完整的全功能 CI/CD 自动化工具，用于部署到 Azure。 Jenkins 是一款常用的基于服务器的第三方 CI/CD 工具，也提供 CI/CD 自动化。 你可以结合使用 Team Services 和 Jenkins 来自定义交付云应用或服务的方式。

在本教程中，将使用 Jenkins 生成 Node.js Web 应用。 随后，使用 Team Services 或 Team Foundation Server 将其部署到包含 Linux 虚拟机 (VM) 的[部署组](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/)。 学习如何：

> [!div class="checklist"]
> * 获取示例应用。
> * 配置 Jenkins 插件。
> * 为 Node.js 配置 Jenkins 自由风格项目。
> * 为 Team Services 集成配置 Jenkins。
> * 创建 Jenkins 服务终结点。
> * 为 Azure 虚拟机创建部署组。
> * 创建 Team Services 发布定义。
> * 执行手动和 CI 触发的部署。

## <a name="before-you-begin"></a>开始之前

* 需要具有对 Jenkins 服务器的访问权限。 如果尚未创建 Jenkins 服务器，请参阅[在 Azure 虚拟机上创建 Jenkins 主节点](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template)。 

* 登录到 Team Services 帐户 (https://{youraccount}.visualstudio.com)。 
  可以获取[免费 Team Services 帐户](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308)。

  > [!NOTE]
  > 有关详细信息，请参阅[连接到 Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services)。

*  部署目标需用到 Linux 虚拟机。  有关详细信息，请参阅[使用 Azure CLI 创建和管理 Linux VM](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)。

*  为虚拟机开启入站端口 80。 有关详细信息，请参阅[使用 Azure 门户创建网络安全组](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal)。

## <a name="get-the-sample-app"></a>获取示例应用

你需要一个待部署的应用，并存储在 Git 存储库中。
对于本教程，我们建议你使用[从 GitHub 获得的此示例应用](https://github.com/azooinmyluggage/fabrikam-node)。 本教程包含用于安装 Node.js 和应用程序的示例脚本。 要使用自己的存储库，请配置一个类似的示例。

创建此应用的一个分支并记下位置 (URL) 以便在本教程的后续步骤中使用。 有关详细信息，请参阅[分支存储库](https://help.github.com/articles/fork-a-repo/)    

> [!NOTE]
> 应用是通过 [Yeoman](http://yeoman.io/learning/index.html) 生成的。 它使用 Express、bower 和 grunt。 它还有一些 npm 包作为依赖项。
> 示例还包含一个用来设置 Nginx 并部署应用的脚本。 它在虚拟机上执行。 具体而言，脚本将执行以下操作：
> 1. 安装 Node、Nginx 和 PM2。
> 2. 配置 Nginx 和 PM2。
> 3. 启动 Node 应用。

## <a name="configure-jenkins-plug-ins"></a>配置 Jenkins 插件

首先，必须配置两个 Jenkins 插件：NodeJS 和 VS Team Services 持续部署。

1. 打开你的 Jenkins 帐户，并选择“管理 Jenkins”。
2. 在“管理 Jenkins”页面上，选择“管理插件”。
3. 筛选列表以查找“NodeJS”插件，然后选择“安装而不重启”选项。
    ![将 NodeJS 插件添加到 Jenkins](media/tutorial-build-deploy-jenkins/jenkins-nodejs-plugin.png)
4. 筛选列表以查找“VS Team Services 持续部署”插件，然后选择“安装而不重启”选项。
5. 返回 Jenkins 仪表板，然后选择“管理 Jenkins”。
6. 选择“全局工具配置”。 查找“NodeJS”，然后选择“NodeJS 安装”。
7. 选择“自动安装”选项，然后输入“名称”值。
8. 选择“保存”。

## <a name="configure-a-jenkins-freestyle-project-for-nodejs"></a>为 Node.js 配置 Jenkins 自由风格项目

1. 选择“新建项”。 输入项目值。
2. 选择“自由风格项目”。 选择“确定”。
3. 在“源代码管理”选项卡上，选择“Git”并输入包含应用代码的存储库和分支的详细信息。    
    ![将存储库添加到生成](media/tutorial-build-deploy-jenkins/jenkins-git.png)
4. 在“生成触发器”选项卡上，选择“轮询 SCM”并输入计划 `H/03 * * * *`，以便每三分钟轮询一次 Git 存储库中的更改。 
5. 在“生成环境”选项卡上，选择“提供节点 &amp; npm bin/ folder PATH”，然后选择“NodeJS 安装”值。 使“npmrc 文件”设置为“用户系统默认值”。
6. 在“生成”选项卡上，选择“执行 shell”，然后输入命令 `npm install`，以确保更新所有依赖项。


## <a name="configure-jenkins-for-team-services-integration"></a>为 Team Services 集成配置 Jenkins

> [!NOTE]
> 确保你用于以下步骤的个人访问令牌 (PAT) 包含 Team Services 中的“发布”（读取、写入、执行和管理）权限。
 
1.  在 Team Services 帐户中创建 PAT（如果还没有）。 Jenkins 需要使用此信息来访问 Team Services 帐户。 确保存储令牌信息以用于本部分后面的步骤。
  
    若要了解如何生成令牌，请阅读[如何为 VSTS 和 TFS 创建个人访问令牌？](https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate)。
2. 在“生成后操作”选项卡中，选择“添加生成后操作”。 选择“存档项目”。
3. 对于“要存档的文件”，输入 `**/*` 以包括所有文件。
4. 若要创建其他操作，请选择“添加生成后操作”。
5. 选择“在 TFS/Team Services 中触发发布” 。 输入 Team Services 帐户的 URI，例如https://{your-account-name}.visualstudio.com。
6. 输入“团队项目”名称。
7. 为发布定义选择名称。 （你可以稍后在 Team Services 中创建此发布定义。）
8. 选择用于连接到 Team Services 或 Team Foundation Server 环境的凭据：
   - 如果你正在使用 Team Services，请将“用户名”留空。 
   - 如果你正在使用 Team Foundation Server 的本地版本，请输入用户名和密码。    
   ![配置 Jenkins 生成后操作](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)
5. 保存 Jenkins 项目。


## <a name="create-a-jenkins-service-endpoint"></a>创建 Jenkins 服务终结点

服务终结点允许 Team Services 连接到 Jenkins。

1. 在 Team Services 中打开“服务”页面，打开“新服务终结点”列表，然后选择“Jenkins”。
   ![添加 Jenkins 终结点](media/tutorial-build-deploy-jenkins/add-jenkins-endpoint.png)
2. 输入连接的名称。
3. 输入 Jenkins 服务器的 URL，然后选择“接受不受信任的 SSL 证书”选项。 示例 URL 为 http://{YourJenkinsURL}.westcentralus.cloudapp.azure.com。
4. 输入 Jenkins 帐户的用户名和密码。
5. 选择“验证连接”确认信息是否正确。
6. 选择“确定”，创建服务终结点。

## <a name="create-a-deployment-group-for-azure-virtual-machines"></a>为 Azure 虚拟机创建部署组。

你需要[部署组](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/)以注册 Team Services 代理，以便可以将发布定义部署到虚拟机。 通过部署组，可以方便地定义目标计算机的逻辑组，以进行部署，并在每台计算机上安装所需的代理。

   > [!NOTE]
   > 在以下过程中，确保安装先决条件，且不使用 sudo 权限运行脚本。

1. 打开“生成 &amp; 发布”中心的“发布”选项卡，打开“部署组”，然后选择“+ 新建”。
2. 为部署组输入名称和可选说明。 然后选择“创建”。
3. 为部署目标虚拟机选择操作系统。 例如，选择“Ubuntu 16.04+”。
4. 选择“使用脚本中的个人访问令牌进行身份验证”。
5. 选择“系统必备组件”链接。 为操作系统安装必备组件。
6. 选择“将脚本复制到剪贴板”以复制脚本。
7. 登录到部署目标虚拟机并运行脚本。 不要使用 sudo 权限运行脚本。
8. 完成安装后，系统将提示你部署组标记。 接受默认值。
9. 在 Team Services 中，在“部署组”下的“目标”中检查新注册的虚拟机。

## <a name="create-a-team-services-release-definition"></a>创建 Team Services 发布定义

发布定义将指定 Team Services 用于部署应用的过程。 在此示例中，你将执行一个 shell 脚本。

若要在 Team Services 中创建发布定义，请执行以下操作：

1. 打开“生成 &amp; 发布”中心的“发布”选项卡，然后选择“创建发布定义”。 
2. 通过选择以“空进程”开始，选择“空”模板。
3. 在“项目”部分，选择“+ 添加项目”，然后在“源类型”中选择“Jenkins”。 选择你的 Jenkins 服务终结点连接。 然后选择 Jenkins 源作业，并选择“添加”。
4. 选择“环境 1”旁边的省略号。 选择“添加部署组阶段”。
5. 选择部署组。
5. 选择“+”，向“部署组阶段”中添加任务。
6. 选择“Shell 脚本”任务，并选择“添加”。 “Shell 脚本”任务为要在每个服务器上运行的脚本提供配置，以安装 Node.js 并启动应用。
8. 对于“脚本路径”，请输入“$(System.DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh”。
9. 选择“高级”，然后启用“指定工作目录”。
10. 对于“工作目录”，请输入“$(System.DefaultWorkingDirectory)/Fabrikam-Node”。
11. 将发布定义的名称编辑为你在 Jenkins 内部版本的“生成后操作”选项卡上指定的名称。 必须提供此名称，Jenkins 才能在源项目更新时触发新的发布。
12. 选择“保存”，然后选择“确定”以保存发布定义。

## <a name="execute-manual-and-ci-triggered-deployments"></a>执行手动和 CI 触发的部署

1. 选择“+ 发布”，然后选择“创建发布”。
2. 选择在突出显示的下拉列表中完成的内部版本，然后选择“队列”。
3. 在弹出消息中选择发布链接。 例如：“发布 **Release-1** 已创建。”
4. 打开“日志”选项卡以查看发布控制台输出。
5. 在浏览器中，打开已添加到部署组的其中一个服务器的 URL。 例如，输入 http://{your-server-ip-address}。
6. 转到源 Git 存储库，使用某些已更改的文本来修改文件 app/views/index.jade 中 h1 标题的内容。
7. 提交更改。
8. 数分钟后，你将看到 Team Services 或 Team Foundation Server 的“发布”页上创建了一个新发布。 打开此发布可以看到部署正在进行。 祝贺你！

## <a name="next-steps"></a>后续步骤

在本教程中，你通过使用 Jenkins 进行生成并使用 Team Services 进行发布，自动将一个应用部署到 Azure。 你已了解如何：

> [!div class="checklist"]
> * 在 Jenkins 中生成应用。
> * 为 Team Services 集成配置 Jenkins。
> * 为 Azure 虚拟机创建部署组。
> * 创建配置 VM 并部署应用的发布定义。

若要了解如何部署 LAMP（Linux、Apache、MySQL 和 PHP）堆栈，请继续学习下一个教程。

> [!div class="nextstepaction"]
> [部署 LAMP 堆栈](tutorial-lamp-stack.md)
