---
title: Azure 付款处理蓝图 - 密码要求
description: PCI DSS 要求 2
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 0df24870-6156-4415-a608-dd385b6ae807
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: d3ca83294702a138713f31e34b0e94b1ccb87be0
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
ms.locfileid: "33894154"
---
# <a name="password-requirements-for-pci-dss-compliant-environments"></a>PCI DSS 相容环境的密码要求 
## <a name="pci-dss-requirement-2"></a>PCI DSS 要求 2

**不要将供应商提供的默认值用作系统密码和其他安全参数**

> [!NOTE]
> 这些要求由[支付卡行业 (PCI) 安全标准委员会](https://www.pcisecuritystandards.org/pci_security/)定义，作为 [PCI 数据安全标准 (DSS) 3.2 版](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss)的一部分。 请参阅 PCI DSS，了解每项要求的测试过程和指南。

恶意攻击者（实体的外部和内部人员）通常使用供应商默认密码和其他的供应商默认设置来入侵系统。 这些密码和设置是黑客社区众所周知的，可以轻松地通过公共信息来确定。

## <a name="pci-dss-requirement-21"></a>PCI DSS 要求 2.1
 
**2.1** 在网络上安装系统**之前**，始终更改供应商提供的默认设置，删除或禁用不必要的默认帐户。
这适用于所有默认密码，包括但不限于由下述项目使用的密码：操作系统、提供安全服务的软件、应用程序和系统帐户、销售点 (POS) 终端、简单网络管理协议 (SNMP) 社区字符串，等等。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 对于客户在 AADUX 门户中提供的新密码，将会强制实施 Microsoft Azure Active Directory 密码策略要求。 客户启动的自助密码更改要求对以前的密码进行验证。 在后续登录时，要求更改管理员重置密码。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 要求要使用的用户为所有用户设置强密码。 演示版中未启用示例帐户或来宾帐户。<br /><br />解决方案中不实施无线和 SNMP 技术。|



### <a name="pci-dss-requirement-211"></a>PCI DSS 要求 2.1.1

**2.1.1** 对于连接到持卡人数据环境或传输持卡人数据的无线环境，请在安装时更改所有无线供应商默认设置，包括但不限于默认的无线加密密钥、密码和 SNMP 社区字符串。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 解决方案中不实施无线和 SNMP 技术。|



## <a name="pci-dss-requirement-22"></a>PCI DSS 要求 2.2

**2.2** 为所有系统组件制定配置标准。 确保这些标准解决了所有已知的安全漏洞，并且符合行业接受的系统强化标准。
行业接受的系统强化标准的来源可能包括但不限于：
- Internet 安全中心 (CIS)
- 国际标准化组织 (ISO)
- 系统管理和网络安全审计 (SANS) 协会
- 国家标准与技术协会 (NIST)

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 对于 Microsoft Azure，OSSC 技术安全服务团队为 Microsoft Azure 环境中的系统制定了安全配置标准，这些标准与行业接受的强化标准一致。 这些配置记录在系统基线中，相关配置更改会告知受影响的团队（例如，IPAK 团队）。 将会执行相关过程来监视安全配置标准是否得到了遵循。 Microsoft Azure 环境中的系统安全配置标准与行业接受的强化标准一致，至少每年审核一次。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 可以通过 Contoso Webstore 来强化持卡人数据环境 (CDE) 范围内的所有服务。 <br /><br />Contoso Webstore 还部署 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)，用于集中查看所有 Azure 资源的安全状态。 可立即确认相应的安全控制措施是否部署到位且配置正确，并且可以快速识别任何需要关注的资源。<br /><br />Contoso Webstore 利用 Operations Management Suite 来记录所有系统更改。 Log Analytics 可广泛记录更改。 可以检查和验证更改的准确性。 如需更具体的指南，请参阅 [PCI 指南 - Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing)。|



### <a name="pci-dss-requirement-221"></a>PCI DSS 要求 2.2.1

**2.2.1** 每个服务器只实现一个主功能，防止需要不同安全级别的功能共存于同一服务器上。 （例如，Web 服务器、数据库服务器和 DNS 应在不同服务器上实现。） 

> [!NOTE]
> 在使用虚拟化技术时，每个虚拟系统组件只实现一个主功能。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 服务部署为 PaaS 服务。 所有服务都是隔离的，使用网络分段来分段。<br /><br />Contoso Webstore 也使用[应用服务环境 (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) 来强制实施密钥规范。 有关详细信息，请参阅 [PCI 指南 - 应用服务环境](payment-processing-blueprint.md#app-service-environment)。|



### <a name="pci-dss-requirement-222"></a>PCI DSS 要求 2.2.2

**2.2.2** 只启用必需的服务、协议、守护程序等，这些都是系统运行所必需的。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | Microsoft Azure 软件和硬件配置至少每季审核一次，以便确定并去除任何不需要的功能、端口、协议和服务。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 服务部署为 PaaS 服务。 所有服务都是隔离的，使用网络分段来分段。<br /><br />Contoso Webstore 也使用[应用服务环境 (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) 来强制实施密钥规范。 有关详细信息，请参阅 [PCI 指南 - 应用服务环境](payment-processing-blueprint.md#app-service-environment)。|



### <a name="pci-dss-requirement-223"></a>PCI DSS 要求 2.2.3

**2.2.3** 对于任何被视为不安全的必需服务、协议或守护程序，实施更多的安全功能。 

> [!NOTE]
> 使用 SSL/早期 TLS 时，必须完成附录 A2 中的要求。


**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 服务部署为 PaaS 服务。 所有服务都是隔离的，使用网络分段来分段。 还可以通过此部署强化 CDE 范围内的所有服务。 <br /><br />Contoso Webstore 还部署 [Azure 安全中心](https://azure.microsoft.com/services/security-center/)，用于集中查看所有 Azure 资源的安全状态。 可立即确认相应的安全控制措施是否部署到位且配置正确，并且可以快速识别任何需要关注的资源。<br /><br />Contoso Webstore 也使用[应用服务环境 (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) 来强制实施密钥规范。 有关详细信息，请参阅 [PCI 指南 - 应用服务环境](payment-processing-blueprint.md#app-service-environment)。|



### <a name="pci-dss-requirement-224"></a>PCI DSS 要求 2.2.4

**2.2.4** 配置系统安全参数，防止滥用。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | Azure 使用多重访问控制和记录业务需求的方式，确保只有经授权的人员能够配置 Azure 平台安全控件。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 利用 AAD 和 AD RBAC 来管理安全参数，确保其部署正确。 有关详细信息，请参阅 [PCI 指南 - 标识管理](payment-processing-blueprint.md#identity-management)。|



### <a name="pci-dss-requirement-225"></a>PCI DSS 要求 2.2.5

**2.2.5** 删除所有不需要的功能，例如脚本、驱动程序、功能、子系统、文件系统，以及不需要的 Web 服务器。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 提供有关如何建立边界的文档。 Contoso 的威胁模型和数据流关系图演示了所有使用的服务和启用的控件。|



## <a name="pci-dss-requirement-23"></a>PCI DSS 要求 2.3

**2.3** 使用强加密加密所有非控制台管理访问权限。 

> [!NOTE]
> 使用 SSL/早期 TLS 时，必须完成附录 A2 中的要求。

**责任：&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | Microsoft Azure 确保在访问虚拟机监控程序基础结构时强制使用强加密。 Microsoft Azure 还确保使用 Microsoft Azure 管理门户的客户能够访问其使用强加密的服务/IaaS 控制台。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 演示了如何在解决方案中实现强密码；另外，所有测试都可以用来验证是否在解决方案中始终实施加密。<br /><br />Contoso Webstore 也使用[应用服务环境 (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) 来强制实施密钥规范。 有关详细信息，请参阅 [PCI 指南 - 应用服务环境](payment-processing-blueprint.md#app-service-environment)。|



## <a name="pci-dss-requirement-24"></a>PCI DSS 要求 2.4

**2.4** 保留一个清单，列出处于 PCI DSS 范围内的系统组件。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 演示版 PaaS 解决方案清单可以在提供的文档中查看。 有关详细信息，请参阅 [PCI 指南 - 预安装的管理解决方案](payment-processing-blueprint.md#management-solutions)。|



## <a name="pci-dss-requirement-25"></a>PCI DSS 要求 2.5

**2.5** 确保记录在进行供应商默认设置和其他安全参数的管理时使用过哪些安全策略和操作过程，并将其告知受影响的各方。

**责任：&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | Contoso Webstore 提供介绍安全参数的文档，并对服务元素进行记录。 |



## <a name="pci-dss-requirement-26"></a>PCI DSS 要求 2.6

**2.6** 共享宿主提供程序必须保护每个实体的托管环境和持卡人数据。 这些提供程序必须满足“附录 A：共享宿主提供程序的其他 PCI DSS 要求”中详述的具体要求。

**责任：&nbsp;&nbsp;`Not Applicable`**

|||
|---|---|
| **提供商<br /> (Microsoft&nbsp;Azure)** | 不适用。 Microsoft Azure 不是共享宿主提供程序。 |
| **客户<br />（PCI&#8209;DSS&nbsp;蓝图）** | 不适用。 Microsoft Azure 不是共享宿主提供程序。|




