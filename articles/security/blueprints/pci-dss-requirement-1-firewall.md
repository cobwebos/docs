---
title: Azure 付款处理蓝图 - 防火墙要求
description: PCI DSS 要求 1
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: b1935d88-acae-42f9-bc25-bb0766f876ab
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 6f82c828b7cd878cc9ed9f42a4bf63d0d7aedb16
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
---
# <a name="firewall-requirements-for-pci-dss-compliant-environments"></a>PCI DSS 相容环境的防火墙要求 
## <a name="pci-dss-requirement-1"></a>PCI DSS 要求 1

**安装并维护防火墙配置，以保护持卡人数据**

> [!NOTE]
> 这些要求由[支付卡行业 (PCI) 安全标准委员会](https://www.pcisecuritystandards.org/pci_security/)定义，作为 [PCI 数据安全标准 (DSS) 3.2 版](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss)的一部分。 请参阅 PCI DSS，了解每项要求的测试过程和指南。

防火墙是专门的设备，用于控制允许在实体的网络（内部）和不受信任网络（外部）之间流动的计算机流量，以及在实体的内部受信任网络中进出较敏感区域的流量。 例如，持卡人数据环境是实体的受信任网络中的较敏感区域。
防火墙会检查所有网络流量，阻止那些不符合指定安全标准的传输内容。
必须保护所有系统，使其不被用户通过不受信任的网络进行未授权的访问，不管在进入系统时是以电子商务方式通过 Internet 进入、通过桌面浏览器以员工身份进行 Internet 访问、以员工身份进行电子邮件访问、通过专用连接（例如企业到企业连接）进入、通过无线网络进入，还是通过其他源进入。 通常情况下，看起来不重要的进出不受信任网络的路径会被用来对重要系统进行不受保护的访问。 对于任何计算机网络来说，防火墙都是重要的保护机制。
其他系统组件也可以提供防火墙功能，只要其满足防火墙的最低要求（参见“要求 1”）。 如果在持卡人数据环境中使用其他系统组件来提供防火墙功能，则必须将相应的设备包括在要求 1 的范围和评估中。

## <a name="pci-dss-requirement-11"></a>PCI DSS 要求 1.1

**1.1** 建立和实施防火墙和路由器配置标准，其中包括以下内容（参见 1.1.1 到 1.1.7）。


**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 通过 PaaS 隔离提供 CDE 的防火墙功能，而应用服务环境实施则确保 CDE 的数据出入受到保护。<br /><br />[应用服务环境 (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) 是一项“高级”服务计划，其使用是出于符合性原因。 有关 ASE 控制和配置的详细信息，请参阅 [PCI 指南 - 应用服务环境](payment-processing-blueprint.md#app-service-environment)。|



### <a name="pci-dss-requirement-111"></a>PCI DSS 要求 1.1.1

**1.1.1** 通过正式的流程来审核和测试所有网络连接以及对防火墙和路由器配置进行的更改


**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 实例可建立一个 CI/CD DevOps 模型，确保对所有更改进行正确的管理。 Log Analytics 可广泛记录更改。 可以检查和验证更改的准确性。 如需更具体的指南，请参阅 [PCI 指南 - Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing)。<br /><br />[Azure 安全中心](https://azure.microsoft.com/services/security-center/)允许集中查看所有 Azure 资源的安全状况。 一眼就可验证适当的安全控制是否配置到位且配置正确，还可快速确认任何需要注意的资源。|



### <a name="pci-dss-requirement-112"></a>PCI DSS 要求 1.1.2

**1.1.2** 提供最新的网络图，其中标识持卡人数据环境和其他网络（包括任何无线网络）之间的所有连接

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 请参阅 Contoso Webstore 参考体系结构和设计文档（已作为解决方案安装模式内容提供）。|



### <a name="pci-dss-requirement-113"></a>PCI DSS 要求 1.1.3

**1.1.3** 提供最新的关系图，显示所有跨系统和网络的持卡人数据流

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 请参阅 Contoso Webstore DFD 和威胁模型。|



### <a name="pci-dss-requirement-114"></a>PCI DSS 要求 1.1.4

**1.1.4** 要求在每个 Internet 连接上以及在任何外围网络 (DMZ) 和内部网络区域之间设置防火墙

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | Microsoft Azure 采用边界保护设备（例如网关、网络 ACL、应用程序防火墙），在平台级别控制外部和内部边界的通信。 然后，客户就可以根据自己的规格和要求对其进行配置。 Microsoft Azure 对进入平台的通信进行筛选。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 通过 PaaS 隔离提供外围网络，而应用服务环境实施则确保 CDE 的数据出入受到保护。<br /><br />[应用服务环境 (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) 是一项“高级”服务计划，其使用是出于符合性原因。 有关 ASE 控制和配置的详细信息，请参阅 [PCI 指南 - 应用服务环境](payment-processing-blueprint.md#app-service-environment)。|



### <a name="pci-dss-requirement-115"></a>PCI DSS 要求 1.1.5

**1.1.5** 描述与管理网络组件相对应的组、角色和责任

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 使用 [Azure 基于角色的访问控制 (RBAC)](/azure/role-based-access-control/role-assignments-portal) 来隔离用户角色。 可以通过 RBAC 对 Azure 进行访问管理，既准确又集中。 针对订阅访问和 Azure Key Vault 访问进行具体的配置。|



### <a name="pci-dss-requirement-116"></a>PCI DSS 要求 1.1.6

**1.1.6** 记录使用所有允许的服务、协议和端口时的业务理由和审核情况，包括记录为那些被视为不安全的协议实施的安全功能。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 在整个 RA 设计过程中，Contoso Webstore 只打开必需的端口和协议。 可以在 DFD 和威胁模型中查看数据流详细信息。|



### <a name="pci-dss-requirement-117"></a>PCI DSS 要求 1.1.7

**1.1.7** 要求至少每六个月审核一次防火墙和路由器规则集

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 在 Contoso Webstore 中，对防火墙规则集进行审核的目的是确保不包括任何不需要的或不使用的规则。 按设计，演示版在部署时会尽量少启用特权，并使用最短的路径。|



## <a name="pci-dss-requirement-12"></a>PCI DSS 要求 1.2

**1.2** 生成防火墙和路由器配置，限制不受信任网络和持卡人数据环境中任何系统组件之间的连接。 

> [!NOTE]
> “不受信任网络”是指那些对于实体拥有的受审核网络来说属于外部网络的网络，以及/或者实体无法控制或管理的网络。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 的 CDE 在 RA 和部署文档中定义。 根据设计，不受信任网络会被拒绝。|



### <a name="pci-dss-requirement-121"></a>PCI DSS 要求 1.2.1

**1.2.1** 对入站和出站流量进行限制，只满足持卡人数据环境的需要（具体说来，就是拒绝所有其他流量）。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 的 CDE 在 RA 和部署文档中定义。 根据设计，不受信任网络会被拒绝。 Contoso Webstore 演示版将 Microsoft Azure 应用程序防火墙配置为只允许指定范围的 IP 地址访问 Microsoft Azure 服务。 Contoso Webstore 在所有 CDE 边界提供“全部拒绝”形式的防火墙。 所有配置在部署的初始设置阶段执行。

> [!NOTE]
> 应用服务环境 (ASE) 用在此解决方案中是为了隔离 CDE。但是，必须由合格的安全评估师 (QSA) 评估此解决方案，因为 ASE 会通过实施外围网络隔离来完成出站连接。 PCI-DSS 要求必须阻止所有不必要的入站和出站连接。 为了正常运行，ASE 会根据需要按[应用服务环境的网络注意事项](/azure/app-service/app-service-environment/network-info)中的说明建立出站连接。 在将解决方案部署到生产环境之前，客户应与 QSA 一起评估出站连接，确保其满足要求。 |



### <a name="pci-dss-requirement-122"></a>PCI DSS 要求 1.2.2

**1.2.2** 保护并同步路由器配置文件。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 提供为了方便 Microsoft Azure 本机网络控制而同步的配置。|



### <a name="pci-dss-requirement-123"></a>PCI DSS 要求 1.2.3

**1.2.3** 在所有无线网络和持卡人数据环境之间安装外围防火墙，并将这些防火墙配置为拒绝所有流量。即使流量是业务需要的，也只允许无线环境和持卡人数据环境之间的授权流量。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 不启用任何无线解决方案或功能。|



## <a name="pci-dss-requirement-13"></a>PCI DSS 要求 1.3

**1.3** 禁止在 Internet 和持卡人数据环境中的任何系统组件之间进行的直接公开访问。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | Microsoft Azure 采用基于网络和基于主机的边界保护设备，例如防火墙、负载均衡器和 ACL。 这些设备使用 VLAN 隔离、NAT、数据包筛选等机制，将客户流量与 Internet 和管理人员流量分开。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 在部署时提供 Azure 应用程序防火墙的配置，此类配置只允许使用指定范围的 IP 地址来访问站点，并在其 CDE 中提供守护 Azure VM。|



### <a name="pci-dss-requirement-131"></a>PCI DSS 要求 1.3.1

**1.3.1** 实施外围网络，只允许入站流量流向相应的系统组件，这些组件提供授权的可公开访问的服务、协议和端口。


**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 实施其外围网络是为了确保仅授权的服务可以与 CDE 连接。|



### <a name="pci-dss-requirement-132"></a>PCI DSS 要求 1.3.2

**1.3.2** 只允许入站 Internet 流量流向外围网络中的 IP 地址。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 实施其外围网络是为了确保仅授权的服务可以与 CDE 连接。|



### <a name="pci-dss-requirement-133"></a>PCI DSS 要求 1.3.3

**1.3.3** 实施反欺骗措施，检测伪造的源 IP 地址并阻止其进入网络。 （例如，阻止源自 Internet 却带有内部源地址的流量。）

**责任：&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | Microsoft Azure 实施网络筛选是为了防止欺骗流量，将传入和传出流量限制为可信平台组件。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 不适用。|



### <a name="pci-dss-requirement-134"></a>PCI DSS 要求 1.3.4

**1.3.4** 不允许从持卡人数据环境流向 Internet 的未授权出站流量。


**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 体系结构阻止未授权出站流量从范围内环境流向 Internet。 为此，可以在 Microsoft Azure 中为核准的端口和协议配置出站流量 ACL。 这些控制包括 SQL Server 数据库中 CDE 的访问权限。 <br /><br />PaaS SQL 数据库实例用于展示数据库的安全措施。 有关详细信息，请参阅 [PCI 指南 - Azure SQL 数据库](payment-processing-blueprint.md#azure-sql-database)。|



### <a name="pci-dss-requirement-135"></a>PCI DSS 要求 1.3.5

**1.3.5** 只允许“已建立的”连接连接到网络中。


**责任：&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | Microsoft Azure 实施网络筛选是为了防止欺骗流量，将传入和传出流量限制为可信平台组件。 隔离 Microsoft Azure 网络，以便将客户流量与管理人员流量分开。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 不适用。|



### <a name="pci-dss-requirement-136"></a>PCI DSS 要求 1.3.6

**1.3.6** 将存储持卡人数据的系统组件（例如数据库）置于内部网络区域，与外围网络和其他不受信任网络分开。


**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | Microsoft Azure 使用网络隔离和 NAT 将客户流量与管理人员流量分开。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 体系结构阻止未授权出站流量从范围内环境流向 Internet。 为此，可以在 Microsoft Azure 中为核准的端口和协议配置出站流量 ACL。 这些控制包括 SQL Server 数据库中 CDE 的访问权限。 <br /><br />PaaS SQL 数据库实例用于展示数据库的安全措施。 有关详细信息，请参阅 [PCI 指南 - Azure SQL 数据库](payment-processing-blueprint.md#azure-sql-database)。|



### <a name="pci-dss-requirement-137"></a>PCI DSS 要求 1.3.7

**1.3.7** 不向未授权方披露专用 IP 地址和路由信息。 

> [!NOTE]
> 用于阻止 IP 寻址的方法包括但不限于：
> - 网络地址转换 (NAT)。
> - 将包含持卡人数据的服务器置于代理服务器/防火墙后面。
> - 删除或筛选掉使用注册寻址的专用网络的路由播发。
> - 在内部使用 RFC1918 地址空间而不是注册地址。


**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | Microsoft Azure 使用网络地址转换 (NAT) 和网络隔离将客户流量与管理人员流量分开。 Azure 设备由其 UUID 唯一标识，并使用 Kerberos 进行身份验证。 Azure 托管的网络设备通过 RFC 1918 IP 地址进行标识。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 将所有持卡人数据置于代理服务器/防火墙后，在内部使用 RFC1918 地址空间。|



## <a name="pci-dss-requirement-14"></a>PCI DSS 要求 1.4

**1.4** 在任何连接到 Internet 的网络外便携式计算设备（包括公司拥有的和/或个人拥有的设备，例如员工使用的笔记本电脑，也用于访问 CDE）上安装个人防火墙软件或等效的功能。 防火墙（或等效功能）配置包括（已定义特定配置设置）：
- 个人防火墙（或等效功能）正在有效运行。
- 个人防火墙（或等效功能）不可由便携式计算设备的用户更改。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 不提供对最终用户设备的保护。 [Microsoft Intune](https://www.microsoft.com/cloud-platform/microsoft-intune) 可以用来管理工作人员访问公司数据时所用的移动设备。|



## <a name="pci-dss-requirement-15"></a>PCI DSS 要求 1.5

**1.5** 确保记录在进行防火墙管理时使用过哪些安全策略和操作过程，并将其告知受影响的各方。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 在部署时提供 Azure 应用程序防火墙的配置，此类配置只允许使用指定范围的 IP 地址来访问站点，并在其 CDE 中提供守护 Azure VM。|




