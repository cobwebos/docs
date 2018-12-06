---
title: 创建包含 Azure 和 Azure Stack 的异地分布式应用程序解决方案 |Microsoft Docs
description: 了解如何创建包含 Azure 和 Azure Stack 的异地分布式应用程序解决方案。
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
ms.date: 09/24/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 632393696274eaf6f876ea717b5fccf7d4fbea3f
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2018
ms.locfileid: "52965387"
---
# <a name="tutorial-create-a-geo-distributed-app-solution-with-azure-and-azure-stack"></a>教程： 创建包含 Azure 和 Azure Stack 的异地分布式应用程序解决方案

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

了解如何将流量定向到特定终结点根据使用异地分布式应用程序模式的各种指标。 创建流量管理器配置文件与基于地理位置的路由和终结点配置可确保信息路由到基于区域的要求、 公司和国际法规和你的数据需求的终结点。

在本教程中，我们将构建一个示例环境来完成以下任务：

> [!div class="checklist"]
> - 创建异地分布式应用程序。
> - 使用流量管理器，以面向您的应用程序。

## <a name="use-the-geo-distributed-apps-pattern"></a>使用异地分布式应用程序模式

使用异地分布式模式时，您的应用程序可以跨多个区域。 可以到公有云，默认值，但某些用户可能需要他们的数据保留在其区域。 您可以将用户定向到最合适的云根据其需求。

### <a name="issues-and-considerations"></a>问题和注意事项

#### <a name="scalability-considerations"></a>可伸缩性注意事项

使用本教程将生成的解决方案是不是为了满足可伸缩性。 但是，如果与其他 Azure 和内部部署技术和解决方案结合使用可以满足可伸缩性要求。 使用自动缩放通过流量管理器创建 hyrbid 解决方案的信息，请参阅[使用 Azure 创建跨云缩放解决方案](azure-stack-solution-cloud-burst.md)。

#### <a name="availability-considerations"></a>可用性注意事项

与可扩展性考虑事项情况一样，此解决方案不会直接解决可用性。 但是，与我们的可扩展性考虑事项也类似，Azure，并在本地技术和解决方案可以实现此解决方案来确保高可用性所涉及的所有组件中。

### <a name="when-to-use-this-pattern"></a>何时使用此模式

- 你的组织有需要自定义区域的安全和通讯组策略的国际分支。

- 每个组织办公室拉取员工、 业务和设施的数据，需要报告每个本地法规和时区的活动。

- 只要对单个区域内和跨区域的多个应用部署进行应用的水平扩展，即可达到高缩放性要求以处理极高的负载要求。

### <a name="planning-the-topology"></a>规划拓扑

分布式应用程序占用空间之前，最好先具有以下知识：

-   **应用的自定义域：** 客户访问应用时使用的自定义域名是什么？ 示例应用的自定义域名是*www.scalableasedemo.com。*

-   **流量管理器域：** 需要在创建时选择的域名[Azure 流量管理器配置文件](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-manage-profiles)。 此名称与 *trafficmanager.net* 后缀相结合，以注册流量管理器所管理的域条目。 就示例应用而言，选择的名称是 *scalable-ase-demo*。 因此，管理流量管理器的完整域名是*可扩展 ase demo.trafficmanager.net*。

-   **缩放应用占用空间的策略：** 应用程序占用空间是否分布到单个区域中的多个应用服务环境？ 是多个区域吗？ 这两种方法混合使用？ 决策依据应来自于客户流量的来源位置，以及其余应用的支持后端基础结构的伸缩性。 例如，对于 100% 无状态的应用程序，可以使用每一 Azure 区域多个应用服务环境的组合，乘以跨多个 Azure 区域部署的应用服务环境数，来大幅缩放应用。 使用 15 + 全球 Azure 区域个可供选择，客户可真正构建世界范围的超大规模应用程序占用空间。 在本文所使用的示例应用中，有三个应用服务环境创建在单个 Azure 区域（美国中南部）。

-   **应用服务环境的命名约定：** 每个应用服务环境要求的唯一名称。 超出一个或两个应用服务环境，最好采用以帮助识别每个应用服务环境的命名约定。 示例应用中使用了简单的命名约定。 三个应用服务环境的名称分别是 *fe1ase*、*fe2ase* 和 *fe3ase*。

-   **应用程序的命名约定：** 由于将部署应用程序的多个实例，为每个已部署的应用实例需要一个名称。 应用服务环境可以跨多个应用服务环境使用相同的应用名称。 由于每个应用服务环境具有唯一的域后缀，开发人员可以选择重复使用每个环境中完全相同的应用程序名称。 例如，开发人员可以将应用命名，如下所示： *myapp.foo1.p.azurewebsites.net*， *myapp.foo2.p.azurewebsites.net*， *myapp.foo3.p.azurewebsites.net*，等等。在此方案中应用，每个应用实例具有唯一的名称。 所用的应用实例名称是 *webfrontend1*、*webfrontend2* 和 *webfrontend3*。

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack 是 Azure 的扩展。 Azure Stack 将云计算的灵活性和创新性带入你的本地环境，并支持唯一的混合云，以允许你在任何地方构建和部署混合应用。  
> 
> 白皮书 [Design Considerations for Hybrid Applications](https://aka.ms/hybrid-cloud-applications-pillars)（混合应用程序的设计注意事项）回顾了设计、部署和运行混合应用程序所需的软件质量要素（位置、可伸缩性、可用性、复原能力、可管理性和安全性）。 这些设计注意事项有助于优化混合应用程序设计，从而最大限度地减少生产环境中的难题。

## <a name="part-1-create-a-geo-distributed-app"></a>第 1 部分： 创建异地分布式应用程序

在此部分中，将创建 web 应用。

> [!div class="checklist"]
> - 创建 web 应用并发布
> - 将代码添加到 Azure 存储库
> - 应用生成指向多个云目标。
> - 管理和配置 CD 过程

### <a name="prerequisites"></a>必备组件

需要 Azure 订阅和 Azure Stack 安装。

### <a name="geo-distributed-app-steps"></a>异地分布式应用程序的步骤

### <a name="obtain-a-custom-domain-and-configure-dns"></a>获取自定义域并配置 DNS

更新 DNS 区域文件 foCreate web 应用和 publishr 域。 然后，azure AD 才能验证自定义域名的所有权。 使用[Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal)在 Azure 中，Azure/Office 365/外部 DNS 记录或添加 DNS 条目处[其他 DNS 注册机构](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)。

1. 使用公共注册机构注册自定义域。

2. 登录到域的域名注册机构。 已批准的管理员可能需要进行 DNS 更新。

3. 通过添加 Azure AD 提供的 DNS 条目更新域的 DNS 区域文件。 DNS 条目不会更改行为，例如邮件路由或 web 托管。

### <a name="create-web-apps-and-publish"></a>创建 web 应用并发布

设置混合 CI/CD，以将 Web 应用部署到 Azure 和 Azure Stack，并自动将更改推送到这两个云中。

> [!Note]  
> Azure 堆栈用于综合运行 （Windows Server 和 SQL） 和应用服务部署到的正确映像是必需的。 查看应用服务文档"[开始使用 Azure Stack 上的应用服务之前](/articles/azure-stack/azure-stack-app-service-before-you-get-started)"部分，了解 Azure Stack 操作员。

#### <a name="add-code-to-azure-repos"></a>将代码添加到 Azure 存储库

1. 登录到 Visual Studio**具有项目创建权限帐户**Azure 存储库。

    混合持续集成/持续交付 (CI/CD) 可以将应用于应用程序代码和基础结构代码。 使用[Azure 资源管理器模板](https://azure.microsoft.com/resources/templates/)用于这两个私有云和托管云开发。

    ![Alt text](media/azure-stack-solution-geo-distributed/image1.JPG)

2. 创建并打开默认 Web 应用以**克隆存储库**。

    ![Alt text](media/azure-stack-solution-geo-distributed/image2.png)

### <a name="create-web-app-deployment-in-both-clouds"></a>在这两个云中创建的 web 应用部署

1.  编辑**WebApplication.csproj**文件： 选择**Runtimeidentifier** ，并添加**win10-x64**。 (请参阅[独立式部署](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd)文档。)

    ![Alt text](media/azure-stack-solution-geo-distributed/image3.png)

1.  **签入到 Azure 存储库代码**使用团队资源管理器。

2.  确认**应用程序代码**已签入到 Azure 存储库。

### <a name="create-the-build-definition"></a>创建生成定义

1. **登录到 Azure 管道**确认能够创建生成定义。

2. 添加 **-r win10-x64** 代码。 在 .Net Core 中触发独立部署时需要此代码。

    ![Alt text](media/azure-stack-solution-geo-distributed/image4.png)

3. **运行生成**。 [独立部署生成](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd)过程将发布可在 Azure 和 Azure Stack 上运行的项目。

**使用 Azure 的托管的代理**

在 Azure 管道中使用托管的代理是一个方便的选项来生成和部署 web 应用。 Microsoft Azure，从而启用不间断地持续开发、 测试和部署自动执行维护和升级。

### <a name="manage-and-configure-the-cd-process"></a>管理和配置 CD 过程

Azure DevOps 和 Azure DevOps 服务器提供高度可配置和可管理管道发布到多个环境，如开发、 过渡、 QA 和生产环境;包括在特定阶段需要审批。

#### <a name="create-release-definition"></a>创建发布定义


![Alt text](media/azure-stack-solution-geo-distributed/image5.png)

1.  选择**加上**按钮添加新的发行版下**版本选项卡**的 Visual Studio Online (VSO) 中的生成和发布页中。

    ![Alt text](media/azure-stack-solution-geo-distributed/image6.png)

2. 应用“Azure 应用服务部署”模板。

    ![Alt text](media/azure-stack-solution-geo-distributed/image7.png)

3. 在“添加项目”下拉菜单中，为 Azure 云生成应用**添加项目**。

    ![Alt text](media/azure-stack-solution-geo-distributed/image8.png)

4. 管道选项卡，选择**阶段时，任务**环境的链接，并设置 Azure 云环境值。

    ![Alt text](media/azure-stack-solution-geo-distributed/image9.png)

5. 设置**环境名称**，并选择 Azure 云终结点的 Azure **订阅**。

    ![Alt text](media/azure-stack-solution-geo-distributed/image10.png)

6. 在“环境名称”下，设置所需的 **Azure 应用服务名称**。

    ![Alt text](media/azure-stack-solution-geo-distributed/image11.png)

7. 在 Azure 云托管环境的“代理队列”下输入 **Hosted VS2017**。

    ![Alt text](media/azure-stack-solution-geo-distributed/image12.png)

8. 在“部署 Azure 应用服务”菜单中，为环境选择有效的**包或文件夹**。 选择**文件夹位置**旁边的“确定”。

    ![Alt text](media/azure-stack-solution-geo-distributed/image13.png)

    ![Alt text](media/azure-stack-solution-geo-distributed/image14.png)

9. 保存所有更改并返回**发布管道**。

    ![Alt text](media/azure-stack-solution-geo-distributed/image15.png)

10. 选择 Azure Stack 应用的生成以添加**新项目**。

    ![Alt text](media/azure-stack-solution-geo-distributed/image16.png)

11. 添加一个详细环境应用**Azure App Service 部署。**

    ![Alt text](media/azure-stack-solution-geo-distributed/image17.png)

12. 将新环境命名**Azure Stack。**

    ![Alt text](media/azure-stack-solution-geo-distributed/image18.png)

13. 在“任务”选项卡下找到 Azure Stack 环境。

    ![Alt text](media/azure-stack-solution-geo-distributed/image19.png)

14. 选择 Azure Stack 终结点的**订阅**。

  ![Alt text](media/azure-stack-solution-geo-distributed/image20.png)

15. 将 Azure Stack Web 应用名称设置为**应用服务名称**。

    ![Alt text](media/azure-stack-solution-geo-distributed/image21.png)

16. 选择“Azure Stack 代理”。

    ![Alt text](media/azure-stack-solution-geo-distributed/image22.png)

17. 在“部署 Azure 应用服务”部分下，为环境选择有效的**包或文件夹**。 选择**文件夹位置**旁边的“确定”。

    ![Alt text](media/azure-stack-solution-geo-distributed/image23.png)

    ![Alt text](media/azure-stack-solution-geo-distributed/image24.png)

18. 下**变量**选项卡上添加一个名为变量`VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`，将作为其值设置`true`，并将范围限定为`Azure Stack`。

    ![Alt text](media/azure-stack-solution-geo-distributed/image25.png)

19. 选择**连续**部署中项目和启用的触发器图标**仍继续**部署触发器。

    ![Alt text](media/azure-stack-solution-geo-distributed/image26.png)

20. 选择**预先部署**在 Azure Stack 环境中的条件图标并将触发器设置为**发布后。**

21. 保存所有更改。

> [!Note]  
>  任务的某些设置可能已在从模板创建发布定义时自动定义为[环境变量](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables)。 无法在任务设置中修改这些设置；必须选择父环境项才能编辑这些设置。

## <a name="part-2-update-web-app-options"></a>第 2 部分： 更新 web 应用程序选项

[Azure Web 应用](https://docs.microsoft.com/azure/app-service/app-service-web-overview)提供高度可缩放、自修补的 Web 托管服务。 

![Alt text](media/azure-stack-solution-geo-distributed/image27.png)

> [!div class="checklist"]
> - 将现有的自定义 DNS 名称映射到 Azure Web 应用
> - 使用 * * CNAME 记录器**记录**自定义的 DNS 名称映射到应用服务。

### <a name="map-an-existing-custom-dns-name-to-azure-web-apps"></a>将现有的自定义 DNS 名称映射到 Azure Web 应用

> [!Note]  
>  以外的所有自定义 DNS 名称的根域 （适用于 example,northwind.com) 使用 CNAME。

若要将实时站点及其 DNS 域名迁移到应用服务，请参阅[将活动 DNS 名称迁移到 Azure 应用服务](https://docs.microsoft.com/azure/app-service/app-service-custom-domain-name-migrate)。

### <a name="prerequisites"></a>必备组件

完成本教程：

-   [创建应用服务应用](https://docs.microsoft.com/azure/app-service/)，或使用另一个教程中创建的应用。

-   购买的域名并确保域提供程序的访问的 DNS 注册表。

更新域的 DNS 区域文件。 Azure AD 将验证自定义域名的所有权。 使用[Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal)在 Azure 中，Azure/Office 365/外部 DNS 记录或添加 DNS 条目处[其他 DNS 注册机构](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)。

-   使用公共注册机构注册自定义域。

-   登录到域的域名注册机构。 （已批准的管理员可能需要进行 DNS 更新。）

-   通过添加 Azure AD 提供的 DNS 条目更新域的 DNS 区域文件。

例如，若要添加 DNS 条目 fornorthwindcloud.comand www.northwindcloud.com，配置 thenorthwindcloud.com 根域的 DNS 设置。

> [!Note]  
>  可使用购买的域名[Azure 门户](https://docs.microsoft.com/azure/app-service/custom-dns-web-site-buydomains-web-app)。  
> 若要映射自定义 DNS 名称到 Web 应用，Web 应用的[应用服务计划](https://azure.microsoft.com/pricing/details/app-service/)必须位于付费层（“共享”、“基本”、“标准”或“高级”）。



### <a name="create-and-map-cname-and-a-records"></a>创建并映射 CNAME 和 A 记录

#### <a name="access-dns-records-with-domain-provider"></a>通过域提供商访问 DNS 记录

> [!Note]  
>  使用 Azure DNS 配置 Azure Web 应用的自定义 DNS 名称。 有关详细信息，请参阅[使用 Azure DNS 为 Azure 服务提供自定义域设置](https://docs.microsoft.com/azure/dns/dns-custom-domain)。

1.  登录到主提供商的网站。

2.  查找管理 DNS 记录的页面。 每个域提供商都有其自己的 DNS 记录界面。 查找站点中标记为“域名”、“DNS”或“名称服务器管理”的区域。

可以在中查看 DNS 记录页**我的域**。 找到名为的链接**区域文件**， **DNS 记录**，或**高级的配置**。

以下屏幕截图是 DNS 记录页的一个示例：

![示例 DNS 记录页](media/azure-stack-solution-geo-distributed/image28.png)

1.  在域名注册机构，选择**添加或创建**创建一条记录。 某些提供商提供了不同的链接来添加不同的记录类型。 请查阅提供商的文档。

2.  添加 CNAME 记录以将子域映射到应用的默认主机名。

  Www.northwindcloud.comdomain 示例中，将添加 CNAME 记录映射 namewwwto < 应用\_名称 >。 azurewebsites.net。

添加 CNAME 后, DNS 记录页如以下示例所示：

![在门户中导航到 Azure 应用](media/azure-stack-solution-geo-distributed/image29.png)

### <a name="enable-the-cname-record-mapping-in-azure"></a>在 Azure 中启用 CNAME 记录映射

1.  在新的选项卡上，登录到 Azure 门户中，

2.  导航到应用服务。

3.  选择 web 应用。

4.  在 Azure 门户中的应用页左侧导航窗格中，选择“自定义域”。

5.  选择“添加主机名”旁边的 **+** 图标。

1.  键入完全限定的域名，如`www.northwindcloud.com`。

2.  选择“验证”。

3.  如果所示，添加其他类型的其他记录 (`A`或`TXT`) 到域名注册机构 DNS 记录。 Azure 将提供的值和这些记录的类型：

    a.  要映射到应用 IP 地址的 A 记录。

    b.  一个**TXT**记录映射到应用的默认主机名 < a p p _ >。 azurewebsites.net。 应用服务仅在配置期间，使用此记录来验证自定义域的所有权。 验证之后，删除 TXT 记录。

4.  完成此任务中的域注册机构选项卡并重新验证直到**添加主机名**按钮会被激活。

5.  请确保 * * 主机名记录类型设置为**CNAME （www.example.com 或任何子域）**。

6.  选择“添加主机名”。

7.  键入完全限定的域名，如`northwindcloud.com`。

8.  选择“验证”。

9.  **添加**激活。

10. 请确保 * * 主机名记录类型设置为**记录 (example.com)**。

11. **添加主机名**。

  它可能需要一些时间才会反映在应用中的新主机名**自定义域**页。 请尝试刷新浏览器来更新数据。
  
  ![Alt text](media/azure-stack-solution-geo-distributed/image31.png) 
  
  如果出现错误，将出现验证错误通知，让它在页面底部。 ![验证错误](media/azure-stack-solution-geo-distributed/image32.png)

> [!Note]  
>  可能会重复上述步骤以映射通配符域 (\*。 northwindcloud.com)... 这样无需创建为每个单独的 CNAME 记录添加到此应用服务的任何其他子域。 按照要配置此设置的注册机构说明。

#### <a name="test-in-a-browser"></a>在浏览器中测试

浏览到前面配置的 DNS 名称 (例如， `northwindcloud.com`，www.northwindcloud.com。

## <a name="part-3-bind-a-custom-ssl-cert"></a>第 3 部分： 绑定的自定义 SSL 证书

在本部分中：

> [!div class="checklist"]
> - 将自定义 SSL 证书绑定到应用服务
> - 应用强制实施 HTTPS
> - 使用脚本自动执行 SSL 证书绑定

> [!Note]  
> 如果需要获取客户在 Azure 门户中的 SSL 证书并将其绑定到 web 应用。 请遵循[应用服务证书教程](https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site)。

### <a name="prerequisites"></a>必备组件

若要完成本教程，需执行以下操作：

-   [创建应用服务应用](https://docs.microsoft.com/azure/app-service/)
-   [将自定义 DNS 名称映射到 Web 应用](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain)
-   获取 SSL 证书从受信任的证书颁发机构，并使用密钥进行签名请求

### <a name="requirements-for-your-ssl-certificate"></a>SSL 证书的要求

若要在应用服务中使用证书，该证书必须满足以下所有要求：

-   已由受信任的证书颁发机构签名

-   已导出为受密码保护的 PFX 文件

-   包含长度至少为 2048 位的私钥

-   包含证书链中的所有中间证书

> [!Note]  
>  **椭圆曲线加密 (ECC) 证书**使用应用服务但不是包括在本指南。 有关创建 ECC 证书中的帮助，请查阅证书颁发机构。 

#### <a name="prepare-the-web-app"></a>准备 web 应用

若要将自定义 SSL 证书绑定到 web 应用[应用服务计划](https://azure.microsoft.com/pricing/details/app-service/)必须在**基本**，**标准**，或**高级**层。

#### <a name="sign-in-to-azure"></a>登录 Azure

1.  打开[Azure 门户](https://portal.azure.com/)并导航到 web 应用。

2.  从左侧菜单中，选择**应用服务**，然后选择 web 应用名称。

![选择 Web 应用](media/azure-stack-solution-geo-distributed/image33.png)

#### <a name="check-the-pricing-tier"></a>检查定价层

1.  在 web 应用页的左侧导航窗格中，向下滚动到**设置**部分，并选择**扩大 （应用服务计划）**。

    ![扩展菜单](media/azure-stack-solution-geo-distributed/image34.png)

1.  确保 web 应用不在**免费**或**共享**层。 Web 应用的当前层中深蓝色框突出显示。

    ![检查定价层](media/azure-stack-solution-geo-distributed/image35.png)

“免费”或“共享”层不支持自定义 SSL。 若要放大，请按照下一节中的步骤或**选择定价层**页上，并跳到[上传和绑定 SSL 证书](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl)。

#### <a name="scale-up-your-app-service-plan"></a>纵向扩展应用服务计划

1.  选择“基本”、“标准”或“高级”层。

2.  选择“选择”。

![选择定价层](media/azure-stack-solution-geo-distributed/image36.png)

显示通知时，缩放操作已完成。

![扩展通知](media/azure-stack-solution-geo-distributed/image37.png)

#### <a name="bind-your-ssl-certificate-and-merge-intermediate-certificates"></a>将 SSL 证书绑定和合并中间证书

合并多个证书链中。

1. **打开每个证书**收到在文本编辑器中。

2. 创建名为 mergedcertificate.crt 的合并证书文件。 在文本编辑器中，将每个证书的内容复制到此文件。 证书的顺序应遵循证书链中的顺序，以你的证书开头，以根证书结尾， 如以下示例所示：

    ```Text

    -----BEGIN CERTIFICATE-----

    <your entire Base64 encoded SSL certificate>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded intermediate certificate 1>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded intermediate certificate 2>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded root certificate>

    -----END CERTIFICATE-----
    ```

#### <a name="export-certificate-to-pfx"></a>将证书导出为 PFX

使用生成的证书的私钥导出合并的 SSL 证书。

私钥文件是通过 OpenSSL 创建的。 若要将证书导出为 PFX，请运行以下命令，替换占位符 *< 私有密钥文件 >* 并 *< 合并证书文件形式的 >* 使用私有密钥路径和合并证书文件。

```PowerShell
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>
```

出现提示时，定义导出密码更高版本将 SSL 证书上载到应用服务。

当 IIS 或**Certreq.exe**用于生成证书请求，将证书安装到本地计算机，然后[将证书导出为 PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx)。

#### <a name="upload-the-ssl-certificate"></a>上传 SSL 证书

1.  选择**SSL 设置**在左侧导航栏中的 web 应用。

2.  选择**将证书上传**。

3.  在中**PFX 证书文件**，选择 PFX 文件。

4.  4. 在中**证书密码**，键入导出 PFX 文件时创建的密码。

5.  选择 **“上传”**。

![上传证书](media/azure-stack-solution-geo-distributed/image38.png)

应用服务上传证书，它会显示在**SSL 设置**页。

![Alt text](media/azure-stack-solution-geo-distributed/image39.png)

#### <a name="bind-your-ssl-certificate"></a>绑定 SSL 证书

1.  在中**SSL 绑定**部分中，选择**添加绑定**。

    > [!Note]  
    >  如果证书已上传的但不会出现在中的域名**主机名**下拉列表中，请尝试刷新浏览器页面。

1.  在中**添加 SSL 绑定**页上，使用相应的下拉列表选择要保护的域名和要使用的证书。

2.  在中**SSL 类型**，选择是使用[**服务器名称指示 (SNI)**](http://en.wikipedia.org/wiki/Server_Name_Indication)或基于 IP 的 SSL。

-   **基于 SNI 的 SSL**-可添加基于多个 SNI 的 SSL 绑定。 选择此选项可以使用多个 SSL 证书来保护同一 IP 地址上的多个域。 大多数新式浏览器（包括 Internet Explorer、Chrome、Firefox 和 Opera）都支持 SNI（在[服务器名称指示](http://wikipedia.org/wiki/Server_Name_Indication)中了解更全面的浏览器支持信息）。

-   **基于 IP 的 SSL**-添加一个基于 IP 的 SSL 绑定。 选择此选项只能使用一个 SSL 证书来保护专用公共 IP 地址。 若要保护多个域，确保其安全所有使用相同的 SSL 证书。 这是 SSL 绑定的传统选项。

    1.  选择**添加绑定**。

    ![Alt text](media/azure-stack-solution-geo-distributed/image40.png)

应用服务上传证书，它会显示在**SSL 绑定**部分。

![Alt text](media/azure-stack-solution-geo-distributed/image41.png)

#### <a name="remap-the-a-record-for-ip-ssl"></a>将 A 记录重新映射 IP ssl

如果未在 web 应用中使用基于 IP 的 SSL，请跳到[为自定义域测试 HTTPS](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl)。

默认情况下，web 应用使用共享的公共 IP 地址。 当证书使用基于 IP 的 SSL 绑定时，应用服务创建 web 应用的新的专用 IP 地址。

当一条 A 记录映射到 web 应用时，必须使用专用 IP 地址更新域注册表。

**自定义域**页更新使用新的专用 IP 地址。 复制这[IP 地址](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain)，然后重新映射[记录](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain)到此新的 IP 地址。

#### <a name="test-https"></a>测试 HTTPS

在各种浏览器，浏览到 https://<your.custom.domain>to 确保 ap，就会提供的 web。

![Alt text](media/azure-stack-solution-geo-distributed/image42.png)

> [!Note]  
> 如果出现证书验证错误，自签名的证书可能就是原因，或中间证书可能已省略导出到 PFX 文件时。

#### <a name="enforce-https"></a>实施 HTTPS

默认情况下，任何人都可以访问使用 HTTP 的 web 应用。 可能会重定向到 HTTPS 端口的所有 HTTP 请求。

在 web 应用页中，选择**SL 设置**。 然后，在“仅 HTTPS”中，选择“启用”。

![实施 HTTPS](media/azure-stack-solution-geo-distributed/image43.png)

该操作完成后，导航到任何指向应用的 HTTP Url。 例如：

-   http://<app_name>.azurewebsites.net
-   http://northwindcloud.com
-   <http://www.northwindcloud.com>

#### <a name="enforce-tls-1112"></a>强制实施 TLS 1.1/1.2

该应用程序让[TLS](https://wikipedia.org/wiki/Transport_Layer_Security)默认情况下，不能再被认为是安全的行业标准，如 1.0 [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)。 若要强制实施更高的 TLS 版本，请按照下列步骤操作：

1.  在 web 应用页中，在左侧导航栏中，选择**SSL 设置**。

2.  在中**TLS 版本**，选择的最低 TLS 版本。

![强制实施 TLS 1.1 或 1.2](media/azure-stack-solution-geo-distributed/image44.png)

### <a name="create-a-traffic-manager-profile"></a>创建流量管理器配置文件

1.  选择**创建资源** > **网络** > **Traffic Manager 配置文件** > **创建**.

2.  在“创建流量管理器配置文件”中，按如下所示完成操作：

    1.  在中**名称**，提供配置文件的名称。 此名称必须是唯一的流量 manager.net 区域和 DNS 名称，流量 manager.net，用于访问流量管理器配置文件中的结果中。

    2.  在中**路由方法**，选择**Geographicrouting 方法**。

    3.  在中**订阅**，选择要在其中创建此配置文件的订阅。

    4.  在**资源组**中，创建新的资源组，以在其下放置此配置文件。

    5.  在**资源组位置**中，选择资源组的位置。 此设置指的是资源组的位置，并且对将全局部署的流量管理器配置文件没有任何影响。

    6.  选择“创建”。

    7.  流量管理器配置文件的全局部署完成后，它被列中相应的资源组作为资源之一。

    ![Alt text](media/azure-stack-solution-geo-distributed/image45.png)

### <a name="add-traffic-manager-endpoints"></a>添加流量管理器终结点

1.  在门户搜索栏中，搜索 * * Traffic Manager 配置文件 * * 创建在前面的部分，选择在结果中的流量管理器配置文件名称的显示。

2.  在中**流量管理器配置文件**，在**设置**部分中，选择**终结点**。

3.  选择 **添加** 。

4.  添加 Azure Stack 终结点。

5.  有关**类型**，选择**外部终结点**。

6.  提供**名称**对于此终结点，理想情况下的 Azure Stack 的名称。

7.  完全限定的域名 (**FQDN**)，适用于 Azure Stack Web 应用的使用外部 URL。

8.  在异地映射下选择资源所在的位置，例如，一个区域/大陆**欧洲。**

9.  在国家/地区下拉列表显示，下选择将应用到此终结点，例如，国家/地区**德国**。

10. 使“添加为已禁用”保持未选中状态。

11. 选择“确定”。

12. 添加 Azure 终结点：

    1.  有关**类型**，选择**Azure 终结点**。

    2.  提供**名称**此终结点。

    3.  有关**目标资源类型**，选择**应用服务**。

    4.  有关**目标资源**，选择**选择一个应用服务**以显示同一订阅下的 Web 应用的列表。 在中**资源**，选择为第一个终结点的应用服务使用。

13. 在异地映射下选择资源所在的位置，例如，一个区域/大陆  **/中美 America/加勒比海地区。**

14. 在国家/地区下拉列表显示，将其留空以选择所有上述区域分组。

15. 使“添加为已禁用”保持未选中状态。

16. 选择“确定”

  > [!Note]  
  >  与所有 （世界） 以用作该资源的默认终结点的地理作用域中创建至少一个终结点。

1.  添加完这两个终结点后，这两个终结点会显示在“流量管理器配置文件”中，并且其监视状态为“联机”。

  ![Alt text](media/azure-stack-solution-geo-distributed/image46.png)

**全球企业依赖于 Azure 地理分布功能**

将数据通过 Azure 流量管理器和特定于地理位置的终结点的流量定向使全球企业能够符合区域法规，并让数据符合要求和安全的本地业务和在远程位置之间的成功至关重要。

## <a name="next-steps"></a>后续步骤

- 若要了解有关 Azure 云模式的详细信息，请参阅[云设计模式](https://docs.microsoft.com/azure/architecture/patterns)。
