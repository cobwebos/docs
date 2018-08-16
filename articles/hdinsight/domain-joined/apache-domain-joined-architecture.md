---
title: 已加入域的 Azure HDInsight 体系结构
description: 了解如何计划已加入域的 HDInsight。
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
editor: jasonwhowell
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: 1744a824e3b3fef848ad0513c95b1c6bb21f9fc9
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2018
ms.locfileid: "39597569"
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>在 HDInsight 中计划 Azure 已加入域的 Hadoop 群集

标准 Azure HDInsight 群集是单用户群集。 适用于大多数使用小型应用程序团队来构建大数据工作负荷的公司。 每个用户可以按需创建专用群集，并在不再需要时将其销毁。 

许多企业已转向这样一种模型：群集由 IT 团队管理，并由多个应用程序团队共享。 这些较大型企业需要在 Azure HDInsight 中实现对每个群集的多用户访问。

HDInsight 以托管方式依赖于常用的标识提供者 - Active Directory。 通过将 HDInsight 与 [Azure Active Directory 域服务 (Azure AD DS)](../../active-directory-domain-services/active-directory-ds-overview.md) 相集成，可以使用你的域凭据来访问群集。 

HDInsight 中的虚拟机 (VM) 将加入你提供的域。 因此，在 HDInsight 上运行的所有服务（Ambari、Hive 服务器、Ranger、Spark Thrift 服务器等）都可以为经身份验证的用户无缝运行。 然后，管理员可以使用 Apache Ranger 创建强大的授权策略，以针对群集中的资源提供基于角色的访问控制。


## <a name="integrate-hdinsight-with-active-directory"></a>将 HDInsight 与 Active Directory 集成

开源 Hadoop 依赖于 Kerberos 来提供身份验证和安全性。 因此，HDInsight 群集节点加入到由 Azure AD DS 管理的域。 将为群集上的 Hadoop 组件配置 Kerberos 安全性。 

针对每个 Hadoop 组件，都会自动创建一个服务主体。 还将为加入到该域的每台计算机创建一个对应的计算机主体。 为了存储这些服务和计算机主体，必须在域控制器 (Azure AD DS) 中提供一个组织单位 (OU)，这些主体将放置在其中。 

概而言之，需要在环境中设置以下项：

- 一个 Active Directory 域（由 Azure AD DS 管理）。
- 在 Azure AD DS 中启用的安全 LDAP (LDAPS)。
- HDInsight 虚拟网络和 Azure AD DS 虚拟网络之间的正常网络连接（如果为这两者选择不同的虚拟网络）。 HDInsight 虚拟网络中的 VM 应通过虚拟网络对等互连与 Azure AD DS 连接。 如果 HDInsight 和 Azure AD DS 部署在同一虚拟网络中，则会自动提供此连接，不需要执行进一步操作。
- [在 Azure AD DS 上创建](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)的 OU。
- 一个有权执行以下操作的服务帐户：
    - 在 OU 中创建服务主体。
    - 将计算机加入到域并在 OU 中创建计算机主体。

以下屏幕截图显示了在 contoso.com 中创建的一个 OU。 其中还显示了一些服务主体和计算机主体。

![用于已加入域的 HDInsight 群集的组织单位](./media/apache-domain-joined-architecture/hdinsight-domain-joined-ou.png)。

## <a name="set-up-different-domain-controllers"></a>设置不同的域控制器
HDInsight 当前仅支持将 Azure AD DS 用作群集用于与 Kerberos 进行通信的主域控制器。 但是，也可以使用其他复杂的 Active Directory 设置，只要该设置能启用 Azure AD DS 进行 HDInsight 访问。

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory 域服务
[Azure AD DS](../../active-directory-domain-services/active-directory-ds-overview.md) 提供与 Windows Server Active Directory 完全兼容的托管域。 Microsoft 负责采用高度可用的 (HA) 设置来管理、修补和监视域。 你可以部署群集，而不用担心如何维护域控制器。 

用户、组和密码将从 Azure Active Directory (Azure AD) 同步。 利用从 Azure AD 实例到 Azure AD DS 的单向同步，用户可以使用相同的企业凭据登录到群集。 

有关详细信息，请参阅[使用 Azure AD DS 配置已加入域的 HDInsight 群集](./apache-domain-joined-configure-using-azure-adds.md)。

### <a name="on-premises-active-directory-or-active-directory-on-iaas-vms"></a>IaaS VM 上的本地 Active Directory 或 Active Directory

如果域具有本地 Active Directory 示例或更复杂的 Active Directory 设置，则可以使用 Azure AD Connect 将这些标识同步到 Azure AD。 然后可在该 Active Directory 租户上启用 Azure AD DS。 

由于 Kerberos 依赖于密码哈希，因此需要[在 Azure AD DS 上启用密码哈希同步](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)。 如果使用 Active Directory 联合身份验证服务 (AD FS) 进行联合身份验证，则可以选择性地设置密码哈希同步，作为在 AD FS 基础结构发生故障时的备用身份验证方式。 有关详细信息，请参阅[使用 Azure AD Connect 同步启用密码哈希同步](../../active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)。 

在 IaaS VM 上单独使用本地 Active Directory 或 Active Directory 而不使用 Azure AD 和 Azure AD DS，这是已加入域的 HDInsight 群集不支持的配置。

## <a name="next-steps"></a>后续步骤
* [配置已加入域的 HDInsight 群集](apache-domain-joined-configure-using-azure-adds.md)
* [为已加入域的 HDInsight 群集配置 Hive 策略](apache-domain-joined-run-hive.md)
* [管理已加入域的 HDInsight 群集](apache-domain-joined-manage.md) 
