---
title: 将容器中的 .NET 应用部署到 Azure Service Fabric | Microsoft Docs
description: 了解如何使用 Visual Studio 将现有 .NET 应用程序容器化并在 Service Fabric 中本地调试容器。 容器化后的应用程序会被推送给 Azure 容器注册表，并部署到 Service Fabric 群集。 部署到 Azure 时，应用程序使用 Azure SQL DB 保存数据。
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/18/2018
ms.author: aljo
ms.openlocfilehash: bd92ac2a36fdb15741799571d3ceff7fbbb36eef
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2019
ms.locfileid: "56807518"
---
# <a name="tutorial-deploy-a-net-application-in-a-windows-container-to-azure-service-fabric"></a>教程：将 Windows 容器中的 .NET 应用程序部署到 Azure Service Fabric

本教程介绍如何将现有 ASP.NET 应用程序容器化，并将其打包为 Service Fabric 应用程序。  在 Service Fabric 开发群集上本地运行该容器，然后将该应用程序部署到 Azure。  应用程序将数据保存在 [Azure SQL 数据库](/azure/sql-database/sql-database-technical-overview)中。 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 使用 Visual Studio 将现有应用程序容器化
> * 创建 Azure SQL 数据库
> * 创建 Azure 容器注册表
> * 将 Service Fabric 应用程序部署到 Azure

## <a name="prerequisites"></a>先决条件

1. 如果还没有 Azure 订阅，[创建一个免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
2. 安装 [Docker CE for Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description)，以便在 Windows 10 上运行容器。
3. 安装 [Service Fabric 运行时版本 6.2 或更高版本](service-fabric-get-started.md)以及 [Service Fabric SDK 版本 3.1](service-fabric-get-started.md) 或更高版本。
4. 安装 [Visual Studio 2017 版本 15.7](https://www.visualstudio.com/) 或更高版本，其中包含 Azure 开发以及 ASP.NET 和 Web 开发工作负荷。
5. 安装 [Azure PowerShell][link-azure-powershell-install]
 

## <a name="download-and-run-fabrikam-fiber-callcenter"></a>下载并运行 Fabrikam Fiber CallCenter
下载 [Fabrikam Fiber CallCenter][link-fabrikam-github] 示例应用程序。  单击“下载存档”链接。  从“fabrikam.zip”文件的“sourceCode”目录中，提取“sourceCode.zip”文件，然后将“VS2015”目录提取至计算机。

验证 Fabrikam Fiber CallCenter 应用程序是否能正确无误地生成和运行。  以管理员身份启动 Visual Studio，然后打开 [FabrikamFiber.CallCenter.sln][link-fabrikam-github] 文件。  按 F5 调试并运行应用程序。

![Fabrikam Web 示例][fabrikam-web-page]

## <a name="containerize-the-application"></a>容器化应用程序
右键单击“FabrikamFiber.Web”项目，再单击“添加” > “容器业务流程协调程序支持”。  选择“Service Fabric”作为容器业务流程协调程序，然后单击“确定”。

单击“是”立即将 Docker 切换到 Windows 容器。

解决方案中将创建一个新的 Service Fabric 应用程序项目，即“FabrikamFiber.CallCenterApplication”。  系统会向现有的“FabrikamFiber.Web”项目添加一个 Dockerfile。  还会向“FabrikamFiber.Web”项目添加一个“PackageRoot”目录，其中包含新 FabrikamFiber.Web 服务的服务清单和设置。 

现在可以在 Service Fabric 应用程序中生成和打包该容器。 在计算机上生成容器映像后，即可将其推送到任何容器注册表并下拉到任何主机上运行。

## <a name="create-an-azure-sql-db"></a>创建 Azure SQL DB
在生产中运行 Fabrikam Fiber CallCenter 应用程序时，需要将数据保存在数据库中。 目前无法保证将数据保留在容器中，因此无法将生产数据存储在容器中的 SQL Server 中。

建议使用 [Azure SQL 数据库](/azure/sql-database/sql-database-get-started-powershell)。 若要在 Azure 中设置和运行托管的 SQL Server DB，请运行以下脚本。  根据需要修改脚本变量。 clientIP 是开发计算机的 IP 地址。 记下脚本输出的服务器的名称。 

```powershell
$subscriptionID="<subscription ID>"

# Sign in to your Azure account and select your subscription.
Login-AzureRmAccount -SubscriptionId $subscriptionID 

# The data center and resource name for your resources.
$dbresourcegroupname = "fabrikam-fiber-db-group"
$location = "southcentralus"

# The logical server name: Use a random value or replace with your own value (do not capitalize).
$servername = "fab-fiber-$(Get-Random)"

# Set an admin login and password for your database.
# The login information for the server.
$adminlogin = "ServerAdmin"
$password = "Password@123"

# The IP address of your development computer that accesses the SQL DB.
$clientIP = "<client IP>"

# The database name.
$databasename = "call-center-db"

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $dbresourcegroupname -Location $location

# Create the SQL server.
New-AzureRmSqlServer -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# Create the firewall rule to allow your development computer to access the server.
New-AzureRmSqlServerFirewallRule -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowClient" -StartIpAddress $clientIP -EndIpAddress $clientIP

# Creeate the database in the server.
New-AzureRmSqlDatabase  -ResourceGroupName $dbresourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -RequestedServiceObjectiveName "S0"

Write-Host "Server name is $servername"
```
> [!TIP]
> 如果你处于企业防火墙之后，则开发计算机的 IP 地址可能不是暴露于 Internet 的 IP 地址。 若要验证数据库是否有防火墙规则的正确 IP 地址，请转到 [Azure 门户](https://portal.azure.com)，在“SQL 数据库”部分找到数据库。 单击其名称，然后在“概览”部分单击“设置服务器防火墙”。 “客户端 IP 地址”是开发计算机的 IP 地址。 请确保它与“AllowClient”规则中的 IP 地址匹配。

## <a name="update-the-web-config"></a>更新 Web 配置
返回到 FabrikamFiber.Web 项目，更新 web.config 文件中的连接字符串，以指向容器中的 SQL Server。  将连接字符串的 *Server* 部分更新为上一脚本创建的服务器名称。 它应该类似于“fab-fiber-751718376.database.windows.net”。

```xml
<add name="FabrikamFiber-Express" connectionString="Server=<server name>,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
<add name="FabrikamFiber-DataWarehouse" connectionString="Server=<server name>,1433;Initial Catalog=call-center-db;Persist Security Info=False;User ID=ServerAdmin;Password=Password@123;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" providerName="System.Data.SqlClient" />
  
```
>[!NOTE]
>可以使用选择的任何 SQL Server 进行本地调试，只要可通过主机访问。 但是，**localdb** 不支持 `container -> host` 通信。 如果想要在生成 Web 应用程序的发布版本时使用其他 SQL 数据库，可将另一个连接字符串添加到 web.release.config 文件。

## <a name="run-the-containerized-application-locally"></a>本地运行已容器化的应用程序
按 F5，在本地 Service Fabric 开发群集的容器中运行和调试应用程序。 如果出现一个消息框，请求授予“ServiceFabricAllowedUsers”组对Visual Studio 项目目录的读取和执行权限，请单击“是”。

## <a name="create-a-container-registry"></a>创建容器注册表
在本地运行应用程序后，开始准备将其部署到 Azure。  需将容器映像保存在容器注册表中。  使用以下脚本创建 [Azure 容器注册表](/azure/container-registry/container-registry-intro)。 容器注册表名称对其他 Azure 订阅可见，因此它必须是唯一的。
将应用程序部署到 Azure 之前，先将容器映像推送给此注册表。  应用程序部署到 Azure 中的群集时，系统会从此注册表中拉取容器映像。

```powershell
# Variables
$acrresourcegroupname = "fabrikam-acr-group"
$location = "southcentralus"
$registryname="fabrikamregistry$(Get-Random)"

New-AzureRmResourceGroup -Name $acrresourcegroupname -Location $location

$registry = New-AzureRMContainerRegistry -ResourceGroupName $acrresourcegroupname -Name $registryname -EnableAdminUser -Sku Basic
```

## <a name="create-a-service-fabric-cluster-on-azure"></a>在 Azure 上创建 Service Fabric 群集
在群集（一组已连接网络的虚拟机或物理计算机）上运行 Service Fabric 应用程序。  需在 Azure 中创建一个 Service Fabric 群集，然后才能将应用程序部署到 Azure。

可以：
- 通过 Visual Studio 创建一个测试群集。 可以通过此选项使用首选的配置直接从 Visual Studio 创建安全的群集。 
- [模板创建安全的群集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)

本教程通过 Visual Studio 创建群集，这非常适合测试方案。 如果通过其他方式创建群集或使用现有的群集，可复制粘贴连接终结点或从订阅中选择连接终结点。 

在开始之前，请在解决方案资源管理器中打开 FabrikamFiber.Web->PackageRoot->ServiceManifest.xml。 记下在“终结点”中列出的 Web 前端的端口。 

创建群集时，请执行以下操作： 

1. 在解决方案资源管理器中右键单击“FabrikamFiber.CallCenterApplication”应用程序项目，然后选择“发布”。

2. 使用 Azure 帐户登录，以便访问订阅。 

3. 选择“连接终结点”对应的下拉列表，然后选择“新建群集...”选项。    
        
4. 在“创建群集”对话框中，修改以下设置：

    a. 在“群集名称”字段中指定群集的名称，并指定要使用的订阅和位置。 记下群集资源组的名称。

    b. 可选：可以修改节点数。 默认有三个节点，这是测试 Service Fabric 方案的最低要求。

    c. 选择“证书”选项卡。在此选项卡中键入一个密码，用于确保群集证书的安全。 此证书有助于确保群集的安全。 也可修改用于保存证书的路径。 Visual Studio 还可以为你导入证书，因为这是将应用程序发布到群集的必需步骤。

    d. 选择“VM 详细信息”选项卡。指定一个密码，以便将其用于构成群集的虚拟机 (VM)。 可以使用用户名和密码远程连接到 VM。 此外还必须选择 VM 大小，并可根据需要更改 VM 映像。 

    > [!IMPORTANT]
    >选择一个支持运行容器的 SKU。 群集节点上的 Windows Server OS 必须与容器的 Windows Server OS 兼容。 若要了解更多信息，请参阅 [Windows Server 容器 OS 与主机 OS 的兼容性](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility)。 默认情况下，本教程将创建基于 Windows Server 2016 LTSC 的 Docker 映像。 基于此映像的容器将运行在使用带容器的 Windows Server 2016 Datacenter 创建的群集上。 但是，如果基于带容器的 Windows Server Datacenter Core 1709 创建群集或使用现有群集，则必须更改容器所基于的 Windows Server OS 映像。 打开 **FabrikamFiber.Web** 项目中的 **Dockerfile**，注释掉现有 `FROM` 语句（基于 `windowsservercore-ltsc`），并取消注释基于 `windowsservercore-1709` 的 `FROM` 语句。 

    e. 在“高级”选项卡中，列出群集部署时要在负载均衡器中打开的应用程序端口。 这是在开始创建群集之前记下的端口。 还可以添加现有的 Application Insights 密钥，用于路由应用程序日志文件。

    f. 修改完设置以后，选择“创建”按钮。 
1. 需要数分钟才能创建完毕；输出窗口会指示群集何时完全创建好。
    

## <a name="allow-your-application-running-in-azure-to-access-the-sql-db"></a>允许在 Azure 中运行的应用程序访问 SQL DB
之前已创建一个 SQL 防火墙规则，允许对本地运行的应用程序进行访问。  接下来，需要使 Azure 中运行的应用程序能够访问 SQL DB。  为 Service Fabric 群集创建[虚拟网络服务终结点](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)，然后创建一个规则，允许该终结点访问 SQL DB。 请务必指定群集资源组变量，该变量是在创建群集时记下的。 

```powershell
# Create a virtual network service endpoint
$clusterresourcegroup = "<cluster resource group>"
$resource = Get-AzureRmResource -ResourceGroupName $clusterresourcegroup -ResourceType Microsoft.Network/virtualNetworks | Select-Object -first 1
$vnetName = $resource.Name

Write-Host 'Virtual network name: ' $vnetName 

# Get the virtual network by name.
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName $clusterresourcegroup `
  -Name              $vnetName

Write-Host "Get the subnet in the virtual network:"

# Get the subnet, assume the first subnet contains the Service Fabric cluster.
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet | Select-Object -first 1

$subnetName = $subnet.Name
$subnetID = $subnet.Id
$addressPrefix = $subnet.AddressPrefix

Write-Host "Subnet name: " $subnetName " Address prefix: " $addressPrefix " ID: " $subnetID

# Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.
$vnet = Set-AzureRmVirtualNetworkSubnetConfig `
  -Name            $subnetName `
  -AddressPrefix   $addressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint Microsoft.Sql | Set-AzureRmVirtualNetwork

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

# Add a SQL DB firewall rule for the virtual network service endpoint
$subnet = Get-AzureRmVirtualNetworkSubnetConfig `
  -Name           $subnetName `
  -VirtualNetwork $vnet;

$VNetRuleName="ServiceFabricClusterVNetRule"
$vnetRuleObject1 = New-AzureRmSqlServerVirtualNetworkRule `
  -ResourceGroupName      $dbresourcegroupname `
  -ServerName             $servername `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnetID;
```
## <a name="deploy-the-application-to-azure"></a>将应用程序部署到 Azure
至此，应用程序已准备就绪，可以直接通过 Visual Studio 将它部署到 Azure 中的群集。  在解决方案资源管理器中，右键单击“FabrikamFiber.CallCenterApplication”应用程序项目，然后选择“发布”。  在“连接终结点”中，选择之前创建的群集的终结点。  在“Azure 容器注册表”中，选择之前创建的容器注册表。  单击“发布”，将应用程序部署到 Azure 中的群集。

![发布应用程序][publish-app]

在“输出”窗口中跟进部署进度。  应用程序部署完毕后，打开浏览器并键入群集地址和应用程序端口。 例如， http://fabrikamfibercallcenter.southcentralus.cloudapp.azure.com:8659/。

![Fabrikam Web 示例][fabrikam-web-page-deployed]

## <a name="set-up-continuous-integration-and-deployment-cicd-with-a-service-fabric-cluster"></a>使用 Service Fabric 群集设置持续集成和部署 (CI/CD)
若要了解如何使用 Azure DevOps 配置到 Service Fabric 群集的 CI/CD 应用程序部署，请参阅[教程：使用 CI/CD 将应用程序部署到 Service Fabric 群集](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)。 本教程中所述的过程与此 (FabrikamFiber) 项目相同，只需跳过下载 Voting 示例并将 FabrikamFiber 替换为存储库名称而不是 Voting。

## <a name="clean-up-resources"></a>清理资源
如果已完成，请务必删除所创建的所有资源。  最简单的方法是删除包含 Service Fabric 群集、Azure SQL DB 和 Azure 容器注册表的资源组。

```powershell
$dbresourcegroupname = "fabrikam-fiber-db-group"
$acrresourcegroupname = "fabrikam-acr-group"
$clusterresourcegroupname="fabrikamcallcentergroup"

# Remove the Azure SQL DB
Remove-AzureRmResourceGroup -Name $dbresourcegroupname

# Remove the container registry
Remove-AzureRmResourceGroup -Name $acrresourcegroupname

# Remove the Service Fabric cluster
Remove-AzureRmResourceGroup -Name $clusterresourcegroupname
```

## <a name="next-steps"></a>后续步骤
本教程介绍了如何：

> [!div class="checklist"]
> * 使用 Visual Studio 将现有应用程序容器化
> * 创建 Azure SQL 数据库
> * 创建 Azure 容器注册表
> * 将 Service Fabric 应用程序部署到 Azure

在本教程的下一部分中，了解如何[使用 CI/CD 将容器应用程序部署到 Service Fabric 群集](service-fabric-tutorial-deploy-container-app-with-cicd-vsts.md)。

[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-azure-powershell-install]: /powershell/azure/azurerm/install-azurerm-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-azure-pricing-calculator]: https://azure.microsoft.com/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /azure/sql-database/

[fabrikam-web-page]: media/service-fabric-host-app-in-a-container/fabrikam-web-page.png
[fabrikam-web-page-deployed]: media/service-fabric-host-app-in-a-container/fabrikam-web-page-deployed.png
[publish-app]: media/service-fabric-host-app-in-a-container/publish-app.png
