---
title: "已加入域的 Azure HDInsight 体系结构 | Microsoft Docs"
description: "了解如何计划已加入域的 HDInsight。"
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/21/2017
ms.author: saurinsh
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 08d61f95fb38962402142c0ccd0a5a2f3a7e802a
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>在 HDInsight 中计划 Azure 已加入域的 Hadoop 群集

传统 Hadoop 是单用户群集， 适用于大多数使用小型应用程序团队来构建大数据工作负荷的公司。 随着 Hadoop 越来越普及，许多企业开始转向这样一种模型：群集由 IT 团队管理，并由多个应用程序团队共享。 因此，涉及到多用户群集的功能是 HDInsight 中最常请求的功能之一。

HDInsight 不构建自己的多用户身份验证和授权，而是依赖于最常用的标识提供者 – Active Directory (AD)。 可以使用 AD 中强大的安全功能来管理 HDInsight 中的多用户授权。 将 HDInsight 与 AD 集成后，可以使用其 AD 凭据来与群集通信。 HDInsight 会将 AD 用户映射到本地 Hadoop 用户，因此在 HDInsight 上运行的所有服务（Ambari、Hive 服务器、Ranger、Spark Thrift 服务器等）都可以为经身份验证的用户无缝运行。

## <a name="integrate-hdinsight-with-active-directory"></a>将 HDInsight 与 Active Directory 集成

将 HDInsight 与 Active Directory 集成时，HDInsight 群集节点可通过域加入 AD 域。 HDInsight 为群集上运行的 Hadoop 服务创建服务主体，并将其置于域中的指定组织单位 (OU) 内。 HDInsight 还会针对已加入域的节点的 IP 地址，在域中创建反向 DNS 映射。

有两个用于 Active Directory 的部署选项：
* **[Azure Active Directory 域服务](../active-directory-domain-services/active-directory-ds-overview.md)：**此服务提供的托管 Active Directory 域与 Windows Server Active Directory 完全兼容。 Microsoft 负责管理、修补和监视 AD 域。 你可以部署群集，而不用担心如何维护域控制器。 将从 Azure Active Directory 同步用户、组和密码，使用户能够使用其公司凭据登录到群集。

* **Azure IaaS VM 上的 Windows Server Active Directory 域：**在此选项中，部署和管理自己在 Azure IaaS VM 上的 Windows Server Active Directory 域。 

可以使用多个体系结构来实现这种设置。 可从以下体系结构中选择。


### <a name="hdinsight-integrated-with-an-azure-ad-domain-services-managed-ad-domain"></a>与 Azure AD 域服务托管的 AD 域集成的 HDInsight
可以部署 [Azure Active Directory 域服务](../active-directory-domain-services/active-directory-ds-overview.md) (Azure AD DS) 托管域。 Azure AD DS 提供了 Azure 中托管的 AD 域，该域由 Microsoft 管理、更新和监视。 它会创建两个域控制器以确保高可用性，并包括 DNS 服务。 然后可以将 HDInsight 群集与此托管域集成。 使用此部署选项，不必担心如何管理、修补、更新和监视域控制器。

![将 HDInsight 群集拓扑加入域](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

与 Azure AD 域服务集成的先决条件：

* [预配 Azure AD 域服务托管域](../active-directory-domain-services/active-directory-ds-getting-started.md)。
* 创建一个[组织单位](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)用于放置 HDInsight 群集 VM 以及群集使用的服务主体。
* 配置 Azure AD DS 时，设置 [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md)。 用于设置 LDAPS 的证书必须由公共证书颁发机构颁发（不是自签名证书）。
* 针对 HDInsight 子网的 IP 地址范围（例如上图中的 10.2.0.0/24）在托管域中创建反向 DNS 区域。
* 配置 [NTLM 和 Kerberos 身份验证所需的密码哈希同步](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)（从 Azure AD 到 Azure AD DS 托管域）。
* 需要一个服务帐户或用户帐户， 到时要使用该帐户创建 HDInsight 群集。 此帐户必须具有以下权限：

    - 在组织单位中创建服务主体对象和计算机对象的权限
    - 创建反向 DNS 代理规则的权限
    - 将计算机加入 Azure AD 域的权限。


### <a name="hdinsight-integrated-with-windows-server-ad-running-on-azure-iaas"></a>与 Azure IaaS 上运行的 Windows Server AD 集成的 HDInsight

可以在 Azure 中的一个（或多个）虚拟机 (VM) 上部署 Windows Server Active Directory 域服务角色，并将它们提升为域控制器。 可以使用资源管理器部署模型将这些域控制器 VM 部署到 HDInsight 群集所在的虚拟网络。 如果域控制器部署到不同的虚拟网络，则需要使用 [VNet 到 VNet 对等互连](../virtual-network/virtual-network-create-peering.md)将这些虚拟网络对等互连。 

[详细信息 - 在 Azure VM 上部署 Windows Server Active Directory](../active-directory/virtual-networks-windows-server-active-directory-virtual-machines.md)

![将 HDInsight 群集拓扑加入域](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_1.png)

> [!NOTE]
> 在此体系结构中，不能将 Azure Data Lake Store 与 HDInsight 群集配合使用。


与 Azure VM 上的 Windows Server Active Directory 集成的先决条件：

* 必须创建一个[组织单位](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)用于放置 HDInsight 群集 VM 以及群集使用的服务主体。
* 必须设置[轻型目录访问协议](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) (LDAP) 来与 AD 通信。 用于设置 LDAPS 的证书必须是实际证书（不是自签名证书）。
* 必须针对 HDInsight 子网的 IP 地址范围（例如上图中的 10.2.0.0/24）在域中创建反向 DNS 区域。
* 需要一个服务帐户或用户帐户， 到时要使用该帐户创建 HDInsight 群集。 此帐户必须具有以下权限：

    - 在组织单位中创建服务主体对象和计算机对象的权限
    - 创建反向 DNS 代理规则的权限
    - 将计算机加入 Active Directory 域的权限


## <a name="next-steps"></a>后续步骤
* 若要配置已加入域的 HDInsight 群集，请参阅 [Configure domain-joined HDInsight clusters](hdinsight-domain-joined-configure.md)（配置已加入域的 HDInsight 群集）。
* 若要管理已加入域的 HDInsight 群集，请参阅 [Manage domain-joined HDInsight clusters](hdinsight-domain-joined-manage.md)（管理已加入域的 HDInsight 群集）。
* 若要配置 Hive 策略和运行 Hive 查询，请参阅 [Configure Hive policies for domain-joined HDInsight clusters](hdinsight-domain-joined-run-hive.md)（为已加入域的 HDInsight 群集配置 Hive 策略）。
* 要在已加入域的 HDInsight 群集上使用 SSH 运行 Hive 查询，请参阅[将 SSH 与 HDInsight 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md)。

