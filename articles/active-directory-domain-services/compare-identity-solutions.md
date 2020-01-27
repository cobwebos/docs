---
title: Azure 中基于 Active Directory 的服务的比较 | Microsoft Docs
description: 本概述文章对 Active Directory 域服务、Azure Active Directory 和 Azure Active Directory 域服务的标识产品/服务做了比较。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 01/22/2020
ms.author: iainfou
ms.openlocfilehash: d2495605cccf658b15e812fd85fd65671e84d15b
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76544270"
---
# <a name="compare-self-managed-active-directory-domain-services-azure-active-directory-and-managed-azure-active-directory-domain-services"></a>自我管理型 Azure Active Directory 域服务、Azure Active Directory 和托管型 Azure Active Directory 域服务的比较

若要为应用程序、服务或设备提供对中心标识的访问权限，可通过三种常用方式使用 Azure 中基于 Active Directory 的服务。 标识解决方案的多样性可让你灵活使用最符合组织需求的目录。 例如，如果主要工作是管理运行移动设备的仅限云的用户，那么生成并运行自己的 Active Directory 域服务 (AD DS) 标识解决方案可能没有意义。 只需使用 Azure Active Directory 即可解决需求。

尽管这三个基于 Active Directory 的标识解决方案采用相同的名称部分和技术，但它们旨在提供满足不同客户需求的服务。 从较高层面讲，这些标识解决方案和功能集包括：

* **Active Directory 域服务 (AD DS)** - 随时可在企业中部署的轻型目录访问协议 (LDAP) 服务器，提供标识和身份验证、计算机对象管理、组策略和信任等关键功能。
    * AD DS 是使用本地 IT 环境的众多组织中的一个中心组件，提供核心用户帐户身份验证和计算机管理功能。
* **Azure Active Directory (Azure AD)** - 基于云的标识和移动设备管理，为 Office 365、Azure 门户或 SaaS 应用程序等资源提供用户帐户和身份验证服务。
    * Azure AD 可与本地 AD DS 环境同步，以便为原本就在云中工作的用户提供单个标识。
* **Azure Active Directory 域服务 (Azure AD DS)** - 为托管域服务提供一部分完全兼容的传统 AD DS 功能，例如域加入、组策略、LDAP 和 Kerberos/NTLM 身份验证。
    * Azure AD DS 与 Azure AD 集成，后者本身可与本地 AD DS 环境同步。 此功能通过直接迁移策略将中心标识用例扩展到在 Azure 中运行的传统 Web 应用程序。

本概述文章将这些标识解决方案根据组织需求相互配合工作或者独立工作时的情况做了对比。

## <a name="azure-ad-ds-and-self-managed-ad-ds"></a>Azure AD DS 和自我管理型 AD DS

如果应用程序和服务需要访问 Kerberos 或 NTLM 等传统身份验证机制，可以通过两种方式在云中提供 Active Directory 域服务：

* 使用 Azure Active Directory 域服务 (AD DS) 创建的托管域  。 Microsoft 将创建并管理所需的资源。
* 使用虚拟机 (VM)、Windows Server 来宾 OS 和 Active Directory 域服务等传统资源创建和配置的自我管理域  。 然后，你需要继续管理这些资源。

使用 Azure AD DS 时，Microsoft 将为你部署和维护核心服务组件（托管域体验）。  你无需部署、管理、修补和保护 VM、Windows Server OS 或域控制器 (DC) 等组件的 AD DS 基础结构。

Azure AD DS 提供传统自我管理型 AD DS 环境的一小部分功能，这在一定程度上可以减轻设计和管理复杂性。 例如，无需设计和维护 AD 林、域、站点和复制链接。 对于在云中运行的、需要访问 Kerberos 或 NTLM 等传统身份验证机制的应用程序和服务，Azure AD DS 以极少量的管理开销提供托管域体验。

部署和运行自我管理型 AD DS 环境时，必须维护所有关联的基础结构和目录组件。 自行管理型 AD DS 环境会产生额外的维护开销，但你可以执行更多的任务，例如扩展架构或创建林信任。

为云中的应用程序和服务提供标识的自我管理型 AD DS 环境的常见部署模型包括：

* **独立的仅限云 AD DS** - 将 Azure VM 配置为域控制器，并创建独立的仅限云的 AD DS 环境。 此 AD DS 环境不会与本地 AD DS 环境集成。 使用一组不同的凭据来登录和管理云中的 VM。
* **资源林部署** - 将 Azure VM 配置为域控制器，并创建一个 AD DS 域作为现有林的一部分。 然后，配置与本地 AD DS 环境之间的信任关系。 其他 Azure VM 可通过域加入添加到云中的此资源林。 用户身份验证通过与本地 AD DS 环境建立的 VPN/ExpressRoute 连接运行。
* **将本地域扩展到 Azure** - 使用 VPN/ExpressRoute 连接将 Azure 虚拟网络连接到本地网络。 Azure VM 将连接到此 Azure 虚拟网络，因此可通过域加入添加到本地 AD DS 环境。
    * 一种替代方法是创建 Azure VM, 并从本地 AD DS 域将其提升为副本域控制器。 这些域控制器通过与本地 AD DS 环境建立的 VPN/ExpressRoute 连接复制。 本地 AD DS 域将有效地扩展到 Azure 中。

下表概述了组织可能需要的某些功能，以及托管型 Azure AD DS 域与自我管理型 AD DS 域之间的差异：

| **功能** | **Azure AD DS** | **自我管理型 AD DS** |
| ----------- |:---------------:|:----------------------:|
| **托管服务**                               | **&#x2713;** | **&#x2715;** |
| **安全部署**                            | **&#x2713;** | 管理员保护部署 |
| **DNS 服务器**                                    | **&#x2713;** （托管服务） |**&#x2713;** |
| **域或企业管理员特权** | **&#x2715;** | **&#x2713;** |
| **域加入**                                   | **&#x2713;** | **&#x2713;** |
| **使用 NTLM 和 Kerberos 进行域身份验证** | **&#x2713;** | **&#x2713;** |
| **Kerberos 约束委派**               | 基于资源 | 基于资源和基于帐户|
| **自定义 OU 结构**                           | **&#x2713;** | **&#x2713;** |
| **组策略**                                  | **&#x2713;** | **&#x2713;** |
| **架构扩展**                             | **&#x2715;** | **&#x2713;** |
| **AD 域/林信任**                     | **&#x2713;** （仅单向出站林信任） | **&#x2713;** |
| **安全 LDAP (LDAPS)**                           | **&#x2713;** | **&#x2713;** |
| **LDAP 读取**                                     | **&#x2713;** | **&#x2713;** |
| **LDAP 写入**                                    | **&#x2713;** （在托管域中） | **&#x2713;** |
| **地理分布式部署**                   | **&#x2715;** | **&#x2713;** |

## <a name="azure-ad-ds-and-azure-ad"></a>Azure AD DS 和 Azure AD

使用 Azure AD 可以管理组织所用的设备标识，以及控制这些设备对企业资源的访问。 用户还可将其个人设备（自带 (BYO) 模式）注册到 Azure AD，后者为设备提供一个标识。 然后，当用户登录到 Azure AD 并使用设备访问受保护的资源时，Azure AD 会对该设备进行身份验证。 可以使用 Microsoft Intune 等移动设备管理 (MDM) 软件来管理设备。 使用此管理功能可以限制受管且符合策略的设备对敏感资源的访问。

传统计算机和便携式计算机也可以加入 Azure AD。 此机制提供将个人设备注册到 Azure AD 所能得到的相同优势，例如，允许用户使用其企业凭据登录到设备。

已加入 Azure AD 的设备提供以下优势：

* 单一登录 (SSO) 到 Azure AD 保护的应用程序。
* 在不同的设备之间以符合企业策略的方式漫游用户设置。
* 使用企业凭据访问 Windows Store for Business。
* Windows Hello for Business。
* 限制从符合企业策略的设备对应用和资源的访问。

设备可以加入到包含本地 AD DS 环境的、使用或不使用混合部署的 Azure AD。 下表概述了常见的设备所有权模型，以及它们在一般情况下如何加入域：

| **设备类型**                                        | **设备平台**             | **机制**          |
|:----------------------------------------------------------| -------------------------------- | ---------------------- |
| 个人设备                                          | Windows 10、iOS、Android、Mac OS | 已注册 Azure AD    |
| 组织拥有的未加入本地 AD DS 的设备 | Windows 10                       | 已加入 Azure AD        |
| 组织拥有的已加入本地 AD DS 的设备  | Windows 10                       | 已加入混合 Azure AD |

在已加入或已注册 Azure AD 的设备上，使用基于 OAuth/OpenID Connect 的新式协议执行用户身份验证。 这些协议设计为通过 Internet 工作，因此非常适合用于移动方案，可让用户从任何位置访问企业资源。

借助已加入 Azure AD DS 的设备，应用程序可以使用 Kerberos 和 NTLM 协议进行身份验证，因此支持在执行直接迁移策略过程中迁移的、在 Azure VM 上运行的传统应用程序。 下表概述了设备的表示方式以及针对目录进行身份验证的差异：

| **方面**                      | **已加入 Azure AD**                                 | **已加入 Azure AD DS**                                                    |
|:--------------------------------| --------------------------------------------------- | ------------------------------------------------------------------------- |
| 设备控制方            | Azure AD                                            | Azure AD DS 托管域                                                |
| 在目录中的表示形式 | Azure AD 目录中的设备对象            | Azure AD DS 托管域中的计算机对象                        |
| 身份验证                  | 基于 OAuth/OpenID Connect 的协议              | Kerberos 和 NTLM 协议                                               |
| 管理                      | Intune 等移动设备管理 (MDM) 软件 | 组策略                                                              |
| 网络                      | 通过 Internet 工作                             | 必须连接到部署管理域的虚拟网络或与其对等互连 |
| 非常适合用于...                    | 最终用户移动设备或台式机设备                  | 在 Azure 中部署的服务器 VM                                              |

## <a name="next-steps"></a>后续步骤

若要开始使用 Azure AD DS，请[使用 Azure 门户创建一个 Azure AD DS 托管域][tutorial-create]。

<!-- INTERNAL LINKS -->
[manage-dns]: manage-dns.md
[deploy-kcd]: deploy-kcd.md
[custom-ou]: create-ou.md
[manage-gpos]: manage-group-policy.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-create]: tutorial-create-instance.md
