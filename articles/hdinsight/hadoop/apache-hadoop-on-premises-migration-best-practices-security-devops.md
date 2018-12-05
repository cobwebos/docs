---
title: 将本地 Apache Hadoop 群集迁移到 Azure HDInsight - 安全性和 DevOps 最佳做法
description: 了解有关将本地 Hadoop 群集迁移到 Azure HDInsight 的安全性和 DevOps 最佳做法。
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: fa72765e02592b72efb09320958a0aa244ae8b08
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2018
ms.locfileid: "52265281"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---security-and-devops-best-practices"></a>将本地 Apache Hadoop 群集迁移到 Azure HDInsight - 安全性和 DevOps 最佳做法

本文提供有关 Azure HDInsight 系统安全性和 DevOps 的建议。 本文是帮助用户将本地 Apache Hadoop 系统迁移到 Azure HDInsight 的最佳做法系列教程中的其中一篇。

## <a name="use-the-enterprise-security-package-to-secure-and-govern-the-cluster"></a>使用企业安全性套餐来保护和管理群集

企业安全性套餐 (ESP) 支持基于 Active Directory 的身份验证、多用户支持和基于角色的访问控制。 选择 ESP 选项后，HDInsight 群集将加入 Active Directory 域，企业管理员可以使用 Apache Ranger 为 Hive 安全性配置基于角色的访问控制 (RBAC)。 管理员可以审核员工的数据访问权限和对访问控制策略所做的任何更改。

ESP 适用于以下群集类型：Apache Hadoop、Apache Spark、Apache HBase、Apache Kafka 和 Interactive Query (Hive LLAP)。 

使用以下步骤部署已加入域的 HDInsight 群集：

- 通过传递域名来部署 Azure Active Directory (AAD)
- 部署 Azure Active Directory 域服务 (AAD DS)
- 创建所需的虚拟网络和子网
- 在虚拟网络中部署 VM 以管理 AAD DS
- 将 VM 加入域
- 安装 AD 和 DNS 工具
- 让 AAD DS 管理员创建组织单位 (OU)
- 为 AAD DS 启用 LDAPS
- 在 Azure Active Directory 中创建一个服务帐户，并为 OU 授予读写管理权限，以便它可进行这些操作。 然后，此服务帐户可将计算机加入域，并将计算机主体置于 OU 中。 还可在群集创建期间指定的 OU 内创建服务主体。

    > [!Note]
    > 服务帐户不需要是 AD 域管理员帐户

- 通过设置以下参数来部署 HDInsight ESP 群集：
    - **域名**：与 Azure AD DS 关联的域名。
    - **域用户名**：在前面的部分中创建的 Azure AD DS DC 托管域中的服务帐户，例如：`hdiadmin@contoso.onmicrosoft.com`。 此域用户将成为此 HDInsight 群集的管理员。
    - **域密码**：服务帐户的密码。
    - **组织单位**：要与 HDInsight 群集配合使用的 OU 的可分辨名称，例如：`OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com`。 如果此 OU 不存在，则 HDInsight 群集会尝试使用服务帐户拥有的权限创建 OU。
    - **LDAPS URL**：例如，`ldaps://contoso.onmicrosoft.com:636`。
    - **访问用户组**：其用户要同步到群集的安全组，例如：`HiveUsers`。 如果想要指定多个用户组，请使用分号“;”分隔。 创建 ESP 群集之前，组必须存在于目录中。

有关详细信息，请参阅以下文章：

- [已加入域的 HDInsight 群集的 Hadoop 安全性简介](../domain-joined/apache-domain-joined-introduction.md)
- [在 HDInsight 中计划 Azure 已加入域的 Hadoop 群集](../domain-joined/apache-domain-joined-architecture.md)
- [使用 Azure Active Directory 域服务配置已加入域的 HDInsight 群集](../domain-joined/apache-domain-joined-configure-using-azure-adds.md)
- [将 Azure Active Directory 用户同步到 HDInsight 群集](../hdinsight-sync-aad-users-to-cluster.md)
- [在已加入域的 HDInsight 中配置 Hive 策略](../domain-joined/apache-domain-joined-run-hive.md)
- [在已加入域的 HDInsight Hadoop 群集中运行 Apache Oozie](../domain-joined/hdinsight-use-oozie-domain-joined-clusters.md)

## <a name="implement-end-to-end-enterprise-security-management"></a>实现端到端企业安全管理

使用以下控件可以实现端到端的企业安全性：

- **专用和受保护的数据管道（外围级别安全性）**
    - 可以通过 Azure 虚拟网络、网络安全组和网关服务实现外围级别安全性

- **数据访问的身份验证和授权**
    - 使用 Azure Active Directory 域服务创建已加入域的 HDInsight 群集。 （企业安全性套餐）
    - 使用 Ambari 为 AD 用户提供对群集资源的基于角色的访问
    - 使用 Apache Ranger 在表/列/行级别为 Hive 设置访问控制策略。
    - 此外，只有管理员能够通过 SSH 访问群集。

- **审核**
    - 查看和报告对 HDInsight 群集资源与数据的所有访问。
    - 查看并报告对访问控制策略的所有更改

- **加密**
    - 使用 Microsoft 托管的密钥或客户管理的密钥进行透明的服务器端加密。
    - 使用客户端加密的传输加密、https 和 TLS

有关详细信息，请参阅以下文章：

- [Azure 虚拟网络概述](../../virtual-network/virtual-networks-overview.md)
- [Azure 网络安全组概述](../../virtual-network/security-overview.md)
- [Azure 虚拟网络对等互连](../../virtual-network/virtual-network-peering-overview.md)
- [Azure 存储安全指南](../../storage/common/storage-security-guide.md)
- [Azure 存储服务静态加密](../../storage/common/storage-service-encryption.md)

## <a name="use-monitoring--alerting"></a>使用监视和警报

有关详细信息，请参阅文章：

[Azure 监视器概述](../../azure-monitor/overview.md)

## <a name="upgrade-clusters"></a>升级群集

定期升级到最新的 HDInsight 版本，以利用最新功能。 可以使用以下步骤将群集升级到最新版本：

1. 使用最新的 HDInsight 版本创建新的 TEST HDInsight 群集。
1. 测试新群集以确保作业和工作负载按预期工作。
1. 根据需要修改作业、应用程序或工作负载。
1. 备份所有存储在本地群集节点上的暂时性数据。
1. 删除现有的群集。
1. 使用与先前群集相同的默认数据和元存储，在同一 VNET 子网中创建最新 HDInsight 版本的群集。
1. 导入任何已备份的临时数据。
1. 使用新群集启动作业/继续处理。

有关详细信息，请参阅文章：[将 HDInsight 群集升级到新版本](../hdinsight-upgrade-cluster.md)

## <a name="patch-cluster-operating-systems"></a>修补群集操作系统

作为托管的 Hadoop 服务，HDInsight 负责修补 HDInsight 群集使用的 VM 的 OS。

有关详细信息，请参阅文章：[针对 HDInsight 的 OS 修补](../hdinsight-os-patching.md)

## <a name="post-migration"></a>迁移后

1. **修复应用程序** - 迭代地对作业、进程和脚本进行必要的更改。
2. **执行测试** - 迭代地运行功能和性能测试。
3. **优化** - 根据上述测试结果解决任何性能问题，然后重新测试以确认性能改进。

## <a name="next-steps"></a>后续步骤

- 了解有关 [HDInsight 4.0](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-introduction) 的详细信息
