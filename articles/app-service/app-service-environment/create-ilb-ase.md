---
title: "在 Azure App Service 环境中创建和使用内部负载平衡器"
description: "创建和使用隔离 Internet 的 Azure 应用服务环境的详细信息"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 8bc82a297ef8e3f1beac773a6a3b0c4a9b80334f
ms.contentlocale: zh-cn
ms.lasthandoff: 06/26/2017

---
# <a name="create-and-use-an-internal-load-balancer-with-an-app-service-environment"></a>在应用服务环境中创建和使用内部负载均衡器 #

应用服务环境 (ASE) 是指 Azure 应用服务部署到 Azure 虚拟网络 (VNet) 的子网中。 可通过两种方式来部署 ASE： 

- 使用外部 IP 地址上的 VIP，它通常称为外部 ASE。
- 使用内部 IP 地址上的 VIP，它通常被称为 ILB ASE，因为内部终结点是一个内部负载平衡器 (ILB)。 

本文介绍如何创建 ILB ASE。  有关 ASE 的概述，可以从[应用服务环境简介][Intro]开始了解，如果想要了解如何创建外部 ASE，可以从[创建外部 ASE][MakeExternalASE]开始了解。

## <a name="overview"></a>概述 ##

可以使用可访问 Internet 的终结点或虚拟网络中的 IP 地址部署 ASE。 若要将 IP 地址设置为虚拟网络地址，必须使用 ILB 部署 ASE。 使用 ILB 部署 ASE 时，必须提供：

-   创建应用程序所使用的自己的域
-   用于 HTTPS 的证书
-   域的 DNS 管理

相对地，用户可以执行以下操作：

-   在可以通过站点到站点或 ExpressRoute VPN 访问的云端，安全地托管 Intranet 应用程序
-   在云端托管未在公用 DNS 服务器中列出的应用
-   创建与 Internet 隔离且前端应用可以与之安全集成的后端应用

***已禁用的功能***

使用 ILB ASE 时，有一些操作无法执行，包括：

-   使用基于 IP 的 SSL
-   将 IP 地址分配给特定应用
-   通过门户购买证书并搭配应用使用。 也可以直接通过证书颁发机构获取证书并搭配应用使用，只是无法通过 Azure 门户这样做。

## <a name="create-an-ilb-ase"></a>创建 ILB ASE ##

若要创建 ILB ASE，请执行以下操作：

1.  在 Azure 门户中，选择“新建”-&gt;“Web + 移动”-&gt;“应用服务环境”。
2.  选择你的订阅。
3.  选择或创建资源组。
4.  选择或创建虚拟网络。
5.  创建子网（如果选择虚拟网络）。 请确保设置的子网大小应该足够容纳 ASE 未来任何的增长。 建议的大小为 `/25`，其地址长度为 128 位且能够应对最大尺寸的 ASE。 不能使用 `/29` 或更小的子网大小。  基础结构的地址长度至少为 5 位。  即使使用 `/28`，`/28` 子网中的最大缩放应为 11 个实例。 如果你认为某天将需要超过应用服务计划中默认的最大值 100 个实例，或在更快速的前端缩放的情况下需要缩放接近 100 个实例，则需使用地址长度为 256 位的 /24。
6.  选择“虚拟网络/位置”-&gt;“虚拟网络配置”，并将“VIP 类型”设置为“内部” 。
7.  提供域名。 该域将用于在此 ASE 中创建的应用程序。 存在一些限制。 不能是：
    - net
    - azurewebsites.net
    - p.azurewebsites.net
    - &lt;asename&gt;.p.azurewebsites.net

    同样，如果想要将自定义域名用于此 ASE 托管的应用程序，自定义域名和 ASE 使用的域名之间不能重叠。 对于域名为 _contoso.com_ 的 ILB ASE，不能将自定义域名用于应用程序，如下所示：
    - www.contoso.com
    - abcd.def.contoso.com
    - abcd.contoso.com

    如果知道想要用于部署到 ILB ASE 的应用的自定义域名，请在创建 ILB ASE 的过程中为 ILB ASE 选择一个不会发生冲突的域。 在此例中，它可能类似于 _contoso-internal.com_。
8.  选择“确定”，然后选择“创建”。

    ![][1]

在虚拟网络边栏选项卡中有“虚拟网络配置”选项。 此选项允许用户选择外部 VIP 或内部 VIP。 默认为“外部”。 如果选择“外部”，则 ASE 将使用可访问 Internet 的 VIP。 如果选择“内部”，则会在虚拟网络的 IP 地址上对 ASE 配置 ILB。

选择“内部”后，无法向 ASE 添加更多 IP 地址，取而代之的是必须提供 ASE 的域。 在使用外部 VIP 的 ASE 中，ASE 的名称会用于在该 ASE 中创建的应用的域。

如果将“VIP 类型”设置为“内部”，ASE 名称将不会用于 ASE 的域。 显式指定域。 如果域为 contoso.corp.net，而用户在该 ASE 中创建的应用名为 timereporting，则该应用的 URL 为 timereporting.contoso.corp.net。

## <a name="apps-in-an-ilb-ase"></a>ILB ASE 中的应用 ##

在 ILB ASE 中创建应用通常与在 ASE 中创建应用相同。

1.  在 Azure 门户中选择“新建”&gt;“Web + 移动”&gt;“Web”或“移动”或“API 应用”。
2.  输入应用名称。
3.  选择订阅。
4.  选择或创建资源组。
5.  选择或创建应用服务计划。 如果想要创建新的应用服务计划，请选择 ASE 作为位置并选择希望在其中创建应用服务计划的工作线程池。 创建应用服务计划时，选择 ASE 作为位置并选择工作线程池。 指定应用的名称时，会看到应用名称下面的域已替换为 ASE 的域。
6.  选择“创建” 。 如果希望应用显示在仪表板上，应选中“固定到仪表板”复选框。

    ![][2]

应用名称下面的域名会更新，以反映 ASE 的域。

## <a name="post-ilb-ase-creation-validation"></a>ILB ASE 创建后验证 ##

ILB ASE 与非 ILB ASE 稍有不同。 如先前所述，除了管理自己的 DNS，还需提供自己的 HTTPS 连接证书。

创建 ASE 之后，用户会注意到域名显示指定的域，且“设置”菜单中有一个名为“ILB 证书”的新项。 创建 ASE 的证书不指定 ILB ASE 的域。 如果 ASE 使用该证书，浏览器将会告知该证书无效。 此证书可以轻松地测试 HTTPS，但需要上传绑定到 ILB ASE 的域的证书，不管它是自签名证书还是从证书颁发机构 (CA) 获取的证书。

![][3]

可通过各种方式获取有效的 SSL 证书，包括内部 CA、向外部颁发者购买证书，以及使用自签名证书。 无论 SSL 证书的来源如何，都需要正确配置以下证书属性：

-   使用者：此属性必须设置为 \*.your-root-domain-here
-   使用者可选名称：此属性必须同时包含 \*.your-root-domain-here 和 \*.scm.your-root-domain-here 。 输入第二项是因为将使用 _your-app-name.scm.your-root-domain-here_ 形式的地址，建立与每个应用关联的 SCM/Kudu 站点的 SSL 连接

备妥有效的 SSL 证书还需要两个额外的准备步骤。 SSL 证书必须转换/另存为 .pfx 文件。 记住，.pfx 文件必须包含所有中间证书和根证书，还必须使用密码保护。

如果想要使用 PowerShell 创建自己的证书，可以使用下面所示的命令。  请务必使用你的 ILB ASE 域名而不是 internal.contoso.com。 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "\*.internal-contoso.com","\*.scm.internal-contoso.com"
    
    $certThumbprint = "cert:\localMachine\my\" +$certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText
    
    $fileName = "exportedcert.pfx" 
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password

生成这些 PowerShell 命令的证书仍然会被浏览器标记，因为它不是由浏览器使用的信任链中的 CA 所创建的。 获取在浏览器中不会出现问题的证书的唯一方法就是采购来自浏览器信任链中商业 CA 的证书。  

![][4]

上传自己的证书和测试访问：

1.  在创建 ASE 后转到 ASE UI（“ASE”>“设置”>“ILB 证书”）。
2.  通过选择证书 pfx 文件来设置 ILB 证书，并提供密码。 此步骤需要一些时间来处理，并且会显示正在进行上传操作的消息。
3.  获取 ASE 的 ILB 地址（“ASE”>“属性”>“虚拟 IP 地址”）。
4.  创建后，在 ASE 中创建 Web 应用。
5.  如果在该虚拟网络中还没有 VM，请创建一个。

    > [!NOTE] 
    > 不要在 ASE 的同一子网中创建此 VM。 否则，设置将会中断。
    >
    >

6.  设置 ASE 域的 DNS。 可以在 DNS 中将通配符与域结合使用，或者如果想要执行一些简单测试，可编辑 VM 上的主机文件，将 Web 应用名称设置为 VIP IP 地址。 如果你的 ASE 域名为 .ilbase.com，并且创建的 web 应用名为 mytestapp，则它所在的地址将为 mytestapp.ilbase.com。 然后设置 mytestapp.ilbase.com 以解析为 ILB 地址。 （在 Windows 上，主机文件位于 _C:\Windows\System32\drivers\etc\_。）如果想要测试发布或访问高级控制台的 web 部署，则还需要创建 mytestapp.scm.ilbase.com 记录。
7.  在该 VM 上使用浏览器转到 http://mytestapp.ilbase.com（或你域中的 Web 应用名称）。
8.  在该 VM 上使用浏览器转到 https://mytestapp.ilbase.com。 如果使用自签名证书，则需接受缺乏安全保障的问题。

你的 ILB 的 IP 地址会在“IP 地址”下列出。 同时还有由外部 VIP 用于入站流量管理的 IP 地址。

![][5]

### <a name="functions-and-the-ilb-ase"></a>函数和 ILB ASE

在 ILB ASE 上使用函数时，可能会遇到一个错误提示：“我们不能立即检索你的函数。请稍后重试。”  原因是 Functions UI 通过 HTTPS 利用 scm 站点。  如果你的 ASE 正在使用在浏览器中不具有根证书的 HTTP 证书，则可能会遇到这种情况。  此外，IE\Edge 浏览器未在选项卡之间共享“接受无效证书”设置。 因此，你可以：

- 将证书添加到受信任的证书存储 
- 或使用 Chrome，但需要首先转到 scm 站点，接受不受信任的证书，然后再转到门户

## <a name="dns-configuration"></a>DNS 配置 ##

使用外部 VIP 时，DNS 由 Azure 管理。 在 ASE 中创建的任何应用都会自动添加到 Azure DNS，这是一个公用 DNS。 在 ILB ASE 中，必须管理自己的 DNS。 针对给定的域（例如 _contoso.net_），必须在 DNS 中为以下项创建指向 ILB 地址的 DNS A 记录：

- *.contoso.net
- *.scm.contoso.net

如果 ILB ASE 的域用于此 ASE 之外的多个操作，则可能需要基于应用程序名称逐个进行 DNS 管理。 这更具有挑战性，因为在创建每个新应用程序时都需要将其名称添加到你的 DNS。 出于此原因，建议使用专用域。

## <a name="publishing-with-an-ilb-ase"></a>使用 ILB ASE 进行发布 ##

每个创建的应用都有两个终结点。 在 ILB ASE 中，有 &lt;应用名称>.&lt;ILB ASE 域> 以及&lt;应用名称>.scm.&lt;ILB ASE 域>。 

SCM 站点名称能将你带到 Kudu 控制台，在 Azure 门户中被称为高级门户。 Kudu 控制台可执行许多操作，包括查看环境变量、浏览磁盘、使用控制台等等。 有关详细信息，请参阅[用于 Azure App Service 的 Kudu 控制台][Kudu]。 

在多租户应用服务和外部 ASE 中，Azure 门户和 Kudu 控制台之间有 SSO。 然而，对于 ILB ASE，需要改为使用发布凭据登录到 Kudu 控制台。

基于 Internet 的 CI 系统（例如 Github 和 VSTS）不支持 ILB ASE，因为发布终结点不可访问 Internet。 需要该用使用拉取模式的 CI 系统，如 Dropbox。

ILB ASE 中应用的发布终结点使用创建该 ILB ASE 所用的域。 可以在应用的发布配置文件和应用门户的边栏选项卡中查看（在“概述” > “软件包”以及“属性”中查看）。 如果 ILB ASE 的子域为 contoso.net，应用名称为 mytest，那么应该 FTP 到 mytest.contoso.net 并对 mytest.scm.contoso.net 执行 Web 部署。

## <a name="coupling-an-ilb-ase-with-a-waf-device"></a>将 ILB ASE 与 WAF 设备耦合 ##

Azure App Service 提供了大量安全措施以保护系统，帮助确定应用程序是否受到了黑客攻击。 Web 应用的最佳保护方法是将 Web 应用防火墙 (WAF) 与托管平台如 Azure App Service 耦合。 ILB ASE 具有隔离网络的应用程序终结点，因此很适合这样做。  

若要了解在 WAF 设备中配置 ILB ASE 的详细信息，请参阅[在应用服务环境中配置 web 应用防火墙][ASEWAF]。 本文介绍了如何在 ASE 中使用 Barracuda 虚拟设备。 另一种做法是使用 Azure 应用程序网关。 应用程序网关使用 OWASP 核心规则来保护放置在它后面的任何应用程序。 有关 Azure 应用程序网关的详细信息，请参阅[ Azure Web 应用程序防火墙简介][AppGW]。

### <a name="getting-started"></a>入门 ###

[应用程序服务环境自述文件][ASEReadme]中提供了有关应用服务环境的所有文章和操作说明。

若要开始使用应用服务环境，请参阅[应用服务环境简介][Intro]。

有关 Azure 应用服务平台的详细信息，请参阅 [Azure 应用服务](http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/)。
 
<!--Image references-->
[1]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-network.png
[2]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-webapp.png
[3]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-certificate.png
[4]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-certificate2.png
[5]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-ipaddresses.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[ASEReadme]: ./readme.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: ../../app-service-web/app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: ../../app-service-web/app-service-app-service-environment-intro.md
[webapps]: ../../app-service-web/app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[APIapps]: ../../app-service-api/app-service-api-apps-why-best-platform.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service-web/web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../../app-service-web/web-sites-deploy.md
[ASEWAF]: ../../app-service-web/app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md

