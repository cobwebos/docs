---
title: 部署使用 Azure 和 Azure Stack 的混合云 |Microsoft Docs
description: 了解如何部署 Azure 和 Azure Stack 的混合云。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/18/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: e4da34be5b1e9eb2675f540fe4069cfd3dc1d056
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2018
ms.locfileid: "49410081"
---
# <a name="tutorial-deploy-a-hybrid-cloud-solution-with-azure-and-azure-stack"></a>教程： 部署使用 Azure 和 Azure Stack 的混合云解决方案

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

本教程演示如何部署使用 Azure 公有云与 Azure Stack 私有云的混合云解决方案。

通过使用混合云解决方案，您可以将法规遵从性优势的私有云与公有云的可伸缩性。 此外，您的开发人员可以充分利用 Microsoft 开发人员生态系统，并将他们的技能应用到在本地和云环境。

## <a name="overview-and-assumptions"></a>概述和假设

可以按照本教程，若要设置允许相同的 web 应用程序部署到公有云与私有云的开发人员的工作流。 此应用程序将能够访问私有云上托管的非 Internet 可路由网络。 监视这些 web 应用程序和程序时在流量出现峰值，修改要将流量重定向到公有云的 DNS 记录。 当流量降到之前图文场级别时，流量将路由到私有云。

本教程涵盖以下任务：

> [!div class="checklist"]
> - 部署混合连接的 SQL Server 数据库服务器。
> - 全球 Azure 中的将 web 应用连接到混合网络。
> - 为跨云缩放配置 DNS。
> - 配置 SSL 证书，以便跨云缩放。
> - 配置和部署 web 应用程序。
> - 创建流量管理器配置文件并将其配置为跨云缩放。
> - 设置 Application Insights 监视和警报增加的流量。
> - 配置自动全局 Azure 和 Azure Stack 之间进行切换的流量。

### <a name="assumptions"></a>假设

本教程假定你有的全球 Azure 和 Azure Stack 的基本知识。 如果你想要了解在开始本教程之前，请查看以下文章：

 - [Azure 简介](https://azure.microsoft.com/overview/what-is-azure/)
 - [Azure Stack 的重要概念](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

本教程还假定你具有 Azure 订阅。 如果你没有订阅，则可以[创建一个免费帐户](https://azure.microsoft.com/free/)在开始之前。

## <a name="prerequisites"></a>必备组件

在开始本教程之前，请确保可以满足以下要求：

- Azure Stack 开发工具包 (ASDK) 或 Azure Stack 集成系统上的订阅。 若要部署 Azure Stack 开发工具包，按照中的说明[使用安装程序部署 ASDK](../asdk/asdk-deploy.md)。
- Azure Stack 安装应已安装以下组件：
  - Azure 应用服务中。 适用于 Azure Stack 运营商来部署和配置 Azure 应用服务环境。 本教程需要应用服务有至少一 （1） 可用的专用的辅助角色。
  - Windows Server 2016 映像
  - 使用 Microsoft SQL Server 的映像为 Windows Server 2016
  - 相应的计划和产品
 - Web 应用程序域名。 如果没有域名称可以购买来自如 GoDaddy、 Bluehost 和 InMotion 域提供程序。
- 从受信任的证书颁发机构，如 LetsEncrypt 域的 SSL 证书。
- Web 应用程序与 SQL Server 数据库通信，并支持将 Application Insights。 您可以下载[dotnetcore sqldb 教程](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial)GitHub 示例应用程序。
- Azure 虚拟网络和 Azure Stack 虚拟网络之间的混合网络。 有关详细说明，请参阅[配置与 Azure 和 Azure Stack 的混合云连接](azure-stack-solution-hybrid-connectivity.md)。

- 混合持续集成/持续部署 (CI/CD) 管道与 Azure Stack 上的专用生成代理。 有关详细说明，请参阅[配置与 Azure 和 Azure Stack 的应用程序的混合云标识](azure-stack-solution-hybrid-identity.md)

## <a name="deploy-a-hybrid-connected-sql-server-database-server"></a>部署混合连接的 SQL Server 数据库服务器

1. 登录到 Azure Stack 用户门户。

2. 上**仪表板**，选择**Marketplace**。

    ![Auzure Stack 应用商店](media/azure-stack-solution-hybrid-cloud/image1.png)

3. 在中**Marketplace**，选择**计算**，然后选择**详细**。 下**更多**，选择**免费 SQL Server 许可证： Windows Server 上的 SQL Server 2017 Developer**映像。

    ![选择虚拟机映像](media/azure-stack-solution-hybrid-cloud/image2.png)

4. 上**免费 SQL Server 许可证： Windows Server 上的 SQL Server 2017 Developer**选择**创建**。

5. 上**基础知识 > 配置基本设置**，提供**名称**虚拟机 (VM)，**用户名**SQL Server sa，和一个**密码** sa。  从**订阅**下拉列表中，选择要部署到的订阅。 有关**资源组**，使用**选择现有的**并将 VM 放在同一资源组作为 Azure Stack web 应用。

    ![配置 VM 的基本设置](media/azure-stack-solution-hybrid-cloud/image3.png)

6. 下**大小**，请为 VM 选择大小。 对于本教程中，我们建议 A2_Standard 或 DS2_V2_Standard。

7. 下**设置 > 配置可选功能**，配置以下设置：

    - **存储帐户**： 如果你需要，创建一个新的帐户。
    - **虚拟网络**

      > [!Important]  
      > 请确保 SQL Server 虚拟机部署在与 VPN 网关位于同一虚拟网络上。

    - **公共 IP 地址**。 您可以使用默认设置。
    - **网络安全组**(NSG)。 创建一个新的 NSG。
    - **扩展和监视**。 保留默认设置。
    - **诊断存储帐户**。 如果你需要，创建一个新的帐户。
    - 选择**确定**以保存配置。

    ![配置可选功能](media/azure-stack-solution-hybrid-cloud/image4.png)

1. 下**SQL Server 设置**，配置以下设置：
   - 有关**SQL 连接**，选择向**公共 (Internet)**。
   - 有关**端口**，保留默认设置， **1433年**。
   - 有关**SQL 身份验证**，选择**启用**。

     > [!Note]  
     > 启用 SQL 身份验证时，它应自动填充你在中配置的"SQLAdmin"信息**基础知识**。

   - 有关设置的其余部分，保留默认值。 选择“确定”。

    ![配置 SQL Server 设置](media/azure-stack-solution-hybrid-cloud/image5.png)

9. 上**摘要**，查看虚拟机配置，然后选择**确定**以开始部署。

    ![配置摘要](media/azure-stack-solution-hybrid-cloud/image6.png)

10. 它将需要一些时间来创建新的 VM。 您可以查看在 Vm 的状态**虚拟机**。

    ![虚拟机](media/azure-stack-solution-hybrid-cloud/image7.png)

## <a name="create-web-apps-in-azure-and-azure-stack"></a>在 Azure 和 Azure Stack 中创建 web 应用

运行和管理 web 应用程序，简化了 Azure 应用服务。 由于 Azure Stack 与 Azure 一致，可将这两个环境中运行应用服务。 将使用应用服务来托管应用程序。

### <a name="create-web-apps"></a>创建 web 应用

1. 中的说明在 Azure 中创建 web 应用[管理在 Azure 应用服务计划](https://docs.microsoft.com/azure/app-service/app-service-plan-manage#create-an-app-service-plan)。 请确保作为你的混合网络的 web 应用放入同一订阅和资源组。

2. 在 Azure Stack 中重复上一步 (1)。

### <a name="add-route-for-azure-stack"></a>适用于 Azure Stack 添加路由

Azure Stack 上的应用服务必须可路由来自公共 Internet，以使用户可以访问你的应用程序。 如果从 Internet 访问 Azure Stack，记下的面向公众的 IP 地址或 URL 的 Azure Stack web 应用。

如果使用 ASDK，则可以[配置静态 NAT 映射](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-vpn-connection-one-node#configure-the-nat-virtual-machine-on-each-azure-stack-development-kit-for-gateway-traversal)公开虚拟环境外部的应用服务。

### <a name="connect-a-web-app-in-azure-to-a-hybrid-network"></a>在 Azure 中的将 web 应用连接到混合网络

若要提供 web 前端在 Azure 中和在 Azure Stack 中的 SQL Server 数据库之间的连接的 web 应用连接到 Azure 和 Azure Stack 之间的混合网络。 若要启用连接，您必须：

- 配置点到站点连接
- 配置 Web 应用
- 修改 Azure Stack 中的本地网络网关。

### <a name="configure-the-azure-virtual-network-for-point-to-site-connectivity"></a>配置 Azure 虚拟网络进行点到站点连接

Azure 端的混合网络中的虚拟网络网关必须允许点到站点连接来与 Azure 应用服务集成。

1. 在 Azure 中，导航到虚拟网络网关页。 下**设置**，选择**点到站点配置**。

    ![点到站点选项](media/azure-stack-solution-hybrid-cloud/image8.png)

2. 选择**立即配置**配置点到站点。

    ![启动点到站点配置](media/azure-stack-solution-hybrid-cloud/image9.png)

3. 上**点到站点**配置页上，输入你想要在中使用的专用 IP 地址范围**地址池**。

   > [!Note]  
   > 请确保指定的范围不与任何已使用全局 Azure 或 Azure Stack 的混合网络组件中的子网的地址范围重叠。

   下**隧道类型**，取消选中**IKEv2 VPN**。 选择**保存**完成配置点到站点。

   ![点到站点设置](media/azure-stack-solution-hybrid-cloud/image10.png)

### <a name="integrate-the-azure-app-service-application-with-the-hybrid-network"></a>Azure 应用服务应用程序与混合网络集成

1. 若要连接到 Azure VNet 的应用程序，按照中的说明[启用 VNet 集成](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet#enabling-vnet-integration)。

2. 导航到**设置**托管 web 应用程序的应用服务计划。 在中**设置**，选择**网络**。

    ![配置网络](media/azure-stack-solution-hybrid-cloud/image11.png)

3. 在中**VNET 集成**，选择**单击此处可管理**。

    ![管理 VNET 集成](media/azure-stack-solution-hybrid-cloud/image12.png)

4. 选择你想要配置的 VNET。 下**IP 地址路由到 VNET**，为 Azure VNet、 Azure Stack VNet，以及点到站点地址空间中输入的 IP 地址范围。 选择**保存**验证和保存这些设置。

    ![若要将路由的 IP 地址范围](media/azure-stack-solution-hybrid-cloud/image13.png)

若要了解有关应用服务如何与 Azure Vnet 集成的详细信息，请参阅[将应用与 Azure 虚拟网络集成](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)。

### <a name="configure-the-azure-stack-virtual-network"></a>配置 Azure Stack 虚拟网络

Azure Stack 虚拟网络中的本地网络网关需要配置为路由将流量从应用服务点到站点地址范围。

1. 在 Azure Stack 中，导航到**本地网络网关**。 下**设置**，选择**配置**。

    ![网关配置选项](media/azure-stack-solution-hybrid-cloud/image14.png)

2. 在中**地址空间**，输入在 Azure.l 中，选择虚拟网络网关的点到站点地址范围**保存**验证并保存此配置。

    ![点到站点地址空间](media/azure-stack-solution-hybrid-cloud/image15.png)

## <a name="configure-dns-for-cross-cloud-scaling"></a>为跨云缩放配置 DNS

通过跨云应用程序的正确配置 DNS，用户可以访问你的 web 应用的全局 Azure 和 Azure Stack 实例。 当负载增加或减少时，本教程中的 DNS 配置还允许 Azure 流量管理器路由流量。

本教程使用 Azure DNS 管理 DNS。 （应用服务域无法工作）。

### <a name="create-subdomains"></a>创建子域

由于流量管理器依赖于 DNS Cname，需要一个子域来正确地将流量路由到终结点。 有关 DNS 记录和域映射的详细信息，请参阅[与流量管理器映射域](https://docs.microsoft.com/azure/app-service/web-sites-traffic-manager-custom-domain-name)

Azure 将创建一个子域的用户的终结点可用于访问你的 web 应用。 对于本教程中，可以使用**app.northwind.com**，但您应自定义你自己的域上基于此值。

您还需要使用 A 记录，Azure Stack 终结点创建子域。 可以使用**azurestack.northwind.com**。

### <a name="configure-a-custom-domain-in-azure"></a>在 Azure 中配置自定义域

1. 添加**app.northwind.com**主机名到通过 Azure web 应用[映射到 Azure 应用服务的 CNAME](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record)。

### <a name="configure-custom-domains-in-azure-stack"></a>在 Azure Stack 中配置自定义域

1. 添加**azurestack.northwind.com**到 Azure Stack web 应用的主机名[将 A 记录映射到 Azure 应用服务](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-an-a-record)。 应用服务应用程序使用 Internet 路由的 IP 地址。

2. 添加**app.northwind.com**到 Azure Stack web 应用的主机名[映射到 Azure 应用服务的 CNAME](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record)。 使用你在上一步 (1) 作为目标为配置 CNAME 的主机名。

## <a name="configure-ssl-certificates-for-cross-cloud-scaling"></a>配置 SSL 证书，以便跨云缩放

您需要确保收集的 web 应用程序的敏感数据的安全传输到和在 SQL 数据库上进行静态中。

将配置到 Azure 和 Azure Stack web 应用程序对所有传入流量使用 SSL 证书。

### <a name="add-ssl-to-azure-and-azure-stack"></a>将 SSL 添加到 Azure 和 Azure Stack

若要将 SSL 添加到 Azure:

1. 请确保你获取的 SSL 证书有效所创建的子域。 （是可以使用通配符证书。）

2. 在 Azure 中，按照中的说明**准备你的 web 应用**并**绑定 SSL 证书**的部分[将现有的自定义 SSL 证书绑定到 Azure Web 应用](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-ssl)文章。 选择**基于 SNI 的 SSL**作为**SSL 类型**。

3. 将所有流量重都定向到 HTTPS 端口。 按照中的说明**强制实施 HTTPS**一部分[将现有的自定义 SSL 证书绑定到 Azure Web 应用](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-ssl)一文。

若要将 SSL 添加到 Azure Stack:

- 重复步骤 1-3 用于 Azure。

## <a name="configure-and-deploy-the-web-application"></a>配置和部署 web 应用程序

将配置应用程序代码来报告遥测数据到正确的 Application Insights 实例，并使用正确的连接字符串配置 web 应用程序。 若要了解有关 Application Insights 的详细信息，请参阅[什么是 Application Insights？](https://docs.microsoft.com/azure/application-insights/app-insights-overview)

### <a name="add-application-insights"></a>添加 Application Insights

1. 在 Microsoft Visual Studio 中打开 web 应用程序。

2. [添加 Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core#add-application-insights-telemetry)到项目中以传输用于创建警报时 web 流量增加或减少 Application Insights 的遥测数据。

### <a name="configure-dynamic-connection-strings"></a>配置动态连接字符串

Web 应用程序的每个实例将使用另一种方法来连接到 SQL 数据库。 在 Azure 中的应用程序使用 SQL Server 虚拟机 (VM) 的专用 IP 地址并在 Azure Stack 中的应用程序使用 SQL Server VM 的公共 IP 地址。

> [!Note]  
> 在 Azure Stack 集成系统中，公共 IP 地址不应 Internet 路由。 在 Azure Stack 开发工具包 (ASDK)，公共 IP 地址不是外部 ASDK 可路由的。

应用服务环境变量可用于将不同的连接字符串传递给应用程序的每个实例。

1. 在 Visual Studio 中打开该应用程序。

2. 打开 Startup.cs 并查找以下代码块：

    ```C#
    services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
    ```

3. 将以下代码，使用 appsettings.json 文件中定义的连接字符串替换为上一个代码块：

    ```C#
    services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
     // Automatically perform database migration
     services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
    ```

### <a name="configure-app-service-application-settings"></a>配置应用服务应用程序设置

1. 为 Azure 和 Azure Stack 中创建连接字符串。 字符串应该是相同的但使用的 IP 地址除外。

2. 在 Azure 和 Azure Stack 中，添加适当的连接字符串[为应用程序设置](https://docs.microsoft.com/azure/app-service/web-sites-configure)web 应用程序中使用`SQLCONNSTR\_`作为前缀的名称。

3. **保存**的 web 应用设置和重新启动该应用程序。

## <a name="enable-automatic-scaling-in-global-azure"></a>启用全球 Azure 中的自动缩放

在应用服务环境中创建 web 应用时开始一个实例。 您可以自动横向扩展以添加实例，以提供更多计算资源为你的应用。 同样，可以自动缩放中，并减少实例数你的应用需要。

> [!Note]  
> 需要具有应用服务计划配置横向扩展和缩小。 如果没有计划，创建一个开始下一个步骤之前。

### <a name="enable-automatic-scale-out"></a>启用自动横向扩展

1. 在 Azure 中，找到你想要横向扩展，然后选择的站点的应用服务计划**横向扩展 （应用服务计划）**。

    ![横向扩展](media/azure-stack-solution-hybrid-cloud/image16.png)

2. 选择**启用自动缩放**。

    ![启用自动缩放](media/azure-stack-solution-hybrid-cloud/image17.png)

3. 输入的名称**自动缩放设置名称**。 有关**默认**自动缩放规则，选择**基于指标缩放**。 设置**实例限制**到**最小值： 1**，**最大： 10**，以及**默认值： 1**。

    ![配置自动缩放](media/azure-stack-solution-hybrid-cloud/image18.png)

4. 选择**添加规则 +**。

5. 在中**指标源**，选择**当前资源**。 使用以下条件和操作规则。

**条件**

1. 下**时间聚合**选择**平均**。

2. 下**度量值名称**，选择**CPU 百分比**。

3. 下**运算符**，选择**大于**。

   - 设置**阈值**到**50**。
   - 设置**持续时间**到**10**。

**Action**

1. 下**操作**，选择**增加计数**。

2. 设置**实例计数**到**2**。

3. 设置**冷却**到**5**。

4. 选择 **添加** 。

5. 选择**添加规则 +**。

6. 在中**指标源**，选择**当前资源。**

   > [!Note]  
   > 当前资源将包含应用服务计划的名称/GUID，并**资源类型**并**资源**下拉列表将灰显。

### <a name="enable-automatic-scale-in"></a>启用自动缩放

时发送的流量减少，Azure web 应用程序可以自动减少以降低成本的活动实例数。 此操作是侧重于准确性比向外扩展，以便最大程度减少对应用程序用户的影响。

1. 导航到**默认**横向扩展条件中，选择 **+ 添加规则**。 使用以下条件和操作规则。

**条件**

1. 下**时间聚合**选择**平均**。

2. 下**度量值名称**，选择**CPU 百分比**。

3. 下**运算符**，选择**小于**。

   - 设置**阈值**到**30**。
   - 设置**持续时间**到**10**。

**Action**

1. 下**操作**，选择**减少计数**。

   - 设置**实例计数**到**1**。
   - 设置**冷却**到**5**。

2. 选择 **添加** 。

## <a name="create-a-traffic-manager-profile-and-configure-cross-cloud-scaling"></a>创建流量管理器配置文件和配置跨云缩放

你将在 Azure 中创建流量管理器配置文件，然后配置终结点以启用跨云缩放。

### <a name="create-traffic-manager-profile"></a>创建流量管理器配置文件

1. 选择**创建资源**
2. 选择**网络**
3. 选择**流量管理器配置文件**进行以下配置：

   - 在中**名称**，输入你的配置文件的名称。 此名称**必须**trafficmanager.net 区域中是唯一的用于创建新的 DNS 名称 (例如，northwindstore.trafficmanager.net)。
   - 有关**路由方法**，选择**加权**。
   - 有关**订阅**，选择你想要创建此配置文件中的订阅。
   - 在中**资源组**，创建此配置文件的新资源组。
   - 在**资源组位置**中，选择资源组的位置。 此设置指的是资源组的位置，并且对将全局部署的流量管理器配置文件没有任何影响。

4. 选择“创建”。

    ![创建流量管理器配置文件](media/azure-stack-solution-hybrid-cloud/image19.png)

 流量管理器配置文件的全局部署完成后，它所示的资源组下创建的资源的列表。

### <a name="add-traffic-manager-endpoints"></a>添加流量管理器终结点

1. 搜索你创建的流量管理器配置文件。 （如果您导航到该配置文件的资源组，选择配置文件。）

2. 在中**流量管理器配置文件**下**设置**，选择**终结点**。

3. 选择 **添加** 。

4. 在中**添加终结点**，适用于 Azure Stack 中使用以下设置：

   - 有关**类型**，选择**外部终结点**。
   - 输入**名称**此终结点。
   - 有关**完全限定的域名 (FQDN) 或 IP**输入 Azure Stack web 应用的外部 URL。
   - 有关**权重**，保留默认设置， **1**。 此权重会导致所有流量转到此终结点，如果运行状况良好。
   - 将保留**添加为已禁用**未选中状态。

5. 选择**确定**保存 Azure Stack 终结点。

接下来，你将配置 Azure 终结点。

1. 上**流量管理器配置文件**，选择**终结点**。
2. 选择“+添加”。
3. 上**添加终结点**，对 Azure 使用以下设置：

   - 有关**类型**，选择**Azure 终结点**。
   - 输入**名称**此终结点。
   - 有关**目标资源类型**，选择**应用服务**。
   - 有关**目标资源**，选择**选择一个应用服务**若要查看同一订阅中的 Web 应用的列表。
   - 在“资源”中，选取要添加为第一个终结点的应用服务。
   - 有关**权重**，选择**2**。 这会导致所有流量转到此终结点，如果主终结点不正常，或者你，重新引导流量时触发的规则/警报。
   - 将保留**添加为已禁用**未选中状态。

4. 选择**确定**保存 Azure 终结点。

这两个终结点配置后，它们列在**流量管理器配置文件**选中**终结点**。 以下屏幕截图中的示例演示两个终结点，其中的每个状态和配置信息。

![终结点](media/azure-stack-solution-hybrid-cloud/image20.png)

## <a name="set-up-application-insights-monitoring-and-alerting"></a>设置 Application Insights 监视和警报

Azure Application Insights 可以监视你的应用程序以及发送警报根据你配置的条件。 下面是一些示例： 应用程序不可用、 遇到故障，或显示的性能问题。

将使用 Application Insights 指标创建警报。 这些警报触发器时，实例将从 Azure Stack 会自动切换到 Azure，以横向扩展，然后再返回到 Azure Web 应用程序堆栈中的刻度。

### <a name="create-an-alert-from-metrics"></a>从指标创建警报

对于本教程中，导航到资源组，然后选择要打开的 Application Insights 实例**Application Insights**。

![Application Insights](media/azure-stack-solution-hybrid-cloud/image21.png)

将使用此视图在警报中创建横向扩展警报和小数位数。

### <a name="create-the-scale-out-alert"></a>创建横向扩展警报

1. 下**配置**，选择**警报 （经典）**。
2. 选择**添加指标警报 （经典）**。
3. 在中**添加规则**，配置以下各项：

   - 有关**名称**，输入**迸发到 Azure 云**。
   - 一个**说明**是可选的。
   - 下**源**，**警报**，选择**指标**。
   - 下**条件**，选择你的订阅，流量管理器配置文件和流量管理器配置文件的名称的资源组的资源。

4. 有关**指标**，选择**请求速率**。
5. 有关**条件**，选择**大于**。
6. 有关**阈值**，输入**2**。
7. 有关**期**，选择**过去 5 分钟内**。
8. 下**通过通知**:
   - 选中的复选框**电子邮件所有者、 参与者和读者**。
   - 输入的电子邮件地址**其他管理员 email(s)**。

9. 在菜单栏上，选择**保存**。

### <a name="create-the-scale-in-alert"></a>在警报中创建规模

1. 下**配置**，选择**警报 （经典）**。
2. 选择**添加指标警报 （经典）**。
3. 在中**添加规则**，配置以下各项：

   - 有关**名称**，输入**恢复到 Azure Stack 缩放**。
   - 一个**说明**是可选的。
   - 下**源**，**警报**，选择**指标**。
   - 下**条件**，选择你的订阅，流量管理器配置文件和流量管理器配置文件的名称的资源组的资源。

4. 有关**指标**，选择**请求速率**。
5. 有关**条件**，选择**小于**。
6. 有关**阈值**，输入**2**。
7. 有关**期**，选择**过去 5 分钟内**。
8. 下**通过通知**:
   - 选中的复选框**电子邮件所有者、 参与者和读者**。
   - 输入的电子邮件地址**其他管理员 email(s)**。

9. 在菜单栏上，选择**保存**。

以下屏幕截图显示了向外扩展和缩小的警报。

   ![警报（经典）](media/azure-stack-solution-hybrid-cloud/image22.png)

## <a name="redirect-traffic-between-azure-and-azure-stack"></a>将 Azure 与 Azure Stack 之间的流量重定向

你可以配置手动或自动切换的 Web 应用程序流量 Azure 和 Azure Stack 之间进行切换。

### <a name="configure-manual-switching-between-azure-and-azure-stack"></a>配置 Azure 和 Azure Stack 之间手动切换

当你的网站达到配置的阈值时，你将收到警报。 使用以下步骤手动将流量重定向到 Azure。

1. 在 Azure 门户中，选择 Traffic Manager 配置文件。

    ![流量管理器终结点](media/azure-stack-solution-hybrid-cloud/image20.png)

2. 选择**终结点**。
3. 选择**Azure 终结点**。
4. 下**状态**，选择**已启用**，然后选择**保存**。

    ![启用 Azure 终结点](media/azure-stack-solution-hybrid-cloud/image23.png)

5. 上**终结点**对于流量管理器配置文件中，选择**外部终结点**。
6. 下**状态**，选择**禁用**，然后选择**保存**。

    ![禁用 Azure Stack 终结点](media/azure-stack-solution-hybrid-cloud/image24.png)

配置终结点后，应用程序流量将转到 Azure 向外缩放 web 应用而不是 Azure Stack web 应用。

 ![终结点更改](media/azure-stack-solution-hybrid-cloud/image25.png)

若要反转流到 Azure Stack，请使用到前面的步骤：

- 启用 Azure Stack 终结点
- 禁用 Azure 终结点

### <a name="configure-automatic-switching-between-azure-and-azure-stack"></a>配置 Azure 和 Azure Stack 之间自动切换

此外可以使用 Application Insights 监视在运行应用程序[无服务器](https://azure.microsoft.com/overview/serverless-computing/)Azure Functions 提供的环境。

在此方案中，可以配置为使用 webhook 调用的函数应用的 Application Insights。 此应用会自动启用或禁用对警报的响应中的终结点。

使用以下步骤作为指南来配置自动流量切换。

1. 创建 Azure Function app。
2. 创建 HTTP 触发的函数。
3. 为资源管理器、 Web 应用和流量管理器导入 Azure Sdk。
4. 开发代码：

   - 向你的 Azure 订阅进行身份验证。
   - 使用切换流量定向到 Azure 或 Azure Stack 的流量管理器终结点的参数。

5. 保存你的代码并添加使用到适当的参数的函数应用的 URL **Webhook** Application Insights 警报规则设置部分。
6. Application Insights 警报触发时，将自动重定向流量。

## <a name="next-steps"></a>后续步骤

- 若要了解有关 Azure 云模式的详细信息，请参阅[云设计模式](https://docs.microsoft.com/azure/architecture/patterns)。
