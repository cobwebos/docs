---
title: 在 Azure 虚拟机上部署 Oracle 应用的体系结构 |微软文档
description: 在具有 Azure 或 Oracle 云基础架构 （OCI） 中的数据库的 Microsoft Azure 虚拟机上部署 Oracle 应用程序的应用程序体系结构，包括电子商务套件、JD Edwards EnterpriseOne 和 PeopleSoft。
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
tags: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/18/2019
ms.author: mimckitt
ms.custom: ''
ms.openlocfilehash: 20e751b322d06ac176ee5c634d92e0efe874baac
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263295"
---
# <a name="architectures-to-deploy-oracle-applications-on-azure"></a>在 Azure 上部署 Oracle 应用程序的体系结构

Microsoft 和 Oracle 合作，使客户能够在云中部署 Oracle 应用程序，如 Oracle 电子商务套件、JD Edwards EnterpriseOne 和 PeopleSoft。 随着 Microsoft Azure 和 Oracle 云基础结构 （OCI） 之间引入预览[专用网络连接](configure-azure-oci-networking.md)，Oracle 应用程序现在可以将其后端数据库部署在 Azure 或 OCI 中。 Oracle 应用程序也可以与 Azure 活动目录集成，允许您设置单个登录，以便用户可以使用 Azure 活动目录 （Azure AD） 凭据登录到 Oracle 应用程序。

OCI 为 Oracle 应用程序提供了多个 Oracle 数据库选项，包括 DBaaS、Exadata 云服务、Oracle RAC 和基础架构即服务 （IaaS）。 目前，自治数据库不是 Oracle 应用程序支持的后端。

在 Azure 中部署 Oracle 应用程序[有多种选项](oracle-overview.md)，包括以高度可用和安全的方式部署。 Azure 还提供[Oracle 数据库 VM 映像](oracle-vm-solutions.md)，如果选择完全在 Azure 上运行 Oracle 应用程序，则可以进行部署。

以下各节概述了 Microsoft 和 Oracle 在跨云配置或完全在 Azure 中部署 Oracle 电子商务套件、JD Edwards EnterpriseOne 和 PeopleSoft 的体系结构建议。 微软和甲骨文已经测试了这些应用程序，并确认性能符合 Oracle 为这些应用程序设定的标准。

## <a name="architecture-considerations"></a>体系结构注意事项

Oracle 应用程序由多个服务组成，这些服务可以托管在 Azure 中的同一虚拟机或多个虚拟机上，也可以在 OCI 中选择托管。 

可以使用私有终结点或公共终结点设置应用程序实例。 Microsoft 和 Oracle 建议在单独的子网中设置具有公共 IP 地址的*堡垒主机 VM，* 以便管理应用程序。 然后，仅将专用 IP 地址分配给其他计算机，包括数据库层。 

在跨云体系结构中设置应用程序时，需要进行规划，以确保 Azure 虚拟网络中的 IP 地址空间不会与 OCI 虚拟云网络中的专用 IP 地址空间重叠。

为了增加安全性，在子网级别设置网络安全组，以确保只允许特定端口和 IP 地址上的流量。 例如，中间层中的计算机应仅从虚拟网络内接收流量。 任何外部流量都不应直接到达中间层计算机。

为获得高可用性，您可以在同一可用性集中或不同的可用性区域中设置不同服务器的冗余实例。 可用性区域允许您实现 99.99% 的 SLA 上用时间，而可用性集允许您在区域内实现 99.95% 的 SLA 上级。 本文中显示的示例体系结构分布在两个可用性区域。

使用跨云互连部署应用程序时，可以继续使用现有的 ExpressRoute 电路将 Azure 环境连接到本地网络。 但是，与连接到本地网络的互连电路相比，您需要单独的 ExpressRoute 电路才能连接到 OCI。

## <a name="e-business-suite"></a>电子商务套件

Oracle 电子商务套件 （EBS） 是一套应用程序，包括供应链管理 （SCM） 和客户关系管理 （CRM）。 为了利用 OCI 的托管数据库组合，可以使用 Microsoft Azure 和 OCI 之间的跨云互连来部署 EBS。 在此配置中，表示层和应用程序层在 Azure 中运行，在 OCI 中的数据库层运行，如下体系结构图所示（图 1）。

![电子商务套件跨云架构](media/oracle-oci-applications/ebs-arch-cross-cloud.png)

*图 1：电子商务套件跨云架构* 

在此体系结构中，Azure 中的虚拟网络使用跨云互连连接到 OCI 中的虚拟云网络。 应用程序层在 Azure 中设置，而数据库在 OCI 中设置。 建议将每个组件部署到具有网络安全组的自己的子网，以便仅允许来自特定端口上的特定子网的流量。

该体系结构还可以完全适应 Azure 的部署，并在区域的两个可用性区域使用 Oracle 数据防护配置了高可用性的 Oracle 数据库。 下图（图 2）是此体系结构模式的示例：

![电子商务套件 Azure 专用体系结构](media/oracle-oci-applications/ebs-arch-azure.png)

*图 2：仅 Azure 电子商务套件体系结构*

以下各节在高级别上描述不同的组件。

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-middle-tier"></a>应用程序（中间）层

应用程序层在其自己的子网中隔离。 为容错和轻松的修补程序管理设置了多个虚拟机。 这些 VM 可以由共享存储进行备份，该存储由 Azure NetApp 文件和超 SSD 提供。 此配置允许在不停机的情况下更轻松地部署修补程序。 应用程序层中的计算机应由公共负载均衡器对前面，以便处理对 EBS 应用程序层的请求，即使层中的一台计算机由于故障而脱机也是如此。

### <a name="load-balancer"></a>负载均衡器

Azure 负载均衡器允许您跨工作负荷的多个实例分配流量，以确保高可用性。 在这种情况下，将设置公共负载均衡器，因为允许用户通过网络访问 EBS 应用程序。 负载均衡器将负载分配到中间层中的两台计算机。 为增加安全性，只允许使用站点到站点 VPN 或 ExpressRoute 和网络安全组从公司网络访问系统的用户进行流量访问。

### <a name="database-tier"></a>数据库层

此层承载 Oracle 数据库，并分隔到其自己的子网中。 建议添加仅允许从应用程序层到特定于 Oracle 的数据库端口 1521 上的数据库层的流量的网络安全组。

微软和甲骨文建议采用高可用性设置。 通过使用 Oracle 数据防护在两个可用性区域中设置两个 Oracle 数据库，或者在 OCI 中使用 Oracle 数据库 Exadata 云服务，可以实现 Azure 中的高可用性。 使用 Oracle 数据库 Exadata 云服务时，数据库将部署在两个子网中。 您还可以使用 Oracle 数据保护在两个可用性域中的 OCI 中的 VM 中设置 Oracle 数据库。


### <a name="identity-tier"></a>标识层

标识层包含 EBS 断言器 VM。 EBS 断言器允许您同步来自 Oracle 标识云服务 （IDCS） 和 Azure AD 的标识。 EBS 断言器是必需的，因为 EBS 不支持单一登录协议，如 SAML 2.0 或 OpenID 连接。 EBS 断言器使用 OpenID 连接令牌（由 IDCS 生成），对其进行验证，然后在 EBS 中为用户创建会话。 

虽然此体系结构显示 IDCS 集成，但 Azure AD 统一访问和单一登录也可以通过 Oracle 访问管理器与 Oracle Internet 目录或 Oracle 统一目录启用。 有关详细信息，请参阅有关[使用 IDCS 集成部署 Oracle EBS](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_idcs.pdf)或[使用 OAM 集成部署 Oracle EBS](https://cloud.oracle.com/iaas/whitepapers/deploy_ebusiness_suite_across_oci_azure_sso_oam.pdf)的白皮书。

为获得高可用性，建议您在多个可用性区域部署 EBS Asserter 的冗余服务器，前面有负载均衡器。

设置基础结构后，可以使用 Oracle 提供的安装指南安装电子商务套件。

## <a name="jd-edwards-enterpriseone"></a>JD Edwards EnterpriseOne

Oracle 的 JD Edwards 企业一号是一套综合性企业资源规划软件的集成应用程序套件。 它是一个多层应用程序，可以使用 Oracle 或 SQL Server 数据库后端进行设置。 本节讨论有关在 OCI 或 Azure 中使用 Oracle 数据库后端部署 JD Edwards 企业One 的详细信息。

在以下建议的体系结构（图 3）中，管理层、演示文稿层和中间层部署到 Azure 中的虚拟网络。 数据库部署在 OCI 的虚拟云网络中。

与电子商务套件一样，您可以为安全管理目的设置可选的堡垒层。 使用堡垒 VM 主机作为跳转服务器访问应用程序和数据库实例。

![JD Edwards 企业一个跨云架构](media/oracle-oci-applications/jdedwards-arch-cross-cloud.png)

*图 3：JD Edwards 企业一个跨云架构*

在此体系结构中，Azure 中的虚拟网络使用跨云互连连接到 OCI 中的虚拟云网络。 应用程序层在 Azure 中设置，而数据库在 OCI 中设置。 建议将每个组件部署到具有网络安全组的自己的子网，以便仅允许来自特定端口上的特定子网的流量。

该体系结构还可以完全适应 Azure 的部署，并在区域的两个可用性区域使用 Oracle 数据防护配置了高可用性的 Oracle 数据库。 下图（图 4）是此体系结构模式的示例：

![JD Edwards 企业一个 Azure 体系结构](media/oracle-oci-applications/jdedwards-arch-azure.png)

*图 4：JD Edwards 企业一个 Azure 专用体系结构*

以下各节在高级别上描述不同的组件。

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="administrative-tier"></a>管理层

顾名思义，此层用于管理任务。 您可以为管理层划分单独的子网。 此层中的服务和服务器主要用于应用程序的安装和管理。 因此，这些服务器的单实例就足够了。 应用程序的高可用性不需要冗余实例。

此层的组件如下所示：
    
 - **预配服务器**- 此服务器用于应用程序的不同组件的端到端部署。 它通过端口 22 与其他层中的实例（包括数据库层中的实例）进行通信。 它承载 JD Edwards 企业一 体的服务器管理器控制台。
 - **部署服务器**- 安装 JD Edwards 企业一台时，主要需要此服务器。 在安装过程中，此服务器充当所需文件和安装包的中央存储库。 该软件从此服务器分发或部署到其他服务器和客户端。
 - **开发客户端**- 此服务器包含在 Web 浏览器中运行的组件以及本机应用程序。

### <a name="presentation-tier"></a>表示层

此层包含各种组件，如应用程序接口服务 （AIS）、应用程序开发框架 （ADF） 和 Java 应用程序服务器 （JAS）。 此层中的服务器与中间层中的服务器通信。 它们由负载均衡器正面显示，该负载均衡器根据接收流量的端口号和 URL 将流量路由到必要的服务器。 建议您部署每种服务器类型的多个实例，以便获得高可用性。

以下是此层中的组件：
    
- **应用程序接口服务 （AIS）** - AIS 服务器提供 JD Edwards 企业一号移动企业应用程序和 JD Edwards 企业一号之间的通信接口。
- **Java 应用程序服务器 （JAS）** - JAS 接收来自负载均衡器的请求，并将其传递到中间层以执行复杂的任务。 JAS 能够执行简单的业务逻辑。
- **BI 发布服务器 （BIP）** - 此服务器基于 JD Edwards 企业一号应用程序收集的数据显示报告。 您可以设计和控制报表如何根据不同的模板显示数据。
- **业务服务服务器 （BSS）** - BSS 支持与其他 Oracle 应用程序的信息交换和互操作性。
- **实时事件服务器 （RTE）** - RTE 服务器允许您设置有关 JDE 企业一号系统中发生的事务的通知。 它使用订户模型并允许第三方系统订阅事件。 要加载对两个 RTE 服务器的平衡请求，请确保服务器位于群集中。
- **应用程序开发框架 （ADF） 服务器**- ADF 服务器用于运行使用 Oracle ADF 开发的 JD Edwards 企业一号应用程序。 这部署在具有 ADF 运行时的 Oracle WebLogic 服务器上。

### <a name="middle-tier"></a>中间层

中间层包含逻辑服务器和批处理服务器。 在这种情况下，两台服务器都安装在同一虚拟机上。 但是，对于生产方案，建议您将逻辑服务器和批处理服务器部署在单独的服务器上。 多个服务器部署在中间层，跨越两个可用性区域，以更高的可用性。 应创建 Azure 负载均衡器，并将这些服务器放在其后端池中，以确保两个服务器都处于活动状态并处理请求。

中间层中的服务器仅接收来自表示层中的服务器和公共负载均衡器的请求。 必须设置网络安全组规则，以拒绝来自演示文稿层子网和负载均衡器以外的任何地址的流量。 还可以设置 NSG 规则，以允许端口 22 从堡垒主机进行流量以用于管理目的。 您可以使用公共负载平衡器在中间层中的 VM 之间加载平衡请求。

以下两个组件位于中间层中：

- **逻辑服务器**- 包含业务逻辑或业务功能。
- **批处理服务器**- 用于批处理

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="peoplesoft"></a>PeopleSoft

Oracle 的 PeopleSoft 应用程序套件包含用于人力资源和财务管理的软件。 应用程序套件是多层次的，应用程序包括人力资源管理系统 （HRMS）、客户关系管理 （CRM）、财务和供应链管理 （FSCM） 以及企业绩效管理 （EPM）。

建议将软件套件的每一层部署在其自己的子网中。 Oracle 数据库或 Microsoft SQL Server 需要作为应用程序的后端数据库。 本节讨论有关使用 Oracle 数据库后端部署 PeopleSoft 的详细信息。

以下是用于在跨云体系结构中部署 PeopleSoft 应用程序套件的规范体系结构（图 5）。

![人软跨云架构](media/oracle-oci-applications/peoplesoft-arch-cross-cloud.png)

*图 5：人软跨云架构*

在此示例体系结构中，Azure 中的虚拟网络使用跨云互连连接到 OCI 中的虚拟云网络。 应用程序层在 Azure 中设置，而数据库在 OCI 中设置。 建议将每个组件部署到具有网络安全组的自己的子网，以便仅允许来自特定端口上的特定子网的流量。

该体系结构还可以完全适应 Azure 的部署，并在区域的两个可用性区域使用 Oracle 数据防护配置了高可用性的 Oracle 数据库。 下图（图 6）是此体系结构模式的示例：

![人软件 Azure 体系结构](media/oracle-oci-applications/peoplesoft-arch-azure.png)

*图 6：用户软件 Azure 体系结构*

以下各节在高级别上描述不同的组件。

[!INCLUDE [virtual-machines-oracle-applications-bastion](../../../../includes/virtual-machines-oracle-applications-bastion.md)]

### <a name="application-tier"></a>应用层

应用程序层包含 PeopleSoft 应用程序服务器、PeopleSoft Web 服务器、弹性搜索和 PeopleSoft 进程调度程序的实例。 Azure 负载均衡器设置为接受来自用户的请求，这些请求被路由到应用程序层中的相应服务器。

为获得高可用性，请考虑在不同的可用性区域设置应用程序层中每个服务器的冗余实例。 Azure 负载均衡器可以使用多个后端池进行设置，以将每个请求定向到正确的服务器。

### <a name="peopletools-client"></a>人工具客户端

PeopleTools 客户端用于执行管理活动，如开发、迁移和升级。 由于实现应用程序的高可用性不需要 PeopleTools 客户端，因此不需要 PeopleTools 客户端的冗余服务器。

[!INCLUDE [virtual-machines-oracle-applications-database](../../../../includes/virtual-machines-oracle-applications-database.md)]

[!INCLUDE [virtual-machines-oracle-applications-identity](../../../../includes/virtual-machines-oracle-applications-identity.md)]

## <a name="next-steps"></a>后续步骤

使用[Terraform 脚本](https://github.com/microsoft/azure-oracle)在 Azure 中设置 Oracle 应用，并与 OCI 建立跨云连接。

有关 OCI 的详细信息和白皮书，请参阅[Oracle 云](https://docs.cloud.oracle.com/iaas/Content/home.htm)文档。
