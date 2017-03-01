---
title: "Azure 政府版合规性| Microsoft Docs"
description: "提供 Azure Government 可用合规性服务的概述"
services: azure-government
cloud: gov
documentationcenter: 
author: jomolesk
manager: zakramer
ms.assetid: 1d2e0938-482f-4f43-bdf6-0a5da2e9a185
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/07/2016
ms.author: jomolesk
translationtype: Human Translation
ms.sourcegitcommit: 474ee83dfc8532f8410b79c6426af3cad6f3d320
ms.openlocfilehash: cd54456c8f9e0f37f53973fca80b8bd3514b62d1


---
# <a name="azure-government-compliance"></a>Azure Government 合规性

## <a name="azure-blueprint"></a>Azure Blueprint

Azure Blueprint 计划旨在为代表政府建立的政府部门和第三方提供商安全、合规地使用 Azure 提供便利。 Azure Government 客户可以利用 Azure Government 的 FedRAMP JAB 临时授权操作 (P-ATO) 或 DoD 临时授权 (PA)，在基于 Azure 的系统中减小客户责任安全控制的范围。 从 Azure Government 继承安全控制实现可使客户专注于特定于 Azure 内置 IaaS、PaaS 或 SaaS 环境的控制实现。

> [!NOTE]
> 在 Azure Blueprint 上下文中，“客户”指代在 Azure 内直接生成的组织。 Azure 客户可包括代表政府生成的第三方 ISV 或在 Azure 中直接生成的政府部门。

## <a name="blueprint-customer-responsibilities-matrix"></a>Blueprint Customer Responsibilities Matrix

Azure Blueprint Customer Responsibilities Matrix (CRM) 旨在帮助 Azure Government 客户实现并记录在 Azure 中实现的系统特定安全控制。 CRM 显式列出包括客户实现要求的 FedRAMP 和 DISA 基线的所有 [NIST SP 800-53](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-53r4.pdf) 安全控制要求。 这包括在 Azure 和 Azure 客户之间具有共享职责的控制，以及必须由 Azure 客户完全实现的控制。 在适当情况下，将控制描述为控制子要求粒度，以提供更具体的指南。

CRM 格式的设计在于实用，有益于实现的安全控制中仅客户部分的已设定焦点的文档。

例如，控制 AC-1 要求记录系统请求 ATO 的访问控制策略和流程。 对于此控制，Microsoft 具有关于用于管理 Azure 基础结构和平台的访问控制机制的内部 Azure 特定策略和流程。 客户还必须创建其自身的访问控制策略和流程，以在 Azure 中创建的特定系统中使用。 CRM 文档控制 AC-1a 部分（该部分要求策略和流程中包括特定内容）和 AC-1b 部分（该部分要求客户每年查看和更新这些文档）。

Blueprint CRM 可用作 FedRAMP 中等和高基线、DISA 云计算 SRG L4 和 L5 基线的 Microsoft Excel 工作簿。

若要请求 Azure Blueprint CRM 的副本或提供反馈，请发送电子邮件到 [AzureBlueprint@microsoft.com](mailto:AzureBlueprint@microsoft.com)。

## <a name="blueprint-system-security-plan"></a>Blueprint 系统安全计划

Azure Blueprint 系统安全计划 (SSP) 模板以客户为中心，旨在用于开发 SSP，以记录客户安全控制实现和从 Azure 继承的控制。 包括客户职责的控制包含有关通过全面且合规的响应记录控制实现的指南。 Azure 继承部分介绍 Azure 代表客户实现安全控制的方法。

Azure Blueprint SSP 可用于 FedRAMP 中等和高基线、DISA 云计算 SRG L4 和 L5 基线。 

若要请求 Azure Blueprint SSP 的副本或提供反馈，请发送电子邮件到 [AzureBlueprint@microsoft.com](mailto:AzureBlueprint@microsoft.com)。

## <a name="azure-blueprint-implementation-guidance"></a>Azure Blueprint 实现指南

Azure Blueprint 实现指南旨在帮助云解决方案架构师和安全管理人员了解如何部署 Azure Government 服务和功能以实现部分客户职责 FedRAMP 和 DoD 安全控制。 文档、工具、模板和其他资源的数组可以指导 Azure 服务和功能的安全部署。 可以使用 Azure Resource Manager 模板[构建基块](https://github.com/mspnp/template-building-blocks)、社区提供的 Azure [快速启动模板](https://azure.microsoft.com/resources/templates/)，或通过使用[客户创作的](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)基于 JSON 的 Resource Manager 模板部署 Azure 资源。 Azure 中基本部署的体系结构包括计算、网络和存储资源。 此实现指南可解决采用满足安全控制实现要求的方式部署这些资源的问题。

### <a name="virtual-machines"></a>虚拟机

Azure Resource Manager 模板可用于部署满足美国政府和行业安全技术实现指南 (STIG) 和安全基准的预配置虚拟机。 可使用现有的预配置计算机创建自定义 Azure 虚拟机，或使用 Active Directory 为已加入域的计算机部署新的虚拟机和应用安全策略，或为独立计算机部署本地组策略对象实用工具。 Azure 虚拟机自定义脚本扩展可用于管理这些部署后配置任务。

Windows 虚拟机配置可能包括以下内容的安全控制实现：

- 系统使用通知和确认 [NIST SP 800-53 控制 AC-8]

  > AC-8 示例控制实现语句：*配置控制的组策略对象 (GPO) 应用于 Azure 中所有客户控制的 Windows 计算机。依据 Windows Server 2012 R2 CIS 基准配置组策略对象，并自定义该对象以满足组织要求，包括使用相应的系统使用通知、哪些用户必须在登录前进行确认（请参阅CIS 基准部分 2.3.7.4、2.3.7.5）。*

- 本地计算机帐户限制包括帐户锁定 [NIST SP 800-53 控制 AC-7]、并发会话控制 [NIST SP 800-53 控制 AC-10]、会话锁定 [NIST SP-800-53 控制 AC-11]、验证器管理 [NIST SP 800-53 控制 IA-5] 和其他限制

  > AC-7 示例控制实现语句：*配置控制的组策略对象 (GPO) 应用于 Azure 中所有客户控制的 Windows 计算机。依据 Windows Server 2012 R2 CIS 基准配置组策略对象，并自定义该对象以满足组织要求，包括连续登录尝试失败后启动帐户锁定（请参阅CIS 基准部分 1.2.1、1.2.2、1.2.3；注意：必须更改 CIS 基准默认值才能满足 FedRAMP 和 DoD 参数要求）。*

  > AC-10 示例控制实现语句：*配置控制的组策略对象 (GPO) 应用于 Azure 中所有客户控制的 Windows 计算机。依据 Windows Server 2012 R2 CIS 基准配置组策略对象，并自定义该对象以满足组织要求，包括将用户限制到满足 FedRAMP 和 DoD 要求的单个远程桌面服务会话。（请参阅CIS 基准部分 18.9.48.3.2）。*

  > AC-11 示例控制实现语句：*配置控制的组策略对象 (GPO) 应用于 Azure 中所有客户控制的 Windows 计算机。依据 Windows Server 2012 R2 CIS 基准配置组策略对象，并自定义该对象以满足组织要求，包括保留 900 秒（15 分钟）非活动状态直至用户重新进行身份验证后启动会话锁定（请参阅CIS 基准部分 2.3.7.3、19.1.3.1、19.1.3.3）。*

  > IA-5 示例控制实现语句：*配置控制的组策略对象 (GPO) 应用于 Azure 中所有客户控制的 Windows 计算机。依据 Windows Server 2012 R2 CIS 基准配置组策略对象，并自定义该对象以满足组织要求，包括对本地帐户密码的限制，从而强制执行满足 FedRAMP 要求的最短（1 天）和最长（60 天）生存期限制、重复使用条件（24 个密码）、长度（14 个字符）和复杂性要求（请参阅CIS 基准部分 1.1.1、1.1.2、1.1.3、1.1.4、1.1.5；注意：必须更改 CIS 基准默认值以满足 DoD 参数要求）。*

- 配置设置，包括最低功能 [NIST SP 800-53 控制 CM-6，CM-7]

  > CM-6 示例控制实现语句：*为 Azure 中所有客户控制的 Windows 计算机保持配置控制的组策略对象 (GPO)。依据 Windows Server 2012 R2 CIS 基准配置组策略对象，并自定义该对象以满足特定于系统操作需求的组织要求。除了其他配置资源，还建立了此组策略对象，以记录并确保配置设置的一致实现。*

  > CM-7 示例控制实现语句：*为 Azure 中所有客户控制的 Windows 计算机保持配置控制的组策略对象 (GPO)。依据 Windows Server 2012 R2 CIS 基准配置组策略对象，并自定义该对象以满足特定于系统操作需求的组织要求。此组策略对象用于建立为 Azure 中所有 Windows 计算机保持的基线虚拟机映像，该映像反映 Windows 计算机的配置，以仅提供系统操作的必需功能。*

  安全基线修正工具包括 Windows 计算机的预配置组策略对象 (GPO) 和 Linux 计算机的 shell 脚本，对于 Windows 的计算机，可从 [Microsoft](https://technet.microsoft.com/itpro/windows/keep-secure/windows-security-baselines) 获取，对于 Windows 和 Linux 计算机，可从 [Internet 安全中心](https://benchmarks.cisecurity.org/)和[防御信息系统代理 (DISA)](http://iase.disa.mil/stigs) 获取。  

可对 Azure 虚拟机进行加密，以满足安全控制要求，从而保护静态信息 [NIST SP 800-53 控制 SC 28]。 可使用 Azure 快速入门模板向[新的](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)和现有 [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm) 和 [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm) 虚拟机部署加密。 

> SC-28 示例控制实现语句：*Azure 中客户控制的虚拟机可实现磁盘加密，保护静态信息的保密性和完整性。 使用 Windows 的 BitLocker 功能实现 Windows 的 Azure 磁盘加密；使用 Linux 的 DM-Crypt 功能实现 Linux 虚拟机的磁盘加密。

可以部署多个 Azure 虚拟机扩展，以满足安全控制要求。 可将 [Microsoft 反恶意软件虚拟机扩展](https://docs.microsoft.com/azure/security/azure-security-antimalware)部署到新的和现有虚拟机。 反恶意软件扩展可以启用实时保护和定期的计划扫描 [NIST SP 800-53 控制 SI-3]。

> SI-3 示例控制实现语句：*使用 Microsoft 反恶意软件虚拟机扩展实现对 Azure 中客户控制的 Windows 虚拟机的基于主机的反恶意软件保护。配置此扩展，以执行实时和定期扫描（每周）、自动更新反恶意软件引擎和保护签名、执行自动修正操作和通过 Azure 诊断提供通知。*

其他工具和资源

可进行自定义和用于部署 Azure 资源的 Azure Resource Manager 模板[构建基块](https://github.com/mspnp/template-building-blocks)，包括[虚拟机](https://github.com/mspnp/template-building-blocks/tree/master/templates/buildingBlocks/multi-vm-n-nic-m-storage)部署和[虚拟机扩展](https://github.com/mspnp/template-building-blocks/tree/master/templates/buildingBlocks/virtualMachine-extensions)。 

### <a name="virtual-network"></a>虚拟网络

Azure 虚拟网络 (VNet) 允许完全控制安全策略，以及通过部署和配置子网、网络安全组和用户定义的路由在虚拟机体系结构内进行路由。 可基于多层体系结构按工作负荷有逻辑地分离资源，从而将网络安全组应用于子网或单独的计算机，以便用于任何其他用途。 在以下参考体系结构中，资源在 web、业务和数据层的单独子网和 Active Directory 资源和管理的子网中进行分组。 将网络安全组应用于每个子网，以限制虚拟网络中的网络流量。 
 
![使用 Microsoft Azure 的 N 层体系结构](./media/documentation-government-plan-compliance/compute-n-tier.png)

网络安全组启用对资源间通信路径的完全控制 [NIST SP 800-53 控制 AC-4]。

> AC-4 示例控制实现语句：*Azure 中实现 web、业务和数据层功能的客户控制虚拟机由子网隔离。定义网络安全组并将其应用于每个子网，以便将网络层信息流限制为发挥信息系统功能的最低需求。*

可从子网和虚拟机将网络安全组应用到传出通信。 如此，可完全控制 Azure 和外部信息系统中信息系统组件之间的通信 [NIST SP 800-53 控制 CA-3 (5)]。 网络安全组规则处理作为 deny-all、permit-by-exception 函数实现。 此外，可配置用户定义的路由，以通过虚拟设备（例如防火墙或 IDS/IPS）从特定子网和虚拟机路由传入和传出通信，从而进一步管理系统通信。

> CA-3 (5) 示例控制实现语句：*通过网络安全组实现限制来自 Azure 中客户控制资源的所有出站通信，这些通信通过出站规则集（拒绝除明确允许的流量外的所有流量，以支持满足 FedRAMP 和 DoD L4 要求的信息系统功能）进行配置。此外，配置用户定义的路由，以通过虚拟防火墙设备（通过仅允许与批准的外部信息系统通信的规则集进行配置）路由所有出站通信。*

以上参考体系结构演示如何将 Azure 资源逻辑分组到应用了网络安全组规则集的单独子网，确保安全函数和非安全函数相隔离。 在这种情况下，3 个 web 应用程序层独立于 Active Directory 子网和管理子网（可以托管信息系统和安全管理工具及资源）[NIST SP 800-53 控制 SC-3]。

> SC-3 示例控制实现语句：*在客户控制的 Azure 环境中，通过实现子网和应用到这些子网的网络安全组，隔离安全函数与非安全函数。专用于 web 应用程序的 web、业务和数据层的信息系统资源在逻辑上与管理子网（在其中执行与信息系统安全相关的任务）分离。*

参考体系结构还实现用于远程访问信息系统的托管访问控制点 [NIST SP 800-53 控制 AC-17 (3)]。 部署面向 Internet 的负载均衡器，以便将传入 Internet 流量分发给 web 应用程序，管理子网包括 jumpbox 或堡垒主机，可通过其控制对系统的所有与管理相关的远程访问。 网络安全组限制虚拟网络内的流量，确保外部流量仅路由到指定的面向公众的资源。

> AC-17 (3) 示例控制实现语句：*在 Azure 中，对信息系统客户控制的组件的远程访问限制为 2 个托管的网络访问控制点。1) 通过面向 Internet 的负载均衡器（将流量分发到 web 层资源）管理为 web 应用程序指定的 Internet 流量。2) 通过环境中隔离子网上的堡垒主机管理管理远程访问。应用于管理子网（其中存在 jumpbox）的网络安全组允许仅从列入白名单的公共 IP 地址进行连接，并仅限远程桌面流量。*

网络安全组允许完全控制 Azure 资源与外部主机和系统之间的通信，以及内部子网和主机之间的通信，以分离指定可公开访问和未指定的信息系统组件。 除了上述参考体系结构中的解决方案外，Azure 还启用了虚拟设备部署，例如防火墙和 IDS/IPS 解决方案，将其与用户定义的路由结合使用时，可进一步保护和管理 Azure 资源与外部网络和信息系统之间的连接 [NIST SP 800-53 控制 SC-7]。

> SC-7 示例控制实现语句：*在 Azure 中，通过多个部署的边界保护机制对客户控制的资源进行保护。定义网络安全组并将其应用于网络子网，以限制信息系统边界和虚拟网络中的流量。网络安全组包括控制子网和虚拟机进出流量的访问控制列表 (ACL) 规则。部署负载均衡器，以便将传入 Internet 流量分发到特定资源。子网确保可公开访问的信息系统组件在逻辑上与非公共资源相分离。*

网络安全组规则集启用对特定网络端口和协议的限制，这是确保以仅提供基本功能的方式实现信息系统的关键组成部分 [NIST SP 800-53 控制 CM-7]。

> CM-7 示例控制实现语句：*使用 deny-all、permit-by-exception 方法实现应用于客户控制的 Azure 环境中的子网和虚拟机的网络安全组。这可确保仅允许通过明确批准的端口和协议的网络流量，支持最低功能的概念。*

其他工具和资源

Azure [文档站点](https://docs.microsoft.com/azure/guidance/guidance-compute-n-tier-vm)上提供了以上参考体系结构文档。 同一页面包含部署参考体系结构的 Azure Resource Manager 模板。 Azure 文档站点还包含有关[网络安全组](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)和[用户定义的路由](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview)的详细信息。

### <a name="storage"></a>存储

复制 Azure 存储中存储的数据，确保高可用性。 尽管有多个复制选项，但异地冗余存储 (GRS) 和读取访问异地冗余存储 (RA-GRS) 可确保将数据复制到次要区域。 主要和次要区域配对可确保数据中心之间的必要距离，确保发生区域范围故障或灾难时的可用性 [NIST SP 800-53 控制 CP-9]。 对于异地冗余、高可用性存储，创建新的存储帐户时选择异地冗余存储 (GRS) 或读取访问异地冗余存储 (RA-GRS)。 

> CP-9 示例控制实现语句：*Azure 内所有客户控制的存储帐户都实现异地冗余存储，确保跨&2; 个数据中心在单独节点上维护所有数据的&6; 个副本。*

Azure 存储服务加密 (SSE) 可保护 Azure 存储帐户中的静态数据 [NIST SP 800-53 控制 SC-28，SC-28 (1)]。 启用时，Azure 会在将数据保存到存储之前自动加密数据。 使用 256 位 AES 加密对数据进行加密。 SSE 支持加密块 blob、追加 blob 和页 blob。

> SC-28，SC-28 (1) 示例控制实现语句：*通过使用 Azure SSE（使用 256 位 AES 加密对所有静态数据进行加密）保护客户控制的 Azure 环境中所有存储 blob 的机密性和完整性。*

其他工具和资源

Azure 文档站点提供有关[存储服务加密](https://docs.microsoft.com/azure/storage/storage-service-encryption)和[存储复制](https://docs.microsoft.com/azure/storage/storage-redundancy)的详细信息。 

### <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory 为在 Microsoft Azure 中运行的信息系统提供标识和访问功能。 通过使用目录服务、安全组和组策略，客户可以帮助控制使用 Azure Active Directory 的计算机的访问和安全策略。 帐户和安全组可用于帮助管理对信息系统的访问。 组策略可帮助确保满足配置要求。

Azure Active Directory 安全组和目录服务可帮助实现基于角色的访问控制 (RBAC) 访问控制方案，以及控制对信息系统的访问。 这可能包括以下内容的安全控制实现：

- 帐户管理 [NIST SP 800-53 控制 AC-2]

  > AC-2.a 示例控制实现语句：*通过使用 Active Directory 组实现基于角色的访问控制将 Azure Active Directory 用于管理访问。已对帐户管理和访问 AAD 所支持域上的帐户建立要求。仅安全组支持对域的成员服务器的访问。每一组都有第一和第二所有者。这些所有者负责维护组成员身份、权限和说明的准确性。*

  > AC-2.c 示例控制实现语句：*用户请求访问任何安全组时，必须由该组所有者基于为成员身份定义的条件批准该请求。帐户条件由安全组所有者确定。*

  > AC-2.f 示例控制实现语句：*Azure Active Directory 用于管理对信息系统的控制访问。根据帐户管理策略，帐户管理员可按要求创建、启用、修改、禁用或删除信息系统帐户。*

  > AC-2 (1) 示例控制实现语句：*通过使用 Active Directory 组实现基于角色的访问控制将 Azure Active Directory 用于管理访问。已对帐户管理和访问 AAD 所支持域上的帐户建立要求。仅安全组支持对域的成员服务器的访问。每一组都有第一和第二所有者。这些所有者负责维护组成员身份、权限和说明的准确性。*

  > AC-2 (2) 示例控制实现语句：*Azure Active Directory 用于管理对信息系统的控制访问。帐户管理员可按照帐户管理策略创建临时帐户。根据策略要求，需要将这些临时帐户设置为过期。*

  > AC-2 (3) 示例控制实现语句：*Azure Active Directory 管理对信息系统的控制访问。帐户管理员可按照帐户管理策略创建临时帐户。根据策略要求，需要将这些临时帐户设置为过期。*

  >AC-2 (7) 示例控制实现语句：*Azure Active Directory 根据基于角色的访问方案（将信息系统特权组织到分配给安全组的角色）管理对信息系统的控制访问。安全组管理员负责授予用户访问权限，将用户分配到正确的安全组。向每个安全组分配最小的适当访问权限，以便其正确完成任务。*

  > AC-2 (11) 示例控制实现语句：*Azure Active Directory 管理对 Azure 中信息系统的控制访问。帐户管理员在 Azure Active Directory 中定义并强制执行安全策略要求的使用限制。*

- 访问强制执行 [NIST SP 800-53 控制 AC-3]

  > AC-3 示例控制实现语句：*Azure Active Directory 会使用基于角色的访问控制将已批准的授权强制执行到客户环境。安全组管理员管理对 Azure Active Directory 安全组的访问。使用最低特权的原则，根据用户角色将其放在适当的安全组。*

- 最低特权 [NIST SP 800-53 控制 AC-6]

  > AC-6 (10) 示例控制实现语句：*Azure Active Directory 会使用基于角色的访问控制将已批准的授权强制实施到客户环境。安全组管理员管理对 Azure Active Directory 安全组的访问。未授予非特权用户对安全组的访问权限，该权限可使其访问特权功能，包括允许禁用、绕过，或更改安全保护机制的任何权限。*

- 远程访问 [NIST SP 800-53 控制 AC-17]

  > AC-17 (1) 示例控制实现语句：*Azure Active Directory 用于监视和控制所有远程访问。Azure Active Directory 包括针对目录的安全报表、活动报表和审核报表。*

- 审核信息保护 [NIST SP 800-53 控制 AU-9]

  > AU-9 示例控制实现语句：*紧密托管的访问控制用于防止审核信息和工具遭到未经授权的访问、修改和删除。Azure Active Directory 通过 Active Directory 策略和基于角色的组成员身份使用访问授权和逻辑控制的层方法向用户强制执行批准的逻辑访问。查看审核信息和使用审核工具的能力仅限于需要这些权限的用户。*

  > AU-9 (4) 示例控制实现语句：*Azure Active Directory 将审核功能的管理限制为适当安全组的成员。仅向对访问审核功能管理具有特定需求的人员授予这些权限。*

- 更改的访问限制 [NIST SP 800-53 控制 CM-5]

  > CM-5 示例控制实现语句：*Azure Active Directory 强制执行的 RBAC 用于定义、记录、批准和强制执行与更改关联的逻辑访问限制。系统中创建的所有帐户都是基于角色的。来自帐户管理员的人员请求访问（如已获批）会根据其角色置于适当的安全组。批准后，仅允许特定安全组的成员对生产环境进行访问。*

  > CM-5 (1) 示例控制实现语句：*Azure Active Directory 会通过安全组成员身份强制执行逻辑访问限制。这要求安全组所有者授予对给定安全组的访问权限。*

- 用户标识和身份验证 [NIST SP 800-53 控制 IA-2]

  > IA-2 示例控制实现语句：*根据用户名对访问信息系统环境的人员进行唯一标识并使用 Azure Active Directory 进行身份验证。需要向 Azure Active Directory 进行身份验证才能访问信息系统。*

  > IA-2 (8) 示例控制实现语句：*Azure Active Directory 的内置 Kerberos 功能可保护对生产环境的访问权限，防止重播攻击。在 Kerberos 身份验证中，客户端发送的验证器包含其他数据，如加密的 IP 列表、客户端时间戳和票证生存期。如果重播数据包，则将检查时间戳。如果时间戳早于、或与上一验证器的时间相同，则会拒绝数据包。*

  > IA-2 (9) 示例控制实现语句：*Azure Active Directory 的内置 Kerberos 功能可保护所有帐户免遭重播攻击。在 Kerberos 身份验证中，客户端发送的验证器包含其他数据，如加密的 IP 列表、客户端时间戳和票证生存期。如果重播数据包，则将检查时间戳。如果时间戳早于、或与上一验证器的时间相同，则会拒绝数据包。*

- 标识符管理 [NIST SP 800-53 控制 IA-4]

  > IA-4.b 示例控制实现语句：*Azure Active Directory 帐户标识符用于标识用户。不会重复使用这些唯一标识符。*

  > IA-4.c 示例控制实现语句：*Azure Active Directory 是用于提供服务环境访问权限的中央帐户存储库。在 Azure Active Directory 中创建帐户时，会将用户的唯一标识符分配到个人。*

  > IA-4.d 示例控制实现语句：*永远不会重复使用唯一的用户标识符。这由 Azure Active Directory 强制执行。*

  > IA-4.e 示例控制实现语句：*Azure Active Directory 中的所有帐户均配置为处于非活动状态 35 天后自动禁用。*

  > IA-4 (4) 示例控制实现语句：*Azure Active Directory 通过使用应用于其唯一 Azure Active Directory 凭据的命名约定表示承包商和供应商。*

- 验证器管理 [NIST SP 800-53 控制 IA-5]

  > IA-5.b 示例控制实现语句：*首次创建帐户时，Azure Active Directory 会分配一个满足策略要求的唯一标识和随机临时密码。Azure Active Directory 会在帐户的整个生命周期内维护与帐户关联的唯一标识。帐户标识绝不会与 Azure Active Directory 重复*

  > IA-5.c 示例控制实现语句：*Azure Active Directory 可确保颁发的密码满足密码复杂性的策略要求。*

  > IA-5.d 示例控制实现语句：*帐户管理员处理初始验证器分发过程。对用户帐户进行访问修改会经历 AC-2 中定义的帐户管理过程。如果丢失或泄露验证器，则帐户管理员将在必要时依据定义的过程重置、重新颁发或撤消验证器。*

  > IA-5 (1).c 示例控制实现语句：*使用 Azure Active Directory 以确保所有密码在存储和传输时得到加密保护。由 Azure Active Directory 存储的密码自动哈希处理为 Azure Active Directory 功能的一部分。*

  > IA-5 (1).f 示例控制实现语句：*Azure Active Directory 用于管理对信息系统的控制访问。最初创建帐户或生成临时密码时，使用 Azure Active Directory 要求用户在下次登录时更改密码。*

  > IA-5 (4) 示例控制实现语句：*Azure Active Directory 是自动工具，用于确定密码验证器是否足够强大，以便满足 IA-5 (1) 中设定的密码长度、复杂性、循环和生存期限制。Azure Active Directory 可确保在创建时密码验证器强度满足这些标准。*

Azure Active Directory 组策略配置可用于部署满足特定安全要求的客户安全策略。 客户 Azure 虚拟机可以由 Azure Active Directory 来应用这些策略。
Azure Active Directory 组策略配置可以包括以下内容的安全控制实现：

- 最低特权 [NIST SP 800-53 控制 AC-6]

  > AC-6 (9) 示例控制实现语句：*配置控制的组策略对象 (GPO) 应用于 Azure 中所有客户控制的 Windows 计算机。自定义组策略对象以满足组织要求，包括对特权功能执行的审核。*

- 不成功的登录尝试 [NIST SP 800-53 控制 AC-7]

  > AC-7 示例控制实现语句：*配置控制的组策略对象 (GPO) 应用于 Azure 中所有客户控制的 Windows 计算机。自定义组策略对象以满足组织要求，包括连续登录尝试失败后启动帐户锁定。*

- 并发会话控制 [NIST SP 800-53 控制 AC-10]、会话锁定 [NIST SP 800-53 控制 AC-11]、会话终止 [NIST SP 800-53 控制 AC-12]

  > AC-10 示例控制实现语句：*配置控制的组策略对象 (GPO) 应用于 Azure 中所有客户控制的 Windows 计算机。自定义组策略对象以满足组织要求，包括将用户限制到满足 FedRAMP 和 DoD 要求的单个远程桌面服务会话。*

  > AC-11.a 示例控制实现语句：*配置控制的组策略对象 (GPO) 应用于 Azure 中所有客户控制的 Windows 计算机。自定义组策略对象以满足组织要求，包括保留 900 秒（15 分钟）非活动状态直至用户重新进行身份验证后启动会话锁定。*

  > AC-12 示例控制实现语句：*一旦收到来自客户用户的注销请求，Windows 就会自动终止远程桌面会话。此外，配置控制的组策略对象 (GPO) 应用于 Azure 中所有客户控制的 Windows 计算机。自定义组策略对象以满足组织要求，包括处于非活动状态 900 秒（15 分钟）后终止远程桌面会话。*

- 验证器管理 [NIST SP 800-53 控制 IA-5]

  > IA-5.f 示例控制实现语句：*配置控制的组策略对象 (GPO) 应用于 Azure 中所有客户控制的 Windows 计算机。自定义组策略对象以满足组织要求，包括对本地帐户密码的限制，从而强制执行满足 FedRAMP 要求的最短（1 天）和最长（60 天）生存期限制、重复使用条件（24 个密码）、长度（14 个字符）和复杂性要求。*

  > IA-5.g 示例控制实现语句：*配置控制的组策略对象 (GPO) 应用于 Azure 中所有客户控制的 Windows 计算机。自定义组策略对象以满足组织要求，包括对本地帐户密码的限制，从而强制执行满足 FedRAMP 要求的最长（60 天）生存期限制。*

  > IA-5 (1).a 示例控制实现语句：*配置控制的组策略对象 (GPO) 应用于 Azure 中所有客户控制的 Windows 计算机。自定义组策略对象以满足组织要求，包括满足 FedRAMP 要求的复杂性要求。*

  > IA-5 (1).b 示例控制实现语句：*配置控制的组策略对象 (GPO) 应用于 Azure 中所有客户控制的 Windows 计算机。自定义组策略对象以满足组织要求，包括满足 FedRAMP 要求的复杂性要求。*

  > IA-5 (1).d 示例控制实现语句：*配置控制的组策略对象 (GPO) 应用于 Azure 中所有客户控制的 Windows 计算机。 自定义组策略对象以满足组织要求，包括对本地帐户密码的限制，从而强制执行满足 FedRAMP 要求的最短（1 天）和最长（60 天）生存期限制。

  > IA-5 (1).e 示例控制实现语句：*配置控制的组策略对象 (GPO) 应用于 Azure 中所有客户控制的 Windows 计算机。自定义组策略对象以满足组织要求，包括对满足 FedRAMP 要求的重复使用条件（24 个密码）的限制。*

其他工具和资源

文档、工具、模板和其他资源可以指导 Azure 服务和功能的安全部署。 请访问 [Microsoft Azure Docs](https://docs.microsoft.com/azure/active-directory/) 大致了解 Azure Active Directory。

### <a name="key-vault"></a>密钥保管库

Azure Key Vault 可为云应用程序和服务使用的加密密钥和机密提供安全保障。 通过使用 Azure Key Vault，客户可以创建、管理和保护密钥和机密。 安全容器（保管库）可用于安全存储和管理加密密钥和机密。 Azure Key Vault 可用于生成使用经过 FIPS 140-2 Level 2 验证的 HSM 的加密密钥。

Azure Key Vault 容器可帮助安全存储加密密钥，同时保持密钥的高可用性。 这可能包括以下内容的安全控制实现：

-   验证器保护 [NIST SP 800-53 控制 IA-5 (7)]

  > IA-5 (7) 示例控制实现语句：*明确禁止使用应用程序、访问脚本或功能键中嵌入的未加密静态验证器。每次使用之前，任何使用验证器的脚本或应用程序都会调用 Azure Key Vault 容器。审核对 Azure Key Vault 容器的访问，如果使用服务帐户访问系统而未相应地调用 Azure Key Vault 容器，则允许检测此禁止的冲突。*

- 加密密钥建立和管理 [NIST SP 800-53 控制 SC-12 (1)]

  > SC-12 (1) 示例控制实现语句：*Azure Key Vault 用于存储加密密钥和机密。此服务跟踪和监视对机密的访问。此服务用于确保不会丢失机密。*

Azure Key Vault 可用于创建使用经过 FIPS 140-2 Level 2 验证的 HSM 的加密密钥。 Azure Active Directory 组策略配置可以包括以下内容的安全控制实现：

- 加密密钥建立和管理 [NIST SP 800-53 控制 SC-12 (2)]

  > SC-12 (2) 示例控制实现语句：*Azure Key Vault 用于生成、控制和分发加密密钥。使用经过 FIPS 140-2 Level 2 验证的 HSM 生成这些密钥。在 Azure Key Vault 中的安全加密容器内存储和管理这些密钥。*

其他工具和资源

文档、工具、模板和其他资源可以指导 Azure 服务和功能的安全部署。 请访问 [Microsoft Azure Docs](https://docs.microsoft.com/azure/key-vault/) 大致了解 Azure Key Vault。

### <a name="operations-management-suite"></a>Operations Management Suite

Microsoft Operations Management Suite (OMS) 是 Microsoft 基于云的 IT 管理解决方案，可帮助管理和保护本地和云基础结构。 安全与合规性可帮助识别、评估和缓解基础结构的安全风险。 通过多个解决方案（包括分析日志数据和监视安全配置的 Log Analytics）来实现 OMS 的这些功能。

OMS 启用的安全和审核服务可帮助实现帐户监视和日志记录。 这可能包括以下内容的安全控制实现：

- 帐户管理 [NIST SP 800-53 控制 AC-2]

  > AC-2.g 示例控制实现语句：*Microsoft Operations Management Suite (OMS) 用于监视系统帐户的使用。OMS 创建系统帐户的审核日志（可稍后通过 OMS 的分析对该日志进行分析）并基于一组定义的条件发出警报。*

  > AC-2 (4) 示例控制实现语句：*Microsoft Operations Management Suite (OMS) 用于生成帐户管理功能的审核日志，如帐户创建、修改、启用、禁用和删除操作。如果已经执行了上述任何条件，则 OMS 用于通知系统所有者。*

  > AC-2 (12) 示例控制实现语句：*Microsoft Operations Management Suite (OMS) 用于监视系统帐户的异常使用。OMS 创建系统帐户的审核日志，可稍后通过 OMS 分析对该日志进行分析并基于一组定义的条件向相应人员发出警报。*

- 最低特权 [NIST SP 800-53 控制 AC-6]

  > AC-6 (9) 示例控制实现语句：*Microsoft Operations Management Suite (OMS) 用于监视特权功能的执行。OMS 创建一系列指定功能的审核日志，可稍后通过 OMS 分析对该日志进行分析并基于一组定义的条件发出警报。*

 - 远程访问 [NIST SP 800-53 控制 AC-17]

  > AC-17 (1) 示例控制实现语句：*Microsoft Operations Management Suite (OMS) 用于监视和控制远程访问。OMS 包括针对目录的安全报表、活动报表和审核报表。*

- 审核事件 [NIST SP 800-53 控制 AU-2]

  > AU-2 示例控制实现语句：*Microsoft Operations Management Suite (OMS) 用于审核：成功和不成功的帐户登录事件、帐户管理事件、对象访问、策略更改、特权功能、进程跟踪和系统事件。对于 Web 应用程序：所有管理员活动、身份验证检查、授权检查、数据删除、数据访问、数据更改和权限更改。以及任何其他客户定义的事件集。*

- 审核记录的内容 [NIST SP 800-53 控制 AU-3]

  > AU-3 (2) 示例控制实现语句：*Microsoft Operations Management Suite (OMS) 用作网络、存储和计算设备生成的所有审核记录的集中管理和配置。*

- 审核存储容量 [NIST SP 800-53 控制 AU-4]

  > AU-4 示例控制实现语句：*Microsoft Operations Management Suite (OMS) 用于网络、存储和计算设备生成的所有审核记录的集中管理和配置。集中管理工具用于配置审核记录存储容量。*

- 对审核处理失败的响应 [NIST SP 800-53 控制 AU-5]

  > AU-5 示例控制实现语句：*将 Microsoft Operations Management Suite (OMS) 与安全和审核解决方案结合使用，以便通知组织定义的人员审核处理失败。*

  > AU-5 (1) 控制实现语句：*将 Microsoft Operations Management Suite (OMS) 与安全和审核解决方案结合使用，以便在组织定义的时间段内通知组织定义的人员审核记录存储已达到组织定义的最大存储库卷百分比。*

  > AU-5 (2) 控制实现语句：*将 Microsoft Operations Management Suite (OMS) 与安全和审核解决方案结合使用，以便在组织策略中定义的可审核事件失败时向组织定义的人员进行实时通知。*

- 审核评审、分析和报告 [NIST SP 800-53 控制 AU-6]

  > AU-6 (3) 示例控制实现语句：*将 Microsoft Operations Management Suite (OMS) 与安全和审核解决方案以及 Log Analytics 解决方案结合使用，以便分析不同的审核日志存储库，从而提供组织范围内的态势感知。分析工具用于就审核日志发出报告，以便提供态势感知。*

  > AU-6 (4) 示例控制实现语句：*Microsoft Operations Management Suite (OMS) 用于所有审核记录的集中管理和配置。集中管理工具允许对来自所有源的审核记录进行评审和分析。*

  > AU-6 (5) 示例控制实现语句：*将 Microsoft Operations Management Suite (OMS) 与安全和审核解决方案以及 Log Analytics 解决方案结合使用，以便分析漏洞扫描、信息系统监视和性能相关数据生成的与安全相关的不同数据。对这些不同资源的分析可提供一种标识可疑活动的增强能力。*

- 审核缩减和报表生成 [NIST SP 800-53 控制 AU-7]

  > AU-7 示例控制实现语句：*将 Microsoft Operations Management Suite (OMS) 与安全和审核解决方案以及 Log Analytics 解决方案结合使用，以便按需生成允许安全事件事后调查的人工可读报表。使用 Microsoft Log Analytics 工具不会永久或不可逆转地更改原始审核记录内容或时间顺序。*

  > AU-7 (1) 示例控制实现语句：*将 Microsoft Operations Management Suite (OMS) 与安全和审核解决方案结合使用，以便查询组织定义的可审核事件。*

- 审核信息保护 [NIST SP 800-53 控制 AU-9]

  > AU-9 示例控制实现语句：*将 Microsoft Operations Management Suite (OMS) 与安全组结合使用，以便管理对审核信息和工具的访问，从而防止对审核记录进行未经授权的访问、修改和删除。查看审核信息和使用审核工具的能力仅限于需要这些权限的用户。*

  > AU-9 (2) 示例控制实现语句：*将 Microsoft Operations Management Suite (OMS) 与安全和审核解决方案以及 Azure 备份结合使用，以便将审核日志备份到 Azure，供其随后复制数据以实现数据可读性和可用性。Azure 备份为审核日志提供了一个安全的存储位置，即除正在审核的系统之外的系统。* 

  > AU-9 (4) 示例控制实现语句：*将 Microsoft Operations Management Suite (OMS) 与安全组结合使用，以便管理对审核信息和工具的访问。仅向对访问审核功能管理具有特定需求的人员授予这些权限。*

- 审核记录保留期 [NIST SP 800-53 控制 AU-11]

  > AU-11 示例控制实现语句：*将 Microsoft Operations Management Suite (OMS) 与安全和审核解决方案结合使用，以便配置审核日志保留期。审核日志保留期配置为保留审核数据至少 90 天。*

- 审核生成 [NIST SP 800-53 控制 AU-12]

  > AU-12 示例控制实现语句：*将 Microsoft Operations Management Suite (OMS) 与安全和审核解决方案结合使用，以便从信息系统组件收集在 AU-02 中定义的可审核事件。OMS 的安全和审核解决方案由组织定义的人员使用，用于定义从特定设备收集的可审核事件。*

  > AU-12 (1) 示例控制实现语句：*将 Microsoft Operations Management Suite (OMS) 与安全和审核解决方案以及 Log Analytics 解决方案结合使用，以便从系统组件（可按时间戳进行排序）编译审核记录，从而创建系统范围的审核记录。*

- 更改的访问限制 [NIST SP 800-53 控制 CM-5]

  > CM-5 (1) 示例控制实现语句：*将 Microsoft Operations Management Suite (OMS) 与安全和审核解决方案以及 Active Directory 结合使用，以便通过安全组成员身份强制执行逻辑访问限制。这些安全组执行的操作由 OMS 进行审核。*

- 信息系统组件清单 [NIST SP 800-53 控制 CM-8]

  > CM-8 (3) 示例控制实现语句：*将 Microsoft Operations Management Suite (OMS) 与安全和审核解决方案以及反恶意软件解决方案结合使用，以便检测未经授权的软件状态。检测后，OMS 会禁用受感染的组件，并向组织定义的人员发送警报。*

文档、工具、模板和其他资源可以指导 Azure 服务和功能的安全部署。 请访问 [Microsoft Azure Docs](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview/) 大致了解 Operations Management Suite。

### <a name="additional-implementation-guidance"></a>其他实现指南

除了上述核心 Azure 服务外，还有几个内置功能可帮助组织满足安全控制合规性要求。 所有 Azure 资源都被组织到资源组中。 可通过应用标记（它们可能是客户选择的键值对，用于按类别或任何其他属性标识资源）进一步组织资产。 Azure 资源组确保对 Azure 中部署的所有客户资源完成标识和跟踪 [NIST SP 800-53 控制 CM-8]。 

> CM-8 示例控制实现语句：*其中部署的所有客户控制的资源都属于“客户资源”资源组。在 Azure 门户中，所有已部署的资产都在资源组边栏选项卡中进行标识，确保呈现准确的最新清单。必要时，将标记应用到资源，满足跟踪和报告要求。*

Azure Resource Manager 模板允许客户定义其 Azure 部署中的资源配置。 Resource Manager 模板基于 JSON，使用声明性语法对资源、配置参数和 Azure 环境的其他方面进行记录。 从 Azure 门户部署 Azure 解决方案时，将自动生成 Resource Manager 模板，并且可以手动创作和编辑该模板以满足特定的客户要求。 这些 Resource Manager 模板可用作信息系统基线配置的表示形式 [NIST SP 800-53 控制 CM 2]。 可自定义现有 Resource Manager 模板（如上面引用的构建基块），满足任何部署需求。

> CM-2 示例控制实现语句：*作为信息系统基线配置的组件，Azure Resource Manager 模板保留在配置控制下，表示部署的客户控制的资源和 Azure 中信息系统组件的配置。这些模板捕获部署的资源，包括虚拟机、存储和网络资源（包括对控制网络流量进入、退出和保持在客户 Azure 环境中的网络资源的配置）。*

## <a name="next-steps"></a>后续步骤

有关与 Azure Blueprint、FedRAMP、DoD 或代理 ATO 进程或其他合规性协助相关的查询；或想要提供 Blueprint 反馈，请发送电子邮件到 [AzureBlueprint@microsoft.com](mailto:AzureBlueprint@microsoft.com)。

[Microsoft 信任中心](https://www.microsoft.com/trustcenter/Compliance/default.aspx)

[Microsoft Azure Government 博客](https://blogs.msdn.microsoft.com/azuregov/)



<!--HONumber=Jan17_HO4-->


