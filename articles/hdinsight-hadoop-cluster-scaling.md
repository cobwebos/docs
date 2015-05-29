<properties
   pageTitle="HDInsight 中的群集缩放 | Azure"
   description="更改 HDInsight 上运行的群集中的数据节点数，而无需删除然后再重新创建群集。"
   services="hdinsight"
   documentationCenter=""
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight" 
   ms.devlang="na" 
   ms.topic="article" 
   ms.tgt_pltfrm="na" 
   ms.workload="big-data" 
   ms.date="04/02/2015" 
   wacn.date="" 
   ms.author="jgao"/>

# HDInsight 中的群集缩放

群集缩放功能可让你更改 Azure HDInsight 中运行的群集使用的数据节点数，而无需删除然后再重新创建群集。你可以通过 Azure PowerShell、HDInsight SDK 或 Azure 门户执行该操作。

## 功能详细信息
本部分介绍更改 HDInsight 支持的每种群集所用数据节点数会有何影响：

* Hadoop
* Storm
* HBase 

## Hadoop 

### 添加数据节点
你可以顺利地增加正在运行的 Hadoop 群集中的数据节点数，而不会影响任何挂起或运行中的作业。你还可以在操作进行中提交新作业。系统会正常处理失败的缩放操作，让群集始终保持正常运行状态。

### 删除数据节点
减少数据节点数目以缩减 Hadoop 群集时，系统会重新启动群集中的某些服务。这会导致所有正在运行和挂起的作业在缩放操作完成时失败。但是，你可以在操作完成后重新提交这些作业。

## Storm
你可以顺利地在 Storm 群集运行时对其添加或删除数据节点。但是，在缩放操作成功完成后，你需要重新平衡拓扑。

可以使用两种方法来完成重新平衡操作：

* Storm Web UI
* 命令行界面 (CLI) 工具 

有关详细信息，请参阅 [Apache Storm 文档](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)。

HDInsight 群集上提供了 Storm Web UI：

![image1](./media/hdinsight-hadoop-cluster-scaling/StormUI.png)

以下是有关如何使用 CLI 命令重新平衡 Storm 拓扑的示例：

	## Reconfigure the topology "mytopology" to use 5 worker processes,
	## the spout "blue-spout" to use 3 executors, and
	## the bolt "yellow-bolt" to use 10 executors

	$ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

## HBase
你可以顺利地在 HBase 群集运行时对其添加或删除节点。在完成缩放操作后的几分钟内，区域服务器就能自动平衡。不过，你也可以手动平衡区域服务器，方法是登录到群集的头节点，然后在命令提示符窗口中运行以下命令：

	>pushd %HBASE_HOME%\bin
	>hbase shell
	>balancer

## 先决条件

* 只支持使用 HDInsight 3.1.3 或更高版本的群集。如果不确定你的群集是什么版本，可以在 Azure 门户中单击 HDInsight 群集名称或者从 Azure PowerShell 运行 `Get-AzureHDInsightCluster -name <clustername>` 命令，来检查群集的版本。

* 若要从 Azure PowerShell 执行该操作，必须使用 Azure PowerShell 0.8.14 或更高版本。你可以在 [Azure 下载](/downloads/)网站上，从"命令行工具"区域下载最新版本的 Azure PowerShell。可以从 Azure PowerShell 窗口使用以下命令，检查所安装的 Azure PowerShell 版本： `(get-module Azure).Version`

## 如何使用群集缩放

### Azure 门户
从 Azure HDInsight 群集仪表板的"缩放"选项卡可以更改正在运行的群集的大小。

![](http://i.imgur.com/u5Mewwx.png)

### Azure PowerShell
若要使用 Azure PowerShell 更改 Hadoop 群集大小，请从客户端计算机运行以下命令：

	Set-AzureHDInsightClusterSize -ClusterSizeInNodes <NewSize> -name <clustername>	

> [AZURE.NOTE] 客户端计算机必须安装 Azure PowerShell 0.8.14 或更高版本才能使用这个命令。

### SDK 中 IsInRole 中的声明
若要使用 HDInsight SDK 更改 Hadoop 群集大小，请使用以下方法之一： 

	ChangeClusterSize(string dnsName, string location, int newSize) 

或 

	ChangeClusterSizeAsync(string dnsName, string location, int newSize) 


此方法的同步和异步版本都会返回 [ClusterDetails](http://msdn.microsoft.com/zh-cn/library/microsoft.windowsazure.management.hdinsight.clusterdetails_properties.aspx) 对象。

以下是部分示例代码，演示如何使用此方法的同步版本：

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using System.Security.Cryptography.X509Certificates;
	using Microsoft.WindowsAzure.Management.HDInsight;
	using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;

	namespace HDInsightClusterScaling
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            // Friendly name for the certificate your created earlier  
	            string certfriendlyname = "<CertificateFriendlyName>";     
	            string subscriptionid = "<SubscriptionID>";
	            string clustername = "<ClusterDNSName>";
	     		string location = "<ClusterLocation>"";
				int newSize = <NewClusterSize>;
	
	            // Get the certificate object from certificate store by using the friendly name to identify it
	            X509Store store = new X509Store();
	            store.Open(OpenFlags.ReadOnly);
	            X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);
	
	            // Create an HDInsightClient object
	            HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
	            var client = HDInsightClient.Connect(creds);
	
	            Console.WriteLine("Rescaling HDInsight cluster ...");
	
	            // Change the cluster size
	     		ClusterDetails cluster = client.ChangeClusterSize(clustername, location, newSize);
	            
	            Console.WriteLine("Cluster Rescaled: {0} \n New Cluster Size = {1}", cluster.ConnectionUrl, cluster.ClusterSizeInNodes);
	            Console.WriteLine("Press ENTER to continue.");
	            Console.ReadKey();
	        }
	    }
	}


了解有关使用 HDInsight .NET SDK 的详细信息，请参阅[使用自定义选项在 HDInsight 中设置 Hadoop 群集](/documentation/articles/hdinsight-provision-clusters/)主题。

<!---HONumber=56-->