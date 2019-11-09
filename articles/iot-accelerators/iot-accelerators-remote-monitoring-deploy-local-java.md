---
title: 在本地部署远程监视解决方案-IntelliJ IDE-Azure |Microsoft Docs
description: 本操作方法指南介绍了如何通过使用 IntelliJ 进行测试和开发，将远程监视解决方案加速器部署到您的本地计算机。
author: v-krghan
manager: dominicbetts
ms.author: v-krghan
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: 779ee1e057d74b11c5e0ba58dc2fd32b803f1e0e
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888815"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>在本地部署远程监视解决方案加速器 - IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

本文展示了如何将远程监视解决方案加速器部署到本地计算机，用于测试和开发。 你将了解如何在 IntelliJ 中运行微服务。 本地微服务部署将使用以下云服务： IoT 中心、Azure Cosmos DB、Azure 流分析和 Azure 时序见解。

若要在本地计算机上的 Docker 中运行远程监视解决方案加速器，请参阅[在本地部署远程监视解决方案加速器 - Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md)。

## <a name="prerequisites"></a>必备组件

若要部署远程监视解决方案加速器使用的 Azure 服务，需要一个有效的 Azure 订阅。

如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/)。

### <a name="machine-setup"></a>计算机设置

若要完成本地部署，需要使用在本地开发计算机上安装的以下工具：

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [IntelliJ 社区版](https://www.jetbrains.com/idea/download/)
* [IntelliJ Scala 插件](https://plugins.jetbrains.com/plugin/1347-scala)
* [IntelliJ SBT 插件](https://plugins.jetbrains.com/plugin/5007-sbt)
* [IntelliJ SBT 执行器插件](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/)

Node.js v8 是脚本用来创建 Azure 资源的 PC CLI 的必备组件。 请勿使用 Node.js v10。

> [!NOTE]
> IntelliJ IDE 适用于 Windows 和 Mac。

## <a name="download-the-source-code"></a>下载源代码

远程监视源代码存储库中包含运行微服务 Docker 映像时所需的源代码和 Docker 配置文件。

若要克隆和创建本地版本的存储库，请使用命令行环境来切换到本地计算机上的合适文件夹。 然后运行以下命令集之一来克隆 Java 存储库：

* 若要下载最新版本的 Java 微服务实现，请运行以下命令：

  ```cmd/sh
  git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git
  ```

* 若要检索最新的子模块，请运行以下命令：

   ```cmd/sh
   cd azure-iot-pcs-remote-monitoring-java
   git submodule foreach git pull origin master
    ```

> [!NOTE]
> 这些命令除了下载用于在本地运行微服务的脚本，还下载所有微服务的源代码。 不需要源代码即可在 Docker 中运行微服务。 但如果您以后计划修改解决方案加速器并在本地测试您的更改，则源代码非常有用。

## <a name="deploy-the-azure-services"></a>部署 Azure 服务

虽然本文展示了如何在本地运行微服务，但它们依赖于在云中运行的 Azure 服务。 使用以下脚本来部署 Azure 服务。 脚本示例假设你在 Windows 计算机上使用 Java 存储库。 若在其他环境中工作，请相应地调整路径、文件扩展名和路径分隔符。

### <a name="create-new-azure-resources"></a>创建新的 Azure 资源

如果尚未创建所需的 Azure 资源，请执行以下步骤：

1. 在命令行环境中，请在存储库的克隆副本中转到 **\services\scripts\local\launch**文件夹。

1. 运行以下命令来安装 **pcs** CLI 工具并登录到你的 Azure 帐户：

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. 运行 **start.cmd** 脚本。 该脚本将提示你输入以下信息：

   * 解决方案名称。
   * 要使用的 Azure 订阅。
   * 要使用的 Azure 数据中心的位置。

   此脚本会在 Azure 中创建一个包含解决方案名称的资源组。 此资源组包含解决方案加速器使用的 Azure 资源。 不再需要相应的资源之后，可以删除此资源组。

   该脚本还会将一组环境变量添加到您的本地计算机。 每个变量名称都具有前缀**pc**。 这些环境变量提供的详细信息允许远程监视从 Azure Key Vault 资源读取其配置值。

   > [!TIP]
   > 当脚本完成时，它会将环境变量保存到名为 **\<主文件夹\>\\pc\\\<解决方案名称\>env**的文件中。 你可以将其用于将来的解决方案加速器部署。 请注意，在运行**docker 撰写**时，本地计算机上设置的任何环境变量都将覆盖**服务\\脚本\\本地\\env**文件中的值。

1. 关闭命令行环境。

### <a name="use-existing-azure-resources"></a>使用现有的 Azure 资源

如果已创建所需的 Azure 资源，请在本地计算机上设置相应的环境变量：
* **PCS_KEYVAULT_NAME**： Key Vault 资源的名称。
* **PCS_AAD_APPID**： Azure Active Directory （Azure AD）应用程序 ID。
* **PCS_AAD_APPSECRET**： Azure AD 应用程序密钥。

将从此 Key Vault 资源读取配置值。 这些环境变量可保存在 **\<主文件夹\>\\pc\\\<** 部署中\>的 env 文件。 请注意，运行 **docker-compose\\ 时，在本地计算机上设置的环境变量将覆盖 \\services\\scripts**local **.env** 文件中的值。

微服务所需的某些配置存储在最初部署时创建的 Key Vault 实例中。 应根据需要修改 key vault 中的相应变量。

## <a name="run-the-microservices"></a>运行微服务

在本部分，我们将运行远程监视微服务。 运行：

* 本地 web UI。
* Docker 中的 Azure IoT 设备模拟、身份验证和 Azure 流分析管理器服务。
* IntelliJ 中的微服务。

### <a name="run-the-device-simulation-service"></a>运行设备模拟服务

打开新的命令提示符窗口。 检查你是否有权访问上一节中的**start .cmd**脚本设置的环境变量。

运行以下命令，打开设备模拟服务的 Docker 容器。 该服务模拟远程监视解决方案的设备。

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>运行身份验证服务

打开一个新的命令提示符窗口，然后运行以下命令以打开用于身份验证服务的 Docker 容器。 通过使用此服务，你可以管理有权访问 Azure IoT 解决方案的用户。

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-stream-analytics-manager-service"></a>运行流分析管理器服务

打开一个新的命令提示符窗口，然后运行以下命令以打开用于流分析管理器服务的 Docker 容器。 通过此服务，你可以管理流分析作业。 此类管理包括设置作业配置、启动、停止和监视作业状态。

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-your-local-machine"></a>在本地计算机上部署所有其他微服务

以下步骤演示如何在 IntelliJ 中运行远程监视微服务。

#### <a name="import-a-project"></a>导入项目

1. 打开 IntelliJ IDE。
1. 选择 "**导入项目**"。
1. 选择**azure-iot-pcs-remote-monitoring-java\services\build.sbt**。

#### <a name="create-run-configurations"></a>创建运行配置

1. 选择 "**运行** > **编辑配置**"。
1. 选择 " > **sbt 任务** **添加新配置**"。
1. 输入**名称**，然后输入 "**运行**" 作为**任务**。
1. 根据要运行的服务选择**工作目录**。
1. 选择 "应用 **" > "确定"** 以保存你的选择。
1. 为以下 web 服务创建运行配置：
    * WebService (services\config)
    * WebService (services\device-telemetry)
    * WebService (services\iothub-manager)
    * WebService (services\storage-adapter)

例如，下图显示了如何添加服务的配置：

[![IntelliJ IDE 运行/调试配置 "窗口的屏幕截图，显示在左窗格的" sbt 任务 "列表中突出显示的 storageAdapter 选项，以及右窗格中" 名称 "、" 任务 "、" 工作目录 "和" VM 参数 "框中的条目。](./media/deploy-locally-intellij/run-configurations.png)](./media/deploy-locally-intellij/run-configurations.png#lightbox)

#### <a name="create-a-compound-configuration"></a>创建复合配置

1. 若要将所有服务一起运行，请选择 "**添加新配置** > **复合**"。
1. 输入**名称**，然后选择 "**添加 sbt 任务**"。
1. 选择 "应用 **" > "确定"** 以保存你的选择。

例如，下图显示了如何将所有 sbt 任务添加到单个配置中：

[![IntelliJ IDE 运行/调试配置 "窗口的屏幕截图，显示在左窗格的复合列表中突出显示的 AllServices 选项，并在右窗格中突出显示 sbt 任务" "Devicetelemetry" "选项。](./media/deploy-locally-intellij/all-services.png)](./media/deploy-locally-intellij/all-services.png#lightbox)

选择 "**运行**"，在本地计算机上生成并运行 web 服务。

每个 web 服务都将打开一个命令提示符窗口和 web 浏览器窗口。 在命令提示符处，可以看到正在运行的服务的输出。 浏览器窗口允许你监视状态。 请不要关闭命令提示符窗口或网页，因为这些操作会停止 web 服务。

若要访问服务的状态，请使用以下 Url：

* IoT 中心管理器： [http://localhost:9002/v1/status](http://localhost:9002/v1/status)
* 设备遥测： [http://localhost:9004/v1/status](http://localhost:9004/v1/status)
* config： [http://localhost:9005/v1/status](http://localhost:9005/v1/status)
* 存储适配器： [http://localhost:9022/v1/status](http://localhost:9022/v1/status)

### <a name="start-the-stream-analytics-job"></a>启动流分析作业

遵循以下步骤启动流分析作业：

1. 转到 [Azure 门户](https://portal.azure.com)。
1. 中转到为解决方案创建的**资源组**。 该资源组的名称是在运行 **start.cmd** 脚本时为解决方案选择的名称。
1. 在资源列表中选择 "**流分析作业**"。
1. 在 "流分析作业**概述**" 页上，选择 "**开始**" 按钮，然后选择 "**启动**" 以启动作业。

### <a name="run-the-web-ui"></a>运行 Web UI

此步骤启动 Web UI。 打开新的命令提示符窗口。 检查你是否有权访问由**start .cmd**脚本设置的环境变量。 中转到存储库本地副本中的**webui**文件夹，并运行以下命令：

```cmd
npm install
npm start
```

**开始**命令完成后，浏览器会在地址[http://localhost:3000/dashboard](http://localhost:3000/dashboard)显示页面。 此页面上出现的错误在意料之中。 若要查看应用程序但不发生错误，请完成以下步骤。

### <a name="configure-and-run-nginx"></a>配置和运行 Nginx

设置反向代理服务器，将 web 应用程序链接到在本地计算机上运行的微服务：

1. 将**nginx**文件从存储库本地副本中的**webui\scripts\localhost**文件夹复制到**nginx\conf**安装目录中。
1. 运行 Nginx。

有关运行 Nginx 的详细信息，请参阅[Nginx For Windows](https://nginx.org/en/docs/windows.html)。

### <a name="connect-to-the-dashboard"></a>连接到仪表板

若要访问远程监视解决方案仪表板，请在浏览器中转到 http://localhost:9000。

## <a name="clean-up"></a>清理

若要避免不必要的费用，请在完成测试后，从 Azure 订阅中删除云服务。 若要删除服务，请前往[Azure 门户](https://ms.portal.azure.com)，并删除**启动 .cmd**脚本的资源组。

你还可以删除从 GitHub 克隆源代码时创建的远程监视存储库的本地副本。

## <a name="next-steps"></a>后续步骤

部署远程监视解决方案后，接下来请[探索解决方案仪表板的功能](quickstart-remote-monitoring-deploy.md)。
