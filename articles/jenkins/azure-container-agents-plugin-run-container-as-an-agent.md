---
title: "使用 Jenkins 和 Azure 容器实例在 Azure 中生成项目"
description: "了解如何使用适用于 Jenkins 的 Azure 容器代理插件通过 Azure 容器实例在 Azure 中生成项目"
services: multiple
documentationcenter: 
author: tomarcher
manager: rloutlaw
editor: 
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 02/28/2018
ms.author: tarcher
ms.custom: jenkins
ms.openlocfilehash: 557b21340a0ba4e5381d7505b14a172aa3478b84
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2018
---
# <a name="build-a-project-in-azure-using-jenkins-and-azure-container-instances"></a>使用 Jenkins 和 Azure 容器实例在 Azure 中生成项目

无需预配虚拟机或采用更高级别的服务，就能使用 Azure 容器实例轻松着手生成项目。 Azure 容器实例根据所需的容量按秒计费，因此，对于暂时性的工作负荷（例如执行生成）而言是一种不错的选项。

学习如何：
> [!div class="checklist"]
> * 在 Azure 上安装和配置 Jenkins 服务器
> * 安装和配置适用于 Jenkins 的 Azure 容器代理插件
> * 使用 Azure 容器实例生成 [Spring PetClinic 示例应用程序](https://github.com/spring-projects/spring-petclinic)

## <a name="prerequisites"></a>先决条件

- **Azure 订阅** - 若要了解 Azure 购买选项，请参阅[如何购买 Azure](https://azure.microsoft.com/pricing/purchase-options/) 或[免费试用一个月](https://azure.microsoft.com/pricing/free-trial/)。

- **Azure CLI 2.0 或 Azure Cloud Shell** - 安装以下产品之一用于输入 Azure 命令：

    - [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest) - 用于通过命令窗口或终端窗口运行 Azure 命令。
    - [Azure Cloud Shell](/azure/cloud-shell/quickstart.md) - 基于浏览器的 shell 体验。 Cloud Shell 能够访问以执行 Azure 管理任务为宗旨构建的基于浏览器的命令行体验。

## <a name="install-a-jenkins-server-on-azure-using-the-jenkins-marketplace-image"></a>使用 Jenkins Marketplace 映像在 Azure 上安装 Jenkins 服务器

Jenkins 支持这样一个模型：其中的 Jenkins 服务器将工作委托给一个或多个代理，使单个 Jenkins 安装即可托管大量的项目，或者为生成或测试活动提供不同的环境。 本部分中的步骤将引导你在 Azure 上安装和配置 Jenkins 服务器。

[!INCLUDE [jenkins-install-from-azure-marketplace-image](../../includes/jenkins-install-from-azure-marketplace-image.md)]

## <a name="connect-to-the-jenkins-server-running-on-azure"></a>连接到 Azure 上运行的 Jenkins 服务器

在 Azure 上安装 Jenkins 之后，需要连接到 Jenkins。 以下步骤引导你设置 Azure 上运行的 Jenkins VM 的 SSH 连接。 

[!INCLUDE [jenkins-connect-to-jenkins-server-running-on-azure](../../includes/jenkins-connect-to-jenkins-server-running-on-azure.md)]

## <a name="update-jenkins-dns"></a>更新 Jenkins DNS

Jenkins 在创建指回自身的链接时，需要知道自身的 URL。 例如，当 Jenkins 发送包含生成结果的直接链接的电子邮件时，就需要使用该 URL。 

本部分逐步讲解如何设置 Jenkins URL。

1. 在浏览器中，通过 `http://localhost:8080` 导航到 Jenkins 仪表板。

1. 选择“管理 Jenkins”。

    ![Jenkins 仪表板中的“管理 Jenkins”选项](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. 选择“配置系统”。

    ![Jenkins 仪表板中用于管理 Jenkins 插件的选项](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-system.png)

1. 在“Jenkins 位置”下，输入 Jenkins 服务器的 URL。

1. 选择“保存”。

## <a name="update-jenkins-to-allow-java-network-launch-protocol-jnlp"></a>更新 Jenkins 以允许 Java 网络启动协议 (JNLP)

Jenkins 代理通过 Java 网络启动协议 (JNLP) 来与 Jenkins 服务器建立连接。 本部分介绍如何指定 JNLP 代理在与 Jenkins 服务器通信时要使用的端口。

1. 在 Jenkins 仪表板中，选择“管理 Jenkins”。

    ![Jenkins 仪表板中的“管理 Jenkins”选项](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. 选择“配置全局安全性”。

    ![Jenkins 仪表板中的“配置全局安全性”](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-global-security.png)

1. 在“代理”下，选择“固定”并输入端口。 该屏幕截图显示了示例端口值 12345。 应指定适合环境的端口。

    ![更新 Jenkins 全局安全性设置以允许 JNLP](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-set-jnlp.png)

1. 选择“保存”。

1. 使用 Azure CLI 2.0 或 Cloud Shell 输入以下命令，以针对 Jenkins 网络安全组创建入站规则：

    ```azurecli
    az network nsg rule create  \
    --resource-group JenkinsResourceGroup \
    --nsg-name JenkinsNSG  \
    --name allow-https \
    --description "Allow access to port 12345 for HTTPS" \
    --access Allow \
    --protocol Tcp  \
    --direction Inbound \
    --priority 102 \
    --source-address-prefix "*"  \
    --source-port-range "*"  \
    --destination-address-prefix "*" \
    --destination-port-range "12345"
    ```

## <a name="create-and-add-an-azure-service-principal-to-the-jenkins-credentials"></a>创建 Azure 服务主体并将其添加到 Jenkins 凭据

部署到 Azure 需要 Azure 服务主体。 以下步骤引导你完成创建服务主体（如果没有）并使用该服务主体更新 Jenkins 的过程。

1. 如果已有服务主体（并知道其订阅 ID、租户、appId 和密码），则可以跳过此步骤。 如果需要创建安全主体，请参阅[使用 Azure CLI 2.0 创建 Azure 服务主体](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)一文。 创建主体时，请记下订阅 ID、租户、appId 和密码的值。

1. 选择“凭据”。

    ![Jenkins 仪表板中用于管理凭据的选项](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials.png)

1. 在“凭据”下，选择“系统”。

    ![Jenkins 仪表板中用于管理系统凭据的选项](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials-system.png)

1. 选择“全局凭据(不受限制)”。

    ![Jenkins 仪表板中用于管理全局系统凭据的选项](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials-global.png)

1. 选择“添加一些凭据”。

    ![在 Jenkins 仪表板中添加凭据](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-adding-credentials.png)

1. 在“种类”下拉列表中，选择“Microsoft Azure 服务主体”，使页面显示专门用于添加服务主体的字段。 然后，按如下所述提供请求的值：

    - **范围** - 选择“全局(Jenkins、节点、项、所有子项等)”对应的选项。
    - **订阅 ID** - 使用运行 `az account set` 时指定的 Azure 订阅 ID。
    - **客户端 ID** - 使用从 `az ad sp create-for-rbac` 返回的 `appId` 值。
    - **客户端机密** - 使用从 `az ad sp create-for-rbac` 返回的 `password` 值。
    - **租户 ID** - 使用从 `az ad sp create-for-rbac` 返回的 `tenant` 值。
    - **Azure 环境** - 选择 `Azure`。
    - **ID** - 输入 `myTestSp`。 本教程稍后还要使用此值。
    - **说明** -（可选）输入此主体的说明值。

    ![在 Jenkins 仪表板中指定新的服务主体属性](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-principal-properties.png)

1. 输入用于定义主体的信息之后，可以根据需要选择“验证服务主体”，确保一切正常运行。 如果已正确定义服务主体，将会看到一条指出“已成功验证 Microsoft Azure 服务主体”的消息显示 在“说明”字段下面。

1. 完成后，请选择“确定”将该主体添加到 Jenkins。 Jenkins 仪表板的“全局凭据”页将显示新添加的主体。

## <a name="create-an-azure-resource-group-for-your-azure-container-instances"></a>为 Azure 容器实例创建 Azure 资源组

必须将 Azure 容器实例放在 Azure 资源组中。 Azure 资源组是用于保存 Azure 解决方案相关资源的容器。

使用 Azure CLI 2.0 或 Cloud Shell 输入以下命令，在 `eastus` 位置创建名为 `JenkinsAciResourceGroup` 的资源组：

```azurecli
az group create --name JenkinsAciResourceGroup --location eastus
```

完成后，`az group create` 命令将显示类似于以下示例的结果：

```JSON
{
  "id": "/subscriptions/<subscriptionId>/resourceGroups/JenkinsAciResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "JenkinsAciResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="install-the-azure-container-agents-plugin-for-jenkins"></a>安装适用于 Jenkins 的 Azure 容器代理插件

如果已安装 Azure 容器代理插件，则可以跳过本部分。

为 Jenkins 代理创建 Azure 资源组之后，可遵循以下步骤安装 Azure 容器代理插件：

1. 在 Jenkins 仪表板中，选择“管理 Jenkins”。

    ![Jenkins 仪表板中的“管理 Jenkins”选项](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. 选择“管理插件”。

    ![Jenkins 仪表板中用于管理 Jenkins 插件的选项](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-plugins.png)

1. 选择“可用”。

    ![在 Jenkins 仪表板中查看可用的 Jenkins 插件选项](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-view-available-plugins.png)

1. 在“筛选器”文本框中输入 `Azure Container Agents`。 （输入该文本时，会筛选列表中的结果。）

    ![在 Jenkins 仪表板中筛选可用的 Jenkins 插件](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-filter-available-plugins.png)

1. 选中“Azure 容器代理”插件旁边的复选框，并选择某个安装选项。 在本演示中，我选择了“安装且不重启”选项。

    ![通过 Jenkins 仪表板安装 Azure 容器代理插件](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-install-aks-agent-plugin.png)

1.  选择用于安装所需插件的选项后，Jenkins 仪表板会显示一个页面，其中详细列出了所要安装的产品的状态。

    ![通过 Jenkins 仪表板安装 Azure 容器代理插件时的安装状态](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-install-aks-agent-plugin-confirmation.png)

    若要返回到 Jenkins 仪表板的主页，请选择“返回首页”。

## <a name="configure-the-azure-container-agents-plugin"></a>配置 Azure 容器代理插件

安装 Azure 容器代理插件之后，可以遵循本部分的指导在 Jenkins 仪表板中配置该插件。

1. 在 Jenkins 仪表板中，选择“管理 Jenkins”。

    ![Jenkins 仪表板中的“管理 Jenkins”选项](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. 选择“配置系统”。

    ![Jenkins 仪表板中用于管理 Jenkins 插件的选项](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-system.png)

1. 在页面底部找到“云”部分，从“添加新云”下拉列表中选择“Azure 容器实例”。

    ![通过 Jenkins 仪表板添加新的云提供程序](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-cloud-provider.png)

1. 在“Azure 容器实例”部分指定以下值：

    - **云名称** -（可选，因为此值会默认设置为自动生成的名称）。指定此实例的名称。 
    - **Azure 凭据** - 选择下拉箭头，然后选择用于标识前面创建的 Azure 服务主体的 `myTestSp` 项。
    - **资源组** - 选择下拉箭头，然后选择用于标识前面创建的 Azure 容器实例资源组的 `JenkinsAciResourceGroup` 项。

    ![定义 Azure 容器实例属性](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-properties.png)

1. 选择“添加容器模板”下拉箭头，然后选择“Aci 容器模板”。

1. 在“Aci 容器模板”部分指定以下值：

    - **名称** - 输入 `ACI-container`。
    - **标签** - 输入 `ACI-container`。
    - **Docker 映像** - 输入 `cloudbees/jnlp-slave-with-java-build-tools`

    ![定义 Azure 容器实例映像属性](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-image-properties.png)

1. 选择“高级”。

1. 选择“保留策略”下拉箭头，然后选择“容器空闲保留策略”。 选择此选项后，Jenkins 会将代理保留到已在该代理上执行新作业，并且指定的空闲时间过去为止。

    ![定义 Azure 容器实例保留策略](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-retention-strategy.png)

1. 选择“保存”。

## <a name="create-the-spring-petclinic-application-job-in-jenkins"></a>在 Jenkins 中创建 Spring PetClinic 应用程序作业

以下步骤引导你创建一个 Jenkins 作业（自由风格项目）用于生成 Spring PetClinic 应用程序。

1. 在 Jenkins 仪表板中，选择“新建项”。

    ![Jenkins 仪表板中的“新建项”菜单选项](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-item.png)

1. 输入 `myPetClinicProject` 作为项名称，并选择“自由风格项目”。

    ![在 Jenkins 仪表板中新建自由风格项目](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-freestyle-project.png)

1. 选择“确定”。

1. 选择“常规”选项卡并指定以下值：

    - **限制运行项目的位置** - 请选择此选项。
    - **标签表达式** - 输入 `ACI-container`。 退出该字段时，将会显示一条消息，确认该标签由上一步骤创建的云配置提供。

    ![Jenkins 仪表板中新自由风格项目的常规设置](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-freestyle-project-general.png)

1. 选择“源代码管理”选项卡并指定以下值：

    - **源代码管理** - 选择“Git”。
    - **存储库 URL** - 输入 Spring PetClinic 示例应用程序 GitHub 存储库的以下 URL：`https://github.com/spring-projects/spring-petclinic.git`。

1. 选择“生成”选项卡并执行以下任务：

    - 选择“添加生成步骤”下拉箭头，然后选择“调用顶级 Maven 目标”。

    - 对于“目标”，请输入 `package`。

1. 选择“保存”以保存新项目的定义。

## <a name="build-the-spring-petclinic-application-job-in-jenkins"></a>在 Jenkins 中生成 Spring PetClinic 应用程序作业

现在可以生成项目了！ 本部分介绍如何通过 Jenkins 仪表板生成项目。

1. 在 Jenkins 仪表板中，选择 `myPetClinicProject`。

    ![在 Jenkins 仪表板中选择要生成的项目。](./media/azure-container-agents-plugin-run-container-as-an-agent/select-project-to-build.png)

1. 选择“立即生成”。 

    ![通过 Jenkins 仪表板生成项目。](./media/azure-container-agents-plugin-run-container-as-an-agent/build-project.png)

1. 在 Jenkins 中启动某个生成时，该生成将会排队。 对于 Azure 容器代理，只有在 Azure 容器代理已启动并已联机时，才能运行生成。 此时会显示一条消息，其中指出了代理名称并且正在等待生成。 （此过程大约需要五分钟时间，但是，只有首次使用代理进行生成时，才需要这么长时间。 以后的生成要快得多，因为在生成时代理已经联机。）

    ![生成将会排队，直到代理已创建并联机。](./media/azure-container-agents-plugin-run-container-as-an-agent/build-pending.png)

    生成开始后，会显示一个螺旋条纹状进度栏，指示生成正在运行：

    ![看到该螺旋条纹状进度栏即表示生成正在运行。](./media/azure-container-agents-plugin-run-container-as-an-agent/build-running.png)

1. 螺旋条纹状进度栏消失后，请选择生成号旁边的箭头。 在上下文菜单中，选择“控制台输出”。

    ![单击“控制台日志”菜单项查看生成信息。](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log-menu.png)

1. 生成过程会发出控制台日志信息。 若要查看所有生成信息（包括有关在所创建的 Azure 代理上远程执行的生成的信息），请选择“完整日志”。

    ![单击“完整日志”链接查看更详细的生成信息。](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log.png)

    完整日志显示更详细的生成信息：

    ![完整日志显示更详细的生成信息。](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log-full.png)
    
1. 若要查看生成处置情况，请滚动到日志底部。

    ![生成处置情况显示在生成日志的底部。](./media/azure-container-agents-plugin-run-container-as-an-agent/build-disposition.png)

## <a name="clean-up-azure-resources"></a>清理 Azure 资源

在本教程中创建的资源包含在两个 Azure 资源组中： 
    - `JenkinsResourceGroup` - 包含 Jenkins 服务器的 Azure 资源。
    - `JenkinsAciResourceGroup` - 包含 Jenkins 代理的 Azure 资源。
    
如果不再需要使用某个 Azure 资源组中的任何资源，可按如下所示，使用 `az group delete` 命令（请将 &lt;resourceGroup> 占位符替换为要删除的资源组的名称）删除该资源组：

```azurecli
az group delete -n <resourceGroup>
```

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [访问 Azure 中心上的“Jenkins”查看最新的文章和示例](https://docs.microsoft.com/azure/jenkins/)
