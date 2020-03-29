---
title: Azure AD 域服务的常见部署方案 |微软文档
description: 了解 Azure 活动目录域服务的一些常见方案和用例，以提供价值并满足业务需求。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: ac67ef64ca4850c6e805b5314ace856114d889a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77917224"
---
# <a name="common-use-cases-and-scenarios-for-azure-active-directory-domain-services"></a>Azure 活动目录域服务的常见用例和方案

Azure 活动目录域服务 （Azure AD DS） 提供托管域服务，如域联接、组策略、轻量级目录访问协议 （LDAP） 和 Kerberos / NTLM 身份验证。 Azure AD DS 与现有 Azure AD 租户集成，因此用户可使用其现有凭据登录。 使用这些域服务无需在云中部署、管理和修补域控制器，从而更顺畅地将本地资源提升到 Azure。

本文概述了 Azure AD DS 提供价值并满足这些需求的一些常见业务方案。

## <a name="secure-administration-of-azure-virtual-machines"></a>安全管理 Azure 虚拟机

为了允许您使用一组 AD 凭据，Azure 虚拟机 （VM） 可以加入到 Azure AD DS 托管域。 此方法可减少凭据管理问题，例如在每个 VM 上维护本地管理员帐户，或在环境之间单独维护帐户和密码。

也可以使用组策略管理和保护加入 Azure AD DS 托管域的 VM。 所需的安全基线可以应用于 VM，以便根据公司安全准则将其锁定。 例如，可以使用组策略管理功能来限制可在 VM 上启动的应用程序类型。

![以简化的方式管理 Azure 虚拟机](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

让我们看一个常见的示例方案。 随着服务器和其他基础架构的生命周期结束，Contoso 希望将当前托管在本地的应用程序迁移到云。 他们当前的 IT 标准要求托管公司应用程序的服务器必须使用组策略加入和管理域。 Contoso 的 IT 管理员更愿意将 Azure 中部署的 VM 域域化，以便简化管理，因为用户可以使用其公司凭据登录。 加入域后，还可以将 VM 配置为使用组策略对象 （GPO） 符合所需的安全基线。 Contoso 不希望在 Azure 中部署、监视和管理自己的域控制器。

Azure AD DS 非常适合此用例。 Azure AD DS 托管域允许您加入 VM、使用一组凭据以及应用组策略。 作为托管域，您不必自己配置和维护域控制器。

### <a name="deployment-notes"></a>部署注意事项

以下部署注意事项适用于此示例用例：

* 默认情况下，Azure AD DS 托管域使用单个平面组织单元 （OU） 结构。 所有加入域的 VM 都在单个 OU 中。 如果需要，您可以创建自定义 O。
* Azure AD DS 对用户和计算机容器使用内置 GPO。 对于其他控件，您可以创建自定义 GPO 并将其定位到自定义 O。
* Azure AD DS 支持基本 AD 计算机对象架构。 无法扩展计算机对象的架构。

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-bind-authentication"></a>使用 LDAP 绑定身份验证的提升和移动本地应用程序

作为示例方案，Contoso 具有多年前从 ISV 购买的本地应用程序。 ISV 当前处于维护模式，请求对应用程序的更改非常昂贵。 此应用程序具有基于 Web 的前端，该前端使用 Web 窗体收集用户凭据，然后通过执行到本地 AD DS 环境的 LDAP 绑定对用户进行身份验证。

![LDAP 绑定](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso 希望将此应用程序迁移到 Azure。 应用程序应继续以正常方式工作，无需进行任何更改。 此外，用户应该能够使用其现有的公司凭据进行身份验证，而无需进行其他培训。 对于运行应用程序的最终用户，它应该是透明的。

对于此方案，Azure AD DS 允许应用程序作为身份验证过程的一部分执行 LDAP 绑定。 旧式本地应用程序可以提升和移入 Azure，并继续无缝地对用户进行身份验证，而无需更改配置或用户体验。

### <a name="deployment-notes"></a>部署注意事项

以下部署注意事项适用于此示例用例：

* 确保应用程序不需要修改/写入目录。 不支持对 Azure AD DS 托管域的 LDAP 写入访问。
* 不能针对 Azure AD DS 托管域直接更改密码。 最终用户可以使用 Azure AD 的自助密码更改机制或针对本地目录更改其密码。 然后，这些更改将自动同步并在 Azure AD DS 托管域中可用。

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-read-to-access-the-directory"></a>使用 LDAP 读取访问目录的提升和移动本地应用程序

与前面的示例方案一样，假设 Contoso 具有近 10 年前开发的本地业务线 （LOB） 应用程序。 此应用程序具有目录感知功能，旨在使用 LDAP 从 AD DS 读取有关用户的信息/属性。 应用程序不修改属性或以其他方式写入目录。

Contoso 希望将此应用程序迁移到 Azure 并停用当前承载此应用程序的老化的本地硬件。 无法重写应用程序以使用现代目录 API，如基于 REST 的 Microsoft 图形 API。 需要一个提升和移动选项，其中应用程序可以迁移到云中运行，而无需修改代码或重写应用程序。

为了帮助执行此方案，Azure AD DS 允许应用程序针对托管域执行 LDAP 读取，以获得所需的属性信息。 应用程序不需要重写，因此，在 Azure 中提升和移动允许用户继续使用应用程序，而无需意识到其运行位置有变化。

### <a name="deployment-notes"></a>部署注意事项

以下部署注意事项适用于此示例用例：

* 确保应用程序不需要修改/写入目录。 不支持对 Azure AD DS 托管域的 LDAP 写入访问。
* 确保应用程序不需要自定义/扩展的活动目录架构。 Azure AD DS 不支持架构扩展。

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure"></a>将本地服务或守护进程应用程序迁移到 Azure

某些应用程序包括多个层，其中其中一个层需要对后端层（如数据库）执行经过身份验证的调用。 在这些方案中，AD 服务帐户通常使用。 将应用程序提升到 Azure 时，Azure AD DS 允许您继续以相同的方式使用服务帐户。 您可以选择使用从本地目录同步到 Azure AD 的相同服务帐户，或者创建自定义 OU，然后在该 OU 中创建单独的服务帐户。 使用任一方法，应用程序继续以相同的方式对其他层和服务进行身份验证调用。

![使用 WIA 的服务帐户](./media/active-directory-domain-services-scenarios/wia-service-account.png)

在此示例中，Contoso 具有一个自定义的软件保管库应用程序，该应用程序包括 Web 前端、SQL 服务器和后端 FTP 服务器。 使用服务帐户的 Windows 集成身份验证对 FTP 服务器的 Web 前端进行身份验证。 该 Web 前端设置为以服务帐户的身份运行。 后端服务器配置为通过服务帐户授权访问 Web 前端。 Contoso 不希望在云中部署和管理自己的域控制器 VM 以将此应用程序移动到 Azure。

对于此方案，托管 Web 前端、SQL 服务器和 FTP 服务器的服务器可以迁移到 Azure VM 并加入到 Azure AD DS 托管域。 然后，VM 可以在其本地目录中使用相同的服务帐户来用于应用的身份验证目的，该帐户使用 Azure AD 连接通过 Azure AD 同步。

### <a name="deployment-notes"></a>部署注意事项

以下部署注意事项适用于此示例用例：

* 确保应用程序使用用户名和密码进行身份验证。 Azure AD DS 不支持基于证书或智能卡的身份验证。
* 不能针对 Azure AD DS 托管域直接更改密码。 最终用户可以使用 Azure AD 的自助密码更改机制或针对本地目录更改其密码。 然后，这些更改将自动同步并在 Azure AD DS 托管域中可用。

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Azure 中的 Windows 服务器远程桌面服务部署

可以使用 Azure AD DS 向 Azure 中部署的远程桌面服务器提供托管域服务。 有关此部署方案的详细信息，请参阅[如何将 Azure AD 域服务与 RDS 部署集成][windows-rds]。

## <a name="domain-joined-hdinsight-clusters"></a>已加入域的 HDInsight 群集

您可以设置 Azure HDInsight 群集，该群集已连接到启用 Apache Ranger 的 Azure AD DS 托管域。 您可以通过 Apache Ranger 创建和应用 Hive 策略，并允许用户（如数据科学家）使用基于 ODBC 的工具（如 Excel 或 Tableau）连接到 Hive。 我们继续努力向加入域的 HDInsight 添加其他工作负载，如 HBase、Spark 和 Storm。

有关此部署方案的详细信息，请参阅[如何配置加入域的 HDInsight 群集][hdinsight]

## <a name="next-steps"></a>后续步骤

要开始，[请创建和配置 Azure 活动目录域服务实例][tutorial-create-instance]

<!-- INTERNAL LINKS -->
[hdinsight]: ../hdinsight/domain-joined/apache-domain-joined-configure.md
[tutorial-create-instance]: tutorial-create-instance.md

<!-- EXTERNAL LINKS -->
[windows-rds]: /windows-server/remote/remote-desktop-services/rds-azure-adds
