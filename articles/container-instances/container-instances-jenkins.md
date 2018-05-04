---
title: 使用 Azure 容器实例作为 Jenkins 生成代理
description: 了解如何使用 Azure 容器实例作为 Jenkins 生成代理。
services: container-instances
author: neilpeterson
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/20/2018
ms.author: nepeters
ms.openlocfilehash: dbe418db8fb3d73739a30b60703f15b3dc47b291
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>使用 Azure 容器实例作为 Jenkins 生成代理

Azure 容器实例提供按需、可迸发的隔离环境用于运行容器化工作负荷。 这些特性使得 Azure 容器实例成了大规模运行 Jenkins 生成作业的极佳平台。 本文档逐步说明如何部署和使用一个将 ACI 预先配置为生成目标的 Jenkins 服务器。

有关 Azure 容器实例的详细信息，请参阅[关于 Azure 容器实例][about-aci]。

## <a name="deploy-jenkins-server"></a>部署 Jenkins 服务器

在 Azure 门户中，选择“创建资源”并搜索 **Jenkins**。 选择发行商为“Microsoft”的 Jenkins 产品/服务，并选择“创建”。

在基本信息窗体中输入以下信息，完成后单击“确定”。

- **名称** - Jenkins 部署的名称。
- **用户名** - 此用户名用作 Jenkins 虚拟机的管理员用户。
- **身份验证类型** - 建议选择 SSH 公钥。 如果选择该选项，请复制一个 SSH 公钥，以便在登录到 Jenkins 虚拟机时使用。
- **订阅** - 选择一个 Azure 订阅。
- **资源组** - 创建新资源组，或选择现有的资源组。
- **位置** - 选择 Jenkins 服务器的位置。

![Jenkins 门户中的部署基本设置](./media/container-instances-jenkins/jenkins-portal-01.png)

在其他设置窗体中完成以下各项：

- **大小** - 选择适合于 Jenkins 虚拟机的调整大小选项。
- **VM 磁盘类型** - 为 Jenkins 服务器指定 HDD（机械硬盘）或 SSD（固态硬盘）。
- **虚拟网络** -（可选）选择要修改默认设置的虚拟网络。
- **子网** - 选择“子网”，检查信息，然后选择“确定”。
- **公共 IP 地址** - 选择“公共 IP 地址”，以指定其自定义名称和分配方法，并配置 SKU。
- **域名标签** - 指定用于创建 Jenkins 虚拟机完全限定 URL 的值。
- **Jenkins 版本类型** - 从以下选项中选择所需的版本类型：“LTS”、“每周版本”或“Azure 认证”。

![Jenkins 门户中的其他部署设置](./media/container-instances-jenkins/jenkins-portal-02.png)

若要进行服务主体集成，请选择“自动(MSI)”，让 [Azure 托管服务标识][managed-service-identity]自动创建 Jenkins 实例的身份验证标识。 选择“手动”可提供自己的服务主体凭据。

云代理将为 Jenkins 生成作业配置基于云的平台。 对于本文档，请选择“ACI”。 使用 ACI 云代理时，每个 Jenkins 生成作业将在 Azure 容器实例中运行。

![Jenkins 门户中的云集成部署设置](./media/container-instances-jenkins/jenkins-portal-03.png)

完成集成设置后，单击“确定”，然后在验证摘要中再次单击“确定”。 在“使用条款摘要”中单击“创建”。 部署 Jenkins 服务器需要几分钟时间。

## <a name="configure-jenkins"></a>配置 Jenkins

在 Azure 门户中，浏览到“Jenkins 资源组”，选择 Jenkins 虚拟机，并记下 DNS 名称。

![Jenkins 登录说明](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

浏览到 Jenkins VM 的 DNS 名称，并复制返回的 SSH 字符串。

![Jenkins 登录说明](./media/container-instances-jenkins/jenkins-portal-04.png)

在开发系统上打开终端会话，并粘贴在上一步骤中复制的 SSH 字符串。 将“username”更新为部署 Jenkins 服务器时指定的用户名。

建立连接后，运行以下命令检索初始管理员密码。

```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

让 SSH 会话和隧道保持运行，在浏览器中导航到 http://localhost:8080。 如下图所示，将初始管理员密码粘贴到字段中。 完成后选择“继续”。

![解锁 Jenkins](./media/container-instances-jenkins/jenkins-portal-05.png)

选择“安装建议的插件”以安装所有建议的 Jenkins 插件。

![安装 Jenkins 插件](./media/container-instances-jenkins/jenkins-portal-06.png)

创建新的管理员用户帐户。 此帐户用于登录和使用 Jenkins 实例。

![创建 Jenkins 用户](./media/container-instances-jenkins/jenkins-portal-07.png)

完成后选择“保存并完成”，然后选择“开始使用 Jenkins”以完成配置。

现已配置 Jenkins，它可以开始生成和部署代码。 本示例使用一个简单的 Java 应用程序来演示 Azure 容器实例中的 Jenkins 生成过程。

## <a name="create-build-job"></a>创建生成作业

使用容器映像作为 Jenkins 生成目标时，需要指定包含一个成功生成所需的所有工具的映像。 若要指定映像，请选择“管理 Jenkins” > “配置系统”，并向下滚动到“云”部分。 本示例将 Docker 映像值更新为 `microsoft/java-on-azure-jenkins-slave`。

完成后，单击“保存”返回到 Jenkins 仪表板。

![Jenkins 云配置](./media/container-instances-jenkins/jenkins-aci-image.png)

现在创建 Jenkins 生成作业。 选择“新建项”，为生成项目命名（例如 `aci-java-demo`），选择“自由风格项目”，并单击“确定”。

![创建 Jenkins 作业](./media/container-instances-jenkins/jenkins-new-job.png)

在“常规”下，确保已选择“限制可以运行此项目的位置”。 为“标签表达式”输入 `linux`。 此配置可确保此生成作业在 ACI 云中运行。

![创建 Jenkins 作业](./media/container-instances-jenkins/jenkins-job-01.png)

在源代码管理下，选择 `Git` 并输入 `https://github.com/spring-projects/spring-petclinic.git` 作为存储库 URL。 此 GitHub 存储库包含示例应用程序代码。

![将源代码添加到 Jenkins 作业](./media/container-instances-jenkins/jenkins-job-02.png)

在“生成”下，选择“添加生成步骤”并选择 `Invoke top-level Maven targets`。 输入 `package` 作为生成步骤目标。

![添加 Jenkins 生成步骤](./media/container-instances-jenkins/jenkins-job-03.png)

完成后，选择“保存”。

## <a name="run-the-build-job"></a>运行生成作业

若要测试生成作业并观察用作生成平台的 Azure 容器实例，请手动启动生成。

选择“立即生成”启动生成作业。 启动该作业需要花费几分钟时间；在作业运行时，应会看到类似于下图的状态。

![添加 Jenkins 生成步骤](./media/container-instances-jenkins/jenkins-job-status.png)

在作业运行时，打开 Azure 门户并查看 Jenkins 资源组。 应会看到已创建 Azure 容器实例。 该实例位于运行 Jenkins 作业的实例内部。

![ACI 中的 Jenkins 生成](./media/container-instances-jenkins/jenkins-aci.png)

随着 Jenkins 运行的作业数超过配置的 Jenkins 执行器数目（默认为 2 个），将会创建多个 Azure 容器实例。

![ACI 中的 Jenkins 生成](./media/container-instances-jenkins/jenkins-aci-multi.png)

完成所有生成作业后，将会删除 Azure 容器实例。

![ACI 中的 Jenkins 生成](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure 上的 Jenkins，请参阅 [Azure 和 Jenkins][jenkins-azure]。

<!-- LINKS - internal -->
[about-aci]: ./container-instances-overview.md
[jenkins-azure]: ../jenkins/overview.md
[managed-service-identity]: ../active-directory/managed-service-identity/overview.md