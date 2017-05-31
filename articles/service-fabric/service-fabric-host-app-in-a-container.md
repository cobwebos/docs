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
ms.date: 05/10/2017
ms.author: mikhegn
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: a965fcb9dd5caf9656af5ae381b25baaaa01ec6d
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---

# <a name="deploy-a-net-app-in-a-container-to-azure-service-fabric"></a>将容器中的 .NET 应用部署到 Azure Service Fabric

本教程介绍如何使用 Visual Studio 2017 Update 3 Preview 在 Windows 容器中部署现有 ASP.NET 应用程序。 然后介绍如何使用 Visual Studio Team Services 将容器部署到 Azure 中并在 Service Fabric 群集中托管容器。

## <a name="prerequisites"></a>先决条件

1. 安装 [Docker CE for Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description)，以便在 Windows 10 上运行容器。
2. 熟悉 [Windows 10 容器快速入门][link-container-quickstart]。
3. 在本文中，我们使用 **Fabrikam Fiber**，可从[此处][link-fabrikam-github]下载该示例应用。
4. [Azure PowerShell][link-azure-powershell-install]
5. [用于 Visual Studio 2017 的持续交付工具扩展][link-visualstudio-cd-extension]

>[!NOTE]
>如果这是首次在计算机上运行 Windows 容器映像，Docker CE 必须下拉容器的基础映像。 本教程中使用的映像大小为 14 GB。 继续操作并在 Powershell 中运行以下命令，拉取基础映像：
>
>```cmd
>docker pull microsoft/mssql-server-windows-developer
>docker pull microsoft/aspnet:4.6.2
>```

## <a name="containerize-the-application"></a>容器化应用程序

若要开始在容器中运行应用程序，我们需要在 Visual Studio 中添加对项目的 **Docker 支持**。 向应用程序添加 **Docker 支持**后，会发生两件事。 首先，项目中会添加一个 _docker_ 文件。 此新文件描述容器映像的生成方式。 其次，解决方案中会添加一个新的 _docker compose_ 项目。 此新项目包含几个 docker-compose 文件，可用于描述容器的运行方式。

有关使用 [Visual Studio 容器工具][link-visualstudio-container-tools]的详细信息。

### <a name="add-docker-support"></a>添加 Docker 支持

1. 在 Visual Studio 中打开 **FabrikamFiber.CallCenter.sln** 文件

2. 右键单击 **FabrikamFiber.Web** 项目 >“添加” > “Docker 支持”。

### <a name="add-support-for-sql"></a>添加 SQL 支持

此应用程序使用 SQL 作为数据提供程序，因此需要 SQL Server 才能运行该应用程序。 在本教程中，我们使用在容器中运行的 SQL Server。
若要告知 Docker 我们想要在容器中运行 SQL Server，可在 docker-compose 项目的 docker-compose.override.yml 文件中引用 SQL Server 容器映像。 这样一来，在 Visual Studio 中调试应用程序时，将使用在容器中运行的 SQL Server。

1. 打开“解决方案资源管理器”。****

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

   >[!NOTE]
   >如果始终想要在容器中运行 SQL Server，可以选择将前述内容添加到 docker-compose.yml 文件，而不添加到 docker compose.override.yml 文件。


4. 修改 `fabrikamfiber.web` 节点，添加名为 `depends_on:` 的新子节点。 这样可确保 `db` 服务（SQL Server 容器）在 Web 应用程序 (fabrikamfiber.web) 之前启动。

   ```yml
     fabrikamfiber.web:
       ports:
         - "80"
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

在本教程的其余部分，使用 Visual Studio Team Services 生成和部署容器、将其推送到 Azure 容器注册表并部署到 Service Fabric（在 Azure 中运行）。

## <a name="create-a-service-fabric-cluster"></a>创建 Service Fabric 群集

如果已经拥有要在其中部署应用程序的 Service Fabric 群集，则可以跳过此步骤。 否则，让我们继续创建 Service Fabric 群集。

>[!NOTE]
>以下过程创建由自签名证书进行保护的 Service Fabric 群集，该证书放在 KeyVault 中，作为部署的一部分创建。 有关使用 Azure Active Directory 身份验证的详细信息，请参阅[使用 Azure Resource Manager 创建 Service Fabric 群集][link-servicefabric-create-secure-clusters]一文。

1. 下载下文中引用的 Azure 模板和参数文件的本地副本。
    * [适用于 Service Fabric 的 Azure Resource Manager 模板][link-sf-clustertemplate]：定义 Service Fabric 群集的 Resource Manager 模板。
    * [模板参数文件][link-sf-clustertemplate-parameters]：用于自定义群集部署的参数文件。
2. 自定义参数文件中的以下参数：
  
   | 参数       | 说明 |
   | --------------- | ----------- |
   | clusterName     | 群集的名称。 |
   | adminUserName   | 群集虚拟机上的本地管理员帐户。 |
   | adminPassword   | 群集虚拟机上的本地管理员帐户的密码。 |
   | clusterLocation | 要在其中部署群集的 Azure 区域。 |
   | vmInstanceCount | 群集中的节点数（可以为 1） |

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
       -TemplateFile C:\users\me\downloads\PreviewSecureClusters.json `
       -ParameterFile C:\users\me\downloads\myCluster.parameters.json `
       -CertificateOutputFolder C:\users\me\desktop\ `
       -CertificatePassword $pwd `
       -CertificateSubjectName "mycluster.westeurope.cloudapp.azure.com" `
       -ResourceGroupName myclusterRG
   ```

   >[!NOTE]
   >`-CertificateSubjectName` 参数应与参数文件中指定的 clusterName 参数以及连接到所选择的 Azure 区域的域保持一致，例如 `clustername.eastus.cloudapp.azure.com`。
   
    完成配置后，将输出有关在 Azure 中创建的群集的信息，并将证书复制到 -CertificateOutputFolder 目录。

8. **双击**要在本地计算机上安装的证书。

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
   
   完成配置后，无论何时将更新推送到存储库，容器都会部署到 Service Fabric 中。

7. 使用**团队资源管理器****开始生成**，并查看在 Service Fabric 中运行的容器应用程序。

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
[link-sf-clustertemplate-parameters]: https://aka.ms/securepreviewonelineclusterparameters

[image-web-preview]: media/service-fabric-host-app-in-a-container/fabrikam-web-sample.png
[image-source-control]: media/service-fabric-host-app-in-a-container/add-to-source-control.png
[image-publish-repo]: media/service-fabric-host-app-in-a-container/publish-repo.png
[image-setup-ci]: media/service-fabric-host-app-in-a-container/configure-continuous-integration.png

