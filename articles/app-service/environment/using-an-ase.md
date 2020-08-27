---
title: 使用和管理应用服务环境
description: 了解如何在应用服务环境中创建、发布和缩放应用。 在本文中查找所有常见任务。
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 5/10/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 5e3cb07730aafed7d1c339f543e7fb09fe956cab
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961901"
---
# <a name="use-an-app-service-environment"></a>使用应用服务环境

应用服务环境 (ASE) 是客户 Azure 虚拟网络实例的子网中的 Azure 应用服务部署。 ASE 包括：

- 前端：这是 HTTP 或 HTTPS 在应用服务环境中终止的地方
- 辅助角色：托管应用的资源
- **数据库**：保存用于定义环境的信息
- **存储**：用于托管客户发布的应用

可以使用外部或内部虚拟 IP (VIP) 来部署 ASE 以进行应用访问。 使用外部 VIP 的部署通常称为“外部 ASE”。 使用内部 VIP 的部署称为“ILB ASE”，因为它使用内部负载均衡器 (ILB)。 若要详细了解 ILB ASE，请参阅[创建和使用 ILB ASE][MakeILBASE]。

## <a name="create-an-app-in-an-ase"></a>在 ASE 中创建应用

在 ASE 中创建应用的过程与一般情况下创建应用的过程大致相同，只存在几处细微的差别。 创建新的应用服务计划时：

- 不要选择某个地理位置来部署应用，而应该选择 ASE 作为位置。
- 在 ASE 中创建的所有应用服务计划只能位于“隔离”定价层中。

如果没有 ASE，可以根据[创建应用服务环境][MakeExternalASE]中的说明创建一个。

在 ASE 中创建应用：

1. 选择“创建资源” > “Web + 移动” > “Web 应用”。

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
    > Linux 应用和 Windows 应用不能位于同一应用服务计划中，但可以位于同一应用服务环境中。
    >

1. 选择“查看 + 创建”，确保信息正确，然后选择“创建”。 

## <a name="how-scale-works"></a>缩放的工作原理

每个应用服务应用在应用服务计划中运行。 应用服务环境保存应用服务计划，应用服务计划保存应用。 缩放某个应用时，也会缩放应用服务计划，以及同一计划中的所有应用。

缩放应用服务计划时，会自动添加所需的基础结构。 添加基础结构时，缩放操作存在一定的时间延迟。 如果按顺序执行多个缩放操作，则会处理第一个基础结构缩放请求，其他请求将会排队。 在第一个缩放操作完成后，所有其他基础结构请求会一起运行。 添加基础结构后，会相应地分配应用服务计划。 创建新应用服务计划本身就是一个缩放操作，因为它会请求额外的硬件。

在多租户应用服务中，缩放是即时发生的，因为有现成可用的资源池用于支持。 ASE 中没有此类缓冲区，而会根据需要分配资源。

在 ASE 中，可将一个应用服务计划扩展到最多 100 个实例。 一个 ASE 在其所有应用服务计划上总共最多可以有 201 个实例。

## <a name="ip-addresses"></a>IP 地址

应用服务可向应用分配专用 IP 地址。 根据[将现有的自定义 SSL 证书绑定到 Azure 应用服务][ConfigureSSL]中所述，可以在配置基于 IP 的 TLS/SSL 后使用此功能。 在 ILB ASE 中，无法添加更多的 IP 地址用于基于 IP 的 SSL。

在外部 ASE 中，可以像在多租户应用服务中那样，为应用配置基于 IP 的 SSL。 ASE 中始终有一个备用地址（最多 30 个 IP 地址）。 每次使用一个地址时，会添加另一个地址，因此，始终有一个随时可用的地址。 分配另一个 IP 地址需要留出一段时间延迟。 这种延迟可以防止快速连续添加 IP 地址。

## <a name="front-end-scaling"></a>前端缩放

扩展应用服务计划时，会自动添加辅助角色来支持这些计划。 创建的每个 ASE 包含两个前端。 前端会按每组（15 个）应用服务计划一个前端的比率自动横向扩展。 例如，如果你有 3 个应用服务计划，每个计划包含 5 个实例，则总共有 15 个实例和 3 个前端。 如果扩展到总共 30 个实例，那么就有 4 个前端。 横向扩展时，此模式将会继续。

默认分配的前端数目适合中等的负载。 可以降低比率，最低可减至每 5 个实例一个前端。 还可以更改前端的大小。 默认情况下，前端采用单核配置。 可以在 Azure 门户中将前端大小更改为双核或四核。

更改比率或前端大小会产生费用。 有关详细信息，请参阅 [Azure 应用服务定价][Pricing]。 若要提高 ASE 的负载容量，可以先扩展为双核前端，然后再调整扩展比率，这样可以获得更好的改进。 更改前端的核心大小会导致 ASE 升级，应在日常工作时间以外执行此操作。

前端资源是 ASE 的 HTTP/HTTPS 终结点。 使用默认前端配置时，每个前端的内存使用率一致地保持在大约 60%。 扩展前端的主要原因在于 CPU 使用率，其主要驱动因素是 HTTPS 流量。

## <a name="app-access"></a>应用访问

在外部 ASE 中，用于创建应用的域后缀是 *。 &lt;asename &gt; . p.azurewebsites.net*。 如果 ASE 名为 external-ase 并且其中托管了名为 contoso 的应用，可以通过以下 URL 访问该应用： 

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

有关如何创建外部 ASE 的信息，请参阅[创建应用服务环境][MakeExternalASE]。

在 ILB ASE 中，用于创建应用的域后缀是 *。 &lt;asename &gt; . appserviceenvironment.net*。 如果 ASE 名为 ilb-ase 并且其中托管了名为 contoso 的应用，可以通过以下 URL 访问该应用： 

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

若要了解如何创建 ILB ASE，请参阅[创建和使用 ILB ASE][MakeILBASE]。

SCM URL 用于访问 Kudu 控制台，也可用于通过 Web 部署发布应用。 有关 Kudu 控制台的信息，请参阅 [Azure 应用服务的 Kudu 控制台][Kudu]。 Kudu 控制台提供 Web UI，可在其中进行调试、上传文件、 编辑文件及执行其他许多操作。

### <a name="dns-configuration"></a>DNS 配置 

使用外部 ASE 时，在 ASE 中创建的应用需要向 Azure DNS 进行注册。 外部 ASE 中没有其他步骤可供应用公开使用。 使用 ILB ASE 时，必须管理自己的 DNS。 可以在自己的 DNS 服务器或 Azure DNS 专用区域中执行此操作。

在自己的 DNS 服务器中通过 ILB ASE 配置 DNS：

1. 为 &lt;ASE 名称&gt;.appserviceenvironment.net 创建一个区域
1. 在该区域中创建一条指向* ILB IP 地址的 A 记录
1. 在该区域中创建一条指向 @ ILB IP 地址的 A 记录
1. 在 &lt;ASE 名称&gt;.appserviceenvironment.net 中创建名为 scm 的区域
1. 在 scm 区域中创建一条指向 * ILB IP 地址的 A 记录

在 Azure DNS 专用区域中配置 DNS：

1. 创建名为“&lt;ASE 名称&gt;.appserviceenvironment.net”的 Azure DNS 专用区域
1. 在该区域中创建一条指向* ILB IP 地址的 A 记录
1. 在该区域中创建一条指向 @ ILB IP 地址的 A 记录
1. 在该区域中创建一条将 *.scm 指向 ILB IP 地址的 A 记录

ASE 默认域后缀的 DNS 设置不会将你的应用限制为只能由这些名称访问。 可以在 ILB ASE 中设置自定义域名而无需对应用进行任何验证。 如果随后想要创建名为 contoso.net 的区域，可以执行此操作并将其指向 ILB IP 地址。 自定义域名适用于应用请求，但不适用于 scm 站点。 scm 站点仅在 &lt;appname&gt;.scm.&lt;asename&gt;.appserviceenvironment.net 中可用。 

名为 .&lt;asename&gt;.appserviceenvironment.net 的区域是全局唯一的。 在 2019 年 5 月之前，客户可以指定 ILB ASE 的域后缀。 如果要将 .contoso.com 用于域后缀，则可以执行此操作，这将包括 scm 站点。 该模型面临一些挑战，其中包括：管理默认的 SSL 证书，缺少对 scm 站点的单一登录，以及要求使用通配符证书。 ILB ASE 默认证书升级过程也会中断，并导致应用程序重启。 为了解决这些问题，ILB ASE 行为已更改为使用基于 ASE 名称域的后缀和 Microsoft 拥有的后缀。 对 ILB ASE 行为的更改仅影响在 2019 日 5 月后发布的 ILB Ase。 预先存在的 ILB ASE 仍必须管理 ASE 及其 DNS 配置的默认证书。

## <a name="publishing"></a>发布

与多租户应用服务一样，在 ASE 中，可以使用以下方法发布应用：

- Web 部署
- FTP
- 持续集成 (CI)
- 在 Kudu 控制台中拖放
- Visual Studio、Eclipse 或 IntelliJ IDEA 等 IDE

使用外部 ASE 时，这些发布选项的工作方式都是相同的。 有关详细信息，请参阅 [Azure 应用服务中的部署][AppDeploy]。

使用 ILB ASE 时，只能通过 ILB 访问发布的终结点。 ILB 位于虚拟网络中 ASE 子网内的专用 IP 上。 如果无法通过网络访问 ILB，则无法在该 ASE 上发布任何应用。 根据[创建和使用 ILB ASE][MakeILBASE] 中所述，必须在系统中配置应用的 DNS。 该要求包括 SCM 终结点。 如果未正确定义终结点，则无法发布。 IDE 还必须能够通过网络访问 ILB 才能直接向其发布。

在不进行额外更改的情况下，基于 Internet 的 CI 系统（例如 GitHub 和 Azure DevOps）不支持 ILB ASE，因为发布终结点不可通过 Internet 进行访问。 可以通过在包含 ILB ASE 的虚拟网络中安装自承载发布代理，来实现从 Azure DevOps 发布到 ILB ASE。 或者，可以利用某个使用提取模型的 CI 系统，例如 Dropbox。

ILB ASE 中应用的发布终结点使用创建该 ILB ASE 所用的域。 可以在应用的发布配置文件和应用的门户窗格中查看此信息（在“概述” > “概要”以及“属性”中查看）  。

## <a name="storage"></a>存储

ASE 为其中的所有应用提供 1 TB 存储空间。 “独立”定价 SKU 中的一个应用服务计划限制为 250 GB。 在 ASE 中，每个应用服务计划增加了 250 GB 的存储空间，最大限制为 1 TB。 你可以有四个以上的应用服务计划，但没有超过 1 TB 限制的额外存储空间。

## <a name="logging"></a>日志记录

可将 ASE 与 Azure Monitor 相集成，以将有关 ASE 的日志发送到 Azure 存储、Azure 事件中心或 Log Analytics。 当前会记录以下项：

| 场景 | Message |
|---------|----------|
| ASE 运行不正常 | 由于虚拟网络配置无效，指定的 ASE 运行不正常。 如果持续出现不正常状态，ASE 将会挂起。 请确保遵循此处定义的准则： https://docs.microsoft.com/azure/app-service/environment/network-info 。 |
| ASE 子网空间几乎已耗尽 | 指定的 ASE 位于一个几乎耗尽了空间的子网中。 还剩下 {0} 个地址。 一旦这些地址耗尽，ASE 就无法缩放。  |
| ASE 即将达到实例总数限制 | 指定的 ASE 即将达到 ASE 的实例总数限制。 它目前包含 {0} 个应用服务计划实例，最多可以包含 201 个实例。 |
| ASE 无法访问某个依赖项 | 指定的 ASE 无法访问 {0}。  请确保遵循此处定义的准则： https://docs.microsoft.com/azure/app-service/environment/network-info 。 |
| ASE 已挂起 | 指定的 ASE 已挂起。 ASE 挂起的可能原因是帐户不足，或虚拟网络配置无效。 解决根本原因并恢复 ASE，以继续为流量提供服务。 |
| ASE 升级已启动 | 已开始对指定的 ASE 进行平台升级。 预期缩放操作会出现延迟。 |
| ASE 升级已完成 | 对指定的 ASE 进行平台升级已完成。 |
| 缩放操作已启动 | 应用服务计划 ({0}) 已开始缩放。 所需状态：{1} I{2} 辅助角色。
| 缩放操作已完成 | 应用服务计划 ({0}) 已完成缩放。 当前状态：{1} I{2} 辅助角色。 |
| 缩放操作失败 | 应用服务计划 ({0}) 无法缩放。 当前状态：{1} I{2} 辅助角色。 |

若要在 ASE 中启用日志记录：

1. 在门户中转到“诊断设置”。
1. 选择“添加诊断设置”。
1. 提供日志集成的名称。
1. 选择并配置所需的日志目标。
1. 选择“AppServiceEnvironmentPlatformLogs”。

![ASE 诊断日志设置][4]

如果与 Log Analytics 集成，可通过以下方式查看日志：在 ASE 门户中选择“日志”，并针对“AppServiceEnvironmentPlatformLogs”创建查询。  仅当 ASE 具有将触发日志的事件时，才发出日志。 如果 ASE 没有此类事件，则不会有任何日志。 若要快速查看 Log Analytics 工作区中的日志示例，请使用 ASE 中的某个应用服务计划执行缩放操作。 然后，你可以针对 AppServiceEnvironmentPlatformLogs 运行查询来查看这些日志。 

**创建警报**

若要针对日志创建警报，请按[使用 Azure Monitor 创建、查看和管理日志警报][logalerts]中的说明操作。 简单地说：

* 在 ASE 门户中打开“警报”页面
* 选择“新建警报规则”
* 选择资源作为 Log Analytics 工作区
* 使用自定义日志搜索设置条件，以使用类似于“AppServiceEnvironmentPlatformLogs”的查询，其中，ResultDescription 包含“已开始缩放”或任何所需的内容。 根据需要设置阈值。 
* 根据需要添加或创建操作组。 你可以在操作组中定义对警报的响应，如发送电子邮件或短信
* 为警报命名并保存。

## <a name="upgrade-preference"></a>升级首选项

如果你有多个 ASE，你可能希望先升级某些 ASE，再升级其他 ASE。 在 ASE“HostingEnvironment 资源管理器”对象中，可以设置 upgradePreference 的值。  可以使用模板、ARMClient 或 https://resources.azure.com 配置 upgradePreference 设置。 三个可能的值为：

- **无**：Azure 将在非特定的批次中升级 ASE。 此值为默认值。
- **Early**：ASE 将在应用服务升级过程的上半阶段升级。
- **Late**：ASE 将在应用服务升级过程的下半阶段升级。

如果使用的是 https://resources.azure.com ，请按照以下步骤设置 **upgradePreferences** 值：

1. 请参阅 resources.azure.com，然后用 Azure 帐户登录。
1. 浏览资源到订阅 \/ \[ 订阅名称 \] \/ resourceGroups \/ \[ 资源组名称 \] \/ 提供程序 \/ \/ hostingEnvironments \/ \[ ASE 名称 \] 。
1. 在顶部选择 " **读/写** "。
1. 选择“编辑”  。
1. 将 **upgradePreference** 设置为所需的三个值中的任何一个。
1. 选择 " **修补**"。

![azure com 显示的资源][5]

当你有多个 Ase 时， **upgradePreferences** 功能最有意义，因为你的 "早期" ase 将在 "延迟" ase 之前升级。 当你有多个 Ase 时，应将你的开发和测试 Ase 设置为 "提前"，并将生产 Ase 设置为 "延迟"。

## <a name="pricing"></a>定价

称为“隔离”的定价 SKU 仅适用于 ASE。 ASE 中托管的所有应用服务计划都在“隔离”定价 SKU 中。 应用服务计划的“隔离”费率可能因区域而异。

除了应用服务计划的价格以外，ASE 本身也有统一的费率。 该统一费率不会根据 ASE 的大小改变。 它按每 15 个应用服务计划实例 1 个额外前端的默认缩放比率为 ASE 基础结构计费。

如果每 15 个应用服务计划实例 1 个前端的默认缩放比率不够快，可以调整前端的添加比率或前端的大小。 调整比率或大小时，需要为默认情况下不会添加的前端核心付费。

例如，如果将缩放比率调整到 10，则会为应用服务计划中的每 10 个实例添加一个前端。 该统一费用涵盖针对每 15 个实例添加 1 个前端的缩放比率。 如果缩放比率为 10，则需要支付针对 10 个应用服务计划实例添加的第三个前端的费用。 如果达到了 15 个实例，则不需要支付该费用，因为前端会自动添加。

如果将前端的大小调整为两个核心，但不调整比率，则需要为额外的核心付费。 ASE 在创建时包含两个前端，因此，如果将大小增加到双核前端，那么即使低于自动缩放阈值，你也要为两个额外的核心付费。

有关详细信息，请参阅 [Azure 应用服务定价][Pricing]。

## <a name="delete-an-ase"></a>删除 ASE

若要删除 ASE，请执行以下操作：

1. 选择“应用服务环境”窗格顶部的“删除”。 

1. 输入 ASE 的名称，确认想要将它删除。 删除 ASE 时，会同时删除它包含的所有内容。

    ![ASE 删除][3]

1. 选择“确定” 。

## <a name="ase-cli"></a>ASE CLI

可通过命令行功能管理 ASE。  下面对 az cli 命令进行了说明。

```azurecli
C:\>az appservice ase --help

Group
    az appservice ase : Manage App Service Environments v2.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    create         : Create app service environment.
    delete         : Delete app service environment.
    list           : List app service environments.
    list-addresses : List VIPs associated with an app service environment.
    list-plans     : List app service plans associated with an app service environment.
    show           : Show details of an app service environment.
    update         : Update app service environment.

For more specific examples, use: az find "az appservice ase"
```



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
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/platform/alerts-log.md