---
title: 已加入域的 Azure HDInsight 体系结构 | Microsoft Docs
description: 了解如何计划已加入域的 HDInsight。
services: hdinsight
documentationcenter: ''
author: omidm1
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: omidm
ms.openlocfilehash: 8503534031dc5774e64c58edd3e158162a5a6aee
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110447"
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>在 HDInsight 中计划 Azure 已加入域的 Hadoop 群集

标准 HDInsight 群集是单用户群集。 适用于大多数使用小型应用程序团队来构建大数据工作负荷的公司。 每个用户在需要时都可以拥有自己专用的群集，在不再需要时将其销毁。 但是，许多企业开始转向这样一种模型：群集由 IT 团队管理，并由多个应用程序团队共享。 因此，对于这些较大型企业，需要在 Azure HDInsight 中实现对群集的多用户访问。

HDInsight 以托管方式依赖于最流行的标识提供者 - Active Directory (AD)。 通过将 HDInsight 与 [Azure Active Directory 域服务 (AAD-DS)](../../active-directory-domain-services/active-directory-ds-overview.md) 相集成，可以使用你的域凭据来访问群集。 HDInsight 中的 VM 加入到你提供的域，因此，在 HDInsight 上运行的所有服务（Ambari、Hive 服务器、Ranger、Spark Thrift 服务器等）都可以为通过身份验证的用户无缝运行。 然后，管理员可以使用 Apache Ranger 创建强大的授权策略来针对群集中的资源提供基于角色的访问控制。


## <a name="integrate-hdinsight-with-active-directory"></a>将 HDInsight 与 Active Directory 集成

开源 Hadoop 依赖于 Kerberos 来提供身份验证和安全性。 因此，HDInsight 群集节点加入到由 AAD-DS 管理的域。 将为群集上的 Hadoop 组件配置 Kerberos 安全性。 针对每个 Hadoop 组件，都会自动创建一个服务主体。 还将为加入到该域的每台计算机创建一个对应的计算机主体。 为了存储这些服务和计算机主体，需要在域控制器 (AAD-DS) 中提供一个组织单位 (OU)，这些主体将放置在其中。 

概而言之，需要在环境中设置以下项：

- 一个 Active Directory 域（由 AAD-DS 管理）
- 在 AAD-DS 中启用的安全 LDAP (LDAPS)。
- 从 HDInsight 的 VNET 到 AAD-DS VNET 的正确网络连接（如果为它们选择不同的 VNET）。 HDI VNET 中的 VM 应当能够使用 VNET 对等互连与 AAD-DS 进行通信。 如果 HDI 和 AAD-DS 部署在同一 VNET 中，则会自动提供此连接，不需要执行进一步的操作。
- [在 AAD-DS 上创建的](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)一个组织单位 (OU)。
- 一个有权执行以下操作的服务帐户：
    - 在 OU 中创建服务主体。
    - 将计算机加入到域并在 OU 中创建计算机主体。

以下屏幕截图显示了在 contoso.com 中创建的一个 OU。 屏幕截图中还显示了一些服务主体和计算机主体。

![已加入域的 HDInsight 群集 OU](./media/apache-domain-joined-architecture/hdinsight-domain-joined-ou.png)。

### <a name="different-domain-controllers-setup"></a>不同的域控制器设置：
HDInsight 当前仅支持使用 AAD-DS 作为要与群集通信的主域控制器，以便通过使用 Kerberos 来保护群集。 但是，也可以实现其他复杂的 AD 设置，只要它能够将 AAD-DS 用于 HDI 访问即可。

- **[Azure Active Directory 域服务 (AAD-DS)](../../active-directory-domain-services/active-directory-ds-overview.md)**：此服务提供的托管域与 Windows Server Active Directory 完全兼容。 Microsoft 负责采用高度可用的 (HA) 设置来管理、修补和监视域。 你可以部署群集，而不用担心如何维护域控制器。 将从 Azure Active Directory (AAD) 同步用户、组和密码 [从 AAD 到 AAD-DS 的单向同步]，使用户能够使用相同的公司凭据登录到群集。 有关详细信息，请参阅[如何使用 AAD-DS 配置已加入域的 HDInsight 群集](./apache-domain-joined-configure-using-azure-adds.md)。
- **本地 AD 或 IaaS VM 上的 AD**：如果你的域具有本地 AD 或其他更复杂的 AD 设置，则可以使用 AD Connect 将这些标识同步到 AAD，然后在该 AD 租户上启用 AAD-DS。 因为 Kerberos 依赖于密码哈希，因此需要[在 AAD-DS 上启用密码哈希同步](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)。 如果你通过 AD 联合身份验证服务 (ADFS) 使用联合身份验证，则还可以将密码哈希同步设置为备用机制，以防 ADFS 基础结构发生故障。 有关详细信息，请参阅[使用 AAD Connect 同步启用密码哈希同步](../../active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)。在加入 HDI 群集域时，不支持单独使用本地 AD（或 IaaS VM 上的 AD）而不使用 AAD 和 AAD-DS。

## <a name="next-steps"></a>后续步骤
* [配置已加入域的 HDInsight 群集](apache-domain-joined-configure-using-azure-adds.md)。
* [为已加入域的 HDInsight 群集配置 Hive 策略](apache-domain-joined-run-hive.md)。
* [管理已加入域的 HDInsight 群集](apache-domain-joined-manage.md)。 
