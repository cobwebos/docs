---
title: Azure Active Directory 域服务概述 | Microsoft 文档
description: 在本概述中，了解 Azure Active Directory 域服务提供的内容，以及如何在组织中用其向云中的应用程序和服务提供标识服务。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 08/14/2020
ms.author: iainfou
ms.custom: contperfq1
ms.openlocfilehash: 2255f2193378d0cc3611680c6aa5f8e0837e99aa
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "88245169"
---
# <a name="what-is-azure-active-directory-domain-services"></a>什么是 Azure Active Directory 域服务？

Azure Active Directory 域服务 (AD DS) 提供托管域服务，例如域加入、组策略、轻型目录访问协议 (LDAP) 和 Kerberos/NTLM 身份验证。 无需在云中部署、管理和修补域控制器 (DC) 即可使用这些域服务。

Azure AD DS 托管域使你能够在云中或你不希望目录查找始终返回到本地 AD DS 环境的位置，运行无法使用现代身份验证方法的旧版应用程序。 你可以将这些旧版应用程序从本地环境直接迁移到托管域，而无需在云中管理 AD DS 环境。

Azure AD DS 与现有的 Azure AD 租户集成。 通过此集成，用户可以使用其现有凭据登录到与托管域相连的服务和应用程序。 还可以使用现有组和用户帐户来保护对资源的访问。 这些功能可更顺畅地将本地资源直接迁移到 Azure。

> [!div class="nextstepaction"]
> [若要开始，请使用 Azure 门户创建 Azure AD DS 托管域][tutorial-create]

## <a name="how-does-azure-ad-ds-work"></a>Azure AD DS 如何工作？

创建 Azure AD DS 托管域时，需定义唯一的命名空间。 该命名空间为域名，例如“aaddscontoso.com”。 两个 Windows Server 域控制器 (DC) 随即部署到选定的 Azure 区域中。 DC 的这种部署称为副本集。

你不需要管理、配置或更新这些 DC。 Azure 平台将 DC 作为托管域的一部分进行处理，包括备份。

托管域配置为从 Azure AD 执行单向同步，以提供对一组集中用户、组和凭据的访问。 你可以直接在托管域中创建资源，但它们不会同步回 Azure AD。 然后，Azure 中连接到该托管域的应用程序、服务和 VM 便可使用常见 AD DS 功能，如域加入、组策略、LDAP 和 Kerberos/NTLM 身份验证。

在具有本地 AD DS 环境的混合环境中，[Azure AD Connect][azure-ad-connect] 会将标识信息与 Azure AD 同步，后者随后将同步到托管域。

![使用 AD Connect 将 Azure AD 域服务与 Azure AD 和本地 AD DS 同步](./media/active-directory-domain-services-design-guide/sync-topology.png)

Azure AD DS 从 Azure AD 中复制标识信息，因此，它适用于仅限云的 Azure AD 租户，或与本地 AD DS 环境同步的租户。 对于这两种环境，都存在相同的一组 Azure AD DS 功能。

* 如果有现有的本地 AD DS 环境，则可以同步用户帐户信息，为用户提供一致的标识。 若要了解详细信息，请参阅[如何在托管域中同步对象和凭据][synchronization]。
* 对于仅限云的环境，则不需要传统的本地 AD DS 环境来使用 Azure AD DS 的集中标识服务。

可以扩展托管域，使每个 Azure AD 租户具有多个副本集。 可以将副本集添加到任何支持 Azure AD DS 的 Azure 区域中的任何对等互连虚拟网络。 如果某个 Azure 区域处于离线状态，则不同 Azure 区域中的其他副本集可为旧版应用程序提供地理灾难恢复。 副本集目前处于预览状态。 有关详细信息，请参阅[托管域的副本集概念和功能][concepts-replica-sets]。

以下视频概述了 Azure AD DS 如何与应用程序和工作负载集成以在云中提供标识服务：

<br />

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

若要查看运行中的 Azure AD DS 部署方案，你可以探索以下示例：

* [混合组织的 Azure AD DS](scenarios.md#azure-ad-ds-for-hybrid-organizations)
* [仅限云的组织的 Azure AD DS](scenarios.md#azure-ad-ds-for-cloud-only-organizations)

## <a name="azure-ad-ds-features-and-benefits"></a>Azure AD DS 功能和优点

为了向云中的应用程序和 VM 提供标识服务，Azure AD DS 与域加入、安全 LDAP (LDAPS)、组策略、DNS 管理以及 LDAP 绑定和读取支持等操作的传统 AD DS 环境完全兼容。 LDAP 写入支持适用于在托管域中创建的对象，但不适用于从 Azure AD 同步的资源。

若要了解有关标识选项的详细信息，[请将 Azure AD DS 与 Azure AD、Azure VM 上的 AD DS 和本地 AD DS 进行比较][compare]。

Azure AD DS 的以下功能简化了部署和管理操作：

* **简化的部署体验：** 在 Azure 门户中使用单个向导为 Azure AD 租户启用 Azure AD DS。
* **与 Azure AD 集成：** 可从 Azure AD 租户自动获得用户帐户、组成员身份和凭据。 新用户、组或者对 Azure AD 租户或本地 AD DS 环境中的属性所做的更改会自动同步到 Azure AD DS。
    * 链接到 Azure AD 的外部目录中的帐户不可用于 Azure AD DS。 凭据不可用于这些外部目录，因此无法同步到托管域。
* **使用企业凭据/密码：** Azure AD DS 中的用户密码与 Azure AD 租户中的用户密码相同。 用户可以使用其企业凭据将计算机加入域，以交互方式或通过远程桌面登录，以及针对托管域进行身份验证。
* **NTLM 和 Kerberos 身份验证：** 借助对 NTLM 和 Kerberos 身份验证的支持，可以部署依赖于 Windows 集成身份验证的应用程序。
* **高可用性：** Azure AD DS 包括多个域控制器，这些域控制器为托管域提供高可用性。 这种高可用性保证了服务运行时间和故障恢复能力。
    * 在支持 [Azure 可用性区域][availability-zones]的区域中，这些域控制器也跨区域分布，以提升复原能力。
    * 如果某个 Azure 区域处于离线状态，则[副本集][concepts-replica-sets]也可用于为旧版应用程序提供地理灾难恢复。

托管域的一些关键方面包括：

* 托管域是独立的域。 它不是本地域的扩展。
    * 如果需要，你可以创建从 Azure AD DS 到本地 AD DS 环境的单向出站林信任。 有关详细信息，请参阅 [Azure AD DS 的资源林概念和功能][ forest-trusts]。
* 你的 IT 团队无需管理、修补或监视此托管域的域控制器。

对于运行本地 AD DS 的混合环境，无需管理到托管域的 AD 复制。 本地目录中的用户帐户、组成员身份和凭据通过 [Azure AD Connect][azure-ad-connect] 同步到 Azure AD。 这些用户帐户、组成员身份和凭据在托管域中自动可用。

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure AD DS 与其他标识解决方案以及同步的工作原理，请参阅以下文章：

* [将 Azure AD DS 与 Azure AD、Azure VM 上的 Active Directory 域服务和本地 Active Directory 域服务进行比较][compare]
* [了解如何将 Azure AD 域服务与 Azure AD 目录同步][synchronization]
* 若要了解如何管理托管域，请参阅 [Azure AD DS 中用户帐户、密码和管理的管理概念][administration-concepts]。

若要开始操作，请[使用 Azure 门户创建托管域][tutorial-create]。

<!-- INTERNAL LINKS -->
[compare]: compare-identity-solutions.md
[synchronization]: synchronization.md
[tutorial-create]: tutorial-create-instance.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md
[password-hash-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md
[availability-zones]: ../availability-zones/az-overview.md
[forest-trusts]: concepts-resource-forest.md
[administration-concepts]: administration-concepts.md
[synchronization]: synchronization.md
[concepts-replica-sets]: concepts-replica-sets.md
