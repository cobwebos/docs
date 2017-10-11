---
title: "在 Azure 上创建 Jenkins 服务器"
description: "从 Jenkins 解决方案模板的 Azure Linux 虚拟机上安装 Jenkins 和生成示例 Java 应用程序。"
author: mlearned
manager: douge
ms.service: multiple
ms.workload: web
ms.devlang: java
ms.topic: hero-article
ms.date: 08/21/2017
ms.author: mlearned
ms.custom: Jenkins
ms.openlocfilehash: 7bb74f297d52fb25171817175cce64187b397c38
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="create-a-jenkins-server-on-an-azure-linux-vm-from-the-azure-portal"></a>在 Azure 门户中的 Azure Linux VM 创建 Jenkins 的服务器

本快速入门演示如何安装[Jenkins](https://jenkins.io) Ubuntu Linux VM 的工具和插件配置为使用 Azure 上。 完成后，必须在生成示例 Java 应用程序从 Azure 中运行的 Jenkins 服务器[GitHub](https://github.com)。

## <a name="prerequisites"></a>必备组件

* Azure 订阅
* 在你的计算机的命令行上 SSH 访问 (例如 Bash shell 或[PuTTY](http://www.putty.org/))

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-jenkins-vm-from-the-solution-template"></a>从解决方案模板创建 Jenkins VM

打开[Jenkins 的应用商店映像](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview)在 web 浏览器，选择**获取 IT**从页面的左侧。 查看定价详细信息，然后选择**继续**，然后选择**创建**在 Azure 门户中配置 Jenkins 服务器。 
   
![Azure 门户对话框](./media/install-jenkins-solution-template/ap-create.png)

在**配置基本设置**选项卡上，填写以下字段：

![配置基本设置](./media/install-jenkins-solution-template/ap-basic.png)

* 使用**Jenkins**为**名称**。
* 输入**用户名**。 用户名称必须满足[特定要求](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm)。
* 选择**密码**作为**身份验证类型**并输入密码。 密码必须包含大写字符、 一个数字和一个特殊字符。
* 使用**myJenkinsResourceGroup**为**资源组**。
* 选择**美国东部** [Azure 区域](https://azure.microsoft.com/regions/)从**位置**下拉列表。

选择**确定**以进入**配置其他选项**选项卡。输入要识别 Jenkins 服务器并选择的唯一域名**确定**。

![设置其他选项](./media/install-jenkins-solution-template/ap-addtional.png)  

 之后，则通过验证，可选择**确定**再次从**摘要**选项卡。最后，选择**购买**创建 Jenkins VM。 准备你的服务器时，你将看到一条通知，在 Azure 门户中：   

![Jenkins 是准备通知](./media/install-jenkins-solution-template/jenkins-deploy-notification-ready.png)

## <a name="connect-to-jenkins"></a>连接到 Jenkins

在 web 浏览器中，导航到你的虚拟机 (例如，http://jenkins2517454.eastus.cloudapp.azure.com/)。 Jenkins 控制台是无法通过不安全的 HTTP 访问，因此页后，可以安全地从你的计算机使用 SSH 隧道访问 Jenkins 控制台上提供了说明。

![解锁 Jenkins](./media/install-jenkins-solution-template/jenkins-ssh-instructions.png)

设置隧道使用`ssh`命令从命令行中，页上替换`username`替换时从解决方案模板设置虚拟机之前选择的虚拟机管理员用户的名称。

```bash
ssh -L 127.0.0.1:8080:localhost:8080 jenkinsadmin@jenkins2517454.eastus.cloudapp.azure.com
```

启动隧道后，导航到 http://localhost:8080 / 本地计算机上。 

通过在命令行时连接通过 ssh 连接到 Jenkins VM 中运行以下命令获取初始密码。

```bash
`sudo cat /var/lib/jenkins/secrets/initialAdminPassword`.
```

首次使用此初始密码解锁 Jenkins 仪表板中。

![解锁 Jenkins](./media/install-jenkins-solution-template/jenkins-unlock.png)

选择**安装建议的插件**在下次页上，然后创建 Jenkins 管理员用户用于访问 Jenkins 仪表板中。

![Jenkins 已准备就绪！](./media/install-jenkins-solution-template/jenkins-welcome.png)

Jenkins 服务器现在已准备好生成的代码。

## <a name="create-your-first-job"></a>创建第一个作业

选择**创建新作业**从 Jenkins 控制台中，然后将其命名**mySampleApp**和选择**自由格式项目**，然后选择**确定**。

![创建新的作业](./media/install-jenkins-solution-template/jenkins-new-job.png) 

选择**源代码管理**选项卡上，启用**Git**，并输入以下 URL**存储库 URL**字段：`https://github.com/spring-guides/gs-spring-boot.git`

![定义的 Git 存储库](./media/install-jenkins-solution-template/jenkins-job-git-configuration.png) 

选择**生成**选项卡，然后选择**添加生成步骤**，**调用 Gradle 脚本**。 选择**使用 Gradle 包装**，然后输入`complete`中**包装位置**和`build`为**任务**。

![使用生成的 Gradle 包装](./media/install-jenkins-solution-template/jenkins-job-gradle-config.png) 

选择**高级...** 然后输入`complete`中**根生成脚本**字段。 选择“保存”。

![在 Gradle 包装构建步骤中设置高级的设置](./media/install-jenkins-solution-template/jenkins-job-gradle-advances.png) 

## <a name="build-the-code"></a>生成代码

选择**立即生成**编译代码并打包示例应用程序。 完成你的生成操作后，选择**工作区**项目的链接。

![浏览到要从生成中获取 JAR 文件的工作区](./media/install-jenkins-solution-template/jenkins-access-workspace.png) 

导航到`complete/build/libs`并确保`gs-spring-boot-0.1.0.jar`是否有以验证你的生成是否成功。 你的 Jenkins 服务器现在已准备好生成您自己在 Azure 中的项目。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [将 Azure Vm 添加为 Jenkins 代理](jenkins-azure-vm-agents.md)
