---
title: 在应用服务环境中创建内部负载均衡器 - Azure
description: 有关如何创建和使用与 Internet 隔离的 Azure 应用服务环境的详细信息
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/05/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 08a18dc115990ad7d44a8b20412e07995c9af390
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70069510"
---
# <a name="create-and-use-an-internal-load-balancer-app-service-environment"></a>创建和使用内部负载均衡器应用服务环境 

Azure 应用服务环境是指将 Azure 应用服务部署到 Azure 虚拟网络 (VNet) 的子网中。 可通过两种方法部署应用服务环境 (ASE)： 

- 使用外部 IP 地址上的 VIP，通常称为外部 ASE。
- 使用内部 IP 地址上的 VIP，通常称为 ILB ASE，因为内部终结点是一个内部负载均衡器 (ILB)。 

本文介绍如何创建 ILB ASE。 有关 ASE 的概述，请参阅[应用服务环境简介][Intro]。 若要了解如何创建外部 ASE，请参阅[创建外部 ASE][MakeExternalASE]。

## <a name="overview"></a>概述 

可以使用可访问 Internet 的终结点或 VNet 中的 IP 地址部署 ASE。 若要将 IP 地址设置为 VNet 地址，必须为 ASE 部署 ILB。 为 ASE 部署 ILB 时，必须提供 ASE 的名称。 该 ASE 名称将在 ASE 内的应用的域后缀中使用。  ILB ASE 的域后缀是 &lt;ASE 名称&gt;.appservicewebsites.net。 在 ILB ASE 中创建的应用不会被放入公共 DNS 中。 

早期版本的 ILB ASE 要求提供一个域后缀和一个用于建立 HTTPS 连接的默认证书。 创建 ILB ASE 时不再收集域后缀，且不再收集默认证书。 现在，在创建 ILB ASE 时，默认证书将由 Microsoft 提供，并受浏览器的信任。 仍可以在 ASE 中的应用上设置自定义域名，并在这些自定义域名中设置证书。 

使用 ILB ASE 可以实现如下所述的目的：

-   在云中安全托管可通过站点到站点连接或 ExpressRoute 访问的 Intranet 应用程序。
-   使用 WAF 设备保护应用
-   在云端托管未在公用 DNS 服务器中列出的应用。
-   创建与 Internet 隔离且前端应用可以与之安全集成的后端应用。

### <a name="disabled-functionality"></a>已禁用的功能 ###

使用 ILB ASE 时，有一些操作无法执行：

-   使用基于 IP 的 SSL。
-   将 IP 地址分配给特定应用。
-   通过 Azure 门户购买证书并搭配应用使用。 可以直接从证书颁发机构获取证书并搭配应用使用。 无法通过 Azure 门户获取这些证书。

## <a name="create-an-ilb-ase"></a>创建 ILB ASE ##

若要创建 ILB ASE，请执行以下操作：

1. 在 Azure 门户中选择“创建资源”   > “Web”   > “应用服务环境”  。

2. 选择订阅。

3. 选择或创建资源组。

4. 输入应用服务环境的名称。

5. 选择“内部”虚拟 IP 类型。

    ![ASE 创建](media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase.png)

6. 选择“网络”

7. 选择或创建虚拟网络。 如果在此处创建新的 VNet，将使用 192.168.250.0/23 地址范围定义该 VNet。 若要使用不同的地址范围或者在除 ASE 以外的另一个资源组中创建 VNet，请使用 Azure 虚拟网络创建门户。 

8. 选择或创建一个空子网。 若要选择子网，它必须是空的且未委托出去。 创建 ASE 后无法更改子网大小。 建议的大小为 `/24`，其地址长度为 256 位且能够容纳最大尺寸的 ASE，满足任何缩放需求。 

    ![ASE 网络][1]

7. 依次选择“查看并创建”、“创建”。  

## <a name="create-an-app-in-an-ilb-ase"></a>在 ILB ASE 中创建应用 ##

在 ILB ASE 中创建应用通常与在 ASE 中创建应用相同。

1. 在 Azure 门户中，选择“创建资源” > “Web” > “Web 应用”。   

1. 输入应用的名称。

1. 选择订阅。

1. 选择或创建资源组。

1. 选择“发布”、“运行时堆栈”和“操作系统”。

1. 选择包含现有 ILB ASE 的位置。  还可以在创建应用期间通过选择一个隔离的应用服务计划来创建新的 ASE。 若要创建新的 ASE，请选择要在其中创建该 ASE 的区域。

1. 选择或创建应用服务计划。 

1. 准备就绪后，依次选择“查看并创建”、“创建”。  

### <a name="web-jobs-functions-and-the-ilb-ase"></a>Web 作业、函数和 ILB ASE 

ILB ASE 上同时支持函数和 Web 作业，但对于与其配合使用的门户，必须具有对 SCM 站点的网络访问权限。  这意味着浏览器必须位于在虚拟网络中或已连接虚拟网络的主机上。 如果 ILB ASE 使用的某个域名不是以 *appserviceenvironment.net* 结尾，则你需要让浏览器信任 scm 站点使用的 HTTPS 证书。

## <a name="dns-configuration"></a>DNS 配置 

使用外部 VIP 时，DNS 由 Azure 管理。 在 ASE 中创建的任何应用都会自动添加到 Azure DNS，这是一个公用 DNS。 在 ILB ASE 中，必须管理自己的 DNS。 对 ILB ASE 使用的域后缀取决于 ASE 的名称。 域后缀为 *&lt;ASE 名称&gt;.appserviceenvironment.net*。 ILB 的 IP 地址显示在门户中的“IP 地址”。  

若要配置 DNS：

- 为 *&lt;ASE 名称&gt;.appserviceenvironment.net* 创建一个区域
- 在该区域中创建一条指向* ILB IP 地址的 A 记录 
- 在 *&lt;ASE 名称&gt;.appserviceenvironment.net* 中创建名为 scm 的区域
- 在 scm 区域中创建一条指向 ILB IP 地址的 A 记录

## <a name="publish-with-an-ilb-ase"></a>使用 ILB ASE 发布

创建的每个应用都有两个终结点。 ILB ASE 中包含 *&lt;应用名称&gt;.&lt;ILB ASE 域&gt;* 和 *&lt;应用名称&gt;.scm.&lt;ILB ASE 域&gt;* 。 

SCM 站点名称能将用户带到 Kudu 控制台，在 Azure 门户中称为**高级门户**。 Kudu 控制台允许查看环境变量、浏览磁盘、使用控制台等等。 有关详细信息，请参阅 [Azure 应用服务的 Kudu 控制台][Kudu]。 

如果生成代理可访问 Internet 并与 ILB ASE 在同一网络上，则基于 Internet 的 CI 系统（例如 GitHub 和 Azure DevOps）仍将使用 ILB ASE。 因此，如果在 ILB ASE 所在的 VNET 上（不同的子网属正常情况）创建生成代理，Azure DevOps 将能从 Azure DevOps git 中拉取代码并部署到 ILB ASE。 如果不想创建自己的生成代理，则需要使用利用拉取模型的 CI 系统，如 Dropbox。

ILB ASE 中应用的发布终结点使用创建该 ILB ASE 所用的域。 此域显示在应用的发布配置文件和应用的门户边栏选项卡中（“概述”   > “软件包”  以及“属性”  ）。 如果 ILB ASE 包含域后缀 *&lt;ASE 名称&gt;.appserviceenvironment.net* 和名为 *mytest* 的应用，请使用 *mytest.&lt;ASE 名称&gt;.appserviceenvironment.net* 进行 FTP 部署，并使用 *mytest.scm.contoso.net* 进行 Web 部署。

## <a name="configure-an-ilb-ase-with-a-waf-device"></a>在 ILB ASE 中配置 WAF 设备 ##

可将 Web 应用程序防火墙 (WAF) 设备与 ILB ASE 相结合，以便仅公开可通过 Internet 访问的应用，使其他应用只能从 VNet 内部访问。 这样，便可以生成安全的多层应用程序并实现其他目的。

若要详细了解如何在 ILB ASE 中配置 WAF 设备，请参阅[在应用服务环境中配置 Web 应用程序防火墙][ASEWAF]。 本文介绍如何在 ASE 中使用 Barracuda 虚拟设备。 另一种做法是使用 Azure 应用程序网关。 应用程序网关使用 OWASP 核心规则来保护放置在它后面的任何应用程序。 有关应用程序网关的详细信息，请参阅 [Azure Web 应用程序防火墙简介][AppGW]。

## <a name="ilb-ases-made-before-may-2019"></a>在 2019 年 5 之前创建的 ILB ASE

在 2019 年 5 之前创建的 ILB ASE 要求在创建 ASE 期间设置域后缀。 它们还要求上传基于该域后缀的默认证书。 此外，使用早期的 ILB ASE 无法单一登录到该 ILB ASE 中的应用的 Kudu 控制台。 为早期的 ILB ASE 配置 DNS 时，需要在与域后缀匹配的区域中设置通配符 A 记录。 

## <a name="get-started"></a>入门 ##

* 若要开始使用 ASE，请参阅[应用服务环境简介][Intro]。 

<!--Image references-->
[1]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-network.png
[2]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-webapp.png
[5]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-ipaddresses.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[webapps]: ../overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[customdomain]: ../app-service-web-tutorial-custom-domain.md
[linuxapp]: ../containers/app-service-linux-intro.md
