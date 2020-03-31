---
title: 在本地部署远程监视解决方案 - IntelliJ IDE - Azure |微软文档
description: 本说明指南演示如何使用 IntelliJ 进行测试和开发将远程监视解决方案加速器部署到本地计算机。
author: v-krghan
manager: dominicbetts
ms.author: v-krghan
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: 779ee1e057d74b11c5e0ba58dc2fd32b803f1e0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73888815"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>在本地部署远程监视解决方案加速器 - IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

本文展示了如何将远程监视解决方案加速器部署到本地计算机，用于测试和开发。 您将学习如何在 IntelliJ 中运行微服务。 本地微服务部署将使用以下云服务：IoT 中心、Azure Cosmos DB、Azure 流分析和 Azure 时间序列见解。

若要在本地计算机上的 Docker 中运行远程监视解决方案加速器，请参阅[在本地部署远程监视解决方案加速器 - Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md)。

## <a name="prerequisites"></a>先决条件

若要部署远程监视解决方案加速器使用的 Azure 服务，需要一个有效的 Azure 订阅。

如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅[Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/)。

### <a name="machine-setup"></a>计算机设置

若要完成本地部署，需要使用在本地开发计算机上安装的以下工具：

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [IntelliJ 社区版](https://www.jetbrains.com/idea/download/)
* [IntelliJ Scala 插件](https://plugins.jetbrains.com/plugin/1347-scala)
* [IntelliJ SBT 插件](https://plugins.jetbrains.com/plugin/5007-sbt)
* [IntelliJ SBT 执行插件](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/)

Node.js v8 是脚本用于创建 Azure 资源的 PCS CLI 的先决条件。 请勿使用 Node.js v10。

> [!NOTE]
> IntelliJ IDE 适用于 Windows 和 Mac。

## <a name="download-the-source-code"></a>下载源代码

远程监视源代码存储库中包含运行微服务 Docker 映像时所需的源代码和 Docker 配置文件。

要克隆和创建存储库的本地版本，请使用命令行环境转到本地计算机上的适当文件夹。 然后运行以下命令集之一以克隆 Java 存储库：

* 要下载最新版本的 Java 微服务实现，请运行以下命令：

  ```cmd/sh
  git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git
  ```

* 要检索最新的子模块，运行以下命令：

   ```cmd/sh
   cd azure-iot-pcs-remote-monitoring-java
   git submodule foreach git pull origin master
    ```

> [!NOTE]
> 这些命令除了下载用于在本地运行微服务的脚本，还下载所有微服务的源代码。 不需要源代码来在 Docker 中运行微服务。 但是，如果您以后计划修改解决方案加速器并在本地测试更改，则源代码非常有用。

## <a name="deploy-the-azure-services"></a>部署 Azure 服务

虽然本文展示了如何在本地运行微服务，但它们依赖于在云中运行的 Azure 服务。 使用以下脚本来部署 Azure 服务。 脚本示例假定您在 Windows 计算机上使用 Java 存储库。 若在其他环境中工作，请相应地调整路径、文件扩展名和路径分隔符。

### <a name="create-new-azure-resources"></a>创建新的 Azure 资源

如果尚未创建所需的 Azure 资源，请执行以下步骤：

1. 在命令行环境中，转到存储库克隆副本中的 **[服务]脚本\local_启动**文件夹。

1. 运行以下命令来安装 **pcs** CLI 工具并登录到你的 Azure 帐户：

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. 运行 **start.cmd** 脚本。 该脚本将提示你输入以下信息：

   * 解决方案名称。
   * 要使用的 Azure 订阅。
   * 要使用的 Azure 数据中心的位置。

   该脚本在 Azure 中创建具有解决方案名称的资源组。 此资源组包含解决方案加速器使用的 Azure 资源。 在不再需要相应的资源后，可以删除此资源组。

   该脚本还会向本地计算机添加一组环境变量。 每个变量名称都有前缀**PCS**。 这些环境变量提供了允许远程监视从 Azure 密钥保管库资源读取其配置值的详细信息。

   > [!TIP]
   > 当脚本完成时，它将环境变量保存到一个称为**\<主文件夹\>\\\\\<.pcs 解决方案名称\>.env**的文件。 您可以将它们用于将来的解决方案加速器部署。 请注意，在本地计算机上设置的任何环境变量在运行**docker-compose**时将覆盖**\\服务脚本\\local\\.env**文件中的值。

1. 关闭命令行环境。

### <a name="use-existing-azure-resources"></a>使用现有的 Azure 资源

如果已创建所需的 Azure 资源，请在本地计算机上设置相应的环境变量：
* **PCS_KEYVAULT_NAME**： 密钥保管库资源的名称。
* **PCS_AAD_APPID**： Azure 活动目录 （Azure AD） 应用程序 ID。
* **PCS_AAD_APPSECRET**：Azure AD 应用程序机密。

配置值将从此密钥保管库资源中读取。 这些环境变量可以保存在**\<主文件夹\>\\\\\<.pcs 解决方案名称\>.env**文件中。 请注意，运行 **docker-compose** 时，在本地计算机上设置的环境变量将覆盖 **services\\scripts\\local\\.env** 文件中的值。

微服务所需的某些配置存储在在初始部署时创建的密钥保管库的实例中。 应根据需要修改密钥保管库中的相应变量。

## <a name="run-the-microservices"></a>运行微服务

在本部分，我们将运行远程监视微服务。 可以运行：

* 本机的 Web UI。
* Docker 中的 Azure IoT 设备模拟、Auth 和 Azure 流分析管理器服务。
* IntelliJ 的微服务。

### <a name="run-the-device-simulation-service"></a>运行设备模拟服务

打开新的命令提示符窗口。 检查您是否有权访问上一节中**start.cmd**脚本设置的环境变量。

运行以下命令以打开设备模拟服务的 Docker 容器。 该服务模拟远程监视解决方案的设备。

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>运行身份验证服务

打开新的命令提示窗口，然后运行以下命令以打开 Auth 服务的 Docker 容器。 通过使用此服务，可以管理有权访问 Azure IoT 解决方案的用户。

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-stream-analytics-manager-service"></a>运行流分析管理器服务

打开新的命令提示窗口，然后运行以下命令以打开流分析管理器服务的 Docker 容器。 使用此服务，您可以管理流分析作业。 此类管理包括设置作业配置和启动、停止和监视作业状态。

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-your-local-machine"></a>在本地计算机上部署所有其他微服务

以下步骤演示如何在 IntelliJ 中运行远程监视微服务。

#### <a name="import-a-project"></a>导入项目

1. 打开 IntelliJ IDE。
1. 选择**导入项目**。
1. 选择**azure-iot-pcs-远程监视-java_服务\build.sbt**。

#### <a name="create-run-configurations"></a>创建运行配置

1. 选择 **"运行** > **编辑配置**"。
1. 选择 **"添加新配置** > **sbt 任务**"。
1. 输入**名称**，然后输入 **"任务****"作为运行**。
1. 根据要运行的服务选择**工作目录**。
1. 选择 **"应用** > **确定"** 以保存您的选择。
1. 为以下 Web 服务创建运行配置：
    * WebService (services\config)
    * WebService (services\device-telemetry)
    * WebService (services\iothub-manager)
    * WebService (services\storage-adapter)

例如，下图演示如何添加服务的配置：

[![IntelliJ IDE 运行/调试配置窗口的屏幕截图，在左侧窗格中的 sbt 任务列表中突出显示的存储适配器选项，并在右侧窗格中的"名称、任务、工作目录和 VM 参数"框中显示条目。](./media/deploy-locally-intellij/run-configurations.png)](./media/deploy-locally-intellij/run-configurations.png#lightbox)

#### <a name="create-a-compound-configuration"></a>创建复合配置

1. 要一起运行所有服务，请选择"**添加新配置** > **复合**"。
1. 输入**名称**，然后选择**添加 sbt 任务**。
1. 选择 **"应用** > **确定"** 以保存您的选择。

例如，下图演示如何将所有 sbt 任务添加到单个配置：

[![IntelliJ IDE 运行/调试配置窗口的屏幕截图，显示左侧窗格中的"复合"列表中突出显示的所有服务选项，并在右侧窗格中突出显示的 sbt 任务"设备遥测"选项。](./media/deploy-locally-intellij/all-services.png)](./media/deploy-locally-intellij/all-services.png#lightbox)

选择 **"运行"** 以在本地计算机上生成和运行 Web 服务。

每个 Web 服务将打开一个命令提示窗口和 Web 浏览器窗口。 在命令提示符下，您将看到来自正在运行的服务的输出。 浏览器窗口允许您监视状态。 不要关闭命令提示窗口或网页，因为这些操作将停止 Web 服务。

要访问服务的状态，请访问以下 URL：

* IoT 中心管理器：[http://localhost:9002/v1/status](http://localhost:9002/v1/status)
* 设备遥测：[http://localhost:9004/v1/status](http://localhost:9004/v1/status)
* 配置：[http://localhost:9005/v1/status](http://localhost:9005/v1/status)
* 存储适配器：[http://localhost:9022/v1/status](http://localhost:9022/v1/status)

### <a name="start-the-stream-analytics-job"></a>启动流分析作业

遵循以下步骤启动流分析作业：

1. 转到[Azure 门户](https://portal.azure.com)。
1. 转到为解决方案创建**的资源组**。 该资源组的名称是在运行 **start.cmd** 脚本时为解决方案选择的名称。
1. 在资源列表中选择**流分析作业**。
1. 在"流分析"作业**概述**页上，选择"**开始"** 按钮，然后选择 **"开始"** 以启动作业。

### <a name="run-the-web-ui"></a>运行 Web UI

此步骤启动 Web UI。 打开新的命令提示符窗口。 检查您是否有权访问**start.cmd**脚本设置的环境变量。 转到存储库本地副本中的**webui**文件夹，然后运行以下命令：

```cmd
npm install
npm start
```

**启动**命令完成后，浏览器将在 地址[http://localhost:3000/dashboard](http://localhost:3000/dashboard)显示页面。 此页面上出现的错误在意料之中。 要查看应用程序时没有错误，请完成以下步骤。

### <a name="configure-and-run-nginx"></a>配置和运行 Nginx

设置反向代理服务器，将 Web 应用程序链接到本地计算机上运行的微服务：

1. 将存储库本地副本中的**webui_scripts_localhost**文件夹中的**nginx.conf**文件复制到**nginx_conf**安装目录。
1. 运行恩金克斯。

有关运行 Nginx 的详细信息，请参阅[适用于 Windows 的 nginx。](https://nginx.org/en/docs/windows.html)

### <a name="connect-to-the-dashboard"></a>连接到仪表板

要访问远程监视解决方案仪表板，请转到http://localhost:9000浏览器中。

## <a name="clean-up"></a>清除

为避免不必要的费用，请在完成测试后从 Azure 订阅中删除云服务。 要删除服务，请转到[Azure 门户](https://ms.portal.azure.com)，然后删除**start.cmd**脚本创建的资源组。

您还可以删除从 GitHub 克隆源代码时创建的远程监视存储库的本地副本。

## <a name="next-steps"></a>后续步骤

部署远程监视解决方案后，接下来请[探索解决方案仪表板的功能](quickstart-remote-monitoring-deploy.md)。
