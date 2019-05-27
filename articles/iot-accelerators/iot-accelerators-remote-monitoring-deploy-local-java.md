---
title: 在本地部署远程监视解决方案（通过 IntelliJ IDE）- Azure | Microsoft Docs
description: 本操作指南展示了如何使用 IntelliJ 将远程监视解决方案加速器部署到本地计算机，以用于测试和开发。
author: v-krghan
manager: dominicbetts
ms.author: v-krghan
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: 2b55fea69fe1affb6cab5d360f1e8355c3bb720d
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2019
ms.locfileid: "66015436"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>在本地部署远程监视解决方案加速器 - IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

本文展示了如何将远程监视解决方案加速器部署到本地计算机，用于测试和开发。 你将了解如何在 IntelliJ 中运行微服务。 本地微服务部署使用以下云服务：云中的 IoT Hub、Cosmos DB、Azure 流分析和 Azure 时序见解服务。

若要在本地计算机上的 Docker 中运行远程监视解决方案加速器，请参阅[在本地部署远程监视解决方案加速器 - Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md)。

## <a name="prerequisites"></a>必备组件

若要部署远程监视解决方案加速器使用的 Azure 服务，需要一个有效的 Azure 订阅。

如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅[Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

### <a name="machine-setup"></a>计算机设置

若要完成本地部署，需要使用在本地开发计算机上安装的以下工具：

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [IntelliJ 社区版](https://www.jetbrains.com/idea/download/)
* [IntelliJ 插件 Scala](https://plugins.jetbrains.com/plugin/1347-scala)
* [IntelliJ 插件 SBT](https://plugins.jetbrains.com/plugin/5007-sbt)
* [IntelliJ 插件 SBT 执行器](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/) - 此软件是 PCS CLI 的先决条件，脚本使用 PCS CLI 来创建 Azure 资源。 请勿使用 Node.js v10。

> [!NOTE]
> IntelliJ IDE 适用于 Windows 和 Mac。

## <a name="download-the-source-code"></a>下载源代码

远程监视源代码存储库中包含运行微服务 Docker 映像时所需的源代码和 Docker 配置文件。

若要克隆并创建本地版本的存储库，请通过命令行环境导航到本地计算机上的合适文件夹。 然后，运行下列命令集之一来克隆 java 存储库：

若要下载最新版本的 java 微服务实现，请运行：


```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-java
git submodule foreach git pull origin master
```

> [!NOTE]
> 这些命令除了下载用于在本地运行微服务的脚本，还下载所有微服务的源代码。 虽然运行 Docker 中的微服务时不需要源代码，但是如果以后计划修改解决方案加速器并在本地测试更改，则源代码非常有用。

## <a name="deploy-the-azure-services"></a>部署 Azure 服务

虽然本文展示了如何在本地运行微服务，但它们依赖于在云中运行的 Azure 服务。 使用以下脚本来部署 Azure 服务。 以下脚本示例假定你在 Windows 计算机上使用 java 存储库。 若在其他环境中工作，请相应地调整路径、文件扩展名和路径分隔符。

### <a name="create-new-azure-resources"></a>创建新的 Azure 资源

如果尚未创建所需的 Azure 资源，请执行以下步骤：

1. 在命令行环境中，导航到已克隆的存储库副本中的 **\services\scripts\local\launch** 文件夹。

1. 运行以下命令来安装 **pcs** CLI 工具并登录到你的 Azure 帐户：

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. 运行 **start.cmd** 脚本。 该脚本将提示你输入以下信息：
   * 解决方案名称。
   * 要使用的 Azure 订阅。
   * 要使用的 Azure 数据中心的位置。

     脚本使用解决方案名称在 Azure 中创建资源组。 此资源组包含解决方案加速器使用的 Azure 资源。 不再需要相关的资源后，可以删除此资源组。

     此脚本还向本地计算机添加一组前缀为 **PCS** 的环境变量。 这些环境变量提供远程监视，以便能够从 Azure 密钥保管库资源中读取的详细的信息。 此密钥保管库资源是远程监视将读取的位置从其配置值。

     > [!TIP]
     > 在此脚本完成时，它还会将环境变量保存到一个名为 **\<your home folder\>\\.pcs\\\<solution name\>.env** 的文件中。 可以将它们用于将来的解决方案加速器部署。 请注意，运行 **docker-compose** 时，在本地计算机上设置的任何环境变量将覆盖 **services\\scripts\\local\\.env** 文件中的值。

1. 退出命令行环境。

### <a name="use-existing-azure-resources"></a>使用现有的 Azure 资源

如果已创建了所需的 Azure 资源，请在本地计算机上创建相应的环境变量。
设置以下环境变量：
* **PCS_KEYVAULT_NAME** -Azure 密钥保管库资源的名称
* **PCS_AAD_APPID** -AAD 应用程序 ID
* **PCS_AAD_APPSECRET** -AAD 应用程序机密

将此 Azure 密钥保管库资源中读取配置值。 这些环境变量可以保存在**\<主文件夹\>\\.pcs\\\<解决方案名称\>.env**从部署的文件。 请注意，运行 **docker-compose** 时，在本地计算机上设置的环境变量将覆盖 **services\\scripts\\local\\.env** 文件中的值。

某些微服务所需的配置存储中的实例**Key Vault**创建初始部署。 根据需要应修改在密钥保管库中的相应变量。

## <a name="run-the-microservices"></a>运行微服务

在本部分，我们将运行远程监视微服务。 在本机运行 Web UI，在 Docker 中运行设备模拟、身份验证和 ASA 管理器服务，并在 IntelliJ 中运行微服务。

### <a name="run-the-device-simulation-service"></a>运行设备模拟服务

打开一个新的命令提示符窗口，以确保能够访问在上一部分中由 **start.cmd** 脚本设置的环境变量。

运行以下命令，启动设备模拟服务的 Docker 容器。 该服务模拟远程监视解决方案的设备。

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>运行身份验证服务

打开一个新的命令提示符窗口，并运行以下命令启动身份验证服务的 Docker 容器。 该服务可用于管理有权访问 Azure IoT 解决方案的用户。

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-asa-manager-service"></a>运行 ASA 管理器服务

打开一个新的命令提示符窗口，并运行以下命令启动 ASA 管理器服务的 Docker 容器。 该服务可用于管理 Azure 流分析 (ASA) 作业，包括设置配置以及启动、停止和监视其状态。

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>在本地计算机上部署其他所有微服务

以下步骤展示了如何在 IntelliJ 中运行远程监视微服务：

#### <a name="import-project"></a>导入项目

1. 启动 IntelliJ IDE
1. 选择“导入项目”，然后选择“azure-iot-pcs-remote-monitoring-java\services\build.sbt”

#### <a name="create-run-configurations"></a>创建运行配置

1. 选择“运行”>“编辑配置”
1. 选择“添加新配置”>“sbt 任务” 
1. 输入**名称**并将**任务**输入为“run” 
1. 根据要运行的服务选择**工作目录**
1. 单击“应用”>“确定”以保存你的选择。
1. 创建以下服务的运行配置：
    * WebService (services\config)
    * WebService (services\device-telemetry)
    * WebService (services\iothub-manager)
    * WebService (services\storage-adapter)

例如下, 图显示添加的服务配置：

[![Add-Configuration](./media/deploy-locally-intellij/run-configurations.png)](./media/deploy-locally-intellij/run-configurations.png#lightbox)


#### <a name="create-compound-configuration"></a>创建复合配置

1. 若要运行所有服务，请同时选择“添加新配置”>“复合”
1. 输入**名称**并**添加 sbt 任务**
1. 单击“应用”>“确定”以保存你的选择。

例如下, 图显示了将所有 sbt 任务都添加到单个配置：

[![Add-All-Services](./media/deploy-locally-intellij/all-services.png)](./media/deploy-locally-intellij/all-services.png#lightbox)

单击“运行”，以在本地计算机上生成并运行 Web 服务。

每个 Web 服务将打开一个命令提示符和 Web 浏览器窗口。 在命令提示符下，查看正在运行的服务的输出；在浏览器窗口中可以监视状态。 请不要关闭命令提示符或网页，此操作会停止 Web 服务。


若要访问服务的状态，可以导航到以下 URL：
* IoT 中心管理器 [http://localhost:9002/v1/status](http://localhost:9002/v1/status)
* 设备遥测 [http://localhost:9004/v1/status](http://localhost:9004/v1/status)
* 配置 [http://localhost:9005/v1/status](http://localhost:9005/v1/status)
* 存储适配器 [http://localhost:9022/v1/status](http://localhost:9022/v1/status)


### <a name="start-the-stream-analytics-job"></a>启动流分析作业

遵循以下步骤启动流分析作业：

1. 导航到 [Azure 门户](https://portal.azure.com)。
1. 导航到为解决方案创建的**资源组**。 该资源组的名称是在运行 **start.cmd** 脚本时为解决方案选择的名称。
1. 在资源列表中单击“流分析作业”。
1. 在流分析作业的“概述”页上，单击“启动”按钮。 然后单击“启动”以立即启动该作业。

### <a name="run-the-web-ui"></a>运行 Web UI

此步骤启动 Web UI。 打开一个新的命令提示符窗口，以确保能够访问由 **start.cmd** 脚本设置的环境变量。 导航到存储库本地副本中的 **webui** 文件夹，并运行以下命令：

```cmd
npm install
npm start
```

启动完成后，在浏览器显示页面**http:\//localhost:3000 / 仪表板**。 此页面上出现的错误在意料之中。 若要在无错误的情况下查看应用程序，请完成以下步骤。

### <a name="configure-and-run-nginx"></a>配置并运行 NGINX

设置反向代理服务器，以链接本地计算机上运行的 Web 应用程序和微服务：

* 将存储库的本地副本的 **webui\scripts\localhost** 文件夹中的 **nginx.conf** 文件复制到 **nginx\conf** 安装目录。
* 运行 **nginx**。

有关运行 **nginx** 的详细信息，请参阅[适用于 Windows 的 nginx](https://nginx.org/en/docs/windows.html)。

### <a name="connect-to-the-dashboard"></a>连接到仪表板

若要访问远程监视解决方案仪表板，导航到 http:\//localhost:9000 在浏览器中的。

## <a name="clean-up"></a>清理

为避免产生不必要的费用，在完成测试后，请从 Azure 订阅中删除云服务。 若要删除这些服务，请导航到 [Azure 门户](https://ms.portal.azure.com)，并删除 **start.cmd** 脚本创建的资源组。

还可以删除在从 GitHub 克隆源代码时创建的远程监视存储库的本地副本。

## <a name="next-steps"></a>后续步骤

部署远程监视解决方案后，接下来请[探索解决方案仪表板的功能](quickstart-remote-monitoring-deploy.md)。
