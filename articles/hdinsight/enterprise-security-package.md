---
title: Azure HDInsight 企业安全性套餐
description: 了解 Azure HDInsight 中的企业安全性套餐组件和版本。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 07210350826001bd3e0be6e04be211c9de43695a
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997251"
---
# <a name="enterprise-security-package-for-azure-hdinsight"></a>Azure HDInsight 企业安全性套餐

Enterprise Security 是一种可选包，可在 HDInsight 群集上作为创建群集工作流的一部分添加。 Enterprise Security 包支持：

* 与 Active Directory 集成进行身份验证。

    过去，你创建了包含本地管理员用户和本地 SSH 用户的 HDInsight 群集。 本地管理员用户可以访问所有文件、文件夹、表和列。  使用企业安全性套餐，可以通过将 HDInsight 与 Active Directory 集成来启用基于角色的访问控制。 其中包括本地 Active Directory Azure Active Directory 域服务。 或 Active Directory。 群集上的域管理员可以授予用户使用其自己的公司（域）用户名和密码的权限。

       有关详细信息，请参阅：

    * [已加入域的 HDInsight 群集的 Apache Hadoop 安全性简介](./domain-joined/hdinsight-security-overview.md)

    * [在 HDInsight 中计划 Azure 已加入域的 Apache Hadoop 群集](./domain-joined/apache-domain-joined-architecture.md)

    * [配置已加入域的沙盒环境](./domain-joined/apache-domain-joined-configure.md)

    * [使用 Azure Active Directory 域服务设置和配置已加入域的 HDInsight 群集](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

* 数据授权

  * 与 Apache Ranger 集成以对 Hive、Spark SQL 和 Yarn 队列授权。
  * 可以对文件和文件夹设置访问控制。

    有关详细信息，请参阅[在已加入域的 HDInsight 中配置 Apache Hive 策略](./domain-joined/apache-domain-joined-run-hive.md)

* 查看审核日志以监视访问和配置的策略。

## <a name="supported-cluster-types"></a>支持的群集类型

目前只有以下群集类型支持 Enterprise Security 包：

* Hadoop（仅限 HDInsight 3.6）
* Spark
* Kafka
* HBase
* 交互式查询

## <a name="support-for-azure-data-lake-storage"></a>支持 Azure Data Lake Storage

Enterprise Security 包支持使用 Azure Data Lake Storage 作为主存储和附加存储。

## <a name="pricing-and-service-level-agreement-sla"></a>定价和服务级别协议（SLA）

有关 Enterprise Security 包的定价和 SLA 的信息，请参阅 [HDInsight 定价](https://azure.microsoft.com/pricing/details/hdinsight/)。

## <a name="next-steps"></a>后续步骤

* [为 HDInsight 上的 Apache Hadoop、Spark 和其他组件设置群集](hdinsight-hadoop-provision-linux-clusters.md)
* [使用 Windows 电脑在 HDInsight 上的 Apache Hadoop 中工作](hdinsight-hadoop-windows-tools.md)
* [与 Azure HDInsight 版本相关的 Hortonworks 发行说明](./hortonworks-release-notes.md)
* [HDInsight 上的 Apache 组件](./hdinsight-component-versioning.md)