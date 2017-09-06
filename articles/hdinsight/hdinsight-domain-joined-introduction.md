---
title: "Hadoop 安全性 - 已加入域的 HDInsight 群集 - Azure | Microsoft 文档"
description: "学习内容"
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
ms.date: 10/31/2016
ms.author: saurinsh
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 303be1d303df8074283cb1d37c74923cca80ae59
ms.contentlocale: zh-cn
ms.lasthandoff: 08/22/2017

---
# <a name="an-introduction-to-hadoop-security-with-domain-joined-hdinsight-clusters-preview"></a>已加入域的 HDInsight 群集（预览版）的 Hadoop 安全性简介

截止目前，Azure HDInsight 仅支持一个本地管理员用户。这种情况对于应用程序团队或部门而言比较有利。 随着基于 Hadoop 的工作负荷在企业细分市场中越来越普及，基于 Active Directory 的身份验证、多用户支持和基于角色的访问控制等企业级功能需求也变得日益重要。 使用已加入域的 HDInsight 群集，可以创建一个已加入 Active Directory 域的 HDInsight 群集，配置可以通过 Azure Active Directory 进行身份验证并登录到 HDInsight 群集的企业员工列表。 企业外部的任何人都无法登录或访问 HDInsight 群集。 企业管理员可以使用 [Apache Ranger](http://hortonworks.com/apache/ranger/) 配置基于角色的访问控制来实现 Hive 安全性，从而根据最大需求权限来限制数据访问权限。 最后，管理员可以审核员工的数据访问活动以及对访问控制策略所做的任何更改，从而以较大的力度监管其企业资源。

> [!NOTE]
> 本文所述的预览版新功能仅适用于 Hive 工作负荷中的基于 Linux 的 HDInsight 群集。 其他工作负荷，如 HBase、Spark、 Storm 和 Kafka，会在以后的版本中启用这些功能。

> [!IMPORTANT]
> 在加入域的 HDInsight 上未启用 Oozie。

## <a name="benefits"></a>优点
企业安全性包含四大支柱 – 外围安全性、身份验证、授权和加密。

![已加入域的 HDInsight 群集可以稳固这些支柱](./media/hdinsight-domain-joined-introduction/hdinsight-domain-joined-four-pillars.png)。

### <a name="perimeter-security"></a>外围安全性
HDInsight 中的外围安全性是使用虚拟网络和网关服务实现的。 现在，企业管理员可以在虚拟网络中创建 HDInsight 群集，使用网络安全组（入站或出站防火墙规则）来限制对虚拟网络的访问。 只有入站防火墙规则中定义的 IP 地址才能与 HDInsight 群集通信，从而实现了外围安全性。 外围安全性的另一个层是使用网关服务实现的。 服务中的网关服务充当传入 HDInsight 群集的任何请求的第一道防线。 它接受请求，只在验证请求之后才允许请求传入群集中的其他节点，因此为群集中的其他名称节点和数据节点提供外围安全性。

### <a name="authentication"></a>身份验证
在本公共预览版中，企业管理员可以在[虚拟网络](https://azure.microsoft.com/services/virtual-network/)中预配已加入域的 HDInsight 群集。 HDInsight 群集的节点将加入企业管理的域。 此过程是使用 [Azure Active Directory 域服务](../active-directory-domain-services/active-directory-ds-overview.md)实现的。 群集中的所有节点已加入企业管理的域。 借助这种设置，企业员工可以使用他们的域凭据登录到群集节点。 他们还可以使用自己的域凭据在其他已批准的终结点（例如 Hue、Ambari视图、ODBC、JDBC、PowerShell 和 REST API）上进行身份验证，以便与群集交互。 管理员拥有完全控制权，可以限制通过这些终结点与群集交互的用户数目。

### <a name="authorization"></a>授权
大多数企业遵循的最佳实践是，并非每位员工都有权访问所有企业资源。 同理，在本版本中，管理员可以针对群集资源定义基于角色的访问控制策略。 例如，管理员可以配置 [Apache Ranger](http://hortonworks.com/apache/ranger/)，为 Hive 设置访问控制策略。 此功能可确保员工最多只能访问为了成功完成其作业而要访问的数据。 此外，只有管理员能够通过 SSH 访问群集。

### <a name="auditing"></a>审核
除了防止未经授权的用户访问HDInsight 群集资源及保护数据以外，还有必要对群集资源和数据的所有访问进行审核，跟踪资源是否受到未经授权或无意的访问。 在本预览版中，管理员可以查看和报告对 HDInsight 群集资源与数据的所有访问。 管理员还可以查看和报告在 Apache Ranger 支持的终结点中对访问控制策略进行的所有更改。 已加入域的 HDInsight 群集使用我们熟悉的 Apache Ranger UI 来搜索审核日志。 在后端，Ranger 使用 [Apache Solr](http://hortonworks.com/apache/solr/) 来存储和搜索日志。

### <a name="encryption"></a>加密
保护数据以及限制未经授权的员工访问数据对于符合组织安全与合规要求至关重要，此外，应该通过加密来确保数据的安全。 HDInsight 群集的数据存储、Azure 存储 Blob 和 Azure Data Lake Storage 都支持在服务器端以透明方式进行静态[数据加密](../storage/common/storage-service-encryption.md)。 HDInsight 安全群集能够与这种服务器端静态数据加密功能无缝协作。

## <a name="next-steps"></a>后续步骤
* 若要配置已加入域的 HDInsight 群集，请参阅 [Configure Domain-joined HDInsight clusters](hdinsight-domain-joined-configure.md)（配置已加入域的 HDInsight 群集）。
* 若要管理已加入域的 HDInsight 群集，请参阅 [Configure Domain-joined HDInsight clusters](hdinsight-domain-joined-manage.md)（管理已加入域的 HDInsight 群集）。
* 若要配置 Hive 策略和运行 Hive 查询，请参阅 [Configure Hive policies for Domain-joined HDInsight clusters](hdinsight-domain-joined-run-hive.md)（为已加入域的 HDInsight 群集配置 Hive 策略）。
* 有关在已加入域的 HDInsight 群集上使用 SSH 运行 Hive 查询，请参阅[将 SSH 与 HDInsight 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)。

