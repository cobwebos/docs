---
title: 使用和管理应用服务环境
description: 如何在 Azure App Service 环境中创建、发布和缩放应用程序。 在一个文档中查找常见任务。
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 01/01/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 7be1676c8949cd30d5e1fe93a73afd75a5a9b67f
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565622"
---
# <a name="use-an-app-service-environment"></a>使用应用服务环境 #

应用服务环境（ASE）是将 Azure App Service 部署到客户的 Azure 虚拟网络中的子网。 ASE 包含：

- **前端**：前端是指 HTTP/HTTPS 在应用服务环境中终止的位置。
- **辅助角色**：辅助角色是托管应用的资源。
- **数据库**：数据库保存用于定义环境的信息。
- **存储**：存储用于托管客户发布的应用。

你可以使用外部或内部 VIP 来部署 ASE 以进行应用访问。 使用外部 VIP 的部署通常称为外部 ASE。 内部版本称为 ILB ASE，因为它使用内部负载均衡器 (ILB)。 若要详细了解 ILB ASE，请参阅[创建和使用 ILB ase][MakeILBASE]。

## <a name="create-an-app-in-an-ase"></a>在 ASE 中创建应用 ##

在 ASE 中创建应用的过程与一般情况下的创建过程大致相同，只存在几处细微的差别。 创建新的应用服务计划（ASP）时：

- 不要选择某个地理位置来部署应用，而应该选择 ASE 作为位置。
- ASE 中创建的所有应用服务计划只能位于隔离的定价层中。

如果没有 ASE，可以根据[创建应用服务环境][MakeExternalASE]中的说明创建一个 ASE。

在 ASE 中创建应用：

1. 选择“创建资源” > “Web + 移动” > “Web 应用”。

2. 输入应用程序的名称。 如果已在 ASE 中选择了应用服务计划，则应用的域名会反映 ASE 的域名。

    ![应用名称选择][1]

1. 选择一个订阅。

1. 输入新资源组的名称，或选择“使用现有”并从下拉列表中选择一个资源组。

1. 选择 OS。 

1. 在 ASE 中选择现有的应用服务计划，或遵循以下步骤创建一个新的计划：

    a. 从 Azure 门户左侧菜单中，选择 "**创建资源 > Web 应用**"。

    b. 选择订阅。
    
    c. 选择或创建资源组。
    
    d. 提供 web 应用的名称。
    
    e. 选择 "代码" 或 DockerContainer。
    
    f. 选择 "运行时堆栈"。
    
    g. 选择 "Linux" 或 "Windows"。 
    
    h. 在 "**区域**" 下拉列表中选择 ASE。 
    
    i. 选择或创建新的应用服务计划。 如果要创建新的应用服务计划，请选择适当的**隔离**SKU 大小。
    
    ![“隔离”定价层][2]

    > [!NOTE]
    > Linux 应用和 Windows 应用不能位于同一应用服务计划中，但可以位于同一应用服务环境中。 
    >

2. 如果信息正确，请选择 "**查看 + 创建**"，然后选择 "**创建**"。

## <a name="how-scale-works"></a>缩放的工作原理 ##

每个应用服务应用在应用服务计划中运行。 应用服务环境保存应用服务计划，应用服务计划保存应用。 缩放某个应用时，会缩放应用服务计划，因此，会缩放同一计划中的所有应用。

缩放应用服务计划时，会自动添加所需的基础结构。 添加基础结构时，缩放操作存在一定的时间延迟。 如果按顺序执行多个缩放操作，则会对第一个基础结构缩放请求进行处理，并使其他队列排队。 当第一个缩放操作完成时，其他基础结构请求所有操作一起运行。 添加基础结构后，将根据需要分配应用服务计划。 创建新的应用服务计划本身就是一种规模操作，因为它会请求额外的硬件。 

在多租户应用服务中，缩放是即时的，因为资源池随时可用于支持。 ASE 中没有此类缓冲区，而会根据需要分配资源。

在 ASE 中，最多可将应用服务计划扩展到100实例。 ASE 在 ASE 中的所有应用服务计划上最多可以有201个实例。 

## <a name="ip-addresses"></a>IP 地址 ##

应用服务能够向应用分配专用的 IP 地址。 按照将[现有自定义 ssl 证书绑定到 Azure App Service][ConfigureSSL]中所述，在配置基于 IP 的 ssl 后，将专用 ip 分配到应用的功能可用。 在 ILB ASE 中，无法添加要用于基于 IP 的 SSL 的其他 IP 地址。

使用外部 ASE，可以采用与在多租户应用服务中相同的方式为应用配置基于 IP 的 SSL。 ASE 中始终有一个备用地址，最多可包含30个 IP 地址。 每次使用一个地址时，会添加另一个地址，因此，始终有一个现成可用的地址。 分配另一个 IP 地址会产生一定的时间延迟，这会给快速连续添加 IP 地址带来阻碍。

## <a name="front-end-scaling"></a>前端缩放 ##

扩展应用服务计划时，会自动添加辅助角色以支持这些计划。 创建的每个 ASE 都具有两个前端。 前端会自动横向扩展，每隔15个应用服务计划实例一个前端。 如果有三个应用服务计划，每个都有五个实例，则总共有15个实例和三个前端。 如果扩展到总计30个实例，则有四个前端，依此类推。 

默认情况下，分配的前端的数目非常适合于中等负载。 对于每五个实例，可以将比率更改为低到一个前端。 还可以更改前端的大小。 默认情况下，它们是单核。 可以改为在门户中将前端的大小更改为两个或四个核心大小。 更改比率或前端大小会产生费用。 有关详细信息，请参阅[Azure App Service 定价][Pricing]。 如果你希望提高 ASE 的负载容量，则在调整缩放比率之前，首先缩放到两个核心前端，你将获得更多改进。 更改前端的核心大小将导致 ASE 升级，应在正常工作时间之外完成。

前端资源是 ASE 的 HTTP/HTTPS 终结点。 使用默认前端配置时，每个前端的内存使用率持续大约60%。 缩放前端的主要原因是 CPU，这主要由 HTTPS 流量驱动。

## <a name="app-access"></a>应用访问 ##

在外部 ASE 中，用于创建应用的域后缀是 *。&lt;asename&gt;. p.azurewebsites.net*。 如果 ASE 命名为_外部 ase_ ，而你在该 ASE 中托管名为_contoso_的应用，则可通过以下 url 访问该应用：

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

有关如何创建外部 ASE 的详细信息，请参阅[创建应用服务环境][MakeExternalASE]

在 ILB ASE 中，用于创建应用的域后缀为 *：&lt;asename&gt;. appserviceenvironment.net*。 如果 ASE 命名为_ilb_ ，并且在该 ase 中托管名为_contoso_的应用，则可通过以下 url 访问该应用：

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

有关如何创建 ILB ASE 的详细信息，请参阅[创建和使用 ILB ase][MakeILBASE]。 

Scm URL 用于访问 Kudu 控制台或用于通过使用 web 部署发布应用。 有关 Kudu 控制台的信息，请参阅[Kudu console for Azure App Service][Kudu]。 Kudu 控制台提供 Web UI，可在其中进行调试、上传文件、 编辑文件及执行其他许多操作。

## <a name="publishing"></a>发布 ##

与多租户应用服务一样，在 ASE 中，可以使用以下方法发布应用：

- Web 部署。
- FTP。
- 持续集成。
- 在 Kudu 控制台中拖放。
- Visual Studio、Eclipse 或 IntelliJ IDEA 等 IDE。

使用外部 ASE 时，这些发布选项的行为都是相同的。 有关详细信息，请参阅[Azure App Service 中的部署][AppDeploy]。 

在发布方面，主要差别在于 ILB ASE。 使用 ILB ASE 时，只能通过 ILB 访问所有发布终结点。 ILB 位于虚拟网络中 ASE 子网内的专用 IP 上。 如果无法通过网络访问 ILB，则无法在该 ASE 上发布任何应用。 如[创建和使用 ILB ASE][MakeILBASE]中所述，需要在系统中为应用配置 DNS。 这包括 SCM 终结点。 如果未正确定义 DNS，则无法发布。 IDE 也需要能够通过网络访问 ILB 才能直接在 ASE 中发布应用。

基于 Internet 的 CI 系统（例如 GitHub 和 Azure DevOps）不能用于 ILB ASE，因为发布终结点不可访问 Internet。 可以通过在包含 ILB ASE 的 VNet 中安装自承载发布代理，从 Azure DevOps 启用到 ILB ASE 的发布。 另外，还可以使用使用请求模型的 CI 系统，如 Dropbox。

ILB ASE 中应用的发布终结点使用创建该 ILB ASE 所用的域。 可以在应用的发布配置文件和应用门户的边栏选项卡中查看此信息（在“概述” **“软件包”以及“属性”中查看）**  > 。 

## <a name="storage"></a>存储

ASE 为 ASE 内的所有应用提供了 1 TB 的存储空间。 默认情况下，隔离的 SKU 应用服务计划的限制为 250 GB。 如果有五个或更多应用服务计划，则需要注意，不能超过 ASE 的 1 TB 限制。 如果在一个应用服务计划中需要超过 250 GB 的限制，请联系支持人员，将应用服务计划限制调整到最大 1 TB。 当调整计划限制时，ASE 中所有应用服务计划的限制仍为 1 TB。 

## <a name="logging"></a>日志记录 ##

可以将 ASE 与 Azure Monitor 集成，以便将有关 ASE 的日志发送到存储、事件中心或 Log Analytics。 当前记录的项是：

| 情形 | 消息 |
|---------|----------|
| ASE 不正常 | 由于虚拟网络配置无效，指定的 ASE 不正常。 如果不正常状态继续，则将挂起 ASE。 请确保遵循此处定义的准则： https://docs.microsoft.com/azure/app-service/environment/network-info |
| ASE 子网几乎用尽了空间 | 指定的 ASE 位于几乎用尽空间的子网中。 剩余地址 {0}。 一旦这些地址用尽，ASE 将无法缩放  |
| ASE 接近实例限制 | 指定的 ASE 接近 ASE 的总实例限制。 它当前包含最多201个实例 {0} 应用服务计划实例。 |
| ASE 无法访问依赖项 | 指定的 ASE 无法访问 {0}。  请确保遵循此处定义的准则： https://docs.microsoft.com/azure/app-service/environment/network-info |
| ASE 已挂起 | 指定的 ASE 处于挂起状态。 ASE 暂停的原因可能是帐户能力远远弥补或虚拟网络配置无效。 解决根本原因并恢复 ASE 以继续提供流量 |
| ASE 升级已启动 | 已开始升级到指定 ASE 的平台。 缩放操作需要延迟 |
| ASE 升级已完成 | 已完成到指定 ASE 的平台升级 |
| 缩放操作已开始 | 应用服务计划（{0}）已开始缩放。 所需状态： {1} 我{2} 辅助角色 
| 缩放操作已完成 | 应用服务计划（{0}）已完成缩放。 当前状态： {1} 我{2} 辅助角色 |
| 缩放操作失败 | 应用服务计划（{0}）未能缩放。 当前状态： {1} 我{2} 辅助角色 |

启用 ASE 上的日志记录： 

1. 在门户中转到 "诊断设置"。  
1. 选择 "添加诊断设置"。
1. 提供日志集成的名称
1. 检查并配置所需的日志目标。 
1. 检查 AppServiceEnvironmentPlatformLogs

![ASE 诊断日志设置][4]

如果与 Log Analytics 集成，则可以通过从 ASE 门户中选择日志并对 AppServiceEnvironmentPlatformLogs 创建查询来查看日志。 

## <a name="upgrade-preference"></a>升级首选项 ##

如果有多个 Ase，则可能需要先升级某些 Ase，再升级其他人。 在 ASE HostingEnvironment 资源管理器对象中，可以设置 UpgradePreference 的值。 可以通过模板、ARMClient 或 https://resources.azure.com配置 upgradePreference 设置。  这三个值选项是：

* 无-默认值为 "无"，表示 Azure 将在无特定批次中升级 ASE
* 早期，这意味着 ASE 将在应用服务升级的前半部分升级
* 后期延迟表示 ASE 将在应用服务升级后升级

如果使用 https://resources.azure.com，则可以通过以下方式设置 upgradePreferences 值：

1. 转到 resources.azure.com 并登录到 Azure 帐户
1. 在订阅中导航\/\[订阅名称\]\/resourceGroups\/\[\]\/\/\/\/\[\]
1. 选择顶部的 "读/写"
1. 选择 "编辑"
1. 将 upgradePreference 的值更改为所需的三个选项中的任何一个。
1. 选择修补程序

![azure com 显示的资源][5]

当你有多个 Ase 时，upgradePreferences 功能非常有意义，因为 "早期" 升级的 Ase 将在 "延迟" Ase 之前升级。 如果有多个 Ase，则应将开发/测试 Ase 设置为 "早期"，并将生产 Ase 设置为 "延迟"。

## <a name="pricing"></a>定价 ##

称为 "**独立**" 的定价 SKU 仅用于 ASE。 ASE 中托管的所有应用服务计划都在隔离定价 SKU 中。 独立应用服务计划费率可能因区域而异。 

除了应用服务计划的价格以外，ASE 本身也会收取一笔统一的费用。 对于每15个应用服务计划实例，平整速率不会随 ASE 的大小变化并为 ASE 基础结构付费，而是以一个附加的前端的默认缩放比率来支付。  

如果每个15个应用服务计划实例的一个前端的默认缩放速率不够快，则可以调整添加前端的比率或前端的大小。  当调整比率或大小时，将为默认情况下不会添加的前端核心付费。  

例如，如果将缩放比率调整到10，则会为应用服务计划中的每10个实例添加一个前端。 固定费用涉及每15个实例一个前端的缩放比率。 如果缩放比率为10，则需支付为10个应用服务计划实例添加的第三个前端的费用。 如果达到了 15 个实例，则不需要支付该费用，因为前端会自动添加。

如果将前端的大小调整到了两个内核，但没有调整比例，则需支付额外的内核数。  将使用两个前端创建 ASE，因此，如果将大小增加到了两个核心前端，则可为两个额外内核支付两个额外内核的数量。

有关详细信息，请参阅[Azure App Service 定价][Pricing]。

## <a name="delete-an-ase"></a>删除 ASE ##

若要删除 ASE，请执行以下操作： 

1. 使用“应用服务环境”边栏选项卡顶部的“删除”。 

1. 输入 ASE 的名称，确认想要将它删除。 删除 ASE 时，会同时删除它包含的所有内容。 

    ![ASE 删除][3]

<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png
[4]: ./media/using_an_app_service_environment/usingase-logsetup.png
[4]: ./media/using_an_app_service_environment/usingase-logs.png
[5]: ./media/using_an_app_service_environment/usingase-upgradepref.png


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
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
