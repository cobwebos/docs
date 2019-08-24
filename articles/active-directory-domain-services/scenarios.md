---
title: Azure AD 域服务的常见部署方案 |Microsoft Docs
description: 了解 Azure Active Directory 域服务提供价值并满足业务需求的一些常见方案和用例。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: iainfou
ms.openlocfilehash: 6f81bc2ccf11cbcc3621dc1149879864c88cf0cf
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69980515"
---
# <a name="common-use-cases-and-scenarios-for-azure-active-directory-domain-services"></a>Azure Active Directory 域服务的常见用例和方案

Azure Active Directory 域服务 (Azure AD DS) 提供了托管域服务, 例如域加入、组策略、LDAP 和 Kerberos/NTLM 身份验证。 Azure AD DS 与现有 Azure AD 租户集成，因此用户可使用其现有凭据登录。 你可以使用这些域服务, 而无需在云中部署、管理和修补域控制器, 这可提供更流畅的本地资源直接迁移到 Azure。

本文概述 Azure AD DS 提供价值并满足这些需求的常见业务方案。

## <a name="secure-administration-of-azure-virtual-machines"></a>Azure 虚拟机的安全管理

为了使你能够使用单个 AD 凭据集, 可以将 Azure 虚拟机 (Vm) 加入到 Azure AD DS 托管域。 此方法减少了凭据管理问题, 例如在每个 VM 上维护本地管理员帐户或在环境之间单独的帐户和密码。

加入到 Azure AD DS 托管域的 Vm 也可以使用组策略进行管理和保护。 所需的安全基准可应用于 Vm, 以根据公司安全准则锁定它们。 例如, 你可以使用组策略管理功能来限制可在 VM 上启动的应用程序的类型。

![以简化的方式管理 Azure 虚拟机](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

让我们看一个常见的示例方案。 随着服务器和其他基础结构的生存期, Contoso 希望将当前托管在本地的应用程序移到云中。 其当前 IT 标准要求托管企业应用程序的服务器必须使用组策略进行域加入和管理。 Contoso 的 IT 管理员喜欢在 Azure 中部署的域加入 Vm, 使管理工作更加轻松, 因为用户可以使用其公司凭据登录。 在已加入域的情况下, 还可以将 Vm 配置为符合使用组策略所需的安全基准。 Contoso 不希望在 Azure 中部署、监视和管理他们自己的域控制器。

Azure AD DS 适用于这种情况。 Azure AD DS 托管域允许域加入 Vm, 使用一组凭据, 并应用组策略。 作为托管域, 无需自行配置和维护域控制器。

### <a name="deployment-notes"></a>部署说明

以下部署注意事项适用于此示例用例:

* 默认情况下, Azure AD DS 托管域使用单个平面组织单位 (OU) 结构。 所有已加入域的 Vm 均位于单个 OU 中。 如果需要, 可以创建自定义 Ou。
* Azure AD DS 使用适用于用户和计算机容器的内置 GPO。 对于其他控制, 可以创建自定义 Gpo 并将其定位到自定义 Ou。
* Azure AD DS 支持基本 AD 计算机对象架构。 不能扩展计算机对象的架构。

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-bind-authentication"></a>使用 LDAP 绑定身份验证的直接迁移本地应用程序

作为示例方案, Contoso 拥有从多年前购买的 ISV 的本地应用程序。 应用程序当前处于维护模式, 由 ISV 并请求对应用程序所做的更改。 此应用程序具有基于 web 的前端, 它使用 web 窗体收集用户凭据, 然后通过对本地 AD DS 环境执行 LDAP 绑定来对用户进行身份验证。

![LDAP 绑定](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso 想要将此应用程序迁移到 Azure。 应用程序应继续按原样工作, 无需进行任何更改。 此外, 用户应该能够使用其现有的公司凭据进行身份验证, 而无需额外的培训。 它对于运行应用程序的最终用户应该是透明的。

对于这种情况, Azure AD DS 允许应用程序在身份验证过程中执行 LDAP 绑定。 传统的本地应用程序可以将其迁移到 Azure, 并继续无缝验证用户身份, 而无需更改配置或用户体验。

### <a name="deployment-notes"></a>部署说明

以下部署注意事项适用于此示例用例:

* 请确保应用程序无需修改/写入目录。 不支持 LDAP 写入 Azure AD DS 托管域的权限。
* 不能直接对 Azure AD DS 托管域更改密码。 最终用户可以使用 Azure AD 的自助密码更改机制或针对本地目录更改其密码。 然后, 这些更改会自动同步, 并在 Azure AD DS 托管域中可用。

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-read-to-access-the-directory"></a>使用 LDAP 读取访问目录的直接迁移本地应用程序

与前面的示例方案类似, 我们假设 Contoso 有一个本地业务线 (LOB) 应用程序, 该应用程序在将近十年前开发。 此应用程序是目录感知的, 其设计目的是使用轻型目录访问协议 (LDAP) 来读取有关来自 AD DS 的用户的信息/属性。 应用程序不会修改属性或以其他方式写入目录。

Contoso 想要将此应用程序迁移到 Azure, 并停用当前托管该应用程序的本地过时硬件。 无法重写应用程序以使用新式目录 Api, 例如基于 REST 的 Azure AD 图形 API。 无需修改代码或重写应用程序, 即可将应用程序迁移到云中运行, 而无需使用提升的选项。

为帮助实现此方案, Azure AD DS 允许应用程序对托管域执行 LDAP 读取, 以获取所需的属性信息。 无需重写应用程序, 因此, 通过直接迁移到 Azure, 用户可以继续使用该应用, 而不会意识到它运行的位置发生了更改。

### <a name="deployment-notes"></a>部署说明

以下部署注意事项适用于此示例用例:

* 请确保应用程序无需修改/写入目录。 不支持 LDAP 写入 Azure AD DS 托管域的权限。
* 请确保应用程序不需要自定义/扩展的 Active Directory 架构。 Azure AD DS 不支持架构扩展。

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure"></a>将本地服务或后台程序应用程序迁移到 Azure

某些应用程序包括多个层, 其中一层需要对后端层 (如数据库) 执行身份验证调用。 通常在这些情况下使用 AD 服务帐户。 当你将应用程序直接迁移到 Azure 中时, Azure AD DS 使你能够以相同的方式继续使用服务帐户。 你可以选择使用与本地目录同步的服务帐户来 Azure AD 或创建自定义 OU, 然后在该 OU 中创建单独的服务帐户。 对于这两种方法, 应用程序将继续以相同的方式运行, 以对其他层和服务进行身份验证调用。

![使用 WIA 的服务帐户](./media/active-directory-domain-services-scenarios/wia-service-account.png)

在此示例方案中, Contoso 提供了一个自定义的软件保管库应用程序, 该应用程序包含一个 web 前端、一个 SQL server 和一个后端 FTP 服务器。 使用服务帐户的 Windows 集成的身份验证将向 FTP 服务器验证 web 前端。 该 Web 前端设置为以服务帐户的身份运行。 后端服务器配置为通过服务帐户授权访问 Web 前端。 Contoso 不希望在云中部署和管理他们自己的域控制器 Vm, 以将此应用程序迁移到 Azure。

对于此方案, 托管 web 前端、SQL server 和 FTP 服务器的服务器可以迁移到 Azure Vm 并加入 Azure AD DS 托管域。 然后, Vm 可以在其本地目录中使用相同的服务帐户, 以用于应用的身份验证目的, 该帐户通过 Azure AD 使用 Azure AD Connect 进行同步。

### <a name="deployment-notes"></a>部署说明

以下部署注意事项适用于此示例用例:

* 请确保应用程序使用用户名和密码进行身份验证。 Azure AD DS 不支持基于证书或智能卡的身份验证。
* 不能直接对 Azure AD DS 托管域更改密码。 最终用户可以使用 Azure AD 的自助密码更改机制或针对本地目录更改其密码。 然后, 这些更改会自动同步, 并在 Azure AD DS 托管域中可用。

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Azure 中的 Windows Server 远程桌面服务部署

你可以使用 Azure AD DS 向 Azure 中部署的远程桌面服务器提供托管域服务。 有关此部署方案的详细信息, 请参阅[如何将 Azure AD 域服务与 RDS 部署集成][windows-rds]。

## <a name="domain-joined-hdinsight-clusters-preview"></a>已加入域的 HDInsight 群集 (预览版)

可以设置一个 Azure HDInsight 群集, 该群集已加入启用了 Apache Ranger 的 Azure AD DS 托管域。 此功能目前处于预览状态。 可以通过 Apache Ranger 创建和应用 Hive 策略, 并允许用户 (如数据科学家) 使用 Excel 或 Tableau 等基于 ODBC 的工具连接到 Hive。 我们继续努力向加入域的 HDInsight 添加其他工作负荷, 如 HBase、Spark 和风暴。

有关此部署方案的详细信息, 请参阅[如何配置已加入域的 HDInsight 群集][hdinsight]

## <a name="next-steps"></a>后续步骤

若要开始, 请[创建并配置 Azure Active Directory 域服务实例][tutorial-create-instance]

<!-- INTERNAL LINKS -->
[hdinsight]: ../hdinsight/domain-joined/apache-domain-joined-configure.md
[tutorial-create-instance]: tutorial-create-instance.md

<!-- EXTERNAL LINKS -->
[windows-rds]: /windows-server/remote/remote-desktop-services/rds-azure-adds
