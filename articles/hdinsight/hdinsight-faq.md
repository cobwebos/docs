---
title: Azure HDInsight 常见问题解答
description: 有关 HDInsight 的常见问题解答
keywords: 常见问题解答, FAQ
author: Ramakoni1
ms.author: ramakoni
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: b05b83086cc9d8449d9517897f347b6e2685aa95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720344"
---
# <a name="azure-hdinsight-frequently-asked-questions"></a>Azure HDInsight：常见问题

本文提供有关如何运行 [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) 的一些最常见问题的解答。

## <a name="creating-or-deleting-hdinsight-clusters"></a>创建或删除 HDInsight 群集

### <a name="how-do-i-provision-an-hdinsight-cluster"></a>如何预配 HDInsight 群集？

若要了解可用的 HDInsight 群集类型和预配方法，请参阅[使用 Apache Hadoop、Apache Spark、Apache Kafka 及其他组件在 HDInsight 中设置群集](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters)。

### <a name="how-do-i-delete-an-existing-hdinsight-cluster"></a>如何删除现有的 HDInsight 群集？

若要详细了解如何删除不再使用的群集，请参阅[删除 HDInsight 群集](hdinsight-delete-cluster.md)。

创建群集后，至少需在 30 到 60 分钟之后才能执行删除操作。 否则，操作可能会失败并出现以下错误消息：

``Conflict (HTTP Status Code: 409) error when attempting to delete a cluster immediately after creation of a cluster. If you encounter this error, wait until the newly created cluster is in operational state before attempting to delete it.``

### <a name="how-do-i-select-the-correct-number-of-cores-or-nodes-for-my-workload"></a>如何为工作负荷选择适当的核心数或节点数？

适当的核心数和其他配置选项取决于多种因素。

有关详细信息，请参阅 [HDInsight 群集的容量规划](https://docs.microsoft.com/azure/hdinsight/hdinsight-capacity-planning)。

### <a name="what-can-i-do-when-cluster-provisioning-fails-because-of-a-capacity-issue"></a>群集预配因容量问题而失败时，我该怎么办？

本部分提供常见的容量错误和缓解方法。

#### <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>错误：部署将超过"800"的配额

Azure 的配额限制为每个资源组 800 个部署。 将按资源组、订阅、帐户和其他范围应用不同的配额。 例如，订阅可能配置为限制某个区域的核心数目。 如果尝试部署的虚拟机的核心数超过允许的数量，你会收到一条错误消息，其中指出超过了配额。

若要解决此问题，请使用 Azure 门户、CLI 或 PowerShell 删除不再需要的部署。

有关详细信息，请参阅[解决资源配额错误](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors)。

#### <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>错误：最大节点超过此区域中的可用内核

订阅可能配置为限制某个区域的核心数目。 如果尝试部署的资源的核心数超过允许的数量，你会收到一条错误消息，其中指出超过了配额。

若要请求增加配额，请按以下步骤操作：

1. 转到 [Azure 门户](https://portal.azure.com)并选择“帮助 + 支持”。****
   
1. 选择 **"新建支持请求**"。
   
1. 在“新建支持请求”页的“基本信息”选项卡上提供以下信息：********
   
   - **问题类型：** 选择**服务和订阅限制（配额）。**
   - **订阅：** 选择要修改的订阅。
   - **配额类型：** 选择**HDInsight**。

有关详细信息，请参阅[创建支持票证来增加核心](hdinsight-capacity-planning.md#quotas)。

### <a name="what-are-the-various-types-of-nodes-in-an-hdinsight-cluster"></a>HDInsight 群集中有哪些不同类型的节点？

Azure HDInsight 群集包含不同类型的虚拟机（或节点）。 每个节点类型对于系统的正常运行发挥着相应的作用。

有关详细信息，请参阅 [Azure HDInsight 群集中的资源类型](hdinsight-virtual-network-architecture.md#resource-types-in-azure-hdinsight-clusters)。

## <a name="individual-components"></a>单个组件

### <a name="can-i-install-additional-components-on-my-cluster"></a>是否可以在群集上安装其他组件？

是的。 若要安装其他组件或自定义群集配置，请使用：

- 在创建期间或之后使用脚本。 脚本可通过[脚本操作](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)调用，脚本操作是一种配置选项，可通过 Azure 门户、HDInsight Windows PowerShell cmdlet 或 HDInsight .NET SDK 使用。 可通过 Azure 门户、HDInsight Windows PowerShell cmdlet 或 HDInsight .NET SDK 使用此配置选项。

- 使用 [HDInsight 应用程序平台](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/)安装生态应用程序。

有关支持的组件列表，请参阅[HDInsight 提供的 Apache Hadoop 组件和版本是什么？](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)

### <a name="can-i-upgrade-the-individual-components-that-are-pre-installed-on-the-cluster"></a>是否可以升级预装在群集上的单个组件？

如果升级预装在群集上的内置组件或应用程序，Microsoft 将不会为最终的配置提供支持。 Microsoft 尚未测试这些系统配置。 请尝试使用不同版本的 HDInsight 群集，该版本可能已预装了组件的升级版本。

例如，不支持将 Hive 作为单个组件进行升级。 HDInsight 是一个托管服务；许多服务已与 Ambari 服务器集成并已经过测试。 单独升级 Hive 会导致其他组件的已编制索引的二进制文件发生更改，并会导致群集上出现组件集成问题。

### <a name="can-spark-and-kafka-run-on-the-same-hdinsight-cluster"></a>Spark 和 Kafka 是否可在同一 HDInsight 群集上运行？

否，无法在同一 HDInsight 群集上运行 Apache Kafka 和 Apache Spark。 单独为 Kafka 和 Spark 创建群集可避免资源争用问题。

### <a name="how-do-i-change-timezone-in-ambari"></a>如何更改 Ambari 中的时区？

1. 通过 `https://CLUSTERNAME.azurehdinsight.net`（其中，CLUSTERNAME 是群集的名称）打开 Ambari Web UI。
2. 在右上角选择“管理”|“设置”。 

   ![Ambari 设置](media/hdinsight-faq/ambari-settings.png)

3. 在“用户设置”窗口中，从“时区”下拉列表中选择新的时区，然后单击“保存”。

   ![Ambari 用户设置](media/hdinsight-faq/ambari-user-settings.png)

## <a name="metastore"></a>元存储

### <a name="how-can-i-migrate-from-the-existing-metastore-to-azure-sql-server"></a>如何从现有的元存储迁移到 Azure SQL Server？ 

要从 SQL 服务器迁移到 Azure SQL 服务器，请参阅[教程：使用 DMS 将 SQL 服务器迁移到 Azure SQL 数据库中的单个数据库或池数据库](../dms/tutorial-sql-server-to-azure-sql.md)。

### <a name="is-the-hive-metastore-deleted-when-the-cluster-is-deleted"></a>删除群集时是否会删除 Hive 元存储？

这取决于群集配置为使用的元存储类型。

对于默认元存储：默认元存储是群集生命周期的一部分。 删除群集时，会一并删除相应的元存储和元数据。

对于自定义元存储：元存储的生命周期不与群集的生命周期相关联。 因此，可以在不丢失元数据的情况下创建和删除群集。 即使在删除和重新创建 HDInsight 群集之后，系统也仍会保留 Hive 架构等元数据。

有关详细信息，请参阅[在 Azure HDInsight 中使用外部元数据存储](hdinsight-use-external-metadata-stores.md)。

### <a name="does-migrating-a-hive-metastore-also-migrate-the-default-policies-of-the-ranger-database"></a>迁移 Hive 元存储是否也会迁移 Ranger 数据库的默认策略？

否，策略定义位于 Ranger 数据库中，因此，迁移 Ranger 数据库会迁移其策略。

### <a name="can-you-migrate-a-hive-metastore-from-an-enterprise-security-package-esp-cluster-to-a-non-esp-cluster-and-vice-versa"></a>是否可以将 Hive 元存储从企业安全包 （ESP） 群集迁移到非 ESP 群集，反之亦然？

可以，您可以将 Hive 元存储从 ESP 迁移到非 ESP 群集。

### <a name="how-can-i-estimate-the-size-of-a-hive-metastore-database"></a>如何估算 Hive 元存储数据库的大小？

Hive 元存储用于存储 Hive 服务器使用的数据源的元数据。大小要求在一定程度上取决于 Hive 数据源的数量和复杂性，而不能提前估算。 如 [Hive 元存储最佳做法](hdinsight-use-external-metadata-stores.md#hive-metastore-best-practices)中所述，可以从 S2 层着手。该层提供 50 个 DTU 和 250 GB 存储空间；如果遇到瓶颈，可以纵向扩展数据库。

### <a name="do-you-support-any-other-database-other-than-azure-sql-database-as-an-external-metastore"></a>是否支持使用除 Azure SQL 数据库以外的其他任何数据库作为外部元存储？

否，Microsoft 仅支持使用 Azure SQL 数据库作为外部自定义元存储。

### <a name="can-i-share-a-metastore-across-multiple-clusters"></a>是否可在多个群集之间共享元存储？

是，可以在多个群集之间共享自定义元存储，前提是这些群集使用相同的 HDInsight 版本。

## <a name="connectivity-and-virtual-networks"></a>连接和虚拟网络  

### <a name="what-are-the-implications-of-blocking-ports-22-and-23-on-my-network"></a>在网络中阻止端口 22 和 23 会造成什么影响？

如果阻止端口 22 和端口 23，则无法通过 SSH 访问群集。 HDInsight 服务不使用这些端口。

有关详细信息，请参阅以下文档：

- [控制网络流量](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#networktraffic)

- [使用专用终结点保护传入虚拟网络中 HDInsight 群集的流量](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)

- [HDInsight 管理 IP 地址](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)

### <a name="can-i-deploy-an-additional-virtual-machine-within-the-same-subnet-as-an-hdinsight-cluster"></a>是否可以在 HDInsight 群集所在的同一子网中部署额外的虚拟机？

是，可以在 HDInsight 群集所在的同一子网中部署额外的虚拟机。 可使用以下配置：

- 边缘节点：您可以将另一个边缘节点添加到群集中，如[HDInsight 中 Apache Hadoop 群集上的空边节点。](hdinsight-apps-use-edge-node.md)

- 独立节点：您可以将独立虚拟机添加到同一子网，并使用专用终结点`https://<CLUSTERNAME>-int.azurehdinsight.net`从该虚拟机访问群集。 有关详细信息，请参阅[控制网络流量](hdinsight-plan-virtual-network-deployment.md#networktraffic)。

### <a name="should-i-store-data-on-the-local-disk-of-an-edge-node"></a>是否应将数据存储在边缘节点的本地磁盘上？

不可以，将数据存储在本地磁盘上不是个好主意。 如果节点发生故障，存储在本地的所有数据都将丢失。 我们建议将数据存储在 Azure 数据湖存储 Gen2 或 Azure Blob 存储中，或者通过安装用于存储数据的 Azure 文件共享。


### <a name="can-i-add-an-existing-hdinsight-cluster-to-another-virtual-network"></a>是否可将现有的 HDInsight 群集添加到另一个虚拟网络？

不可以。 应在预配时指定虚拟网络。 如果在预配期间未指定任何虚拟网络，则部署过程将会创建一个无法从外部访问的内部网络。 有关详细信息，请参阅[将 HDInsight 添加到现有的虚拟网络](hdinsight-plan-virtual-network-deployment.md#existingvnet)。

## <a name="security-and-certificates"></a>安全性和证书

### <a name="what-are-the-recommendations-for-malware-protection-on-azure-hdinsight-clusters"></a>对于 Azure HDInsight 群集上的恶意软件防护，你们有什么建议？

有关恶意软件防护的详细信息，请参阅[适用于 Azure 云服务和虚拟机的 Microsoft Antimalware](../security/fundamentals/antimalware.md)。

### <a name="how-do-i-create-a-keytab-for-an-hdinsight-esp-cluster"></a>如何为 HDInsight ESP 群集创建 keytab？

为域用户名创建 Kerberos keytab。 以后可以使用此 keytab 对已加入远程域的群集进行身份验证，而无需输入密码。 域名采用大写：

```shell
ktutil
ktutil: addent -password -p <username>@<DOMAIN.COM> -k 1 -e RC4-HMAC
Password for <username>@<DOMAIN.COM>: <password>
ktutil: wkt <username>.keytab
ktutil: q
```

### <a name="can-i-use-an-existing-azure-active-directory-tenant-to-create-an-hdinsight-cluster-that-has-the-esp"></a>是否可以使用现有的 Azure Active Directory 租户创建具有 ESP 的 HDInsight 群集？

必须先启用 Azure Active Directory 域服务 (Azure AD DS)，然后才能创建具有 ESP 的 HDInsight 群集。 开源 Hadoop 依赖于使用 Kerberos 进行身份验证（与 OAuth 不同）。

若要将 VM 加入域，必须有一个域控制器。 Azure AD DS 是托管域控制器，它被视为 Azure Active Directory 的一个扩展，可以满足以托管方式构建安全 Hadoop 群集的所有 Kerberos 要求。 用作托管服务的 HDInsight 与 Azure AD DS 集成，以提供端到端的安全性。

### <a name="can-i-use-a-self-signed-certificate-in-an-aad-ds-secure-ldap-setup-and-provision-an-esp-cluster"></a>是否可以在 AAD-DS 安全 LDAP 设置中使用自签名证书并预配 ESP 群集？

建议使用证书颁发机构颁发的证书，但 ESP 上也支持使用自签名证书。 有关详细信息，请参阅：

- [启用 Azure Active Directory 域服务](domain-joined/apache-domain-joined-configure-using-azure-adds.md#enable-azure-ad-ds)

- [教程：为 Azure 活动目录域服务托管域配置安全 LDAP](../active-directory-domain-services/tutorial-configure-ldaps.md)

### <a name="how-can-i-pull-login-activity-shown-in-ranger"></a>如何拉取 Ranger 中显示的登录活动？

对于审核要求，Microsoft 建议启用 Azure 监视器日志，如[使用 Azure 监视器日志来监视 HDInsight 群集](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial)。

### <a name="can-i-disable-clamscan-on-my-cluster"></a>是否可在群集上禁用 Clamscan？

Clamscan 是在 HDInsight 群集上运行的防病毒软件，Azure 安全性服务 (azsecd) 使用它来防范群集遭到病毒攻击。 Microsoft 强烈建议用户不要对默认的 Clamscan 配置进行任何更改。

此进程不会干扰其他进程，也不会中断其运行周期。 它始终屈从于其他进程。 仅当系统处于空闲状态时，才会看到 Clamscan 的 CPU 峰值。  

如果必须控制计划，可使用以下步骤：

1. 使用以下命令禁用自动执行：
   
   `/usr/local/vbin/azsecd config -s clamav -d Disabled`
   
1. 添加一个 Cron 作业，以便以 root 身份运行以下命令：
   
   `/usr/local/bin/azsecd manual -s clamav`

有关如何设置和运行 Cron 作业的详细信息，请参阅[如何设置 Cron 作业](https://askubuntu.com/questions/2368/how-do-i-set-up-a-cron-job)？

### <a name="why-is-llap-available-on-spark-esp-clusters"></a>为什么在 Spark ESP 群集上可以使用 LLAP？
在 ESP Spark 群集上，由于安全原因（即 Apache Ranger），而不是性能，启用 LLAP。 应使用较大的节点 VM 来适应 LLAP 的资源使用情况（例如最小 D13V2）。 

### <a name="how-can-i-add-additional-aad-groups-after-creating-an-esp-cluster"></a>创建 ESP 群集后，如何添加其他 AAD 组？
实现此目的的方法有两种：1- 您可以在群集创建时重新创建群集并添加其他组。 如果您在 AAD-DS 中使用作用域同步，请确保组 B 包含在作用域同步中。
2- 将组添加为用于创建 ESP 群集的前一组的嵌套子组。 例如，如果已创建具有组的`A`ESP 群集，则可以稍后将组添加为嵌套`B`子组，`A`大约一小时后，该组将自动同步并在群集中可用。 

## <a name="storage"></a>存储

### <a name="can-i-add-an-azure-data-lake-storage-gen2-to-an-existing-hdinsight-cluster-as-an-additional-storage-account"></a>是否可将 Azure Data Lake Storage Gen2 作为附加存储帐户添加到现有的 HDInsight 群集？

否，目前无法将 Azure Data Lake Storage Gen2 存储帐户添加到使用 Blob 存储作为主要存储的群集。 有关详细信息，请参阅[存储选项的比较](hdinsight-hadoop-compare-storage-options.md)。

### <a name="how-can-i-find-the-currently-linked-service-principal-for-a-data-lake-storage-account"></a>如何查找 Data Lake 存储帐户当前链接的服务主体？

可以在 Azure 门户中群集属性下的“Data Lake Storage Gen1 访问”中找到相关设置。**** 有关详细信息，请参阅[验证群集设置](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md#verify-cluster-set-up)。
 
### <a name="how-can-i-calculate-the-usage-of-storage-accounts-and-blob-containers-for-my-hdinsight-clusters"></a>如何计算 HDInsight 群集的存储帐户和 Blob 容器的用量？

执行下列操作之一：

- [使用电源外壳](../storage/scripts/storage-blobs-container-calculate-size-powershell.md)

- 使用以下命令行查找 HDInsight 群集上的 */user/hive/.Trash/* 文件夹大小：
  
  `hdfs dfs -du -h /user/hive/.Trash/`

### <a name="how-can-i-set-up-auditing-for-my-blob-storage-account"></a>如何设置 Blob 存储帐户的审核？

若要审核 Blob 存储帐户，请使用[在 Azure 门户中监视存储帐户](../storage/common/storage-monitor-storage-account.md)中的过程配置监视。 HDFS 审核日志仅提供本地 HDFS 文件系统 (hdfs://mycluster) 的审核信息。  此日志不包括针对远程存储执行的操作。

### <a name="how-can-i-transfer-files-between-a-blob-container-and-an-hdinsight-head-node"></a>如何在 Blob 容器与 HDInsight 头节点之间传输文件？

在头节点上运行类似于以下 shell 脚本的脚本：

```shell
for i in cat filenames.txt
do
   hadoop fs -get $i <local destination>
done
```
 
> [!NOTE]
> 文件 *filenames.txt* 包含 Blob 容器中的文件的绝对路径。
 
### <a name="are-there-any-ranger-plugins-for-storage"></a>是否有任何适用于存储的 Ranger 插件？

目前，Blob 存储和 Azure Data Lake Storage Gen1 或 Gen2 没有任何 Ranger 插件。 对于 ESP 群集，应使用 Azure Data Lake Storage，因为至少可以使用 HDFS 工具在文件系统级别手动设置精细权限。 此外，在使用 Azure Data Lake Storage 时，ESP 群集将在群集级别使用 Azure Active Directory 执行某种文件系统访问控制。 

可以使用 Azure 存储资源管理器将数据访问策略分配到用户的安全组。 有关详细信息，请参阅：

- [如何设置 Azure AD 用户的权限，以使用 Hive 或其他服务在 Data Lake Storage Gen2 中查询数据？](hdinsight-hadoop-use-data-lake-storage-gen2.md#how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services)
- [使用 Azure 存储资源管理器和 Azure Data Lake Storage Gen2 设置文件和目录级别权限](/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

### <a name="can-i-increase-hdfs-storage-on-a-cluster-without-increasing-the-disk-size-of-worker-nodes"></a>是否可以在不增大工作器节点的磁盘大小的情况下增大群集上的 HDFS 存储？

否。无法增大任何工作器节点的磁盘大小，因此，增大磁盘大小的唯一方法是删除群集，然后使用更大的工作器 VM 重新创建群集。 请不要使用 HDFS 来存储任何 HDInsight 数据，因为删除群集时会删除这些数据。 请改为在 Azure 中存储数据。 纵向扩展群集也可以将更多的容量添加到 HDInsight 群集。

## <a name="edge-nodes"></a>边缘节点

### <a name="can-i-add-an-edge-node-after-the-cluster-has-been-created"></a>创建群集后是否可以添加边缘节点？

只能在创建群集时将边缘节点添加到 HDInsight 群集或新的群集。 有关详细信息，请参阅[在 HDInsight 中的 Apache Hadoop 群集上使用空边缘节点](hdinsight-apps-use-edge-node.md)一文。

### <a name="how-can-i-connect-to-an-edge-node"></a>如何连接到边缘节点？

创建边缘节点后，可以在端口 22 上使用 SSH 连接到该节点。 可以在群集门户中找到边缘节点的名称。 名称通常以 *-ed* 结尾。

### <a name="why-are-persisted-scripts-not-running-automatically-on-newly-created-edge-nodes"></a>持久化脚本为何未在新建的边缘节点上自动运行？

持久化脚本用于自定义通过缩放操作添加到群集的新工作器节点。 持久化脚本不适用于边缘节点。

## <a name="rest-api"></a>REST API

### <a name="what-are-the-rest-api-calls-to-pull-a-tez-query-view-from-the-cluster"></a>可使用哪些 REST API 调用从群集提取 Tez 查询视图？

可使用以下 REST 终结点来提取 JSON 格式的所需信息。 使用基本身份验证标头发出请求。

- Tez 查询视图*\//\<：https：群集名称>.azurehdinsight.net/ws/v1/时间线/HIVE_QUERY_ID/*
- Tez Dag 视图*\//\<：https：群集名称>.azurehdinsight.net/ws/v1/时间线/TEZ_DAG_ID/*

### <a name="how-do-i-retrieve-the-configuration-details-from-hdi-cluster-by-using-an-azure-active-directory-user"></a>如何以 Azure Active Directory 用户身份从 HDI 群集检索配置详细信息？

若要以 AAD 用户身份协商正确的身份验证令牌，请使用以下格式浏览网关：

* https://`<cluster dnsname>`.azurehdinsight.net/api/v1/clusters/testclusterdem/stack_versions/1/repository_versions/1 

### <a name="how-do-i-use-ambari-restful-api-to-monitor-yarn-performance"></a>如何使用 Ambari Restful API 来监视 YARN 性能？

如果在同一虚拟网络或对等互连的虚拟网络中调用 Curl 命令，则该命令为：

```curl
curl -u <cluster login username> -sS -G
http://<headnodehost>:8080/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
```
 
如果从虚拟网络外部或者从非对等互连的虚拟网络调用该命令，则命令格式为：

- 对于非 ESP 群集：
  
  ```curl
  curl -u <cluster login username> -sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

- 对于 ESP 群集：
  
  ```curl
  curl -u <cluster login username>-sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

> [!NOTE]
> Curl 将提示输入密码。 必须输入群集登录用户名的有效密码。

## <a name="billing"></a>计费

### <a name="how-much-does-it-cost-to-deploy-an-hdinsight-cluster"></a>部署一个 HDInsight 群集需要多少费用？

有关定价以及计费相关的常见问题解答的详细信息，请参阅 [Azure HDInsight 定价](https://azure.microsoft.com/pricing/details/hdinsight/)页。

### <a name="when-does-hdinsight-billing-start--stop"></a>HDInsight 计费何时开始和停止？

创建群集后便开始 HDInsight 群集计费，删除群集后停止计费。 群集按分钟按比例计费。

### <a name="how-do-i-cancel-my-subscription"></a>如何取消订阅？

有关如何取消订阅的信息，请参阅[取消 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-how-to-cancel-azure-subscription)。

### <a name="for-pay-as-you-go-subscriptions-what-happens-after-i-cancel-my-subscription"></a>对于即用即付订阅，取消订阅后会发生什么情况？

有关取消订阅后发生的情况的详细信息，请参阅[取消订阅之后会发生什么情况？](/azure/billing/billing-how-to-cancel-azure-subscription)

## <a name="hive"></a>Hive

### <a name="why-does-the-hive-version-appear-as-121000-instead-of-21-in-the-ambari-ui-even-though-i-am-running-an-hdinsight-36-cluster"></a>为何即使我运行的是 HDInsight 3.6 群集，Ambari UI 中显示的 Hive 版本也仍是 1.2.1000 而不是 2.1？

尽管 Ambari UI 中仅显示 1.2，但 HDInsight 3.6 同时包含 Hive 1.2 和 Hive 2.1。

## <a name="other-faq"></a>其他常见问题解答

### <a name="what-does-hdinsight-offer-in-terms-of-real-time-stream-processing-capabilities"></a>HDInsight 提供哪些有关实时流处理的功能？

有关 Azure HDInsight 中的流处理集成功能的信息，请参阅[在 Azure 中选择流处理技术](/azure/architecture/data-guide/technology-choices/stream-processing)。

### <a name="is-there-a-way-to-dynamically-terminate-the-head-node-of-the-cluster-when-the-cluster-is-idle-for-a-specific-period"></a>当群集空闲特定的时间段后，是否可以动态终止群集的头节点？

无法对 HDInsight 群集执行此操作。 可以使用 Azure 数据工厂实现此类目的。

### <a name="what-compliance-offerings-does-hdinsight-offer"></a>HDInsight 提供哪些合规性产品？

有关合规性信息，请参阅[Microsoft 信任中心和](https://www.microsoft.com/trust-center) [Microsoft Azure 合规性概述](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)。
