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
ms.date: 12/14/2017
ms.author: saurinsh
ms.openlocfilehash: eca019fa5e7866ed6281e8cfee105ba1d99249bc
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2017
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>在 HDInsight 中计划 Azure 已加入域的 Hadoop 群集

标准 HDInsight 群集是单用户群集。 适用于大多数使用小型应用程序团队来构建大数据工作负荷的公司。 随着 Hadoop 越来越普及，许多企业开始转向这样一种模型：群集由 IT 团队管理，并由多个应用程序团队共享。 因此，涉及到多用户群集的功能是 HDInsight 中最常请求的功能之一。

HDInsight 不构建自己的多用户身份验证和授权，而是依赖于最常用的标识提供者 – Active Directory (AD)。 可以使用 AD 中强大的安全功能来管理 HDInsight 中的多用户授权。 将 HDInsight 与 AD 集成后，可以使用其 AD 凭据来与群集通信。 HDInsight 中的 VM 通过域加入到你的 AD，并且这是 HDInsight 将 AD 用户映射到本地 Hadoop 用户的方式，因此在 HDInsight 上运行的所有服务（Ambari、Hive 服务器、Ranger、Spark Thrift 服务器等）都可以为通过身份验证的用户无缝运行。 然后，管理员可以使用 Apache Ranger 创建强大的授权策略来针对 HDInsight 中的资源提供基于角色的访问控制。


## <a name="integrate-hdinsight-with-active-directory"></a>将 HDInsight 与 Active Directory 集成

将 HDInsight 与 Active Directory 集成时，HDInsight 群集节点通过域加入某个 AD 域。 将为群集上的 Hadoop 组件配置 Kerberos 安全性。 对于每个 Hadoop 组件，将在 Active Directory 中创建一个服务主体。 还将为加入到该域的每台计算机创建一个对应的计算机主体。 这些服务主体和计算机主体可能会使 Active Directory 变得凌乱。 因此，需要在 Active Directory 中提供一个组织单位 (OU) 来放置这些主体。 

概而言之，需要在环境中设置以下项：

- 一个配置了 LDAPS 的 Active Directory 域。
- 从 HDInsight 的虚拟网络到你的 Active Directory 域控制器的连接。
- 在 Active Directory 上创建的一个组织单位。
- 一个有权执行以下操作的服务帐户：

    - 在 OU 中创建服务主体。
    - 将计算机加入到域并在 OU 中创建计算机主体。

以下屏幕截图显示了在 contoso.com 中创建的一个 OU。屏幕截图中还显示了一些服务主体和计算机主体。

![已加入域的 HDInsight 群集 OU](./media/apache-domain-joined-architecture/hdinsight-domain-joined-ou.png)。

### <a name="three-ways-of-bringing-your-own-active-directory-domain-controllers"></a>自带 Active Directory 域控制器的三种方式

可以通过三种方式自带 Active Directory 域控制器来创建已加入域的 HDInsight 群集。 

- **Azure Active Directory 域服务**：此服务提供的托管 Active Directory 域与 Windows Server Active Directory 完全兼容。 Microsoft 负责管理、修补和监视 AD 域。 你可以部署群集，而不用担心如何维护域控制器。 将从 Azure Active Directory 同步用户、组和密码，使用户能够使用其公司凭据登录到群集。 有关详细信息，请参阅[使用 Azure Active Directory 域服务配置已加入域的 HDInsight 群集](./apache-domain-joined-configure-using-azure-adds.md)。

- **Azure IaaS VM 上的 Active Directory**：在此选项中，你将在 Azure IaaS VM 上部署和管理你自己的 Windows Server Active Directory 域。 有关详细信息，请参阅[配置已加入域的沙盒环境](./apache-domain-joined-configure.md)。

- **本地 Active Directory**：在此选项中，你需要将 HDInsight 与你的本地 Active Directory 域控制器进行集成。


## <a name="next-steps"></a>后续步骤
* 若要配置已加入域的 HDInsight 群集，请参阅 [Configure domain-joined HDInsight clusters](apache-domain-joined-configure.md)（配置已加入域的 HDInsight 群集）。
* 若要管理已加入域的 HDInsight 群集，请参阅 [Manage domain-joined HDInsight clusters](apache-domain-joined-manage.md)（管理已加入域的 HDInsight 群集）。
* 若要配置 Hive 策略和运行 Hive 查询，请参阅 [Configure Hive policies for domain-joined HDInsight clusters](apache-domain-joined-run-hive.md)（为已加入域的 HDInsight 群集配置 Hive 策略）。
* 要在已加入域的 HDInsight 群集上使用 SSH 运行 Hive 查询，请参阅[将 SSH 与 HDInsight 配合使用](../hdinsight-hadoop-linux-use-ssh-unix.md)。
