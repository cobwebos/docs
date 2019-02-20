---
title: 在本地部署远程监视解决方案 - Docker - Azure | Microsoft Docs
description: 本操作指南展示了如何使用 Docker 将远程监视解决方案加速器部署到本地计算机，以用于测试和开发。
author: avneet723
manager: hegate
ms.author: avneet723
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 252aacfeb85f23699c6a2e2ac1f457f9e2b59b0e
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2019
ms.locfileid: "55997177"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---docker"></a>在本地部署远程监视解决方案加速器 - Docker

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

本文展示了如何将远程监视解决方案加速器部署到本地计算机，用于测试和开发。 了解如何将微服务部署到本地 Docker 容器。 本地微服务部署使用以下云服务：云中的 IoT Hub、Cosmos DB、Azure 流分析和 Azure 时序见解服务。

若要在本地计算机上的 IDE 中运行远程监视解决方案加速器，请参阅[在本地部署远程监视解决方案加速器 - Visual Studio](iot-accelerators-remote-monitoring-deploy-local.md)。

## <a name="prerequisites"></a>先决条件

若要部署远程监视解决方案加速器使用的 Azure 服务，需要一个有效的 Azure 订阅。

如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/)。

### <a name="machine-setup"></a>计算机设置

若要完成本地部署，需要使用在本地开发计算机上安装的以下工具：

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/) - 如果你打算对微服务进行更改。
* [Node.js v8](https://nodejs.org/) - 此软件是 PCS CLI 的先决条件，脚本使用 PCS CLI 来创建 Azure 资源。 请勿使用 Node.js v10。

> [!NOTE]
> 这些工具在许多平台上可用，包括 Windows、Linux 和 iOS。

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices-in-docker"></a>运行 Docker 中的微服务

打开新的命令提示符，以确保能够访问 **start.cmd** 脚本设置的环境变量。 在 Windows 上，可以通过运行以下命令来验证是否设置了环境变量：

```cmd
set PCS
```

该命令会显示 **start.cmd** 脚本设置的所有环境变量。

请确保 Docker 正在本地计算机上运行。
> [!NOTE]
> 如果 Docker 在 Windows 上运行，则必须运行 [Linux 容器](https://docs.docker.com/docker-for-windows/)。

本地 Docker 容器中运行的微服务需要访问 Azure 云服务。 可使用以下命令从容器内部 ping 某个 Internet 地址，以测试 Docker 环境的 Internet 连接：

```cmd/sh
docker run --rm -ti library/alpine ping google.com
```

若要运行解决方案加速器，请导航到命令行环境中的 **services\\scripts\\local** 文件夹并运行以下命令：

```cmd/sh
docker-compose up
```

> [!NOTE] 
> 在运行 `docker-compose up` 之前，请确保与 Docker [共享本地驱动器](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/issues/115)。

首次运行此命令时，Docker 会从 Docker 中心下载微服务映像以在本地构建容器。 在后续运行中，Docker 会立即运行容器。

> [!TIP]
> Microsoft 经常会发布具有新功能的新 Docker 映像。 在提取最新映像之前，可使用以下命令集来清理本地 Docker 容器和相应的映像：

```cmd/sh
docker list
docker rm <list_of_containers>
docker rmi <list_of_images>
```

可以使用一个单独的 shell 来查看来自容器的日志。 首先，使用 `docker ps` 命令查找容器 ID。 然后，使用 `docker logs {container-id} --tail 1000` 查看指定容器的最后 1000 个条目。

### <a name="start-the-stream-analytics-job"></a>启动流分析作业

遵循以下步骤启动流分析作业：

1. 导航到 [Azure 门户](https://portal.azure.com)。
1. 导航到为解决方案创建的**资源组**。 该资源组的名称是在运行 **start.cmd** 脚本时为解决方案选择的名称。
1. 在资源列表中单击“流分析作业”。
1. 在流分析作业的“概述”页上，单击“启动”按钮。 然后单击“启动”以立即启动该作业。

### <a name="connect-to-the-dashboard"></a>连接到仪表板

若要访问远程监视解决方案仪表板，请在浏览器中导航到 [http://localhost:8080](http://localhost:8080)。 现在可以使用 Web UI 和本地微服务。

## <a name="clean-up"></a>清理

为避免产生不必要的费用，在完成测试后，请从 Azure 订阅中删除云服务。 若要删除这些服务，请导航到 [Azure 门户](https://ms.portal.azure.com)，并删除 **start.cmd** 脚本创建的资源组。

使用 `docker-compose down --rmi all` 命令删除 Docker 映像并释放本地计算机上的空间。 还可以删除在从 GitHub 克隆源代码时创建的远程监视存储库的本地副本。

## <a name="next-steps"></a>后续步骤

部署远程监视解决方案后，接下来请[探索解决方案仪表板的功能](quickstart-remote-monitoring-deploy.md)。
