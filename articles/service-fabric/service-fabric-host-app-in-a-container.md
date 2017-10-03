---
title: "将容器中的 .NET 应用部署到 Azure Service Fabric | Microsoft Docs"
description: "讲解如何在 Docker 容器中打包 Visual Studio 中的 .NET 应用。 这一新的“容器”应用随后会部署到 Service Fabric 群集。"
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/19/2017
ms.author: mikhegn
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 021c695a91ff46274b2a5174918711d04bcff239
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="deploy-a-net-application-in-a-windows-container-to-azure-service-fabric"></a>将 Windows 容器中的 .NET 应用程序部署到 Azure Service Fabric

本教程介绍如何在 Azure 上的 Windows 容器中部署现有 ASP.NET 应用程序。

本教程介绍如何：

> [!div class="checklist"]
> * 在 Visual Studio 中创建 Docker 项目
> * 容器化现有应用程序
> * 设置与 Visual Studio 和 VSTS 的持续集成

## <a name="prerequisites"></a>先决条件

1. 安装 [Docker CE for Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description)，以便在 Windows 10 上运行容器。
2. 熟悉 [Windows 10 容器快速入门][link-container-quickstart]。
3. 下载 [Fabrikam Fiber CallCenter][link-fabrikam-github] 示例应用程序。
4. 安装 [Azure PowerShell][link-azure-powershell-install]
5. 安装[用于 Visual Studio 2017 的持续交付工具扩展][link-visualstudio-cd-extension]
6. 创建 [Azure 订阅][link-azure-subscription]和 [Visual Studio Team Services 帐户][link-vsts-account]。 
7. [在 Azure 上创建群集](service-fabric-tutorial-create-cluster-azure-ps.md)

## <a name="containerize-the-application"></a>容器化应用程序

现在 [Azure 中已在运行 Service Fabric 群集](service-fabric-tutorial-create-cluster-azure-ps.md)，可以创建和部署容器化应用程序了。 若要开始在容器中运行应用程序，我们需要在 Visual Studio 中添加对项目的 **Docker 支持**。 向应用程序添加 **Docker 支持**后，会发生两件事。 首先，将 Dockerfile 添加到项目。 此新文件描述容器映像的生成方式。 其次，解决方案中会添加一个新的 _docker compose_ 项目。 新项目包含一些 docker-compose 文件。 Docker-compose 文件可用于描述容器的运行方式。

有关使用 [Visual Studio 容器工具][link-visualstudio-container-tools]的详细信息。

>[!NOTE]
>如果这是首次在计算机上运行 Windows 容器映像，Docker CE 必须下载容器的基础映像。 本教程中使用的映像为 14 GB。 继续操作并运行终端命令，拉取基础映像：
>```cmd
>docker pull microsoft/mssql-server-windows-developer
>docker pull microsoft/aspnet:4.6.2
>```

### <a name="add-docker-support"></a>添加 Docker 支持

在 Visual Studio 中打开 [FabrikamFiber.CallCenter.sln][link-fabrikam-github] 文件。

右键单击 **FabrikamFiber.Web** 项目 >“添加” > “Docker 支持”。

### <a name="add-support-for-sql"></a>添加 SQL 支持

此应用程序使用 SQL 作为数据提供程序，因此需要 SQL Server 才能运行该应用程序。 引用 docker compose.override.yml 文件中的 SQL Server 容器映像。

在 Visual Studio 中，打开“解决方案资源管理器”，查找“docker-compose”，然后打开文件“docker-compose.override.yml”。

导航到 `services:` 节点，添加名为 `db:` 的节点，该节点定义容器的 SQL Server 条目。

```yml
  db:
    image: microsoft/mssql-server-windows-developer
    environment:
      sa_password: "Password1"
      ACCEPT_EULA: "Y"
    ports:
      - "1433"
    healthcheck:
      test: [ "CMD", "sqlcmd", "-U", "sa", "-P", "Password1", "-Q", "select 1" ]
      interval: 1s
      retries: 20
```

>[!NOTE]
>可以使用选择的任何 SQL Server 进行本地调试，只要可通过主机访问。 但是，**localdb** 不支持 `container -> host` 通信。

>[!WARNING]
>在容器中运行 SQL Server 不支持保留数据。 当容器停止时，将清除数据。 请勿对生产使用此配置。

导航到 `fabrikamfiber.web:` 节点，然后添加一个名为 `depends_on:` 的子节点。 这样可确保 `db` 服务（SQL Server 容器）在 Web 应用程序 (fabrikamfiber.web) 之前启动。

```yml
  fabrikamfiber.web:
    depends_on:
      - db
```

### <a name="update-the-web-config"></a>更新 Web 配置

返回到 FabrikamFiber.Web 项目，更新 web.config 文件中的连接字符串，以指向容器中的 SQL Server。

```xml
<add name="FabrikamFiber-Express" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />

<add name="FabrikamFiber-DataWarehouse" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />
```

>[!NOTE]
>如果想要在生成 Web 应用程序的发布版本时使用其他 SQL Server，可将另一个连接字符串添加到 web.release.config 文件。

### <a name="test-your-container"></a>测试你的容器

按 **F5**，在容器中运行和调试应用程序。

Edge 会在内部 NAT 网络上使用容器的 IP 地址（通常为 172.x.x.x）打开应用程序的定义启动页。 若要了解有关使用 Visual Studio 2017 在容器中调试应用程序的详细信息，请参阅[本文][link-debug-container]。

![容器中的 fabrikam 示例][image-web-preview]

现在可以在 Service Fabric 应用程序中生成和打包该容器。 在计算机上生成容器映像后，即可将其推送到任何容器注册表并下拉到任何主机上运行。

## <a name="get-the-application-ready-for-the-cloud"></a>让应用程序准备好使用云

在 Azure 中若要让应用程序准备好在 Service Fabric 中运行，需要完成以下两个步骤：

1. 公开想要通过其访问 Service Fabric 群集中的 Web 应用程序的端口。
2. 为应用程序提供生产就绪 SQL 数据库。

### <a name="expose-the-port-for-the-app"></a>公开应用程序的端口
默认情况下，配置的 Service Fabric 群集在 Azure 负载均衡器中的端口 80 处于打开状态，Azure 负载均衡器负责均衡传入到群集的流量。 可以通过 docker-compose.yml 文件公开此端口上的容器。

在 Visual Studio 中，打开“解决方案资源管理器”，查找“docker-compose”，然后打开文件“docker-compose.yml”。

修改 `fabrikamfiber.web:` 节点，添加名为 `ports:` 的子节点。

添加字符串项 `- "80:80"`。 docker-compose.yml 文件应如下所示：

```yml
  version: '3'

  services:
    fabrikamfiber.web:
      image: fabrikamfiber.web
      build:
        context: .\FabrikamFiber.Web
        dockerfile: Dockerfile
      ports:
        - "80:80"
```

### <a name="use-a-production-sql-database"></a>使用生产 SQL 数据库
在生产中运行时，需要将数据保留在数据库中。 目前无法保证将数据保留在容器中，因此无法将生产数据存储在容器中的 SQL Server 中。

我们建议你利用 Azure SQL 数据库。 若要在 Azure 中设置和运行托管 SQL Server，请访问 [Azure SQL 数据库快速入门][link-azure-sql]一文。

>[!NOTE]
>在 FabrikamFiber.Web 项目中，请务必在 web.release.config 文件中将连接字符串更改为 SQL Server。
>
>如果 SQL 数据库不可访问，此应用程序将无法正常运行。 可以选择继续操作，在不使用 SQL Server 的情况下部署应用程序。

## <a name="deploy-with-visual-studio-team-services"></a>使用 Visual Studio Team Services 进行部署

若要使用 Visual Studio Team Services 设置部署，需要安装[用于 Visual Studio 2017 的持续交付工具扩展][link-visualstudio-cd-extension]。 借助此扩展，可以轻松地通过配置 Visual Studio Team Services 部署到 Azure，以及将应用部署到 Service Fabric 群集。

首先，必须在源控件中托管代码。 本部分的其余部分假定使用 **git**。

### <a name="set-up-a-vsts-repo"></a>设置 VSTS 存储库
在 Visual Studio 的右下角，单击“添加到源控件” > “Git”（或想要使用的任何选项）。

![按“源控件”按钮][image-source-control]

在“团队资源管理器”窗格中，按“发布 Git 存储库”。

选择 VSTS 存储库名称并按“存储库”。

![将存储库发布到 VSTS][image-publish-repo]

现在，代码与 VSTS 源存储库同步，可以配置持续集成和持续交付。

### <a name="setup-continuous-delivery"></a>设置持续交付

在“解决方案资源管理器”中，右键单击“解决方案” > “配置持续交付”。

选择 Azure 订阅。

将“主机类型”设置为“Service Fabric 群集”。

将“目标主机”设置为上一部分中创建的 Service Fabric 群集。

选择要在其中发布容器的**容器注册表**。

>[!TIP]
>使用“编辑”按钮创建容器注册表。

按“确定”。

![设置 Service Fabric 持续集成][image-setup-ci]
   
   完成配置后，容器会部署到 Service Fabric。 每次将更新推送给存储库时将执行新版本。
   
   >[!NOTE]
   >生成容器映像大约需要 15 分钟。
   >首次部署到 Service Fabric 群集时会下载 Windows Server Core 容器的基础映像。 下载另需 5-10 分钟才能完成。

使用群集的 URL 浏览到 Fabrikam Call Center 应用程序，例如 *http://mycluster.westeurope.cloudapp.azure.com*

现在，已容器化并部署 Fabrikam 呼叫中心解决方案，可以打开 [Azure 门户][link-azure-portal]并查看在 Service Fabric 中运行的应用程序。 若要试用应用程序，可打开 Web 浏览器并转到 Service Fabric 群集的 URL。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 在 Visual Studio 中创建 Docker 项目
> * 容器化现有应用程序
> * 设置与 Visual Studio 和 VSTS 的持续集成

在本教程的下一部分中，了解如何设置[容器监视](service-fabric-tutorial-monitoring-wincontainers.md)。

<!--   NOTE SURE WHAT WE SHOULD DO YET HERE

Advance to the next tutorial to learn how to bind a custom SSL certificate to it.

> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure Web Apps](app-service-web-tutorial-custom-ssl.md)

## Next steps

- [Container Tooling in Visual Studio][link-visualstudio-container-tools]
- [Get started with containers in Service Fabric][link-servicefabric-containers]
- [Creating Service Fabric applications][link-servicefabric-createapp]
-->

[link-debug-container]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-container-quickstart]: /virtualization/windowscontainers/quick-start/quick-start-windows-10
[link-visualstudio-container-tools]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-azure-powershell-install]: /powershell/azure/install-azurerm-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-servicefabric-createapp]: service-fabric-create-your-first-application-in-visual-studio.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-azure-pricing-calculator]: https://azure.microsoft.com/en-us/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/en-us/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /azure/sql-database/

[image-web-preview]: media/service-fabric-host-app-in-a-container/fabrikam-web-sample.png
[image-source-control]: media/service-fabric-host-app-in-a-container/add-to-source-control.png
[image-publish-repo]: media/service-fabric-host-app-in-a-container/publish-repo.png
[image-setup-ci]: media/service-fabric-host-app-in-a-container/configure-continuous-integration.png

