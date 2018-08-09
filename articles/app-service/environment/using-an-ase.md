---
title: 使用 Azure 应用服务环境
description: 如何在 Azure 应用服务环境中创建、发布和缩放应用
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.openlocfilehash: b3550c771b4c2916987c66f318010e5bb246fa39
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39446844"
---
# <a name="use-an-app-service-environment"></a>使用应用服务环境 #

## <a name="overview"></a>概述 ##

Azure 应用服务环境指将 Azure 应用服务部署到客户 Azure 虚拟网络的子网中。 该环境包括：

- **前端**：前端是 HTTP/HTTPS 在应用服务环境 (ASE) 中的终止位置。
- **辅助角色**：辅助角色是托管应用的资源。
- **数据库**：数据库保存用于定义环境的信息。
- **存储**：存储用于托管客户发布的应用。

> [!NOTE]
> 应用服务环境有两个版本：ASEv1 和 ASEv2。 在 ASEv1 中，使用资源之前必须先管理资源。 若要了解如何配置和管理 ASEv1，请参阅[配置应用服务环境 v1][ConfigureASEv1]。 本文的余下内容重点介绍 ASEv2。
>
>

可以使用外部或内部 VIP 来部署 ASE（ASEv1 和 ASEv2）进行应用访问。 使用外部 VIP 的部署通常称为外部 ASE。 内部版本称为 ILB ASE，因为它使用内部负载均衡器 (ILB)。 若要详细了解 ILB ASE，请参阅[创建和使用 ILB ASE][MakeILBASE]。

## <a name="create-a-web-app-in-an-ase"></a>在 ASE 中创建 Web 应用 ##

在 ASE 中创建 Web 应用的过程与一般情况下的创建过程大致相同，只存在几处小小的差别。 创建新的应用服务计划时：

- 不要选择某个地理位置来部署应用，而应该选择 ASE 作为位置。
- 在 ASE 中创建的所有应用服务计划必须在“隔离”定价层中。

如果没有 ASE，可以根据[创建应用服务环境][MakeExternalASE]中的说明创建一个。

若要在 ASE 中创建 Web 应用，请执行以下操作：

1. 选择“创建资源” > “Web + 移动” > “Web 应用”。

1. 输入 Web 应用的名称。 如果已在 ASE 中选择了应用服务计划，则应用的域名会反映 ASE 的域名。

    ![Web 应用名称选择][1]

1. 选择一个订阅。

1. 输入新资源组的名称，或选择“使用现有”并从下拉列表中选择一个资源组。

1. 选择 OS。 

    * 在 ASE 中托管 Linux 应用是一项新的预览功能，因此建议不要将 Linux 应用添加到当前正在运行生产工作负荷的 ASE 中。 
    * 将 Linux 应用添加到 ASE 中意味着 ASE 也将处于预览模式。 

1. 在 ASE 中选择现有的应用服务计划，或遵循以下步骤创建一个新的计划：

    a. 选择“新建”。

    b. 输入应用服务计划的名称。

    c. 在“位置”下拉列表中选择自己的 ASE。 在 ASE 中托管 Linux 应用的功能目前仅在 6 个区域中启用：**美国西部、美国东部、西欧、北欧、澳大利亚东部、东南亚**。 

    d. 选择“隔离”定价层。 选择“选择”。

    e. 选择“确定”。
    
    ![“隔离”定价层][2]

    > [!NOTE]
    > Linux Web 应用和 Windows Web 应用不能位于同一应用服务计划中，但可以位于同一应用服务环境中。 
    >

1. 选择**创建**。

## <a name="how-scale-works"></a>缩放的工作原理 ##

每个应用服务应用在应用服务计划中运行。 容器模型是环境保存应用服务计划，应用服务计划保存应用。 缩放某个应用时，会缩放应用服务计划，因此，会缩放同一计划中的所有应用。

在 ASEv2 中缩放应用服务计划时，会自动添加所需的基础结构。 添加基础结构时，缩放操作存在一定的时间延迟。 在 ASEv1 中，只有在添加所需的基础结构之后，才能创建或扩展应用服务计划。 

在多租户应用服务中，缩放通常是即时发生的，因为有现成可用的资源池用于支持。 ASE 中没有此类缓冲区，而会根据需要分配资源。

在 ASE 中，最多可以扩展到 100 个实例。 这 100 个实例可以全部位于一个应用服务计划中，也可以分布在多个应用服务计划中。

## <a name="ip-addresses"></a>IP 地址 ##

应用服务能够向应用分配专用的 IP 地址。 根据[将现有的自定义 SSL 证书绑定到 Azure Web 应用][ConfigureSSL]中所述，可以在配置基于 IP 的 SSL 后使用此功能。 但是，ASE 中有一个明显的差异。 无法添加更多的 IP 地址用于 ILB ASE 中基于 IP 的 SSL。

在 ASEv1 中，需要先分配 IP 地址作为资源，然后才能使用这些 IP 地址。 在 ASEv2 中，可以从应用使用 IP 地址，就像在多租户应用服务中一样。 ASEv2 中始终有一个备用地址，最多可包含 30 个 IP 地址。 每次使用一个地址时，会添加另一个地址，因此，始终有一个现成可用的地址。 分配另一个 IP 地址会产生一定的时间延迟，这会给快速连续添加 IP 地址带来阻碍。

## <a name="front-end-scaling"></a>前端缩放 ##

在 ASEv2 中扩展应用服务计划时，会自动添加工作线程来支持这些计划。 创建的每个 ASE 包含两个前端。 此外，前端还会自动扩展，扩展的速率是针对应用服务计划中的每 15 个实例扩展一个前端。 例如，如果有 15 个实例，则会获得 3 个前端。 如果扩展到 30 个实例，则会获得 4 个前端，依此类推。

在大多数情况下，前端数目应该足够。 但是，可以按更快的速率扩展。 可以将比率改到最小，即针对每 5 个实例扩展一个前端。 更改比率不会产生费用。 有关详细信息，请参阅 [Azure 应用服务定价][Pricing]。

前端资源是 ASE 的 HTTP/HTTPS 终结点。 使用默认前端配置时，每个前端的内存使用率一致地保持在大约 60%。 客户工作负荷不会在前端上运行。 在缩放方面，前端的一个关键因素是 CPU（主要取决于 HTTPS 流量）。

## <a name="app-access"></a>应用访问 ##

在外部 ASE 中，创建应用时使用的域不同于多租户应用服务。 它包括 ASE 的名称。 有关如何创建外部 ASE 的详细信息，请参阅[创建应用服务环境][MakeExternalASE]。 外部 ASE 中的域名类似于 *.&lt;asename&gt;.p.azurewebsites.net*。 例如，如果 ASE 名为 _external-ase_ 并托管名为 _contoso_ 的应用，则可以通过以下 URL 访问该应用：

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

URL contoso.scm.external-ase.p.azurewebsites.net 用于访问 Kudu 控制台，或通过 Web 部署发布应用。 有关 Kudu 控制台的信息，请参阅 [Azure 应用服务的 Kudu 控制台][Kudu]。 Kudu 控制台提供 Web UI，可在其中进行调试、上传文件、 编辑文件及执行其他许多操作。

在 ILB ASE 中，可在部署时确定域。 有关如何创建 ILB ASE 的详细信息，请参阅[创建和使用 ILB ASE][MakeILBASE]。 如果指定类似于 _ilb-ase.info_ 的域名，则在创建应用的过程中，该 ASE 中的应用将使用该域。 对于名为 _contoso_ 的应用，URL 为：

- contoso.ilb-ase.info
- contoso.scm.ilb-ase.info

## <a name="publishing"></a>发布 ##

与多租户应用服务一样，在 ASE 中，可以使用以下方法发布应用：

- Web 部署。
- FTP。
- 持续集成。
- 在 Kudu 控制台中拖放。
- Visual Studio、Eclipse 或 IntelliJ IDEA 等 IDE。

使用外部 ASE 时，这些发布选项的行为都是相同的。 有关详细信息，请参阅 [Azure 应用服务中的部署][AppDeploy]。 

在发布方面，主要差别在于 ILB ASE。 使用 ILB ASE 时，只能通过 ILB 访问所有发布终结点。 ILB 位于虚拟网络中 ASE 子网内的专用 IP 上。 如果无法通过网络访问 ILB，则无法在该 ASE 上发布任何应用。 根据[创建和使用 ILB ASE][MakeILBASE] 中所述，需要在系统中配置应用的 DNS。 这包括 SCM 终结点。 如果未正确定义 DNS，则无法发布。 IDE 也需要能够通过网络访问 ILB 才能直接在 ASE 中发布应用。

基于 Internet 的 CI 系统（例如 GitHub 和 Visual Studio Team Services）不支持 ILB ASE，因为发布终结点不可访问 Internet。 需要该用使用拉取模式的 CI 系统，如 Dropbox。

ILB ASE 中应用的发布终结点使用创建该 ILB ASE 所用的域。 可以在应用的发布配置文件和应用门户的边栏选项卡中查看此信息（在“概述” > “软件包”以及“属性”中查看）。 

## <a name="pricing"></a>定价 ##

创建了名为 Isolated 的定价 SKU，仅用于与 ASEv2 一起使用。 ASEv2 中托管的所有应用服务计划都在“隔离”定价 SKU 中。 独立应用服务计划费率可能因区域而异。 

除了应用服务计划的价格以外，ASE 本身也会收取一笔统一的费用。 统一费率不会随 ASE 的大小发生改变，并按每 15 个应用服务计划实例 1 个额外前端的默认缩放率为 ASE 基础结构计费。  

如果每 15 个应用服务计划实例 1 个前端的默认缩放率不够快，可调整添加前端的比率或前端的大小。  调整比率或大小时，需要为默认情况下不会添加的前端核心付费。  

例如，如果将缩放比率调整到 10，则会为应用服务计划中的每 10 个实例添加一个前端。 该统一费用涵盖针对每 15 个实例添加 1 个前端的缩放比率。 如果缩放比率为 10，则需要支付针对 10 个应用服务计划实例添加的第三个前端的费用。 如果达到了 15 个实例，则不需要支付该费用，因为前端会自动添加。

如果将前端的大小调整为 2 个核心，但不调整比率，则需要为额外的核心付费。  对于使用 2 个前端创建的 ASE，即使低于自动缩放阈值，也需在将大小增加到 2 个核心的前端时为 2 个额外的核心付费。

有关详细信息，请参阅 [Azure 应用服务定价][Pricing]。

## <a name="delete-an-ase"></a>删除 ASE ##

若要删除 ASE，请执行以下操作： 

1. 使用“应用服务环境”边栏选项卡顶部的“删除”。 

1. 输入 ASE 的名称，确认想要将它删除。 删除 ASE 时，会同时删除它包含的所有内容。 

    ![ASE 删除][3]

<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png


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
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../app-service-deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
