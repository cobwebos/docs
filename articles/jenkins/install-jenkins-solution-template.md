---
title: 在 Azure 上创建 Jenkins 服务器
description: 通过 Jenkins 解决方案模板在 Azure Linux 虚拟机上安装 Jenkins，然后生成示例 Java 应用程序。
ms.topic: quickstart
ms.author: tarcher
author: tomarcher
manager: jpconnock
ms.service: devops
ms.custom: jenkins
ms.date: 07/31/2018
ms.openlocfilehash: af1082d9b7b350f1845d6c8e22108b9238a40f28
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2018
ms.locfileid: "39390422"
---
# <a name="create-a-jenkins-server-on-an-azure-linux-vm-from-the-azure-portal"></a>通过 Azure 门户在 Azure Linux VM 上创建 Jenkins 服务器

本快速入门介绍如何使用经过配置的适用于 Azure 的工具和插件在 Ubuntu Linux VM 上安装 [Jenkins](https://jenkins.io)。 完成后，你会有一个在 Azure 中运行的 Jenkins 服务器，并可生成 [GitHub](https://github.com) 中的示例 Java 应用。

## <a name="prerequisites"></a>先决条件

* Azure 订阅
* 可以在计算机的命令行（例如 Bash shell 或 [PuTTY](http://www.putty.org/)）上访问 SSH

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-jenkins-vm-from-the-solution-template"></a>从解决方案模板创建 Jenkins VM
Jenkins 支持这样一个模型：其中的 Jenkins 服务器将工作委托给一个或多个代理，使单个 Jenkins 安装即可托管大量的项目，或者为生成或测试活动提供不同的环境。 本部分中的步骤将引导你在 Azure 上安装和配置 Jenkins 服务器。

[!INCLUDE [jenkins-install-from-azure-marketplace-image](../../includes/jenkins-install-from-azure-marketplace-image.md)]

## <a name="connect-to-jenkins"></a>连接到 Jenkins

导航到虚拟机（例如，Web 浏览器中的 http://jenkins2517454.eastus.cloudapp.azure.com/)。 Jenkins 控制台无法通过不安全的 HTTP 进行访问，因此在页面上提供了说明，介绍如何在计算机中使用 SSH 隧道安全地访问 Jenkins 控制台。

![解锁 Jenkins](./media/install-jenkins-solution-template/jenkins-ssh-instructions.png)

在页面上通过命令行使用 `ssh` 命令设置该隧道，将 `username` 替换为此前从解决方案模板设置虚拟机时选择的虚拟机管理员用户的名称。

```bash
ssh -L 127.0.0.1:8080:localhost:8080 jenkinsadmin@jenkins2517454.eastus.cloudapp.azure.com
```

启动隧道后，导航到本地计算机上的 http://localhost:8080/。 

通过 SSH 连接到 Jenkins VM 时，在命令行中运行以下命令，以便获取初始密码。

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

首次解锁 Jenkins 仪表板时，请使用此初始密码。

![解锁 Jenkins](./media/install-jenkins-solution-template/jenkins-unlock.png)

在下一页选择“安装建议的插件”，然后创建用于访问 Jenkins 仪表板的 Jenkins 管理员用户。

![Jenkins 已准备就绪！](./media/install-jenkins-solution-template/jenkins-welcome.png)

Jenkins 服务器现在已就绪，可以生成代码了。

## <a name="create-your-first-job"></a>创建第一个作业

从 Jenkins 控制台选择“创建新作业”，将其命名为“mySampleApp”并选择“自由格式项目”，然后选择“确定”。

![创建新的作业](./media/install-jenkins-solution-template/jenkins-new-job.png) 

选择“源代码管理”选项卡，启用“Git”，然后在“存储库 URL”字段中输入以下 URL：`https://github.com/spring-guides/gs-spring-boot.git`

![定义 Git 存储库](./media/install-jenkins-solution-template/jenkins-job-git-configuration.png) 

依次选择“生成”选项卡、“添加生成步骤”、“调用 Gradle 脚本”。 选择“使用 Gradle 包装器”，然后在“包装器位置”中输入 `complete`，并输入 `build` 作为“任务”。

![使用要生成的 Gradle 包装器](./media/install-jenkins-solution-template/jenkins-job-gradle-config.png) 

选择“高级...”， 然后在“根生成脚本”字段中输入 `complete`。 选择“保存”。

![在 Gradle 包装器生成步骤中设置高级设置](./media/install-jenkins-solution-template/jenkins-job-gradle-advances.png) 

## <a name="build-the-code"></a>生成代码

选择“立即生成”，编译代码并打包示例应用。 生成完成后，选择项目的 Workspace 链接。

![浏览到要从生成中获取 JAR 文件的工作区](./media/install-jenkins-solution-template/jenkins-access-workspace.png) 

导航到 `complete/build/libs`，确保能够验证生成是否成功的 `gs-spring-boot-0.1.0.jar` 位于其中。 Jenkins 服务器现已就绪，可以在 Azure 中生成你自己的项目了。

## <a name="troubleshooting-the-jenkins-solution-template"></a>排查 Jenkins 解决方案模板问题

如果 Jenkins 解决方案模板出现 Bug，请在 [Jenkins GitHub 存储库](https://github.com/azure/jenkins/issues)中提交问题。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [将 Azure VM 作为 Jenkins 代理添加](jenkins-azure-vm-agents.md)
