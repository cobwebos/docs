---
title: 用于在 Azure 虚拟机上部署 Oracle 应用程序的体系结构 |Microsoft Docs
description: 应用程序体系结构, 用于将 Oracle 应用 (包括电子商务套件、JD Edwards EnterpriseOne 和 PeopleSoft) 部署在 Azure 中的数据库或 Oracle 云基础结构 (OCI) 中 Microsoft Azure 虚拟机上。
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/18/2019
ms.author: rogirdh
ms.custom: ''
ms.openlocfilehash: ca16dceae9ab1afab17b2893b61f6a1c3309cf93
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361578"
---
# <a name="architectures-to-deploy-oracle-applications-on-azure"></a>用于在 Azure 上部署 Oracle 应用程序的体系结构

Microsoft 和 Oracle 共同合作, 使客户能够在云中部署 oracle 电子商务套件、JD Edwards EnterpriseOne 和 PeopleSoft 等 Oracle 应用程序。 随着在 Microsoft Azure 和 Oracle 云基础结构 (OCI) 之间引入预览版[专用网络互连](configure-azure-oci-networking.md), 现在可以在 azure 中部署 Oracle 应用程序, 并将其后端数据库部署到 AZURE 或 OCI。 Oracle 应用程序也可以与 Azure Active Directory 集成, 使你能够设置单一登录, 使用户能够使用其 Azure Active Directory (Azure AD) 凭据登录到 Oracle 应用程序。

OCI 为 Oracle 应用程序提供多个 Oracle 数据库选项, 包括 DBaaS、Exadata 云服务、Oracle RAC 和基础结构即服务 (IaaS)。 目前, Oracle 应用程序不支持自治数据库后端。

可以使用[多个选项](oracle-overview.md)在 Azure 中部署 Oracle 应用程序, 包括高度可用且安全的方式。 如果选择完全在 Azure 上运行 Oracle 应用程序, Azure 还提供可部署的[oracle 数据库 VM 映像](oracle-vm-solutions.md)。

以下部分概述了 Microsoft 和 Oracle 部署 Oracle 电子商务套件、JD Edwards EnterpriseOne 和 PeopleSoft, 以及完全在 Azure 中的架构建议。 Microsoft 和 Oracle 已对这些应用程序进行了测试, 并确认了性能符合 Oracle 为这些应用程序设置的标准。

## <a name="architecture-considerations"></a>体系结构注意事项

Oracle 应用程序由多个服务组成, 这些服务可以托管在 Azure 中的相同或多个虚拟机上, 也可以选择在 OCI 中。 

可以设置应用程序实例的私有或公共终结点。 Microsoft 和 Oracle 建议使用单独的子网中的公共 IP 地址设置*堡垒主机 VM* , 以管理应用程序。 然后, 仅将专用 IP 地址分配给其他计算机, 包括数据库层。 

在跨云的体系结构中设置应用程序时, 需要规划以确保 Azure 虚拟网络中的 IP 地址空间不与 OCI 虚拟云网络中的专用 IP 地址空间重叠。

为了增加安全性, 请在子网级别设置网络安全组, 以确保仅允许特定端口和 IP 地址上的流量。 例如, 中间层中的计算机应仅接收来自虚拟网络的流量。 外部流量不会直接到达中间层计算机。

为实现高可用性, 你可以在同一个可用性集或不同的可用性区域中设置不同服务器的冗余实例。 可用性区域允许实现 99.99% 的正常运行时间 SLA, 而可用性集则允许在区域内实现 99.95% 的运行时间 SLA。 本文中所示的示例结构是跨两个可用性区域部署的。

使用跨云互连部署应用程序时, 可以继续使用现有 ExpressRoute 线路将 Azure 环境连接到本地网络。 但是, 需要将与连接到本地网络的连接的 ExpressRoute 线路单独用于 OCI。

## <a name="e-business-suite"></a>电子商务套件

Oracle 电子商务套件 (EBS) 是一套应用程序, 包括供应链管理 (SCM) 和客户关系管理 (CRM)。 若要利用 OCI 的托管数据库组合, 可以使用 Microsoft Azure 和 OCI 之间的跨云互连来部署 EBS。 在此配置中, 表示层和应用层在 Azure 和 OCI 中的数据库层中运行, 如以下体系结构关系图所示 (图 1)。

![电子商务套件跨云体系结构](media/oracle-oci-applications/ebs-arch-cross-cloud.png)

图 1：*电子商务套件跨云体系结构* 

在此体系结构中, Azure 中的虚拟网络使用跨云互连连接到 OCI 中的虚拟云网络。 应用程序层是在 Azure 中设置的, 而数据库是在 OCI 中设置的。 建议使用网络安全组将每个组件部署到其自己的子网, 以便仅允许来自特定端口上特定子网的流量。

此外, 还可以使用在区域的两个可用性区域中使用 Oracle 数据防护配置的高可用性 Oracle 数据库, 对 Azure 中的完全部署进行改编。 下图 (图 2) 是此体系结构模式的示例:

![电子商务套件仅限 Azure 体系结构](media/oracle-oci-applications/ebs-arch-azure.png)

图 2：*电子商务套件仅限 Azure 体系结构*

以下部分介绍了较高级别的不同组件。

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-middle-tier"></a>应用程序 (中间) 层

应用层在其自己的子网中隔离。 为容错和轻松修补管理设置了多个虚拟机。 这些 Vm 可由 Azure NetApp 文件和超 Ssd 提供的共享存储提供支持。 此配置允许在不停机的情况下更轻松地部署修补程序。 应用程序层中的计算机应由公共负载均衡器前端, 以便即使该层中的一台计算机由于故障而脱机, 也会处理对 EBS 应用程序层的请求。

### <a name="load-balancer"></a>负载均衡器

使用 Azure 负载均衡器可以在工作负荷的多个实例之间分配流量, 以确保高可用性。 在这种情况下, 将设置公共负载均衡器, 因为允许用户通过 web 访问 EBS 应用程序。 负载均衡器将负载分配给中间层中的两台计算机。 为了增加安全性, 只允许用户使用站点到站点 VPN 或 ExpressRoute 和网络安全组从企业网络访问系统的流量。

### <a name="database-tier"></a>数据库层

此层承载 Oracle 数据库, 并被分隔到其自己的子网中。 建议将仅允许来自应用程序层的流量的网络安全组添加到特定于 Oracle 的数据库端口1521上的数据库层。

Microsoft 和 Oracle 建议高可用性设置。 Azure 中的高可用性可以通过 Oracle 数据防护在两个可用性区域中设置两个 Oracle 数据库来实现, 也可以在 OCI 中使用 Oracle Database Exadata 云服务。 使用 Oracle Database Exadata 云服务时, 数据库将部署在两个子网中。 还可以通过 Oracle Data Guard 在两个可用性域的 OCI 中的 Vm 中设置 Oracle Database。


### <a name="identity-tier"></a>标识层

标识层包含 EBS Asserter VM。 EBS Asserter 允许您从 Oracle 标识云服务 (IDCS) 和 Azure AD 同步标识。 EBS Asserter 是必需的, 因为 EBS 不支持单一登录协议 (如 SAML 2.0 或 OpenID Connect)。 EBS Asserter 使用 OpenID connect 令牌 (由 IDCS 生成), 对其进行验证, 然后为 EBS 中的用户创建一个会话。 

尽管此体系结构显示了 IDCS 集成, 但也可以使用 oracle Internet 目录或 Oracle 统一目录通过 Oracle Access Manager 启用 Azure AD 统一访问和单一登录。 有关详细信息, 请参阅[通过 IDCS 集成部署 ORACLE ebs](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_idcs.pdf)或[部署具有 Tld-oa-oam 集成的 oracle ebs](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_oam.pdf)白皮书。

为实现高可用性, 建议在多个可用性区域中部署 EBS Asserter 的冗余服务器, 并在其前面部署负载均衡器。

设置基础结构后, 可以按照 Oracle 提供的安装指南来安装电子商务套件。

## <a name="jd-edwards-enterpriseone"></a>JD Edwards EnterpriseOne

Oracle 的 JD Edwards EnterpriseOne 是综合性企业资源规划软件的集成应用套件。 它是一个多层应用程序, 可以使用 Oracle 或 SQL Server 数据库后端进行设置。 本部分介绍有关在 OCI 或 Azure 中使用 Oracle 数据库后端部署 JD Edwards EnterpriseOne 的详细信息。

在以下建议的体系结构 (图 3) 中, 管理层、表示层和中间层部署到 Azure 中的虚拟网络。 该数据库部署在 OCI 中的虚拟云网络中。

与电子商务套件一样, 你可以设置一个可选的堡垒层来实现安全的管理。 使用堡垒 VM 主机作为跳转服务器来访问应用程序和数据库实例。

![JD Edwards EnterpriseOne 跨云体系结构](media/oracle-oci-applications/jdedwards-arch-cross-cloud.png)

图 3：*JD Edwards EnterpriseOne 跨云体系结构*

在此体系结构中, Azure 中的虚拟网络使用跨云互连连接到 OCI 中的虚拟云网络。 应用程序层是在 Azure 中设置的, 而数据库是在 OCI 中设置的。 建议使用网络安全组将每个组件部署到其自己的子网, 以便仅允许来自特定端口上特定子网的流量。

此外, 还可以使用在区域的两个可用性区域中使用 Oracle 数据防护配置的高可用性 Oracle 数据库, 对 Azure 中的完全部署进行改编。 下图 (图 4) 是此体系结构模式的示例:

![仅限 Azure 的 JD Edwards 体系结构](media/oracle-oci-applications/jdedwards-arch-azure.png)

图 4：*仅限 Azure 的 JD Edwards 体系结构*

以下部分介绍了较高级别的不同组件。

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="administrative-tier"></a>管理层

顾名思义, 此层用于管理任务。 您可以划分管理层的单独子网。 此层中的服务和服务器主要用于安装和管理应用程序。 因此, 这些服务器的单个实例就够了。 应用程序的高可用性不需要冗余的实例。

此层的组件如下所示:
    
 - **设置服务器**-此服务器用于对应用程序的不同组件进行端到端部署。 它与其他层中的实例通信, 包括数据库层中的实例 (通过端口 22)。 它承载了用于 JD Edwards EnterpriseOne 的服务器管理器控制台。
 - **部署服务器**-此服务器主要需要安装 JD Edwards EnterpriseOne。 在安装过程中, 此服务器充当所需文件和安装包的中央存储库。 该软件会分发或部署到此服务器上的其他服务器和客户端。
 - **开发客户端**-此服务器包含在 web 浏览器和本机应用程序中运行的组件。

### <a name="presentation-tier"></a>呈现层

此层包含各种组件, 如应用程序接口服务 (AIS)、应用程序开发框架 (ADF) 和 Java 应用程序服务器 (JA)。 此层中的服务器与中间层中的服务器进行通信。 它们通过负载均衡器进行前端, 该负载均衡器根据接收流量的端口号和 URL 将流量路由到所需的服务器。 建议你部署每个服务器类型的多个实例以实现高可用性。

下面是此层中的组件:
    
- **应用程序接口服务 (AIS)** -AIS 服务器在 JD Edwards EnterpriseOne mobile 企业应用程序与 JD Edwards EnterpriseOne 之间提供通信接口。
- **Java 应用程序服务器 (ja)** -ja 接收来自负载均衡器的请求, 并将其传递给中间层以执行复杂的任务。 JA 可以执行简单的业务逻辑。
- **BI 发布服务器 (BIP)** -此服务器根据 JD Edwards EnterpriseOne 应用程序收集的数据提供报表。 您可以根据不同的模板来设计和控制报表显示数据的方式。
- **业务服务服务器 (bss)** -BSS 允许信息交换和与其他 Oracle 应用程序的互操作性。
- **实时事件服务器 (rte)** -RTE 服务器允许您设置有关 JDE EnterpriseOne 系统中发生的事务的外部系统通知。 它使用订阅者模型, 并允许第三方系统订阅事件。 若要对两个 RTE 服务器的请求进行负载均衡, 请确保服务器位于群集中。
- **应用程序开发框架 (adf) 服务器**-ADF 服务器用于运行使用 Oracle ADF 开发的 JD Edwards EnterpriseOne 应用程序。 这是使用 ADF 运行时部署在 Oracle WebLogic 服务器上的。

### <a name="middle-tier"></a>中间层

中间层包含逻辑服务器和批处理服务器。 在这种情况下, 这两台服务器都安装在同一虚拟机上。 但对于生产方案, 建议你在单独的服务器上部署逻辑服务器和批处理服务器。 在两个可用性区域的中间层中部署多个服务器, 以实现更高的可用性。 应创建 Azure 负载均衡器, 并且应将这些服务器置于后端池中, 以确保两台服务器都处于活动状态且正在处理请求。

中间层中的服务器仅接收来自表示层中的服务器和公共负载均衡器的请求。 网络安全组规则必须设置为拒绝来自表示层子网和负载均衡器之外的任何地址的流量。 还可以设置 NSG 规则, 以允许来自堡垒主机的端口22上的流量进行管理。 您可以使用公共负载均衡器对中间层中的 Vm 之间的请求进行负载均衡。

中间层中有以下两个组件:

- **逻辑服务器**-包含业务逻辑或业务职能。
- **批处理服务器**-用于批处理

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="peoplesoft"></a>PeopleSoft

Oracle 的 PeopleSoft 应用程序套件包含用于人力资源和财务管理的软件。 应用程序套件为多层和应用程序, 包括人力资源管理系统 (HRMS)、客户关系管理 (CRM)、财务和供应链管理 (FSCM) 以及企业性能管理 (EPM)。

建议将软件套件的每个层部署在其自己的子网中。 需要 Oracle 数据库或 Microsoft SQL Server 作为应用程序的后端数据库。 本部分介绍有关使用 Oracle 数据库后端部署 PeopleSoft 的详细信息。

下面是在跨云体系结构中部署 PeopleSoft 应用程序套件的规范体系结构 (图 5)。

![PeopleSoft 跨云体系结构](media/oracle-oci-applications/peoplesoft-arch-cross-cloud.png)

图 5：*PeopleSoft 跨云体系结构*

在此示例结构中, Azure 中的虚拟网络使用跨云互连连接到 OCI 中的虚拟云网络。 应用程序层是在 Azure 中设置的, 而数据库是在 OCI 中设置的。 建议使用网络安全组将每个组件部署到其自己的子网, 以便仅允许来自特定端口上特定子网的流量。

此外, 还可以使用在区域的两个可用性区域中使用 Oracle 数据防护配置的高可用性 Oracle 数据库, 对 Azure 中的完全部署进行改编。 下图 (图 6) 是此体系结构模式的示例:

![PeopleSoft 仅限 Azure 体系结构](media/oracle-oci-applications/peoplesoft-arch-azure.png)

图 6：*PeopleSoft 仅限 Azure 体系结构*

以下部分介绍了较高级别的不同组件。

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-tier"></a>应用层

应用层包含 PeopleSoft 应用程序服务器的实例、PeopleSoft web 服务器、弹性搜索和 PeopleSoft 进程计划程序。 Azure 负载均衡器已设置为接受来自用户的请求, 这些请求路由到应用程序层中的相应服务器。

为实现高可用性, 请考虑在不同的可用性区域中为应用程序层中的每个服务器设置冗余实例。 可以通过多个后端池设置 Azure 负载均衡器, 以将每个请求定向到正确的服务器。

### <a name="peopletools-client"></a>PeopleTools 客户端

PeopleTools 客户端用于执行管理活动, 如开发、迁移和升级。 由于 PeopleTools 客户端不是实现应用程序的高可用性所必需的, 因此不需要 PeopleTools 客户端的冗余服务器。

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="next-steps"></a>后续步骤

使用[Terraform 脚本](https://github.com/microsoft/azure-oracle)在 Azure 中设置 Oracle 应用, 并建立与 OCI 的跨云连接。

有关 OCI 的详细信息和白皮书, 请参阅[Oracle 云](https://docs.cloud.oracle.com/iaas/Content/home.htm)文档。
