---
title: 使用和管理应用服务环境
description: 了解如何在应用服务环境中创建、发布和缩放应用。 在本文中查找所有常见任务。
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 01/01/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 7ca6882aea7225dcb97c9f913267b2543de07fef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133123"
---
# <a name="use-an-app-service-environment"></a>使用应用服务环境

应用服务环境 （ASE） 是将 Azure 应用服务部署到客户 Azure 虚拟网络实例中的子网。 ASE 包括：

- **前端**：HTTP 或 HTTPS 在应用服务环境中终止的位置。
- **辅助功能**：托管应用的资源。
- **数据库**：保存定义环境的信息。
- **存储**：用于托管客户发布的应用。

您可以使用外部或内部虚拟 IP （VIP） 部署 ASE 以进行应用访问。 具有外部 VIP 的部署通常称为外部*ASE。* 具有内部 VIP 的部署称为*ILB ASE，* 因为它使用内部负载均衡器 （ILB）。 若要详细了解 ILB ASE，请参阅[创建和使用 ILB ASE][MakeILBASE]。

## <a name="create-an-app-in-an-ase"></a>在 ASE 中创建应用

要在 ASE 中创建应用，请使用与通常创建应用时相同的过程，但存在一些小差异。 创建新的应用服务计划时：

- 不要选择某个地理位置来部署应用，而应该选择 ASE 作为位置。
- 在 ASE 中创建的所有应用服务计划只能位于隔离定价层中。

如果没有 ASE，则可以按照["创建应用服务环境][MakeExternalASE]"中的说明创建 ASE。

在 ASE 中创建应用：

1. 选择 **"创建资源** > **Web + 移动** > **Web 应用**"。

1. 输入应用程序的名称。 如果您在 ASE 中选择了应用服务计划，则应用的域名将反映 ASE 的域名：

    ![应用名称选择][1]

1. 选择一个订阅。

1. 输入新资源组的名称，或选择“使用现有”并从下拉列表中选择一个资源组。****

1. 选择 OS。

1. 在 ASE 中选择现有的应用服务计划，或遵循以下步骤创建一个新的计划：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 从 Azure 门户左侧菜单中，选择"**创建资源> Web 应用**。

    b.保留“数据库类型”设置，即设置为“共享”。 选择订阅。

    c. 选择或创建资源组。

    d.单击“下一步”。 输入 Web 应用的名称。

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 选择**代码**或**Docker 容器**。

    f. 选择运行时堆栈。

    g. 选择 **Linux** 或 **Windows**。 

    h. 在 **"区域**"下拉列表中选择 ASE。 

    i. 选择或创建新的应用服务计划。 如果创建新的应用服务计划，请选择相应的**独立**SKU 大小。

    ![“隔离”定价层][2]

    > [!NOTE]
    > Linux 应用和 Windows 应用不能位于相同的应用服务计划中，但它们可以位于相同的应用服务环境中。
    >

1. 选择 **"审阅" = 创建**，请确保信息正确，然后选择"**创建**"。

## <a name="how-scale-works"></a>缩放的工作原理

每个应用服务应用在应用服务计划中运行。 应用服务环境保存应用服务计划，应用服务计划保存应用。 缩放应用时，还可以缩放应用服务计划和同一计划中的所有应用。

缩放应用服务计划时，将自动添加所需的基础结构。 在添加基础结构时，扩展操作的时间延迟。 如果按顺序执行多个缩放操作，则对第一个基础结构规模请求执行操作，而其他基础结构规模请求将排队。 当第一个规模操作完成时，其他基础结构请求都一起操作。 添加基础结构时，将根据需要分配应用服务计划。 创建新的应用服务计划本身就是一个规模操作，因为它请求其他硬件。

在多租户应用服务中，缩放是即时的，因为资源池随时可用于支持它。 在 ASE 中，没有此类缓冲区，并且资源会根据需要分配。

在 ASE 中，可以缩放最多 100 个实例的应用服务计划。 ASE 可以在整个 ASE 中的所有应用服务计划中拥有最多 201 个实例。

## <a name="ip-addresses"></a>IP 地址

应用服务可以将专用 IP 地址分配给应用。 在配置基于 IP 的 SSL 后，此功能可用，如[将现有自定义 SSL 证书绑定到 Azure 应用服务][ConfigureSSL]中所述。 在 ILB ASE 中，无法添加更多 IP 地址以用于基于 IP 的 SSL。

使用外部 ASE，您可以像在多租户应用服务中一样为应用配置基于 IP 的 SSL。 ASE 中始终有一个备用地址，最多 30 个 IP 地址。 每次使用另一个地址时，都会添加另一个地址，以便地址始终随时可用。 分配另一个 IP 地址需要时间延迟。 这种延迟可防止快速连续添加 IP 地址。

## <a name="front-end-scaling"></a>前端缩放

横向扩展应用服务计划时，将自动添加工作人员以支持这些计划。 创建的每个 ASE 包含两个前端。 对于每组 15 个应用服务计划实例，前端以一个前端的速率自动横向扩展。 例如，如果您有三个应用服务计划，每个计划有 5 个实例，则总共有 15 个实例和 3 个前端。 如果扩展到总共 30 个实例，则有四个前端。 当您横向扩展时，此模式将继续。

默认情况下分配的前端数适用于中等负载。 您可以将比率降低为每五个实例一个前端。 您还可以更改前端的大小。 默认情况下，它们是单核。 在 Azure 门户中，可以将其大小更改为两个或四个内核。

更改比率或前端尺寸需要付费。 有关详细信息，请参阅 [Azure 应用服务定价][Pricing]。 如果要提高 ASE 的负载容量，在调整比例之前，首先缩放到双核前端，从而获得更多的改进。 更改前端的核心尺寸将导致 ASE 的升级，并且应在正常工作时间之外完成。

前端资源是 ASE 的 HTTP/HTTPS 终结点。 使用默认前端配置时，每个前端的内存使用率一致地保持在大约 60%。 扩展前端的主要原因是 CPU 使用率，这主要是由 HTTPS 流量驱动的。

## <a name="app-access"></a>应用访问

在外部 ASE 中，用于创建应用的域后缀为*&lt; 。asename&gt;.p.azurewebsites.net*。 如果您的 ASE 被命名为_外部 ase，_ 并且您托管了该 ASE 中名为_contoso_的应用，则可以通过以下 URL 访问它：

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

有关如何创建外部 ASE 的信息，请参阅[创建应用服务环境][MakeExternalASE]。

在 ILB ASE 中，用于创建应用的域后缀为 *。&lt;asename&gt;.appserviceenvironment.net*. . 如果您的 ASE 名为_ilb-ase，_ 并且您托管了该 ASE 中名为_contoso_的应用，则通过以下 URL 访问它：

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

有关如何创建 ILB ASE 的信息，请参阅[创建和使用 ILB ASE][MakeILBASE]。

SCM URL 用于访问 Kudu 控制台或使用 Web 部署发布应用。 有关 Kudu 控制台的信息，请参阅 [Azure 应用服务的 Kudu 控制台][Kudu]。 Kudu 控制台提供 Web UI，可在其中进行调试、上传文件、 编辑文件及执行其他许多操作。

## <a name="publishing"></a>发布

在 ASE 中，与多租户应用服务一样，可以通过以下方法发布：

- Web 部署
- FTP
- 持续集成 （CI）
- 在 Kudu 控制台中拖放
- IDE，如视觉工作室、Eclipse 或 IntelliJ IDEA

使用外部 ASE 时，这些发布选项的工作方式都相同。 有关详细信息，请参阅 [Azure 应用服务中的部署][AppDeploy]。

ILB ASE 的发布与 ILB ASE 有很大不同，因为 ILB AsE 的发布终结点只能通过 ILB 提供。 ILB 位于虚拟网络中 ASE 子网内的专用 IP 上。 如果您没有对 ILB 的网络访问权限，则无法在此 ASE 上发布任何应用。 如[创建和使用 ILB ASE][MakeILBASE]中所述，您必须为系统中的应用配置 DNS。 该要求包括 SCM 终结点。 如果终结点定义不正确，则无法发布。 您的 IDA 还必须具有对 ILB 的网络访问权限才能直接发布到 ILB。

如果没有其他更改，基于 Internet 的 CI 系统（如 GitHub 和 Azure DevOps）将不能与 ILB ASE 配合使用，因为发布终结点无法访问 Internet。 通过在包含 ILB ASE 的虚拟网络中安装自托管发布代理，可以从 Azure DevOps 启用发布到 ILB ASE。 或者，可以利用某个使用提取模型的 CI 系统，例如 Dropbox。

ILB ASE 中应用的发布终结点使用创建该 ILB ASE 所用的域。 您可以在应用的发布配置文件和应用的门户窗格中（在**概述** > **基本值**和**属性**中）中看到它。

## <a name="storage"></a>存储

ASE 对于 ASE 中的所有应用都有 1 TB 的存储空间。 默认情况下，隔离定价 SKU 中的应用服务计划限制为 250 GB。 如果您有五个或更多应用服务计划，请注意不要超过 ASE 的 1-TB 限制。 如果您在一个应用服务计划中需要超过 250 GB 的限制，请联系支持人员将应用服务计划限制调整为最多 1 TB。 调整计划限制后，ASE 中的所有应用服务计划仍有 1 TB 的限制。

## <a name="logging"></a>Logging

您可以将 ASE 与 Azure 监视器集成，以便将有关 ASE 的日志发送到 Azure 存储、Azure 事件中心或日志分析。 这些项目今天记录：

| 场景 | 消息 |
|---------|----------|
| ASE 不正常 | 由于虚拟网络配置无效，指定的 ASE 不正常。 如果不正常状态继续，ASE 将挂起。 确保遵循此处定义的准则： https://docs.microsoft.com/azure/app-service/environment/network-info |
| ASE 子网几乎空间不足 | 指定的 ASE 位于几乎空间不足的子网中。 {0}其余地址。 一旦这些地址用尽，ASE将无法缩放。  |
| ASE 正在接近总实例限制 | 指定的 ASE 正在接近 ASE 的总实例限制。 它当前包含{0}最多 201 个实例的应用服务计划实例。 |
| ASE 无法访问依赖项 | 指定的 ASE 无法访问{0}。  确保遵循此处定义的准则： https://docs.microsoft.com/azure/app-service/environment/network-info |
| ASE 已挂起 | 指定的 ASE 挂起。 ASE 挂起可能是由于帐户不足或虚拟网络配置无效造成的。 解决根本原因并恢复 ASE 以继续服务流量。 |
| ASE 升级已启动 | 平台升级到指定的 ASE 已经开始。 预计缩放操作会出现延迟。 |
| ASE 升级已完成 | 平台升级到指定的 ASE 已完成。 |
| 缩放操作已启动 | 应用服务计划 （{0}） 已开始扩展。 所需状态：{1}我{2}工作。
| 缩放操作已完成 | 应用服务计划 （{0}） 已完成缩放。 当前状态：{1}我{2}工作。 |
| 缩放操作失败 | 应用服务计划 （{0}） 无法扩展。 当前状态：{1}我{2}工作。 |

要在 ASE 上启用日志记录，请：

1. 在门户中，转到**诊断设置**。
1. 选择“添加诊断设置”。****
1. 提供日志集成的名称。
1. 选择并配置所需的日志目标。
1. 选择**应用服务环境平台日志**。

![ASE 诊断日志设置][4]

如果与日志分析集成，则可以通过从 ASE 门户中选择**日志**并针对**AppService 环境平台日志**创建查询来查看日志。

## <a name="upgrade-preference"></a>升级首选项

如果您有多个 ASEs，您可能希望在其它 ASEs 之前升级某些 ASA。 在 ASE**托管环境资源管理器**对象中，您可以设置**升级首选项**的值。 可以使用模板、ARMClient 或https://resources.azure.com配置**升级首选项**设置。 三个可能的值是：

- **无**：Azure 将在没有特定批处理中升级 ASE。 此值为默认值。
- **早期**：您的 ASE 将在应用服务升级的前半部分升级。
- **延迟**：您的 ASE 将在应用服务升级的后半部分升级。

如果您使用的https://resources.azure.com是 ，请按照以下步骤设置**升级首选项**值：

1. 转到resources.azure.com，然后使用 Azure 帐户登录。
1. \/\[通过资源订阅\]\/订阅名称资源组\/\[资源组名称\]\/提供程序\/Microsoft.Web\/托管环境\/\[ASE 名称\]。
1. 选择顶部**的"读/写**"。
1. 选择 **"编辑**"。
1. 将 **"升级首选项"** 设置为所需的三个值之一。
1. 选择 **"补丁**"。

![资源 azure com 显示][5]

当您有多个 ASEs 时，**升级首选项**功能最有意义，因为"早期"ASEs 将在"后期"ASEs 之前进行升级。 当您有多个 ASEs 时，应将开发和测试 ASEs 设置为"早期"，并将生产 ASEs 设置为"后期"。

## <a name="pricing"></a>定价

称为 *"隔离"* 的定价 SKU 仅适用于 ASEs。 托管在 ASE 中的所有应用服务计划都位于隔离定价 SKU 中。 应用服务计划的隔离费率可能因地区而异。

除了应用服务计划的价格外，ASE 本身还有统一费率。 统一速率不会随 ASE 的大小而变化。 它为每 15 个应用服务计划实例的默认比例为一个前端的 ASE 基础结构付费。

如果每 15 个应用服务计划实例的默认前端比例不够快，则可以调整添加前端的比率或前端的大小。 调整比率或大小时，需要为默认情况下不会添加的前端核心付费。

例如，如果将缩放比率调整到 10，则会为应用服务计划中的每 10 个实例添加一个前端。 该统一费用涵盖针对每 15 个实例添加 1 个前端的缩放比率。 如果缩放比率为 10，则需要支付针对 10 个应用服务计划实例添加的第三个前端的费用。 如果达到了 15 个实例，则不需要支付该费用，因为前端会自动添加。

如果将前端的大小调整为两个内核，但不调整比率，则为额外的内核付费。 ASE 是创建具有两个前端的，因此，如果将大小增加到双核前端，即使低于自动缩放阈值，您也会为两个额外的内核付费。

有关详细信息，请参阅 [Azure 应用服务定价][Pricing]。

## <a name="delete-an-ase"></a>删除 ASE

若要删除 ASE，请执行以下操作：

1. 在 **"应用服务环境**"窗格的顶部选择 **"删除**"。

1. 输入 ASE 的名称，确认想要将它删除。 删除 ASE 时，还会删除其中的所有内容。

    ![ASE 删除][3]

1. 选择“确定”。

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
