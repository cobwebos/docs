---
title: 在本地部署远程监视解决方案 - Azure | Microsoft Docs
description: 本操作方法指南展示了如何将远程监视解决方案加速器部署到本地计算机，用于测试和开发。
author: asdonald
manager: timlt
ms.author: asdonald
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 09/26/2018
ms.topic: conceptual
ms.openlocfilehash: 7007b1406dbcfab3af4700418ac2ce07b9e521c0
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2018
ms.locfileid: "47407427"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally"></a>在本地部署远程监视解决方案加速器

本文展示了如何将远程监视解决方案加速器部署到本地计算机，用于测试和开发。 本文中介绍的此方法将微服务部署到本地 Docker 容器并使用云中的 IoT 中心、Cosmos DB 和 Azure 时序见解服务。 若要了解如何在本地计算机的 IDE 中运行远程监视解决方案加速器，请参阅 GitHub 上的 [Starting Microservices on local environment](https://github.com/Azure/remote-monitoring-services-java/blob/master/docs/LOCAL_DEPLOYMENT.md)（在本地环境中启动微服务）。

## <a name="prerequisites"></a>先决条件

若要部署远程监视解决方案加速器使用的 Azure 服务，需要一个有效的 Azure 订阅。

如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用](http://azure.microsoft.com/pricing/free-trial/)。

若要完成本地部署，需要使用在本地开发计算机上安装的以下工具：

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Docker compose](https://docs.docker.com/compose/install/)
* [Node.js v8](https://nodejs.org/) - 此软件是 PCS CLI 的先决条件，脚本使用 PCS CLI 来创建 Azure 资源。 请勿使用 Node.js v10。

> [!NOTE]
> 这些工具在许多平台上可用，包括 Windows、Linux 和 iOS。

### <a name="download-the-source-code"></a>下载源代码

远程监视源代码 GitHub 存储库中包含你在下载、配置和运行包含微服务的 Docker 映像时所需的 Docker 配置文件。 若要克隆并创建本地版本的存储库，请通过命令行环境导航到本地计算机上的合适文件夹，然后运行以下命令之一：

若要下载最新版本的 Java 微服务实现，请运行：

```cmd/sh
git clone https://github.com/Azure/remote-monitoring-services-java.git
```

若要下载最新版本的 .NET 微服务实现，请运行：

```cmd\sh
git clone https://github.com/Azure/remote-monitoring-services-dotnet.git
```

> [!NOTE]
> 这些命令除了下载用于在本地运行微服务的脚本，还下载所有微服务的源代码。 虽然运行 Docker 中的微服务时不需要源代码，但是如果以后计划修改解决方案加速器并在本地测试更改，则源代码非常有用。

## <a name="deploy-the-azure-services"></a>部署 Azure 服务

虽然本文展示了如何在本地运行微服务，但它们依赖于在云中运行的 Azure 服务。 可以[通过 Azure 门户手动](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Manual-steps-to-create-azure-resources-for-local-setup)部署这些 Azure 服务，也可以使用提供的脚本进行部署。 以下脚本示例假定你在 Windows 计算机上使用 .NET 存储库。 若在其他环境中工作，请相应地调整路径、文件扩展名和路径分隔符。 若要使用提供的脚本来执行以下操作：

### <a name="create-new-azure-resources"></a>创建新的 Azure 资源

如果尚未创建所需的 Azure 资源，请执行以下步骤：

1. 在命令行环境中，导航到已克隆的存储库副本中的 **remote-monitoring-services-dotnet\scripts\local\launch** 文件夹。

2. 运行 **start.cmd** 脚本并按提示操作。 该脚本会提示你登录到 Azure 帐户并重启脚本。 该脚本然后会提示你输入以下信息：
    * 解决方案名称。
    * 要使用的 Azure 订阅。
    * 要使用的 Azure 数据中心的位置。

    脚本使用解决方案名称在 Azure 中创建资源组。 此资源组包含解决方案加速器使用的 Azure 资源。

3. 脚本在完成后会显示环境变量的列表。 按照命令输出中的说明操作，将这些变量保存到 **remote-monitoring-services-dotnet\\scripts\\local\\.env** 文件。

### <a name="use-existing-azure-resources"></a>使用现有的 Azure 资源

若已创建所需的 Azure 资源，请使用所需的值编辑 **remote-monitoring-services-dotnet\\scripts\\local\\.env** 文件中的环境变量定义。 **.env** 文件包含的信息详述了在何处查找所需的值。

## <a name="run-the-microservices-in-docker"></a>运行 Docker 中的微服务

在本地 Docker 容器中运行的微服务需访问在 Azure 中运行的服务。 可以使用以下命令测试 Docker 环境的 Internet 连接。此命令先启动一个小的容器，然后尝试 ping 一个 Internet 地址：

```cmd/sh
docker run --rm -ti library/alpine ping google.com
```

若要运行解决方案加速器，请导航到命令行环境中的 **remote-monitoring-services-dotnet\\scripts\\local** 文件夹并运行以下命令：

```cmd\sh
docker-compose up
```

首次运行此命令时，Docker 会从 Docker 中心下载微服务映像以在本地构建容器。 在后续运行中，Docker 会立即运行容器。

可以使用一个单独的 shell 来查看来自容器的日志。 首先，使用 `docker ps -a` 命令查找容器 ID。 然后，使用 `docker logs {container-id} --tail 1000` 查看指定容器的最后 1000 个日志条目。

若要访问远程监视解决方案仪表板，请在浏览器中导航到 [http://localhost:8080](http://localhost:8080)。

## <a name="clean-up"></a>清理

为避免产生不必要的费用，在完成测试后，请从 Azure 订阅中删除云服务。 若要删除这些服务，最简单的方法是先导航到 [Azure 门户](https://ms.portal.azure.com)，然后删除运行 **start.cmd** 脚本时创建的资源组。

使用 `docker-compose down --rmi all` 命令删除 Docker 映像并释放本地计算机上的空间。 还可以删除在从 GitHub 克隆源代码时创建的远程监视存储库的本地副本。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 设置本地开发环境
> * 配置解决方案加速器
> * 部署解决方案加速器
> * 登录到解决方案加速器

现在已部署远程监视解决方案，下一步是[浏览解决方案仪表板的功能](quickstart-remote-monitoring-deploy.md)。

<!-- Next tutorials in the sequence -->
