---
title: Azure HDInsight 常见问题
description: 有关 HDInsight 的常见问题
keywords: 常见问题，常见问题解答
author: Ramakoni1
ms.author: ramakoni
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: 37b8ad0fc09644d746c3528c174d1bf95d546d0f
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706260"
---
# <a name="azure-hdinsight-frequently-asked-questions"></a>Azure HDInsight：常见问题

本文提供了有关如何运行[Azure HDInsight](https://azure.microsoft.com/services/hdinsight/)的一些最常见问题的解答。

## <a name="creating-or-deleting-hdinsight-clusters"></a>创建或删除 HDInsight 群集

### <a name="how-do-i-provision-an-hdinsight-cluster"></a>如何实现预配 HDInsight 群集？

若要查看可用的 HDInsight 群集类型和设置方法，请参阅[在 HDInsight 中使用 Apache Hadoop、Apache Spark、Apache Kafka 等设置群集](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters)。

### <a name="how-do-i-delete-an-existing-hdinsight-cluster"></a>如何实现删除现有的 HDInsight 群集？

若要详细了解删除不再使用的群集的详细信息，请参阅[删除 HDInsight 群集](hdinsight-delete-cluster.md)。

创建和删除操作之间至少30到60分钟。 否则，该操作可能会失败，并出现以下错误消息：

``Conflict (HTTP Status Code: 409) error when attempting to delete a cluster immediately after creation of a cluster. If you encounter this error, wait until the newly created cluster is in operational state before attempting to delete it.``

### <a name="how-do-i-select-the-correct-number-of-cores-or-nodes-for-my-workload"></a>如何实现为工作负荷选择正确的核心或节点数？

适当数量的核心和其他配置选项取决于不同的因素。

有关详细信息，请参阅[HDInsight 群集的容量规划](https://docs.microsoft.com/azure/hdinsight/hdinsight-capacity-planning)。

### <a name="what-can-i-do-when-cluster-provisioning-fails-because-of-a-capacity-issue"></a>由于容量问题而导致群集预配失败，我该怎么办？

此部分中提供了常见容量问题和缓解技术。

#### <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>错误：部署将超过 "800" 的配额

Azure 的配额限制为每个资源组 800 个部署。 每个资源组、订阅、帐户或其他范围应用不同的配额。 例如，订阅可能配置为限制某个区域的核心数目。 如果尝试部署的虚拟机的内核数多于允许的数量，你会收到一条错误消息，指出已超出配额。

若要解决此问题，请使用 Azure 门户、CLI 或 PowerShell 删除不再需要的部署。

有关详细信息，请参阅[解决资源配额错误](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors)。

#### <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>错误：最大节点已超过此区域中的可用内核数

订阅可能配置为限制某个区域的核心数目。 如果尝试部署的资源的内核数多于允许的数量，会收到一条错误消息，指出已超出配额。

若要请求增加配额，请按以下步骤操作：

1. 请参阅[Azure 门户](https://portal.azure.com)，并选择 "**帮助 + 支持**"。
   
1. 选择“新建支持请求”。
   
1. 在 "**新建支持请求**" 页的 "**基本**信息" 选项卡上，提供以下信息：
   
   - **问题类型：** 选择**服务和订阅限制（配额）** 。
   - **订阅：** 选择要修改的订阅。
   - **配额类型：** 选择 " **HDInsight**"。

有关详细信息，请参阅[创建支持票证来增加核心](hdinsight-capacity-planning.md#quotas)。

### <a name="what-are-the-various-types-of-nodes-in-an-hdinsight-cluster"></a>HDInsight 群集中的各种类型的节点是什么？

Azure HDInsight 群集具有不同类型的虚拟机或节点。 每个节点类型在系统的操作中扮演着一个角色。

有关详细信息，请参阅[Azure HDInsight 群集中的资源类型](hdinsight-virtual-network-architecture.md#resource-types-in-azure-hdinsight-clusters)。

## <a name="individual-components"></a>单个组件

### <a name="can-i-install-additional-components-on-my-cluster"></a>能否在群集上安装其他组件？

可以。 若要安装其他组件或自定义群集配置，请使用：

- 在创建过程中或之后创建脚本。 脚本可通过[脚本操作调用，脚本操作](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux)是一种配置选项，可在 Azure 门户、Hdinsight Windows PowerShell Cmdlet 或 HDINSIGHT .net SDK 中使用。 此配置选项可在 Azure 门户、HDInsight Windows PowerShell cmdlet 或 HDInsight .NET SDK 中使用。

- 预配群集后 `sudo` 或其他方法。
  
- 用于安装生态应用程序的[HDInsight 应用程序平台](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/)。

但 Microsoft 支持部门团队仅在以下情况下才会提供支持：

- 加载脚本时发生的问题或错误。 执行自定义脚本过程中出现的任何错误都超出了支持票证的范围。

- 作为群集创建过程的一部分的其他应用程序。 

有关支持的组件的列表，请参阅[HDInsight 提供的 Apache Hadoop 组件和版本有哪些？](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)

对单个组件的支持也会因群集类型而有所不同。 例如，Spark 在 Kafka 群集上不受支持，反之亦然。

对于群集创建过程以外的应用程序和服务，请与供应商或服务提供商联系以获取支持。 你还可以使用多个社区支持站点。 例如，HDInsight 和[Stack Overflow](https://stackoverflow.com/)的[MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)。 Apache 项目还在 [Apache 网站](https://apache.org/)上提供了项目站点， 例如 [Hadoop](https://hadoop.apache.org/)。 

有关 Azure 支持的详细信息，请参阅[Azure 支持常见问题](https://azure.microsoft.com/support/faq/)。

### <a name="can-i-upgrade-the-individual-components-that-are-pre-installed-on-the-cluster"></a>是否可以升级预安装在群集上的单个组件？

如果升级内置组件或预安装在群集上的应用程序，Microsoft 将不支持生成的配置。 Microsoft 未测试这些系统配置。 尝试使用其他版本的 HDInsight 群集，该版本可能已预安装了组件的升级版本。

例如，不支持将 Hive 升级为单个组件。 HDInsight 是一种托管服务，许多服务与 Ambari server 集成并经过测试。 自行升级 Hive 会导致其他组件的索引二进制文件发生更改，并将导致群集上出现组件集成问题。

### <a name="can-spark-and-kafka-run-on-the-same-hdinsight-cluster"></a>Spark 和 Kafka 是否可以在同一 HDInsight 群集上运行？

不能，无法在同一 HDInsight 群集上运行 Apache Kafka 和 Apache Spark。 为 Kafka 和 Spark 创建单独的群集，以避免资源争用问题。

### <a name="how-do-i-change-timezone-in-ambari"></a>如何实现更改 Ambari 中的时区？

1. 在 https://CLUSTERNAME.azurehdinsight.net 打开 Ambari Web UI，其中 CLUSTERNAME 是群集的名称。
2. 在右上角选择 "管理" |设置。 

   ![Ambari 设置](media/hdinsight-faq/ambari-settings.png)

3. 在 "用户设置" 窗口中，从 "时区" 下拉范围中选择新时区，然后单击 "保存"。

   ![Ambari 用户设置](media/hdinsight-faq/ambari-user-settings.png)

## <a name="metastore"></a>元存储

### <a name="how-can-i-migrate-from-the-existing-metastore-to-azure-sql-server"></a>如何从现有的元存储迁移到 Azure SQL Server？ 

若要从 SQL Server 迁移到 Azure SQL Server，请参阅[教程：在 AZURE SQL 数据库中使用 DMS 脱机迁移 SQL Server 到单一数据库或池中的数据库](../dms/tutorial-sql-server-to-azure-sql.md)。

### <a name="is-the-hive-metastore-deleted-when-the-cluster-is-deleted"></a>删除群集后是否删除 Hive 元存储？

这取决于群集配置为使用的元存储类型。

对于默认的元存储：默认元存储是群集生命周期的一部分。 删除群集时，会一并删除相应的元存储和元数据。

对于自定义元存储：元存储的生命周期未绑定到群集的生命周期。 因此，你可以在不丢失元数据的情况下创建和删除群集。 即使在删除并重新创建 HDInsight 群集后，也会保留你的 Hive 架构等元数据。

有关详细信息，请参阅[在 Azure HDInsight 中使用外部元数据存储](hdinsight-use-external-metadata-stores.md)。

### <a name="does-migrating-a-hive-metastore-also-migrate-the-default-policies-of-the-ranger-database"></a>迁移 Hive 元存储还会迁移 Ranger 数据库的默认策略吗？

不需要，策略定义位于 Ranger 数据库中，因此迁移 Ranger 数据库将迁移其策略。

### <a name="can-you-migrate-a-hive-metastore-from-an-enterprise-security-package-esp-cluster-to-a-non-esp-cluster-and-vice-versa"></a>是否可以将 Hive 元存储从企业安全性套餐（ESP）群集迁移到非 ESP 群集，反之亦然？

是的，可以将 Hive 元存储从 ESP 迁移到非 ESP 群集。

### <a name="how-can-i-estimate-the-size-of-a-hive-metastore-database"></a>如何估算 Hive 元存储数据库的大小？

Hive 元存储用于存储 Hive 服务器使用的数据源的元数据。大小要求在一定程度上取决于 Hive 数据源的数量和复杂程度，并且不能提前估算。 如[Hive 元存储最佳实践](hdinsight-use-external-metadata-stores.md#hive-metastore-best-practices)中所述，可以从 S2 层开始，该层提供 50 DTU 和 250 GB 的存储空间，如果看到瓶颈，则可以纵向扩展数据库。

### <a name="do-you-support-any-other-database-other-than-azure-sql-database-as-an-external-metastore"></a>是否支持作为外部元存储的 Azure SQL 数据库以外的其他任何数据库？

不，Microsoft 仅支持将 Azure SQL 数据库作为外部自定义元存储。

### <a name="can-i-share-a-metastore-across-multiple-clusters"></a>能否跨多个群集共享元存储？

是的，你可以在多个群集之间共享自定义元存储，只要它们使用相同版本的 HDInsight。

## <a name="connectivity-and-virtual-networks"></a>连接和虚拟网络  

### <a name="what-are-the-implications-of-blocking-ports-22-and-23-on-my-network"></a>阻止端口22和23在网络上有什么影响？

如果阻止端口22和端口23，则不会有权访问群集。 HDInsight 服务不使用这些端口。

有关详细信息，请参阅以下文档：

- [控制网络流量](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#networktraffic)

- [使用专用终结点将传入流量保护到虚拟网络中的 HDInsight 群集](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)

- [HDInsight 管理 IP 地址](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)

### <a name="can-i-deploy-an-additional-virtual-machine-within-the-same-subnet-as-an-hdinsight-cluster"></a>是否可以在 HDInsight 群集所在的子网中部署其他虚拟机？

是的，你可以在 HDInsight 群集所在的子网中部署其他虚拟机。 可以进行以下配置：

- 边缘节点：可将其他边缘节点添加到群集，如在[HDInsight 中的 Apache Hadoop 群集上使用空边缘节点](hdinsight-apps-use-edge-node.md)中所述。

- 独立节点：可以将独立虚拟机添加到相同的子网，并使用专用终结点 `https://<CLUSTERNAME>-int.azurehdinsight.net`从该虚拟机访问群集。 有关详细信息，请参阅[控制网络流量](hdinsight-plan-virtual-network-deployment.md#networktraffic)。

### <a name="should-i-store-data-on-the-local-disk-of-an-edge-node"></a>是否应将数据存储在边缘节点的本地磁盘上？

不是，将数据存储在本地磁盘上并不是个好主意。 如果该节点发生故障，则本地存储的所有数据都将丢失。 建议在 Azure Data Lake Storage Gen2 或 Azure Blob 存储中存储数据，或通过装载 Azure 文件共享来存储数据。


### <a name="can-i-add-an-existing-hdinsight-cluster-to-another-virtual-network"></a>是否可以将现有 HDInsight 群集添加到另一个虚拟网络？

不，不可以。 应在设置时指定虚拟网络。 如果在预配期间未指定任何虚拟网络，则部署将创建无法从外部访问的内部网络。 有关详细信息，请参阅[将 HDInsight 添加到现有虚拟网络](hdinsight-plan-virtual-network-deployment.md#existingvnet)。

## <a name="security-and-certificates"></a>安全性和证书

### <a name="what-are-the-recommendations-for-malware-protection-on-azure-hdinsight-clusters"></a>对于 Azure HDInsight 群集上的恶意软件防护有哪些建议？

有关恶意软件防护的详细信息，请参阅[适用于 Azure 云服务和虚拟机的 Microsoft 反恶意软件](../security/fundamentals/antimalware.md)。

### <a name="how-do-i-create-a-keytab-for-an-hdinsight-esp-cluster"></a>如何实现为 HDInsight ESP 群集创建 keytab？

为域用户名创建 Kerberos keytab。 稍后可以使用此 keytab 在未输入密码的情况下向已加入域的远程群集进行身份验证。 域名为大写字母：

```shell
ktutil
ktutil: addent -password -p <username>@<DOMAIN.COM> -k 1 -e RC4-HMAC
Password for <username>@<DOMAIN.COM>: <password>
ktutil: wkt <username>.keytab
ktutil: q
```

### <a name="can-i-use-an-existing-azure-active-directory-tenant-to-create-an-hdinsight-cluster-that-has-the-esp"></a>能否使用现有 Azure Active Directory 租户创建具有 ESP 的 HDInsight 群集？

必须先启用 Azure Active Directory 域服务（Azure AD DS），然后才能创建具有 ESP 的 HDInsight 群集。 开源 Hadoop 依赖于 Kerberos 进行身份验证（而不是 OAuth）。

若要将 Vm 加入域，则必须具有域控制器。 Azure AD DS 是托管域控制器，被视为 Azure Active Directory 扩展，它提供以托管方式构建安全 Hadoop 群集的所有 Kerberos 要求。 作为托管服务的 HDInsight 与 Azure AD DS 集成，以提供端到端的安全性。

### <a name="can-i-use-a-self-signed-certificate-in-an-aad-ds-secure-ldap-setup-and-provision-an-esp-cluster"></a>能否在 AAD DS 安全 LDAP 设置中使用自签名证书并设置 ESP 群集？

建议使用证书颁发机构颁发的证书，但 ESP 上也支持使用自签名证书。 有关详细信息，请参阅：

- [启用 Azure Active Directory 域服务](domain-joined/apache-domain-joined-configure-using-azure-adds.md#enable-azure-ad-ds)

- [教程：为 Azure Active Directory 域服务托管域配置安全 LDAP](../active-directory-domain-services/tutorial-configure-ldaps.md)

### <a name="how-can-i-pull-login-activity-shown-in-ranger"></a>如何请求 Ranger 中显示的登录活动？

出于审核要求，Microsoft 建议启用 Azure Monitor 日志，如[使用 Azure Monitor 日志监视 HDInsight 群集](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial)中所述。

### <a name="can-i-disable-clamscan-on-my-cluster"></a>能否在群集上禁用 Clamscan？

Clamscan 是在 HDInsight 群集上运行的防病毒软件，由 Azure 安全（azsecd）用于保护群集免受病毒攻击。 Microsoft 强烈建议用户避免对默认 Clamscan 配置进行任何更改。

此过程不会干扰其他进程，也不会对其进行任何循环。 它将始终产生其他进程。 仅当系统处于空闲状态时，才应看到来自 Clamscan 的 CPU 峰值。  

在必须控制计划的情况下，可以使用以下步骤：

1. 使用以下命令禁用自动执行：
   
   `/usr/local/vbin/azsecd config -s clamav -d Disabled`
   
1. 添加一个 Cron 作业，它以 root 身份运行以下命令：
   
   `/usr/local/bin/azsecd manual -s clamav`

有关如何设置和运行 cron 作业的详细信息，请参阅[如何实现设置 cron 作业](https://askubuntu.com/questions/2368/how-do-i-set-up-a-cron-job)？

### <a name="why-is-llap-available-on-spark-esp-clusters"></a>为什么 Spark ESP 群集上有 LLAP？
在 ESP Spark 群集上，出于安全原因（即 Apache Ranger）而不是性能，启用 LLAP。 应使用更大的节点 Vm 来适应 LLAP 的资源使用情况（例如，最小 D13V2）。 

### <a name="how-can-i-add-addional-aad-groups-after-creating-an-esp-cluster"></a>创建 ESP 群集后如何添加其他 AAD 组？
可以通过两种方式实现此目的： 1-可以重新创建群集，并在创建群集时添加其他组。 如果正在 AAD-DS 中使用作用域同步，请确保范围内同步中包含组 B。
2-将组添加为之前组中用于创建 ESP 群集的嵌套子组。 例如，如果你已使用组 `A`创建了一个 ESP 群集，稍后你可以将组 `B` 作为 `A` 的嵌套子组，在大约一小时后，它将自动同步并在群集中可用。 

## <a name="storage"></a>存储空间

### <a name="can-i-add-an-azure-data-lake-storage-gen2-to-an-existing-hdinsight-cluster-as-an-additional-storage-account"></a>是否可以将 Azure Data Lake Storage Gen2 添加到现有 HDInsight 群集作为附加存储帐户？

不可以，目前无法将 Azure Data Lake Storage Gen2 的存储帐户添加到将 blob 存储作为其主存储的群集。 有关详细信息，请参阅[比较存储选项](hdinsight-hadoop-compare-storage-options.md)。

### <a name="how-can-i-find-the-currently-linked-service-principal-for-a-data-lake-storage-account"></a>如何查找 Data Lake 存储帐户的当前链接的服务主体？

你可以在 Azure 门户的群集属性下**Data Lake Storage Gen1 访问**中找到你的设置。 有关详细信息，请参阅[验证群集设置](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md#verify-cluster-set-up)。
 
### <a name="how-can-i-calculate-the-usage-of-storage-accounts-and-blob-containers-for-my-hdinsight-clusters"></a>如何计算 HDInsight 群集的存储帐户和 blob 容器的使用情况？

执行下列操作之一：

- [使用 PowerShell](../storage/scripts/storage-blobs-container-calculate-size-powershell.md)

- 查找/user/hive/. 的大小HDInsight 群集上的垃圾桶/文件夹，使用以下命令行：
  
  `hdfs dfs -du -h /user/hive/.Trash/`

### <a name="how-can-i-set-up-auditing-for-my-blob-storage-account"></a>如何为 blob 存储帐户设置审核？

若要审核 blob 存储帐户，请使用在[Azure 门户中监视存储帐户中](../storage/common/storage-monitor-storage-account.md)的过程配置监视。 HDFS 审核日志仅提供本地 HDFS 文件系统（hdfs://mycluster）的审核信息。  它不包括在远程存储上执行的操作。

### <a name="how-can-i-transfer-files-between-a-blob-container-and-an-hdinsight-head-node"></a>如何在 blob 容器和 HDInsight 头节点之间传输文件？

在头节点上运行类似于以下 shell 脚本的脚本：

```shell
for i in cat filenames.txt
do
   hadoop fs -get $i <local destination>
done
```
 
> [!NOTE]
> 文件*文件名 .txt*将具有 blob 容器中的文件的绝对路径。
 
### <a name="are-there-any-ranger-plugins-for-storage"></a>是否有适用于存储的 Ranger 插件？

目前 blob 存储和 Azure Data Lake Storage Gen1 或 Gen2 不存在任何 Ranger 插件。 对于 ESP 群集，你应使用 Azure Data Lake Storage，因为你至少可以使用 HDFS 工具在文件系统级别上手动设置精细权限。 此外，在使用 Azure Data Lake Storage 时，ESP 群集将使用群集级别的 Azure Active Directory 执行一些文件系统访问控制。 

可以使用 Azure 存储资源管理器将数据访问策略分配给用户的安全组。 有关详细信息，请参阅：

- [如何实现使用 Hive 或其他服务为 Azure AD 用户设置 Data Lake Storage Gen2 查询数据的权限？](hdinsight-hadoop-use-data-lake-storage-gen2.md#how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services)
- [使用 Azure 存储资源管理器和 Azure Data Lake Storage Gen2 设置文件和目录级别权限](/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

### <a name="can-i-increase-hdfs-storage-on-a-cluster-without-increasing-the-disk-size-of-worker-nodes"></a>能否增加群集上的 HDFS 存储而不增加辅助角色节点的磁盘大小？

不可以增加任何辅助角色节点的磁盘大小，因此，增加磁盘大小的唯一方法是删除群集，并在更大的辅助 Vm 上重新创建。 请勿使用 HDFS 存储任何 HDInsight 数据，因为如果删除群集，则会删除数据。 请改为将数据存储在 Azure 中。 增加群集还可以将额外容量添加到 HDInsight 群集。

## <a name="edge-nodes"></a>边缘节点

### <a name="can-i-add-an-edge-node-after-the-cluster-has-been-created"></a>创建群集后，是否可以添加边缘节点？

HDInsight 群集，或者在创建群集时到新群集。 有关详细信息，请参阅[在 HDInsight 中的 Apache Hadoop 群集上使用空边缘节点](hdinsight-apps-use-edge-node.md)一文。

### <a name="how-can-i-connect-to-an-edge-node"></a>如何连接到边缘节点？

创建边缘节点后，可以在端口22上使用 SSH 连接到该节点。 你可以从群集门户中找到边缘节点的名称。 名称通常以 *-ed*结尾。

### <a name="why-are-persisted-scripts-not-running-automatically-on-newly-created-edge-nodes"></a>为什么持久性脚本未在新创建的边缘节点上自动运行？

使用持久化脚本通过缩放操作自定义添加到群集的新辅助角色节点。 持久化脚本不适用于边缘节点。

## <a name="rest-api"></a>REST API

### <a name="what-are-the-rest-api-calls-to-pull-a-tez-query-view-from-the-cluster"></a>从群集请求 Tez 查询视图有哪些 REST API 调用？

你可以使用以下 REST 终结点以 JSON 格式请求所需的信息。 使用基本身份验证标头发出请求。

- Tez 查询视图： *https：\//\<群集名称 > clustername>.azurehdinsight.net/ws/v1/timeline/HIVE_QUERY_ID* /
- Tez Dag 视图： *https：\//\<群集名称 >. net/ws/v1/timeline/TEZ_DAG_ID* /

### <a name="how-do-i-retrieve-the-configuration-details-from-hdi-cluster-by-using-an-azure-active-directory-user"></a>如何实现使用 Azure Active Directory 用户从 HDI 群集中检索配置详细信息吗？

若要与 AAD 用户协商正确的身份验证令牌，请使用以下格式浏览网关：

* https://`<cluster dnsname>`. azurehdinsight.net/api/v1/clusters/testclusterdem/stack_versions/1/repository_versions/1 

### <a name="how-do-i-use-ambari-restful-api-to-monitor-yarn-performance"></a>如何实现使用 Ambari Restful API 来监视 YARN 性能？

如果在同一虚拟网络或对等互连的虚拟网络中调用了卷曲命令，则该命令为：

```curl
curl -u <cluster login username> -sS -G
http://<headnodehost>:8080/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
```
 
如果从虚拟网络外部或从非对等互连的虚拟网络调用命令，则命令格式为：

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
> 卷将提示你输入密码。 必须输入群集登录用户名的有效密码。

## <a name="billing"></a>计费

### <a name="how-much-does-it-cost-to-deploy-an-hdinsight-cluster"></a>部署 HDInsight 群集需要多少费用？

有关与计费相关的定价和常见问题解答的详细信息，请参阅[Azure HDInsight 定价](https://azure.microsoft.com/pricing/details/hdinsight/)页。

### <a name="when-does-hdinsight-billing-start--stop"></a>HDInsight 计费何时开始 & 停止？

创建群集后便开始 HDInsight 群集计费，删除群集后停止计费。 每分钟按比例计费。

### <a name="how-do-i-cancel-my-subscription"></a>如何实现取消我的订阅？

有关如何取消订阅的信息，请参阅[取消 Azure 订阅](https://docs.microsoft.com/azure/billing/billing-how-to-cancel-azure-subscription)。

### <a name="for-pay-as-you-go-subscriptions-what-happens-after-i-cancel-my-subscription"></a>对于即用即付订阅，在取消订阅后会发生什么情况？

有关订阅在取消后的详细信息，请参阅[在取消订阅后会发生什么情况？](/azure/billing/billing-how-to-cancel-azure-subscription)

## <a name="hive"></a>Hive

### <a name="why-does-the-hive-version-appear-as-121000-instead-of-21-in-the-ambari-ui-even-though-i-am-running-an-hdinsight-36-cluster"></a>即使我在运行 HDInsight 3.6 群集，Hive 版本为何会在 Ambari UI 中显示为1.2.1000 而不是2.1？

尽管 Ambari UI 中仅显示1.2，但 HDInsight 3.6 同时包含 Hive 1.2 和 Hive 2.1。

## <a name="other-faq"></a>其他常见问题

### <a name="what-does-hdinsight-offer-in-terms-of-real-time-stream-processing-capabilities"></a>HDInsight 提供的实时流处理功能是什么？

有关 Azure HDInsight 中的流处理集成功能的信息，请参阅[在 azure 中选择流处理技术](/azure/architecture/data-guide/technology-choices/stream-processing)。

### <a name="is-there-a-way-to-dynamically-terminate-the-head-node-of-the-cluster-when-the-cluster-is-idle-for-a-specific-period"></a>当群集在特定时间段内处于空闲状态时，是否可以动态终止群集的头节点？

不能对 HDInsight 群集执行此操作。 在这些情况下，可以使用 Azure 数据工厂。

### <a name="what-compliance-offerings-does-hdinsight-offer"></a>HDInsight 提供哪些符合性产品？

有关符合性信息，请参阅[Microsoft 信任中心](https://www.microsoft.com/trust-center)和[Microsoft Azure 符合性概述](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)。
