---
title: Azure AD 域服务的常见部署场景 | Microsoft Docs
description: 了解 Azure Active Directory 域服务提供价值和满足业务需求的一些常见场景和用例。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: iainfou
ms.openlocfilehash: a27d97e7e030da216b2296f11b6876ccf28c8ad0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88722749"
---
# <a name="common-use-cases-and-scenarios-for-azure-active-directory-domain-services"></a>Azure Active Directory 域服务的常见用例和场景

Azure Active Directory 域服务 (Azure AD DS) 提供托管域服务，例如域加入、组策略、轻型目录访问协议 (LDAP) 和 Kerberos/NTLM 身份验证。 Azure AD DS 与现有 Azure AD 租户集成，因此用户可使用其现有凭据登录。 可以使用这些域服务，而无需在云中部署、管理和修补域控制器，从而更顺畅地将本地资源直接迁移到 Azure。

本文概述 Azure AD DS 提供价值并满足这些需求的一些常见业务场景。

## <a name="common-ways-to-provide-identity-solutions-in-the-cloud"></a>在云中提供标识解决方案的常见方法

将现有工作负载迁移到云时，目录感知的应用程序可以使用 LDAP 对本地 AD DS 目录进行读取或写入访问。 Windows Server 上运行的应用程序通常部署在已加入域的虚拟机 (VM) 上，因此可以使用组策略安全地对其进行管理。 若要对最终用户进行身份验证，应用程序还可能依赖于 Windows 集成的身份验证，如 Kerberos 或 NTLM 身份验证。

IT 管理员通常使用以下某一解决方案为 Azure 中运行的应用程序提供标识服务：

* 在 Azure 中运行的工作负载与本地 AD DS 环境之间配置站点到站点 VPN 连接。
    * 然后，本地域控制器通过 VPN 连接提供身份验证。
* 使用 Azure 虚拟机 (VM) 创建副本域控制器来从本地扩展 AD DS 域/林。
    * 在 Azure VM 上运行的域控制器提供身份验证，并在本地 AD DS 环境之间复制目录信息。
* 使用 Azure VM 上运行的域控制器在 Azure 中部署独立的 AD DS 环境。
    * 在 Azure VM 上运行的域控制器提供身份验证，但是没有复制自本地 AD DS 环境的目录信息。

借助这些方法，与本地目录的 VPN 连接使得应用程序容易发生暂时性网络问题或中断。 如果使用 Azure 中的 VM 部署域控制器，IT 团队必须管理 VM，然后对其进行保护、修补、监视、备份和故障排除。

Azure AD DS 提供了替代方法，由此能够创建返回到本地 AD DS 环境的 VPN 连接，或在 Azure 中运行和管理 VM 以提供标识服务。 作为托管服务，Azure AD DS 降低了为混合环境和仅限云环境创建集成标识解决方案的复杂性。

> [!div class="nextstepaction"]
> [将 Azure AD DS 与 Azure VM 或本地上的 Azure AD 和自托管 AD DS 进行比较][compare]

## <a name="azure-ad-ds-for-hybrid-organizations"></a>混合组织的 Azure AD DS

许多组织都运行有一个包含云和本地应用程序工作负载的混合基础结构。 按照直接迁移策略迁移到 Azure 的旧版应用程序可能使用传统的 LDAP 连接来提供标识信息。 若要支持此混合基础结构，可以将本地 AD DS 环境中的标识信息同步到 Azure AD 租户。 然后，Azure AD DS 使用标识源在 Azure 中提供这些旧版应用程序，而无需配置和管理应用程序与本地目录服务的连接。

让我们看一个 Litware Corporation 的示例，这是一个同时运行本地和 Azure 资源的混合组织：

![适用于包含本地同步的混合组织的 Azure Active Directory 域服务](./media/overview/synced-tenant.png)

* 需要域服务的应用程序和服务器工作负载部署在 Azure 的虚拟网络中。
    * 这可能包括迁移到 Azure（作为直接迁移策略的一部分）的旧版应用程序。
* 为了将标识信息从其本地目录同步到其 Azure AD 租户，Litware Corporation 部署了 [Azure AD Connect][azure-ad-connect]。
    * 同步的标识信息包括用户帐户和组成员身份。
* Litware 的 IT 团队在此虚拟网络中或在对等互连的虚拟网络中为其 Azure AD 租户启用 Azure AD DS。
* 然后，在 Azure 虚拟网络中部署的应用程序和 VM 便可使用 Azure AD DS 功能，如域加入、LDAP 读取、LDAP 绑定、NTLM、Kerberos 身份验证以及组策略等。

> [!IMPORTANT]
> 安装和配置的 Azure AD Connect 应仅用于与本地 AD DS 环境同步。 不支持在托管域中安装 Azure AD Connect 以将对象同步回 Azure AD。

## <a name="azure-ad-ds-for-cloud-only-organizations"></a>仅限云的组织的 Azure AD DS

仅限云的 Azure AD 租户没有本地标识源。 例如，用户帐户和组成员身份是直接在 Azure AD 中创建和管理的。

现在，让我们看看 Contoso 的一个示例，这是一个使用 Azure AD 来管理标识的纯云组织。 所有用户标识、其凭据和组成员身份都在 Azure AD 中进行创建和管理。 Azure AD Connect 未配置任何其他内容来同步本地目录中的任何标识信息。

![仅限云的组织的 Azure Active Directory 域服务（无本地同步）](./media/overview/cloud-only-tenant.png)

* 需要域服务的应用程序和服务器工作负载部署在 Azure 的虚拟网络中。
* Contoso 的 IT 团队在此虚拟网络中或在对等互连的虚拟网络中为其 Azure AD 租户启用 Azure AD DS。
* 然后，在 Azure 虚拟网络中部署的应用程序和 VM 便可使用 Azure AD DS 功能，如域加入、LDAP 读取、LDAP 绑定、NTLM、Kerberos 身份验证以及组策略等。

## <a name="secure-administration-of-azure-virtual-machines"></a>安全管理 Azure 虚拟机

为了使你能够使用一组 AD 凭据，可以将 Azure 虚拟机 (VM) 加入 Azure AD DS 托管域。 此方法可减少凭据管理问题，例如维护每个 VM 上的本地管理员帐户或环境之间的帐户和密码。

还可以使用组策略来管理和保护已加入托管域的 VM。 可将所需的安全基准应用到 VM，根据企业安全指导原则锁定这些 VM。 例如，可以使用组策略管理功能来限制可在 VM 上启动的应用程序类型。

![以简化的方式管理 Azure 虚拟机](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

我们来看一下常见示例场景。 由于服务器和其他基础结构已达到生命周期，Contoso 想要将目前托管在本地的应用程序迁移到云中。 他们当前的 IT 标准强制要求托管企业应用程序的服务器必须加入域，并使用组策略进行管理。

Contoso 的 IT 管理员更希望将 Azure 中部署的 VM 加入域，这样用户随后便可使用其企业凭据登录，从而使管理更轻松。 加入域时，还可以根据所需的安全基准，使用组策略对象 (GPO) 配置 VM。 Contoso 不希望在 Azure 中部署、监视和管理自己的域控制器。

Azure AD DS 非常适合这种情况。 通过托管域，你可以将 VM 加入域、使用一组凭据并应用组策略。 因为它是托管域，因此无需配置和维护自己的域控制器。

### <a name="deployment-notes"></a>部署注意事项

以下部署注意事项适用于此示例用例：

* 默认情况下，托管域使用单个平面组织单位 (OU) 结构。 所有已加入域的 VM 均位于单个 OU 中。 如果需要，可以创建[自定义 OU][custom-ou]。
* Azure AD DS 使用分别适用于用户和计算机容器的内置 GPO。 若要进行其他控制，可以[创建自定义 GPO][create-gpo]，并将其目标设为自定义 OU。
* Azure AD DS 支持基本 AD 计算机对象架构。 无法扩展计算机对象的架构。

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-bind-authentication"></a>直接迁移使用 LDAP 绑定身份验证的本地应用程序

有一个示例场景，多年前，Contoso 从一家 ISV 采购了一个本地应用程序。 该应用程序目前已被 ISV 置于维护模式，请求对该应用程序进行更改将代价不菲，让人难以负担。 此应用程序有一个基于 Web 的前端，该前端使用 Web 表单收集用户凭据，并向本地 AD DS 环境执行 LDAP 绑定来验证用户的身份。

![LDAP 绑定](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso 希望将此应用程序迁移到 Azure。 此应用程序应继续照常工作，无需进行任何更改。 此外，用户应该能够使用其现有的企业凭据进行身份验证，且无需其他培训。 应用程序运行的位置应向最终用户公开。

对于这种情况，Azure AD DS 允许应用程序在身份验证过程中执行 LDAP 绑定。 旧版本地应用程序可以直接迁移到 Azure，并继续无缝验证用户身份，而无需更改任何配置或用户体验。

### <a name="deployment-notes"></a>部署注意事项

以下部署注意事项适用于此示例用例：

* 确保应用程序无需在目录中修改/写入数据。 不支持对托管域进行 LDAP 写入访问。
* 无法直接针对托管域更改密码。 最终用户可以使用 [Azure AD 的自助密码更改机制][sspr]或针对本地目录更改其密码。 随后这些更改会自动同步，并出现在托管域中。

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-read-to-access-the-directory"></a>直接迁移使用 LDAP 读取访问目录的本地应用程序

与上一个示例场景一样，我们假设大约十年前，Contoso 开发了一个本地业务线 (LOB) 应用程序。 此应用程序可识别目录，并旨在使用 LDAP 从 AD DS 读取有关用户的信息/属性。 该应用程序不会修改属性，也不会在目录中写入数据。

Contoso 想要将此应用程序迁移到 Azure，并淘汰目前托管此应用程序的已过时本地硬件。 无法重新编写该应用程序，让其使用现代目录 API，例如基于 REST 的 Microsoft 图形 API。 直接迁移选项可以满足需要，因为它可以在不修改代码或重新编写应用程序的情况下，将应用程序迁移到云中运行。

为对这种场景提供帮助，Azure AD DS 允许应用程序针对托管域执行 LDAP 读取，以获取所需的属性信息。 无需重新编写应用程序，通过直接迁移到 Azure，用户可以继续使用该应用，而不会意识到其运行位置发生了变化。

### <a name="deployment-notes"></a>部署注意事项

以下部署注意事项适用于此示例用例：

* 确保应用程序无需在目录中修改/写入数据。 不支持对托管域进行 LDAP 写入访问。
* 确保应用程序不需要自定义/扩展的 Active Directory 架构。 Azure AD DS 不支持架构扩展。

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure"></a>将本地服务或守护程序应用程序迁移到 Azure

某些应用程序包含多个层，其中有一个层需要对后端层（如数据库）执行经身份验证的调用。 在这些情况下，通常会使用 AD 服务帐户。 将应用程序直接迁移到 Azure 时，通过 Azure AD DS，你能够以相同的方式继续使用服务帐户。 可以选择使用已从本地目录同步到 Azure AD 的相同服务帐户，也可以创建自定义 OU，然后在该 OU 中创建一个单独的服务帐户。 使用其中任意一种方法，应用程序都将继续以相同的方式对其他层和服务进行经身份验证的调用。

![使用 WIA 的服务帐户](./media/active-directory-domain-services-scenarios/wia-service-account.png)

在此示例场景中，Contoso 有一个定制的软件保管库应用程序，其中包含 Web 前端、SQL 服务器和后端 FTP 服务器。 使用服务帐户的 Windows 集成身份验证在 FTP 服务器中对 Web 前端进行身份验证。 该 Web 前端设置为以服务帐户的身份运行。 后端服务器配置为通过服务帐户授权访问 Web 前端。 Contoso 不希望在云中部署和管理自己的域控制器 VM，从而将此应用程序迁移到 Azure。

对于此场景，托管 Web 前端的服务器、SQL 服务器和 FTP 服务器的服务器可以迁移到 Azure VM 并加入托管域。 然后，这些 VM 可以使用其本地目录中的相同服务帐户，以对应用进行身份验证，这将通过 Azure AD 使用 Azure AD Connect 进行同步。

### <a name="deployment-notes"></a>部署注意事项

以下部署注意事项适用于此示例用例：

* 确保应用程序使用用户名和密码进行身份验证。 Azure AD DS 不支持基于证书或智能卡的身份验证。
* 无法直接针对托管域更改密码。 最终用户可以使用 [Azure AD 的自助密码更改机制][sspr]或针对本地目录更改其密码。 随后这些更改会自动同步，并出现在托管域中。

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Azure 中的 Windows Server 远程桌面服务部署

可以使用 Azure AD DS 向 Azure 中部署的远程桌面服务器提供托管域服务。

有关此部署场景的详细信息，请参阅[如何将 Azure AD 域服务与 RDS 部署集成][windows-rds]。

## <a name="domain-joined-hdinsight-clusters"></a>已加入域的 HDInsight 群集

可以设置已加入启用了 Apache Ranger 的托管域的 Azure HDInsight 群集。 可以通过 Apache Ranger 创建并应用 Hive 策略，并允许数据科学家等用户使用基于 ODBC 的工具（如 Excel、Tableau）连接到 Hive。 我们会继续努力将其他工作负载（如 HBase、Spark 和 Storm）添加到已加入域的 HDInsight。

有关此部署方案的详细信息，请参阅[如何配置已加入域的 HDInsight 群集][hdinsight]

## <a name="next-steps"></a>后续步骤

若要开始使用，请[创建并配置 Azure Active Directory 域服务托管域][tutorial-create-instance]。

<!-- INTERNAL LINKS -->
[hdinsight]: ../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md
[tutorial-create-instance]: tutorial-create-instance.md
[custom-ou]: create-ou.md
[create-gpo]: manage-group-policy.md
[sspr]: ../active-directory/authentication/overview-authentication.md#self-service-password-reset
[compare]: compare-identity-solutions.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md

<!-- EXTERNAL LINKS -->
[windows-rds]: /windows-server/remote/remote-desktop-services/rds-azure-adds