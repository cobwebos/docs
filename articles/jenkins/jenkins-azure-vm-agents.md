---
title: "使用 Azure VM 代理缩放 Jenkins 部署。"
description: "使用安装了 Jenkins Azure VM 代理插件的 Azure 虚拟机将额外的容量添加到 Jenkins 管道。"
services: multiple
documentationcenter: 
author: rloutlaw
manager: justhe
ms.service: multiple
ms.workload: multiple
ms.topic: article
ms.date: 8/25/2017
ms.author: mlearned
ms.custom: Jenkins
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: a3bfa3a17f0d7300f9d1ceb529410bbbb7bb2fee
ms.contentlocale: zh-cn
ms.lasthandoff: 08/29/2017

---

# <a name="scale-your-jenkins-deployments-to-meet-demand-with-azure-vm-agents"></a>使用 Azure VM 代理缩放 Jenkins 部署以满足需求

本教程介绍如何使用 Jenkins [Azure VM 代理插件](https://plugins.jenkins.io/azure-vm-agents)通过在 Azure 中运行的 Linux 虚拟机添加按需容量。

在本教程中，将：

> [!div class="checklist"]
> * 安装 Azure VM 代理插件
> * 配置插件以在 Azure 订阅中创建资源
> * 设置对每个代理可用的计算资源
> * 设置操作系统和每个代理上安装的工具
> * 创建新的 Jenkins 自由风格作业
> * 在 Azure VM 代理上运行作业

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-Integration-with-Jenkins-Using-Azure-VM-Agents/player]

## <a name="prerequisites"></a>先决条件

* Azure 订阅
* Jenkins 主服务器。 如果没有，请查看[快速入门](install-jenkins-solution-template.md)以在 Azure 中设置一个。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-azure-vm-agents-plugin"></a>安装 Azure VM 代理插件

> [!TIP]
> 如果已使用[解决方案模版](install-jenkins-solution-template.md)在 Azure 上部署了 Jenkins，那么已经安装 Azure VM 代理插件。

1. 在 Jenkins 仪表板中，选择“管理 Jenkins”，然后选择“管理插件”。
2. 选择“可用”选项卡，然后搜索“Azure VM 代理”。 选择插件项旁的复选框，然后在仪表盘底部选择“安装后不重启”。

## <a name="configure-the-azure-vm-agents-plugin"></a>配置 Azure VM 代理插件

1. 在 Jenkins 仪表板中，选择“管理 Jenkins”，再选择“配置系统”。
2. 滚动到页面底部，找到有“添加新的云”下拉列表的“云”部分，选择“Microsoft Azure VM 代理”。
3. 从“Azure 凭据”部分中的“添加”下拉列表，选择一个现有的服务主体。 如果没有列出内容，请执行以下步骤，为 Azure 帐户[创建服务主体](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager)并将其添加到 Jenkins 配置：   

    a. 选择“Azure 凭据”旁的“添加”，然后选择“Jenkins”。   
    b. 在“添加凭据”对话框中，从“种类”下拉列表中选择“Microsoft Azure 服务主体”。   
    c. 使用 Azure CLI 或 [Cloud Shell](/azure/cloud-shell/overview) 创建 Active Directory 服务主体。
    
    ```azurecli-interactive
    az ad sp create-for-rbac --name jenkins_sp --password secure_password
    ```

    ```json
    {
        "appId": "BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBB",
        "displayName": "jenkins_sp",
        "name": "http://jenkins_sp",
        "password": "secure_password",
        "tenant": "CCCCCCCC-CCCC-CCCC-CCCCCCCCCCC"
    }
    ```
    d.单击“下一步”。 将服务主体的凭据输入到“添加凭据”对话框。 如果不知道 Azure 订阅 ID，则可以从 CLI 中查询：
     
     ```azurecli-interactive
     az account list
     ```

     ```json
        {
            "cloudName": "AzureCloud",
            "id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA",
            "isDefault": true,
            "name": "Visual Studio Enterprise",
            "state": "Enabled",
            "tenantId": "CCCCCCCC-CCCC-CCCC-CCCC-CCCCCCCCCCC",
            "user": {
            "name": "raisa@fabrikam.com",
            "type": "user"
            }
     ```

    完成的服务主体应使用“订阅 ID”的 `id` 字段、“客户端 ID”的 `appId` 值、“客户端密码”的 `password` 和 `https://login.windows.net/<tenant_value>` 的 **OAuth 2.0 令牌终结点** URL。 选择“添加”添加服务主体，然后配置插件以使用新创建的凭据。

    ![配置 Azure 服务主体](./media/jenkins-azure-vm-agents/new-service-principal.png)

    

4. 在“资源组名称”部分中，选择“创建新项”，然后输入 `myJenkinsAgentGroup`。
5. 选择“验证配置”连接到 Azure 以测试配置文件设置。
6. 选择“应用”更新插件配置。

## <a name="configure-agent-resources"></a>配置代理资源

配置用于定义 Azure VM 代理的模板。 此模板定义每个代理创建时具有的计算资源。

1. 选择“添加 Azure 虚拟机模板”旁的“添加”。
2. 对于“名称”，请输入 `defaulttemplate`
3. 对于“标签”，请输入 `ubuntu`
4. 从组合框中选择所需 [Azure 区域](https://azure.microsoft.com/regions/)。
5. 从“虚拟机大小”下的下拉列表中选择 [VM 大小](/azure/virtual-machines/linux/sizes)。 通用 `Standard_DS1_v2` 大小对本教程来说就可以了。   
6. 将“保留时间” 设为 `60`。 此设置定义 Jenkins 在解除分配闲置代理前可等待的分钟数。 如果不希望自动删除闲置代理，请指定为 0。

   ![常规 VM 配置](./media/jenkins-azure-vm-agents/general-config.png)

## <a name="configure-agent-operating-system-and-tools"></a>配置代理操作系统和工具

在插件配置的“映像配置”中，选择“Ubuntu 16.04 LTS”。 选中“安装 Git (最新版本)”、“安装 Maven (V3.5.0)”和“安装 Docker” 旁的复选框，以在新创建的代理上安装这些工具。

![创建 VM OS 和工具](./media/jenkins-azure-vm-agents/jenkins-os-config.png)

选择“管理员凭据”旁的“添加”，然后选择“Jenkins”。 输入登录代理使用的用户名和密码，请确保它们满足 Azure VM 上管理者帐户的[用户名和密码策略](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm)。

选择“验证模板”验证配置，然后选择“保存”保存更改并返回到 Jenkins 仪表板。

## <a name="create-a-job-in-jenkins"></a>在 Jenkins 中创建作业

1. 在 Jenkins 仪表板中，单击“新建项目”。 
2. 输入 `demoproject1` 作为名称，然后依次选择“自由风格项目” “确定”。
3. 在“常规”选项卡中，选择“限制可运行项目的位置”，并在“标签表达式”中键入 `ubuntu`。 将看到一条消息，确认标签由上一步创建的云配置提供。 
   ![设置作业](./media/jenkins-azure-vm-agents/job-config.png)
4. 在“源代码管理”选项卡中，选择“Git”，然后将以下 URL 添加到“存储库 URL”字段：`https://github.com/spring-projects/spring-petclinic.git`
5. 在“生成”选项卡中，选择“添加生成步骤”，再选择“调用顶级 Maven 目标”。 在“目标”字段中输入 `package`。
6. 选择“保存”保存作业定义。

## <a name="build-the-new-job-on-an-azure-vm-agent"></a>在 Azure VM 代理上生成新作业

1. 返回 Jenkins 仪表板。
2. 选择上一步创建的作业，然后单击“立即生成”。 新的生成加入队列但不会开始，除非在 Azure 订阅中已代理 VM。
3. 生成完成后，转到“控制台输出”。 可看到该生成在 Azure 代理上远程执行。

![控制台输出](./media/jenkins-azure-vm-agents/console-output.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [CI/CD 到 Azure App Service](deploy-jenkins-app-service-plugin.md)

