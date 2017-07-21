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
ms.date: 05/19/2017
ms.author: mikhegn
ms.translationtype: Human Translation
ms.sourcegitcommit: d9ae8e8948d82b9695d7d144d458fe8180294084
ms.openlocfilehash: 56788914452c0f3a7072d6b2805866072b9b7fea
ms.contentlocale: zh-cn
ms.lasthandoff: 05/23/2017

---

# <a name="deploy-a-net-app-in-a-container-to-azure-service-fabric"></a>将容器中的 .NET 应用部署到 Azure Service Fabric

本教程介绍如何使用 Visual Studio 2017 Update 3 Preview 在 Windows 容器中部署现有 ASP.NET 应用程序。 然后介绍如何使用 Visual Studio Team Services 将容器部署到 Azure 中并在 Service Fabric 群集中托管容器。

## <a name="prerequisites"></a>先决条件

1. 安装 [Docker CE for Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description)，以便在 Windows 10 上运行容器。
2. 熟悉 [Windows 10 容器快速入门][link-container-quickstart]。
3. 本教程中将使用 Fabrikam Fiber CallCenter，可从[此处][link-fabrikam-github]下载该示例应用。
4. [Azure PowerShell][link-azure-powershell-install]
5. [用于 Visual Studio 2017 的持续交付工具扩展][link-visualstudio-cd-extension]
6. [Azure 订阅][link-azure-subscription]和 [Visual Studio Team Services 帐户][link-vsts-account]。 使用所有服务的免费层即可完成本教程。

>[!NOTE]
>如果这是首次在计算机上运行 Windows 容器映像，Docker CE 必须下载容器的基础映像。 本教程中使用的映像为 14 GB。 继续操作并在 Powershell 中运行以下命令，拉取基础映像：
>1. docker pull microsoft/mssql-server-windows-developer
>2. docker pull microsoft/aspnet:4.6.2

## <a name="containerize-the-application"></a>容器化应用程序

若要开始在容器中运行应用程序，我们需要在 Visual Studio 中添加对项目的 **Docker 支持**。 向应用程序添加 **Docker 支持**后，会发生两件事。 首先，项目中会添加一个 _docker_ 文件。 此新文件描述容器映像的生成方式。 其次，解决方案中会添加一个新的 _docker compose_ 项目。 新项目包含一些 docker-compose 文件。 Docker-compose 文件可用于描述容器的运行方式。

有关使用 [Visual Studio 容器工具][link-visualstudio-container-tools]的详细信息。

### <a name="add-docker-support"></a>添加 Docker 支持

1. 在 Visual Studio 中打开 **FabrikamFiber.CallCenter.sln** 文件

2. 右键单击 **FabrikamFiber.Web** 项目 >“添加” > “Docker 支持”。

### <a name="add-support-for-sql"></a>添加 SQL 支持

此应用程序使用 SQL 作为数据提供程序，因此需要 SQL Server 才能运行该应用程序。 本教程中使用在容器中运行的 SQL Server 进行本地调试。
若要在容器中运行 SQL Server，调试时可在 docker-compose.override.yml 文件中引用 SQL Server 容器映像。 

1. 打开“解决方案资源管理器”。

2. 打开 **docker-compose** > **docker-compose.yml** > **docker-compose.override.yml**。

3. 在 `services:` 节点下，添加名为 `db:` 的新节点。 此节点声明在容器中运行 SQL Server。

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
   >在容器中运行 SQL Server 不支持在容器停止时保留数据。 请勿对生产使用此配置。

4. 修改 `fabrikamfiber.web` 节点，添加名为 `depends_on:` 的新子节点。 这样可确保 `db` 服务（SQL Server 容器）在 Web 应用程序 (fabrikamfiber.web) 之前启动。

   ```yml
     fabrikamfiber.web:
       depends_on:
         - db
   ```

5. 在 **FabrikamFiber.Web** 项目中，更新 **web.config** 文件中的连接字符串，以指向容器中的 SQL Server。

   ```xml
   <add name="FabrikamFiber-Express" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />
   <add name="FabrikamFiber-DataWarehouse" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />
   ```

   >[!NOTE]
   >如果想要在生成 Web 应用程序的发布版本时使用其他 SQL Server，可将另一个连接字符串添加到 web.release.config 文件。

6. 按 **F5**，在容器中运行和调试应用程序。

   Edge 会在内部 NAT 网络上使用容器的 IP 地址（通常为 172.x.x.x）打开应用程序的定义启动页。 若要了解有关使用 Visual Studio 2017 在容器中调试应用程序的详细信息，请参阅[本文][link-debug-container]。

   ![容器中的 fabrikam 示例][image-web-preview]

应用程序现在已为在容器中生成和打包做好准备。 在计算机上生成容器映像后，即可将其推送到任何容器注册表并下拉到任何主机上运行。

本教程的其余部分中使用 Visual Studio Team Services 将容器部署到 Service Fabric（在 Azure 中运行）。

## <a name="create-a-service-fabric-cluster"></a>创建 Service Fabric 群集

如果已经拥有要在其中部署应用程序的 Service Fabric 群集，则可以跳过此步骤。 否则，让我们继续创建 Service Fabric 群集。

>[!NOTE]
>以下过程创建单节点（单虚拟机）预览版 Service Fabric 群集，该群集由位于 KeyVault 中的自签名证书保护。
>单节点群集无法扩展超出一个虚拟机，并且预览版群集无法升级到较新版本。
>若要计算在 Azure 中运行 Service Fabric 群集的成本，请使用 [Azure 定价计算器][link-azure-pricing-calculator]。
>有关创建 Service Fabric 群集的详细信息，请参阅[使用 Azure Resource Manager 创建 Service Fabric 群集][link-servicefabric-create-secure-clusters]一文。

1. 从以下 GitHub 存储库中下载 Azure Resource Manager 模板和参数文件的本地副本：
    * [Service Fabric 的 Azure Resource Manager 模板][link-sf-clustertemplate]
       - azuredeploy.json - 定义 Service Fabric 群集的 Azure Resource Manager 模板。
       - azuredeploy.parameters.json - 用于自定义群集部署的参数文件。
2. 自定义参数文件中的以下参数：
  
   | 参数       | 说明 | 建议的值 |
   | --------------- | ----------- | --------------- |
   | clusterLocation | 要在其中部署群集的 Azure 区域。 | 例如 westeurope、eastasia、eastus |
   | clusterName     | 群集的名称。 | 例如 bobs-sfpreviewcluster |
   | adminUserName   | 群集虚拟机上的本地管理员帐户。 | 任何有效的 Windows Server 用户名 |
   | adminPassword   | 群集虚拟机上的本地管理员帐户的密码。 | 任何有效的 Windows Server 密码 |
   | clusterCodeVersion | 要运行的 Service Fabric 版本。 （255.255.X.255 是预览版）。 | 255.255.5713.255 |
   | vmInstanceCount | 群集中的虚拟机数量（可以是 1 或 3-99 之间的数字）。 | **1** |

3. 打开 **PowerShell**。
4. **登录** Azure。

   ```powershell
   Login-AzureRmAccount
   ```

5. 选择想要在其中部署群集的**订阅**。

   ```powershell
   Select-AzureRmSubscription -SubscriptionId <subscription-id>
   ```

6. 为 Service Fabric 使用的证书创建并**加密密码**。

   ```powershell
   $pwd = "<your password>" | ConvertTo-SecureString -AsPlainText -Force
   ```

7. 通过运行以下命令**创建群集**：

   ```powershell
      New-AzureRmServiceFabricCluster
          -TemplateFile C:\Users\me\Desktop\azuredeploy.json `
          -ParameterFile C:\Users\me\Desktop\azuredeploy.parameters.json `
          -CertificateOutputFolder C:\Users\me\Desktop\ `
          -CertificatePassword $pwd `
          -CertificateSubjectName "mycluster.westeurope.cloudapp.azure.com" `
          -ResourceGroupName myclusterRG
   ```

   >[!NOTE]
   >`-CertificateSubjectName` 参数应与参数文件中指定的 clusterName 参数以及连接到所选择的 Azure 区域的域保持一致，例如 `clustername.eastus.cloudapp.azure.com`。
   
    完成配置后，将输出有关在 Azure 中创建的群集的信息，并将证书复制到 -CertificateOutputFolder 目录。

8. 双击证书打开证书导入向导。

9. 使用默认设置，但务必勾选“将此密钥标记为可导出” 复选框（在“私钥保护”步骤中）。 本教程中稍后将 Azure 容器注册表配置为 Service Fabric 群集身份验证时，Visual Studio 需要导出该证书。

10. 现在可以在浏览器中打开 Service Fabric Explorer。 URL 是 PowerShell CmdLet 输出中的 ManagementEndpoint，例如 https://mycluster.westeurope.cloudapp.azure.com:19080 

>[!NOTE]
>打开 Service Fabric Explorer 时会看到证书错误，因为使用的是自签名证书。 在Microsoft Edge 中，必须单击“详细信息”，然后单击“转到网页”链接。 在 Chrome 中，必须单击“高级”，然后单击“继续”链接。

>[!NOTE]
>如果群集创建失败，始终可以重新运行命令，这样可以更新已部署的资源。 如果在失败部署的过程中创建了证书，则将创建新的证书。 若要解决群集创建问题，请访问：[使用 Azure Resource Manager 创建 Service Fabric 群集][link-servicefabric-create-secure-clusters]一文。

## <a name="getting-the-application-ready-for-the-cloud"></a>让应用程序准备好使用云

在 Azure 中若要让应用程序准备好在 Service Fabric 中运行，需要完成以下两个步骤：

1. 公开想要通过其访问 Service Fabric 群集中的 Web 应用程序的端口
2. 为应用程序提供生产准备就绪 SQL 数据库

### <a name="1-exposing-the-web-application-in-service-fabric"></a>1.公开 Service Fabric 中的 Web 应用程序
默认情况下，配置的 Service Fabric 群集在 Azure 负载均衡器中的端口 80 处于打开状态，Azure 负载均衡器负责均衡传入到群集的流量。 可以通过 docker-compose.yml 文件公开此端口上的容器。

1. 打开“解决方案资源管理器”。

2. 打开 docker-compose > docker-compose.yml。

3. 修改 `fabrikamfiber.web` 节点，添加名为 `ports:` 的新子节点和字符串 `- "80:80"`。 完成后的 docker-compose.yml 文件应如下所示：

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

### <a name="2-provide-a-production-ready-sql-database-for-our-application"></a>2.为应用程序提供生产准备就绪 SQL 数据库
容器化此应用程序并启用本地调试时，将应用程序设置为运行容器中的 SQL Server。 本地调试应用程序时，这是一个不错的解决方案，因为无需保留数据库中的数据。 但是在生产中运行时，需要将数据保留在数据库中。 目前无法保证将数据保留在容器中，因此无法将生产数据存储在容器中的 SQL Server 中。

因此，如果服务需要永久 SQL 数据库，建议使用 Azure SQL 数据库。 若要在 Azure 中设置和运行托管 SQL Server，请访问 [Azure SQL 数据库快速入门] [[link-azure-sql]] 一文。

>[!NOTE]
>在 FabrikamFiber.Web 项目中，请务必在 web.release.config 文件中将连接字符串更改为 SQL Server。
>如果 SQL 数据库不可访问，此应用程序将无法正常运行。 可以选择继续操作，在不使用 SQL Server 的情况下部署应用程序。

## <a name="deploy-with-visual-studio"></a>使用 Visual Studio 进行部署

若要使用 Visual Studio Team Services 设置部署，需要安装[用于 Visual Studio 2017 的持续交付工具扩展][link-visualstudio-cd-extension]。 借助此扩展，可以轻松地通过配置 Visual Studio Team Services 部署到 Azure，以及将应用部署到 Service Fabric 群集。

首先，必须在源控件中托管代码。 本部分的其余部分假定使用 **git**。

1. 在 Visual Studio 的右下角，单击“添加到源控件” > “Git”（或想要使用的任何选项）。

   ![按“源控件”按钮][image-source-control]

2. 在“团队资源管理器”窗格中，按“发布 Git 存储库”。

3. 选择 VSTS 存储库名称并按“存储库”。

   ![将存储库发布到 VSTS][image-publish-repo]

现在，代码与 VSTS 源存储库同步，可以配置持续集成和持续交付。

1. 在“解决方案资源管理器”中，右键单击“解决方案” > “配置持续交付”。

2. 选择 Azure 订阅。

3. 将“主机类型”设置为“Service Fabric 群集”。

   >[!NOTE]
   >根据要生成的容器的类型，我们会添加更多选项，方便在 Azure 中将应用程序托管到容器中。 

4. 将“目标主机”设置为上一部分中创建的 Service Fabric 群集。

5. 选择要在其中发布容器的**容器注册表**。

   >[!TIP]
   >使用“编辑”按钮创建容器注册表。
    
6. 按“确定”。

   ![设置 Service Fabric 持续集成][image-setup-ci]
   
   完成配置后，容器会部署到 Service Fabric。 每次将更新推送给存储库时将执行新版本。
   
   >[!NOTE]
   >生成容器映像大约需要 15 分钟。
   >首次部署到 Service Fabric 群集时会下载 Windows Server Core 容器的基础映像。 下载另需 5-10 分钟才能完成。

7. 使用群集的 URL 浏览到 Fabrikam Call Center 应用程序，例如 *http://mycluster.westeurope.cloudapp.azure.com*

现在，已容器化并部署 Fabrikam 呼叫中心解决方案，可以打开 [Azure 门户][link-azure-portal]并查看在 Service Fabric 中运行的应用程序。 若要试用应用程序，可打开 Web 浏览器并转到 Service Fabric 群集的 URL。

## <a name="next-steps"></a>后续步骤

- [Visual Studio 中的容器工具][link-visualstudio-container-tools]
- [Service Fabric 中的容器入门][link-servicefabric-containers]
- [创建 Service Fabric 应用程序][link-servicefabric-createapp]

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
[link-azure-sql]: /sql-database

[image-web-preview]: media/service-fabric-host-app-in-a-container/fabrikam-web-sample.png
[image-source-control]: media/service-fabric-host-app-in-a-container/add-to-source-control.png
[image-publish-repo]: media/service-fabric-host-app-in-a-container/publish-repo.png
[image-setup-ci]: media/service-fabric-host-app-in-a-container/configure-continuous-integration.png
