---
title: 使用和管理应用服务环境
description: 了解如何在应用服务环境中创建、发布和缩放应用。 在本文中查找所有常见任务。
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 5/10/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: fd1ffc8636e11ca20bc32b4b6f600e03d923d8b5
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125802"
---
# <a name="use-an-app-service-environment"></a>使用应用服务环境

应用服务环境 (ASE) 是客户 Azure 虚拟网络实例的子网中的 Azure 应用服务部署。 ASE 包括：

- **前端**： HTTP 或 HTTPS 在应用服务环境中终止的位置
- **辅助角色**：托管应用的资源
- **数据库**：保存用于定义环境的信息
- **存储**：用于托管客户发布的应用

可以使用外部或内部虚拟 IP (VIP) 来部署 ASE 以进行应用访问。 使用外部 VIP 的部署通常称为“外部 ASE”。  使用内部 VIP 的部署称为“ILB ASE”，因为它使用内部负载均衡器 (ILB)。  若要详细了解 ILB ASE，请参阅[创建和使用 ILB ASE][MakeILBASE]。

## <a name="create-an-app-in-an-ase"></a>在 ASE 中创建应用

在 ASE 中创建应用的过程与一般情况下创建应用的过程大致相同，只存在几处细微的差别。 创建新的应用服务计划时：

- 不要选择某个地理位置来部署应用，而应该选择 ASE 作为位置。
- 在 ASE 中创建的所有应用服务计划只能位于“隔离”定价层中。

如果没有 ASE，可以根据[创建应用服务环境][MakeExternalASE]中的说明创建一个。

在 ASE 中创建应用：

1. 选择“创建资源”   > “Web + 移动”   >   “Web 应用”。

1. 输入应用程序的名称。 如果已在 ASE 中选择了应用服务计划，则应用的域名会反映 ASE 的域名：

    ![应用名称选择][1]

1. 选择一个订阅。

1. 输入新资源组的名称，或选择“使用现有”并从下拉列表中选择一个资源组。 

1. 选择 OS。

1. 在 ASE 中选择现有的应用服务计划，或遵循以下步骤创建一个新的计划：

    a. 在 Azure 门户的左侧菜单中，选择“创建资源”>“Web 应用”。 

    b. 选择订阅。

    c. 选择或创建资源组。

    d. 输入 Web 应用的名称。

    e. 选择“代码”或“DockerContainer”。  

    f. 选择运行时堆栈。

    g. 选择 **Linux** 或 **Windows**。 

    h.如果该值不存在，请单击“添加行”。 在“区域”下拉列表中选择自己的 ASE。  

    i. 选择或新建应用服务计划。 如果创建新的应用服务计划，请选择相应的“隔离”SKU 大小。 

    ![“隔离”定价层][2]

    > [!NOTE]
    > Linux 应用和 Windows 应用不能位于同一个应用服务计划中，但可以在同一应用服务环境中。
    >

1. 选择 "查看" 和 "**创建**"，确保信息正确，然后选择 "**创建**"。

## <a name="how-scale-works"></a>缩放的工作原理

每个应用服务应用在应用服务计划中运行。 应用服务环境保存应用服务计划，应用服务计划保存应用。 缩放应用时，还可以缩放同一计划中的应用服务计划和所有应用。

缩放应用服务计划时，会自动添加所需的基础结构。 添加基础结构时，缩放操作存在时间延迟。 如果按顺序执行多个缩放操作，则会对第一个基础结构缩放请求进行操作，其他将对其进行排队。 第一次缩放操作完成后，其他基础结构请求所有操作一起运行。 添加基础结构后，将根据需要分配应用服务计划。 创建新的应用服务计划本身就是一种缩放操作，因为它会请求额外的硬件。

在多租户应用服务中，缩放是即时的，因为资源池随时可用于支持。 在 ASE 中，没有此类缓冲区，并根据需要分配资源。

在 ASE 中，最多可将应用服务计划扩展到100实例。 ASE 最多可在该 ASE 的所有应用服务计划中包含201个实例。

## <a name="ip-addresses"></a>IP 地址

应用服务可以向应用分配专用 IP 地址。 此功能在配置基于 IP 的 SSL 之后可用，如将[现有的自定义 TLS/SSL 证书绑定到 Azure App Service][ConfigureSSL]中所述。 在 ILB ASE 中，无法添加更多的 IP 地址用于基于 IP 的 SSL。

使用外部 ASE，可以采用与多租户应用服务中相同的方式为应用配置基于 IP 的 SSL。 ASE 中始终有一个备用地址，最多可包含30个 IP 地址。 每次使用一个地址时，会添加另一个地址，使地址始终可用。 需要延迟时间才能分配其他 IP 地址。 这种延迟会阻止快速连续添加 IP 地址。

## <a name="front-end-scaling"></a>前端缩放

扩展应用服务计划时，会自动添加辅助角色以支持这些计划。 创建的每个 ASE 包含两个前端。 前端会自动横向扩展，以实现每组15个应用服务计划实例的一个前端。 例如，如果有三个应用服务计划，每个都有五个实例，则总共有15个实例，三个前端。 如果扩展到总计30个实例，则有四个前端。 当你横向扩展时，此模式将继续。

默认情况下分配的前端数适用于中等负载。 对于每五个实例，可以将比率降低到最小一个前端。 还可以更改前端的大小。 默认情况下，它们是单核。 在 Azure 门户中，可以改为将其大小更改为两个或四个核心。

更改比率或前端大小会产生费用。 有关详细信息，请参阅 [Azure 应用服务定价][Pricing]。 如果要改善 ASE 的负载容量，则在调整缩放比率之前，首先要扩展到两核前端，将会获得更多改进。 更改前端的核心大小将导致 ASE 升级，应在正常工作时间之外完成。

前端资源是 ASE 的 HTTP/HTTPS 终结点。 使用默认前端配置时，每个前端的内存使用率一致地保持在大约 60%。 缩放前端的主要原因是 CPU 使用情况，这主要由 HTTPS 流量驱动。

## <a name="app-access"></a>应用访问

在外部 ASE 中，用于创建应用的域后缀是 *。 &lt;asename &gt; . p.azurewebsites.net*。 如果 ASE 命名为_外部 ase_ ，而你在该 ASE 中托管名为_contoso_的应用，则可通过以下 url 访问该应用：

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

有关如何创建外部 ASE 的信息，请参阅[创建应用服务环境][MakeExternalASE]。

在 ILB ASE 中，用于创建应用的域后缀是 *。 &lt;asename &gt; . appserviceenvironment.net*。 如果 ASE 命名为_ilb_ ，并且在该 ase 中托管名为_contoso_的应用，则可在以下 url 中找到它：

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

有关如何创建 ILB ASE 的信息，请参阅[创建和使用 ILB ase][MakeILBASE]。

SCM URL 用于访问 Kudu 控制台或使用 Web 部署来发布应用程序。 有关 Kudu 控制台的信息，请参阅 [Azure 应用服务的 Kudu 控制台][Kudu]。 Kudu 控制台提供 Web UI，可在其中进行调试、上传文件、 编辑文件及执行其他许多操作。

### <a name="dns-configuration"></a>DNS 配置 

使用外部 ASE 时，在 ASE 中创建的应用将注册到 Azure DNS。 外部 ASE 中无附加步骤，应用可供公开使用。 使用 ILB ASE 时，必须管理自己的 DNS。 可以在自己的 DNS 服务器或 Azure DNS 专用区域中执行此操作。

若要在自己的 DNS 服务器中通过 ILB ASE 配置 DNS：

1. 为 appserviceenvironment.net 创建区域 <ASE name>
1. 在该区域中创建一条指向* ILB IP 地址的 A 记录
1. 在该区域中创建一条指向 @ ILB IP 地址的 A 记录
1. 在 <ASE name> 名为 scm 的 appserviceenvironment.net 中创建区域
1. 在 scm 区域中创建一条指向 * ILB IP 地址的 A 记录

若要在 Azure DNS 专用区域中配置 DNS：

1. 创建名为 appserviceenvironment.net 的 Azure DNS 专用区域 <ASE name> 。
1. 在该区域中创建一条指向* ILB IP 地址的 A 记录
1. 在该区域中创建一条指向 @ ILB IP 地址的 A 记录
1. 在该区域中创建 A 记录，将 *. scm 指向 ILB IP 地址

ASE 默认域后缀的 DNS 设置不会将你的应用限制为只能由这些名称访问。 可以在 ILB ASE 中设置自定义域名，无需对应用进行任何验证。 如果随后想要创建名为*contoso.net*的区域，则可以将其指向 ILB IP 地址。 自定义域名适用于应用请求，但不适用于 scm 站点。 Scm 站点仅在 appname 中可用* &lt; &gt; 。 &lt;asename &gt; . appserviceenvironment.net*。 

名为的区域 *。 &lt;asename &gt; . appserviceenvironment.net*是全局唯一的。 在5月 2019 5 日之前，客户可以指定 ILB ASE 的域后缀。 如果要将 *. contoso.com*用于域后缀，则可以这样做，这将包括 scm 站点。 该模型有一些挑战，其中包括：管理默认的 SSL 证书，缺少对 scm 站点的单一登录，并要求使用通配符证书。 ILB ASE 默认证书升级过程也会中断，并导致应用程序重新启动。 为了解决这些问题，ILB ASE 行为已更改为使用基于 ASE 名称和 Microsoft 拥有的后缀的域后缀。 对 ILB ASE 行为的更改仅影响在5月2019日后发出的 ILB Ase。 预先存在的 ILB Ase 仍必须管理 ASE 及其 DNS 配置的默认证书。

## <a name="publishing"></a>发布

在 ASE 中，与多租户应用服务一样，可以通过以下方法进行发布：

- Web 部署
- FTP
- 持续集成（CI）
- 在 Kudu 控制台中拖放
- IDE （如 Visual Studio、Eclipse 或 IntelliJ）

使用外部 ASE 时，这些发布选项的工作方式都相同。 有关详细信息，请参阅 [Azure 应用服务中的部署][AppDeploy]。

使用 ILB ASE 时，发布终结点仅可通过 ILB 使用。 ILB 位于虚拟网络中 ASE 子网内的专用 IP 上。 如果你没有对 ILB 的网络访问权限，则无法在该 ASE 上发布任何应用。 如[创建和使用 ILB ASE][MakeILBASE]中所述，必须在系统中为应用配置 DNS。 该要求包括 SCM 终结点。 如果终结点定义不正确，则无法发布。 Ide 还必须具有对 ILB 的网络访问权限才能直接向其发布。

如果没有其他更改，则基于 internet 的 CI 系统（例如 GitHub 和 Azure DevOps）不能使用 ILB ASE，因为发布终结点不可访问 internet。 可以通过在包含 ILB ASE 的虚拟网络中安装自承载发布代理，从 Azure DevOps 启用到 ILB ASE 的发布。 或者，可以利用某个使用提取模型的 CI 系统，例如 Dropbox。

ILB ASE 中应用的发布终结点使用创建该 ILB ASE 所用的域。 可以在应用的发布配置文件和应用的门户窗格中查看（在 "概述" **Overview**  >  **Essentials**和 "**属性**" 中）。

## <a name="storage"></a>存储

ASE 为 ASE 中的所有应用提供了 1 TB 的存储空间。 隔离定价 SKU 中的应用服务计划的限制为 250 GB。 在 ASE 中，每个应用服务计划增加了 250 GB 的存储空间，最大限制为 1 TB。 你的应用服务计划比只是四个多，但没有超过 1 TB 限制增加的存储空间。

## <a name="logging"></a>Logging

可以将 ASE 与 Azure Monitor 集成，将有关 ASE 的日志发送到 Azure 存储、Azure 事件中心或 Log Analytics。 今天记录以下项：

| 场景 | 消息 |
|---------|----------|
| ASE 不正常 | 由于虚拟网络配置无效，指定的 ASE 不正常。 如果不正常状态继续，则将挂起 ASE。 请确保遵循此处定义的准则： https://docs.microsoft.com/azure/app-service/environment/network-info 。 |
| ASE 子网几乎用尽了空间 | 指定的 ASE 位于几乎用尽空间的子网中。 存在 {0} 剩余的地址。 一旦这些地址用尽，ASE 将无法缩放。  |
| ASE 接近实例限制 | 指定的 ASE 接近 ASE 的总实例限制。 它当前包含 {0} 最多201个实例的应用服务计划实例。 |
| ASE 无法访问依赖项 | 指定的 ASE 无法连接 {0} 。  请确保遵循此处定义的准则： https://docs.microsoft.com/azure/app-service/environment/network-info 。 |
| ASE 已挂起 | 指定的 ASE 处于挂起状态。 ASE 暂停的原因可能是帐户能力远远弥补或虚拟网络配置无效。 解决根本原因并恢复 ASE 以继续提供流量。 |
| ASE 升级已启动 | 已开始升级到指定 ASE 的平台。 缩放操作需要延迟。 |
| ASE 升级已完成 | 到指定 ASE 的平台升级已完成。 |
| 缩放操作已开始 | 应用服务计划（ {0} ）已开始缩放。 所需状态： {1} 我 {2} 的辅助角色。
| 缩放操作已完成 | 应用服务计划（ {0} ）已完成缩放。 当前状态： {1} 我 {2} 的工作线程数。 |
| 缩放操作失败 | 应用服务计划（ {0} ）未能缩放。 当前状态： {1} 我 {2} 的工作线程数。 |

启用 ASE 上的日志记录：

1. 在门户中，请参阅 "**诊断设置**"。
1. 选择“添加诊断设置”。****
1. 提供日志集成的名称。
1. 选择并配置所需的日志目标。
1. 选择**AppServiceEnvironmentPlatformLogs**。

![ASE 诊断日志设置][4]

如果与 Log Analytics 集成，则可以通过从 ASE 门户中选择**日志**并对**AppServiceEnvironmentPlatformLogs**创建查询来查看日志。 仅当 ASE 的事件将触发日志时才发出日志。 如果 ASE 没有此类事件，则不会有任何日志。 若要快速查看 Log Analytics 工作区中日志的示例，请使用 ASE 中的应用服务计划之一执行缩放操作。 然后，可以对**AppServiceEnvironmentPlatformLogs**运行查询以查看这些日志。 

**创建警报**

若要针对日志创建警报，请按照[使用 Azure Monitor 创建、查看和管理日志警报][logalerts]中的说明进行操作。 简单地说：

* 在 ASE 门户中打开 "警报" 页
* 选择**新警报规则**
* 选择资源作为 Log Analytics 工作区
* 使用自定义日志搜索设置条件，以使用类似于 "AppServiceEnvironmentPlatformLogs |其中，ResultDescription 包含 "已开始缩放" 或任何所需的内容。 根据需要设置阈值。 
* 根据需要添加或创建操作组。 在操作组中定义对警报的响应，如发送电子邮件或短信
* 为警报命名并保存。

## <a name="upgrade-preference"></a>升级首选项

如果有多个 Ase，则可能需要先升级某些 Ase，然后再进行其他设置。 在 ASE **HostingEnvironment 资源管理器**对象中，可以设置**upgradePreference**的值。 可以使用模板、ARMClient 或配置**upgradePreference**设置 https://resources.azure.com 。 这三个可能的值是：

- **无**： Azure 将在无特定批次中升级 ASE。 此值为默认值。
- **早**于： ASE 将在应用服务升级的前半部分升级。
- **晚**：你的 ASE 将在应用服务升级的下半年升级。

如果使用的是 https://resources.azure.com ，请按照以下步骤设置**upgradePreferences**值：

1. 请参阅 resources.azure.com，然后用 Azure 帐户登录。
1. 浏览资源到订阅 \/ \[ 订阅名称 \] \/ resourceGroups \/ \[ 资源组名称 \] \/ 提供程序 \/ \/ hostingEnvironments \/ \[ ASE 名称 \] 。
1. 在顶部选择 "**读/写**"。
1. 选择“编辑”  。
1. 将**upgradePreference**设置为所需的三个值中的任何一个。
1. 选择 "**修补**"。

![azure com 显示的资源][5]

当你有多个 Ase 时， **upgradePreferences**功能最有意义，因为你的 "早期" ase 将在 "延迟" ase 之前升级。 当你有多个 Ase 时，应将你的开发和测试 Ase 设置为 "提前"，并将生产 Ase 设置为 "延迟"。

## <a name="pricing"></a>定价

称为 "*独立*" 的定价 SKU 仅用于 ase。 ASE 中托管的所有应用服务计划都在隔离定价 SKU 中。 应用服务计划的隔离速率可能因区域而异。

除了应用服务计划的价格以外，ASE 本身也有一个固定费率。 平整速率不会随 ASE 的大小变化。 对于每15个应用服务计划实例，为 ASE 基础结构支付一个额外前端的默认缩放率。

如果每个15个应用服务计划实例的前端的默认缩放速率不够快，则可以调整前端的添加比率或前端的大小。 调整比率或大小时，需要为默认情况下不会添加的前端核心付费。

例如，如果将缩放比率调整到 10，则会为应用服务计划中的每 10 个实例添加一个前端。 该统一费用涵盖针对每 15 个实例添加 1 个前端的缩放比率。 如果缩放比率为 10，则需要支付针对 10 个应用服务计划实例添加的第三个前端的费用。 如果达到了 15 个实例，则不需要支付该费用，因为前端会自动添加。

如果将前端的大小调整为两个核心但不调整此比率，则需要为额外的核心付费。 使用两个前端创建 ASE，因此，如果将大小增加到了两核前端，则需要为两个额外内核支付额外的阈值。

有关详细信息，请参阅 [Azure 应用服务定价][Pricing]。

## <a name="delete-an-ase"></a>删除 ASE

若要删除 ASE，请执行以下操作：

1. 选择 "**应用服务环境**" 窗格顶部的 "**删除**"。

1. 输入 ASE 的名称，确认想要将它删除。 删除 ASE 后，还会删除其中的所有内容。

    ![ASE 删除][3]

1. 选择“确定”  。

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
[logalerts]: ../../azure-monitor/platform/alerts-log.md
