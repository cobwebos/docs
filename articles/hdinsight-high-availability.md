<properties 
	pageTitle="HDInsight 中 Hadoop 群集的可用性 | Azure" 
	description="HDInsight 可部署高可用的可靠群集。" 
	services="hdinsight" 
	editor="cgronlun" 
	manager="paulettm" 
	authors="bradsev" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/10/2014" 
	wacn.date="" 
	ms.author="bradsev"/>


# HDInsight 中 Hadoop 群集的可用性和可靠性

## 介绍 ##
第二个头节点已添加到 HDInsight 所部署的 Hadoop 群集，以提高管理工作负载所需的服务的可用性和可靠性。Hadoop 群集的标准实现通常具有单个头节点。这些群集设计为顺畅管理从节点故障，不过在头节点上运行的主服务的任何停机都会导致群集停止工作。 

![](http://i.imgur.com/jrUmrH4.png)

HDInsight 可通过添加辅助头节点 (Head Node1) 避免此单点故障。[ZooKeeper][zookeeper] nodes (ZK) 已添加，用于头节点的主导选择，并确保从节点和网关 (GW) 知道在活动头节点 (Head Node0) 处于非活动状态的情况下何时故障转移到辅助头节点 (Head Node1)。


## 如何检查活动头节点上的服务状态 ##
若要确定哪个头节点处于活动状态，并检查该头节点上运行的服务状态，你必须通过使用远程桌面协议 (RDP) 连接到 Hadoop 群集。默认情况下，将在 Azure 中禁用远程连接到群集的功能，因此，必须首先启用它。有关如何在门户中执行此操作的说明，请参阅[使用 RDP 连接到 HDInsight 群集](/documentation/articles/hdinsight-administer-use-management-portal/#rdp)
一旦远程连接到了该群集，请双击位于桌面上的 **Hadoop 服务可用状态**图标，以便通过相关状态来了解 Namenode、Jobtracker、Templeton、Oozieservice、Metastore 和 Hiveserver2 服务正在哪个头节点上运行，如果是 HDI 3.0，则可通过相关状态来了解 Namenode、Resource Manager、History Server、Templeton、Oozieservice、Metastore 和 Hiveserver2 服务正在哪个头节点上运行。

![](http://i.imgur.com/MYTkCHW.png)


## 如何访问辅助头节点上的日志文件 ##

当辅助头节点变为活动头节点时，可以通过浏览 JobTracker UI 来访问辅助头节点上的作业日志，就像是在主活动节点上操作一样。若要访问 JobTracker，你必须通过使用 RDP 连接到 Hadoop 群集，如以上部分中所述。一旦远程连接到了该群集，就请双击位于桌面上的**"Hadoop 名称节点"**图标，然后单击**"NameNode 日志"**，即可转到辅助头节点上的日志目录。

![](http://i.imgur.com/eL6jzgB.png)


## 如何配置头节点的大小 ##
默认情况下，头节点分配作为大型虚拟机 (VM)。管理在群集上运行的大多数 Hadoop 作业只需要这种大小。但是有时，头节点可能需要超大 VM。例如，当群集需要管理大量的小型 Oozie 作业时。 

超大 VM 可通过使用 Azure PowerShell cmdlet 或 HDInsight SDK 来配置。

通过使用 Azure PowerShell 创建和设置群集的过程记录在[使用 PowerShell 管理 HDInsight](hdinsight-administer-use-powershell) 中。配置超大头节点需要将 `-HeadNodeVMSize ExtraLarge` 参数添加到此代码中使用的  `New-AzureHDInsightcluster` cmdlet。

    # Create a new HDInsight cluster in Azure PowerShell
	# Configured with an ExtraLarge head-node VM
    New-AzureHDInsightCluster -Name $clusterName -Location $location -HeadNodeVMSize ExtraLarge -DefaultStorageAccountName "$storageAccountName.blob.core.chinacloudapi.cn" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

对于 SDK，情况也是如此。通过使用 SDK 创建和设置群集的过程记录在[使用 HDInsight .NET SDK](hdinsight-provision-clusters#sdk) 中。配置超大头节点需要将  `HeadNodeSize = NodeVMSize.ExtraLarge` 参数添加到此代码中使用的  `ClusterCreateParameters()` 方法。

    # Create a new HDInsight cluster with the HDInsight SDK
	# Configured with an ExtraLarge head-node VM
    ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
    {
    Name = clustername,
    Location = location,
    HeadNodeSize = NodeVMSize.ExtraLarge,
    DefaultStorageAccountName = storageaccountname,
    DefaultStorageAccountKey = storageaccountkey,
    DefaultStorageContainer = containername,
    UserName = username,
    Password = password,
    ClusterSizeInNodes = clustersize
    };


**参考**	

- [ZooKeeper][zookeeper]
- [使用 RDP 连接到 HDInsight 群集](hdinsight-administer-use-management-portal#rdp)
- [使用 HDInsight .NET SDK](hdinsight-provision-clusters#sdk) 


[zookeeper]: http://zookeeper.apache.org/ 

<!---HONumber=56-->