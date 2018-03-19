---
title: "在本地部署远程监视解决方案 - Azure | Microsoft Docs"
description: "本教程展示了如何将远程监视预配置解决方案部署到本地计算机以用于测试和开发。"
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 03/07/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 77f40e2f10cbdb9930a22a4248e19bb3356af7af
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="deploy-the-remote-monitoring-preconfigured-solution-locally"></a>在本地部署远程监视预配置解决方案

本文展示了如何将远程监视预配置解决方案部署到本地计算机以用于测试和开发。 此方法将微服务部署到本地 Docker 容器并使用云中的 IoT 中心、Cosmos DB 和 Azure 存储服务。 你将使用预配置解决方案 (PCS) CLI 来部署 Azure 云服务。

## <a name="prerequisites"></a>先决条件

若要部署远程监视预配置解决方案使用的 Azure 服务，需要一个有效的 Azure 订阅。

如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用](http://azure.microsoft.com/pricing/free-trial/)。

若要完成本地部署，需要使用在本地开发计算机上安装的以下工具：

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Docker compose](https://docs.docker.com/compose/install/)
* [Node.js](https://nodejs.org/) - 此软件是 PCS CLI 的先决条件。
* PCS CLI

> [!NOTE]
> 这些工具在许多平台上可用，包括 Windows、Linux 和 iOS。

### <a name="install-the-pcs-cli"></a>安装 PCS CLI

要安装 CLI，请在命令行环境中运行以下命令：

```cmd/sh
npm install iot-solutions -g
```

有关 CLI 的详细信息，请参阅 [How to use the CLI](https://github.com/Azure/pcs-cli/blob/master/README.md)（如何使用 CLI）。

## <a name="deploy-the-azure-services"></a>部署 Azure 服务

虽然本文展示了如何在本地运行微服务，但它们依赖于在云中运行的三个 Azure 服务。 你可以通过 Azure 门户或使用 PCS CLI 手动部署这些 Azure 服务。 本文展示了如何使用 `pcs` 工具。

### <a name="sign-in-to-the-cli"></a>登录到 CLI

在部署预配置解决方案之前，必须先使用 CLI 登录到 Azure 订阅，如下所示：

```cmd/sh
pcs login
```

按照屏幕说明完成登录过程。

### <a name="run-a-local-deployment"></a>运行本地部署

使用以下命令启动本地部署：

```cmd/pcs
pcs -s local
```

该脚本将提示你输入以下信息：

* 解决方案名称。
* 要使用的 Azure 订阅。
* 要使用的 Azure 数据中心的位置。

该脚本在你的 Azure 订阅中的资源组中创建一个 IoT 中心实例、一个 Cosmos DB 实例和一个 Azure 存储帐户。 资源组的名称是你在运行 `pcs` 工具时选择的解决方案的名称。

脚本需要几分钟的时间来运行。 在脚本完成时，你会看到消息 `Copy the following environment variables to /scripts/local/.env file:`。 根据消息复制环境变量定义，在稍后的步骤中需要使用它们。

## <a name="download-the-source-code"></a>下载源代码

远程监视源代码存储库中包含你在下载、配置和运行包含微服务的 Docker 映像时所需的 Docker 配置文件。 若要克隆存储库，请导航到本地计算机上的合适文件夹并运行以下命令之一：

若要安装微服务的 Java 实现，请运行：

```cmd/sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-java
```

若要安装微服务的 .Net 实现，请运行：

```cmd\sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet
```

这些命令将下载所有微服务的源代码。 虽然运行 Docker 中的微服务时不需要源代码，但是如果以后计划修改预配置解决方案并在本地测试更改，则源代码非常有用。

## <a name="run-the-microservices-in-docker"></a>运行 Docker 中的微服务

若要运行 Docker 中的微服务，请首先编辑存储库的本地副本中的 **scripts\\local\\.env** 文件。 将该文件的整个内容替换为之前在运行 `pcs` 命令时记下的环境变量定义。 这些环境变量使得 Docker 容器中的微服务能够连接到由 `pcs` 工具创建的 Azure 服务。

若要运行预配置解决方案，请导航到命令行环境中的 **scripts\local** 文件夹并运行以下命令：

```cmd\sh
docker-compose up
```

首次运行此命令时，Docker 会从 Docker 中心下载微服务映像以在本地构建容器。 在后续运行中，Docker 会立即运行容器。

可以使用一个单独的 shell 来查看来自容器的日志。 首先，使用 `docker ps -a` 命令查找容器 ID。 然后，使用 `docker logs {container-id} --tail 1000` 查看指定容器的最后 1000 个日志条目。

若要访问远程监视解决方案仪表板，请在浏览器中导航到 [http://localhost:8080](http://localhost:8080)。

## <a name="tidy-up"></a>整理

为避免产生不必要的费用，在完成测试后，请从 Azure 订阅中删除云服务。 删除这些服务的最简单方法是使用 Azure 门户删除由 `pcs` 工具创建的资源组。

使用 `docker-compose down --rmi all` 命令删除 Docker 映像并释放本地计算机上的空间。 还可以删除在从 GitHub 克隆源代码时创建的远程监视存储库的本地副本。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 配置预配置解决方案
> * 部署预配置解决方案
> * 登录到预配置解决方案

现在已部署远程监视解决方案，下一步是[浏览解决方案仪表板的功能](./iot-suite-remote-monitoring-deploy.md)。

<!-- Next tutorials in the sequence -->