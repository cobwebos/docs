---
title: 使用 Azure 容器实例作为 Jenkins 生成代理
description: 了解如何使用 Azure 容器实例作为 Jenkins 生成代理。
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 08/31/2018
ms.author: marsma
ms.openlocfilehash: 6419753a48e1356b2d94592fca72cee1f848dcdb
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2018
ms.locfileid: "39390647"
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>使用 Azure 容器实例作为 Jenkins 生成代理

Azure 容器实例 (ACI) 提供按需、可迸发的隔离环境用于运行容器化工作负荷。 这些特性使得 ACI 成了大规模运行 Jenkins 生成作业的极佳平台。 本文逐步说明如何部署和使用一个将 ACI 预先配置为生成目标的 Jenkins 服务器。

有关 Azure 容器实例的详细信息，请参阅[关于 Azure 容器实例][about-aci]。

## <a name="deploy-a-jenkins-server"></a>部署 Jenkins 服务器

1. 在 Azure 门户中，选择“创建资源”并搜索 **Jenkins**。 选择发行商为“Microsoft”的 Jenkins 产品/服务，然后选择“创建”。

2. 在“基本信息”窗体中输入以下信息，然后选择“确定”。

   - **名称**：输入 Jenkins 部署的名称。
   - **用户名**：输入 Jenkins 虚拟机的管理员用户名。
   - **身份验证类型**：建议使用 SSH 公钥进行身份验证。 如果选择此选项，请粘贴一个 SSH 公钥，以便在登录到 Jenkins 虚拟机时使用。
   - **订阅**：选择一个 Azure 订阅。
   - **资源组**：创建资源组，或选择现有的资源组。
   - **位置**：选择 Jenkins 服务器的位置。

   ![Jenkins 门户部署的基本设置](./media/container-instances-jenkins/jenkins-portal-01.png)

3. 在“其他设置”窗体中填写以下各项：

   - **大小**：选择适合于 Jenkins 虚拟机的调整大小选项。
   - **VM 磁盘类型**：为 Jenkins 服务器指定“HDD”（机械硬盘）或“SSD”（固态硬盘）。
   - **虚拟网络**：若要修改默认设置，请选择箭头。
   - **子网**：选择箭头，检查信息，然后选择“确定”。
   - **公共 IP 地址**：选择箭头，为公共 IP 地址指定自定义名称，配置 SKU，并设置分配方法。
   - **域名标签**：指定用于创建 Jenkins 虚拟机完全限定 URL 的值。
   - **Jenkins 版本类型**：从以下选项中选择所需的版本类型：“LTS”、“每周版本”或“Azure 认证”。

   ![Jenkins 门户部署的其他设置](./media/container-instances-jenkins/jenkins-portal-02.png)

4. 若要进行服务主体集成，请选择“自动(MSI)”，让 [Azure 托管服务标识][managed-service-identity]自动创建 Jenkins 实例的身份验证标识。 选择“手动”可提供自己的服务主体凭据。

5. 云代理将为 Jenkins 生成作业配置基于云的平台。 对于本文档，请选择“ACI”。 使用 ACI 云代理时，每个 Jenkins 生成作业将在容器实例中运行。

   ![Jenkins 门户部署的云集成设置](./media/container-instances-jenkins/jenkins-portal-03.png)

6. 完成集成设置后，选择“确定”，然后在验证摘要中再次选择“确定”。 在“使用条款”摘要中选择“创建”。 部署 Jenkins 服务器需要几分钟时间。

## <a name="configure-jenkins"></a>配置 Jenkins

1. 在 Azure 门户中，浏览到 Jenkins 资源组，选择 Jenkins 虚拟机，并记下 DNS 名称。

   ![有关 Jenkins 虚拟机的详细信息中的 DNS 名称](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

2. 浏览到 Jenkins VM 的 DNS 名称，并复制返回的 SSH 字符串。

   ![Jenkins 登录说明和 SSH 字符串](./media/container-instances-jenkins/jenkins-portal-04.png)

3. 在开发系统上打开终端会话，并粘贴在上一步骤中复制的 SSH 字符串。 将 `username` 更新为部署 Jenkins 服务器时指定的用户名。

4. 连接会话后，运行以下命令检索初始管理员密码：

   ```
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

5. 让 SSH 会话和隧道保持运行，在浏览器中转到 http://localhost:8080。 将初始管理员密码粘贴到字段中，然后选择“继续”。

   ![包含管理员密码输入框的“解锁 Jenkins”屏幕](./media/container-instances-jenkins/jenkins-portal-05.png)

6. 选择“安装建议的插件”以安装所有建议的 Jenkins 插件。

   ![“自定义 Jenkins”屏幕，其中的“安装建议的插件”已选中](./media/container-instances-jenkins/jenkins-portal-06.png)

7. 创建管理员用户帐户。 此帐户用于登录和使用 Jenkins 实例。

   ![“创建第一个管理员用户”屏幕，其中已填写凭据](./media/container-instances-jenkins/jenkins-portal-07.png)

8. 选择“保存并完成”，然后选择“开始使用 Jenkins”以完成配置。

现已配置 Jenkins，它可以开始生成和部署代码。 本示例使用一个简单的 Java 应用程序来演示 Azure 容器实例中的 Jenkins 生成过程。

## <a name="create-a-build-job"></a>创建生成作业

现在，创建 Jenkins 生成作业来演示 Azure 容器实例中的 Jenkins 生成。

1. 选择“新建项”，为生成项目命名（例如 aci-demo），选择“自由风格项目”，然后选择“确定”。

   ![用于指定生成作业名称的框，以及项目类型列表](./media/container-instances-jenkins/jenkins-new-job.png)

2. 在“常规”下，确保已选择“限制可以运行此项目的位置”。 为标签表达式输入 **linux**。 此配置可确保此生成作业在 ACI 云中运行。

   ![包含配置详细信息的“常规”选项卡](./media/container-instances-jenkins/jenkins-job-01.png)

3. 在“生成”下，选择“添加生成步骤”并选择“执行 Shell”。 输入 `echo "aci-demo"` 作为命令。

   ![包含生成步骤选项的“生成”选项卡](./media/container-instances-jenkins/jenkins-job-02.png)

5. 选择“保存”。

## <a name="run-the-build-job"></a>运行生成作业

若要测试生成作业并观察用作生成平台的 Azure 容器实例，请手动启动生成。

1. 选择“立即生成”启动生成作业。 启动该作业需要花费几分钟时间。 应会看到类似于下图的状态：

   ![包含作业状态的“生成历史记录”信息](./media/container-instances-jenkins/jenkins-job-status.png)

2. 在作业运行时，打开 Azure 门户并查看 Jenkins 资源组。 应会看到已创建一个容器实例。 Jenkins 作业正在此实例内部运行。

   ![资源组中的容器实例](./media/container-instances-jenkins/jenkins-aci.png)

3. 随着 Jenkins 运行的作业数超过配置的 Jenkins 执行器数目（默认为 2 个），将会创建多个容器实例。

   ![新建的容器实例](./media/container-instances-jenkins/jenkins-aci-multi.png)

4. 完成所有生成作业后，会删除容器实例。

   ![已删除容器实例的资源组](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>排查 Jenkins 插件问题

如果 Jenkins 插件出现任何 bug，请在 [Jenkins JIRA](https://issues.jenkins-ci.org/) 中提出特定组件的问题。

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure 上的 Jenkins，请参阅 [Azure 和 Jenkins][jenkins-azure]。

<!-- LINKS - internal -->
[about-aci]: ./container-instances-overview.md
[jenkins-azure]: ../jenkins/overview.md
[managed-service-identity]: ../active-directory/managed-service-identity/overview.md