<!-- not suitable for Mooncake -->

<properties
	pageTitle="在 HDInsight 中使用空边缘节点 | Azure"
	description="如何将空边缘节点添加到可充当客户端的 HDInsight 群集，以及如何测试/托管 HDInsight 应用程序。"
	services="hdinsight"
	editor="cgronlun"
	manager="jhubbard"
	authors="mumian"
	tags="azure-portal"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/14/2016"
	wacn.date=""
	ms.author="jgao"/>

# 在 HDInsight 中使用空边缘节点

了解如何将空边缘节点添加到基于 Linux 的 HDInsight 群集。空边缘节点是安装并配置了与头节点中相同的客户端工具，但未运行 hadoop 服务的 Linux 虚拟机。可以使用该边缘节点来访问群集、测试客户端应用程序和托管客户端应用程序。

可以将空边缘节点添加到现有 HDInsight 群集，或者在创建群集时将此类节点添加到新群集。添加空边缘节点的操作是使用 Azure Resource Manager 模板完成的。以下示例演示如何使用模板执行此操作：

    "resources": [
		{
			"name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
			"type": "Microsoft.HDInsight/clusters/applications",
			"apiVersion": "[variables('clusterApiVersion')]",
			"properties": {
				"marketPlaceIdentifier": "EmptyNode",
				"computeProfile": {
					"roles": [{
						"name": "edgenode",
						"targetInstanceCount": 1,
						"hardwareProfile": {
							"vmSize": "[parameters('edgeNodeSize')]"
						}
					}]
				},
				"installScriptActions": [{
					"name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
					"uri": "https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh",
					"roles": ["edgenode"]
				}],
				"uninstallScriptActions": [],
				"httpsEndpoints": [],
				"applicationType": "CustomApplication"
			}
		}
	],

如示例中所示，可以选择性地调用[脚本操作](/documentation/articles/hdinsight-hadoop-customize-cluster-v1/)来执行其他配置，例如，在边缘节点中安装 [Apache Hue](/documentation/articles/hdinsight-hadoop-hue-linux/)。

创建边缘节点后，可以使用 SSH 连接到该节点，运行客户端工具访问 HDInsight 中的 Hadoop 群集。

## 将边缘节点添加到现有群集

本部分介绍如何使用 Resource Manager 模板将边缘节点添加到现有 HDInsight 群集。可以在 [GitHub](https://github.com/hdinsight/Iaas-Applications/tree/master/EmptyNode) 中找到 Resource Manager 模板。Resource Manager 模板调用位于 https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh 中的脚本操作脚本。该脚本不执行任何操作。此处只是演示如何从 Resource Manager 模板调用脚本操作。

**将空边缘节点添加到现有群集**

1. 创建一个 HDInsight 群集（如果没有）。请参阅 [Hadoop tutorial: Get started using Linux-based Hadoop in HDInsight](/documentation/articles/hdinsight-hadoop-tutorial-get-started-windows-v1/)（Hadoop 教程：开始使用 HDInsight 中基于 Linux 的 Hadoop）
2. 单击以下图像登录到 Azure，然后在 Azure 门户中打开 Azure Resource Manager 模板。

    <a href="https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FEmptyNode%2Fazuredeploy.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/documentation/articles/hdinsight-hbase-tutorial-get-started-v1/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

3. 配置以下属性：

	- CLUSTERNAME：输入现有 HDInsight 群集的名称。
	- EDGENODESIZE：选择一个 VM 大小。
	- EDGENODEPREFIX：默认值为 **new**。如果使用默认值，边缘节点的名称为 **new-edgenode**。可以通过门户自定义前缀。也可以通过模板自定义完整名称。


4. 单击“确定”以保存更改。
5. 在“资源组”中选择资源组。
6. 单击“查看法律条款”，然后单击“购买”。
7. 选择“固定到仪表板”，然后单击“创建”。

## 创建群集时添加边缘节点

本部分介绍如何使用 Resource Manager 模板创建包含边缘节点的 HDInsight 群集。可以在公共 [Azure Blob 存储容器](http://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hadoop-cluster-in-hdinsight-with-edge-node.json)中找到 Resource Manager 模板。Resource Manager 模板调用位于 https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/EmptyNode/scripts/EmptyNodeSetup.sh 中的脚本操作脚本。该脚本不执行任何操作。此处只是演示如何从 Resource Manager 模板调用脚本操作。

**将空边缘节点添加到现有群集**

1. 创建一个 HDInsight 群集（如果没有）。请参阅 [Hadoop tutorial: Get started using Linux-based Hadoop in HDInsight](/documentation/articles/hdinsight-hadoop-tutorial-get-started-windows-v1/)（Hadoop 教程：开始使用 HDInsight 中基于 Linux 的 Hadoop）
2. 单击以下图像登录到 Azure，然后在 Azure 门户中打开 Azure Resource Manager 模板。

    <a href="https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hadoop-cluster-in-hdinsight-with-edge-node.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/documentation/articles/hdinsight-hbase-tutorial-get-started-v1/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

3. 配置以下属性：
		
	- CLUSTERNAME：输入要创建的新群集的名称。
	- CLUSTERLOGINUSERNAME：输入 Hadoop HTTP 用户名。默认名称为 **admin**。
	- CLUSTERLOGINPASSWORD：输入 Hadoop HTTP 用户密码。
	- SSHUSERNAME：输入 SSH 用户名。默认名称为 **sshuser**。
	- SSHPASSWORD：输入 SSH 用户密码。
	- LOCATION：输入资源组、群集和默认存储帐户的位置。
	- CLUSTERTYPE：默认值为 **hadoop**。
	- CLUSTERWORKERNODECOUNT：默认值为 2。
	- EDGENODESIZE：选择一个 VM 大小。
	- EDGENODEPREFIX：默认值为 **new**。如果使用默认值，边缘节点的名称为 **new-edgenode**。可以通过门户自定义前缀。也可以通过模板自定义完整名称。

4. 单击“确定”以保存更改。
5. 在“资源组”中，输入新的资源组名称。
6. 单击“查看法律条款”，然后单击“购买”。
7. 选择“固定到仪表板”，然后单击“创建”。


## 访问边缘节点

边缘节点 ssh 终结点为 <边缘节点名称>.<群集名称>-ssh.azurehdinsight.cn:22。例如，new-edgenode.myedgenode0914-ssh.azurehdinsight.cn:22。

在 Azure 门户上，边缘节点显示为应用程序。门户中提供了使用 SSH 访问边缘节点时所需的信息。

**验证边缘节点 SSH 终结点**

1. 登录到 [Azure 门户](https://portal.azure.cn)。
2. 打开包含边缘节点的 HDInsight 群集。
3. 在群集边栏选项卡中单击“应用程序”。此时将显示该边缘节点。默认名称为 **new-edgenode**。
4. 单击该边缘节点。此时将显示 SSH 终结点。

**在边缘节点上使用 Hive**

5. 使用 SSH 连接到边缘节点。请参阅 [Use SSH with Linux-based Hadoop on HDInsight from Linux, Unix, or OS X](/documentation/articles/hdinsight-hadoop-linux-use-ssh-unix/)（在 Linux、Unix 或 OS X 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用）或 [Use SSH with Linux-based Hadoop on HDInsight from Windows](/documentation/articles/hdinsight-hadoop-linux-use-ssh-windows/)（在 Windows 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用）。
6. 使用 SSH 连接到边缘节点后，使用以下命令打开 Hive 控制台：

		hive
7. 运行以下命令显示群集中的 Hive 表：

		show tables;

## 删除边缘节点

可以在 Azure 门户中删除边缘节点。

**访问边缘节点**

1. 登录到 [Azure 门户](https://portal.azure.cn)。
2. 打开包含边缘节点的 HDInsight 群集。
3. 在群集边栏选项卡中单击“应用程序”。此时将显示边缘节点的列表。
4. 右键单击要删除的边缘节点，然后单击“删除”。
5. 单击“是”确认。

## 后续步骤

本文介绍了如何添加边缘节点以及如何访问边缘节点。若要了解更多信息，请参阅下列文章：

- [Install HDInsight applications](/documentation/articles/hdinsight-apps-install-applications/)（安装 HDInsight 应用程序）：了解如何将 HDInsight 应用程序安装到群集。
- [Install custom HDInsight applications](/documentation/articles/hdinsight-apps-install-custom-applications/)（安装自定义 HDInsight 应用程序）：了解如何将未发布的 HDInsight 应用程序部署到 HDInsight。
- [Publish HDInsight applications](/documentation/articles/hdinsight-apps-publish-applications/)（发布 HDInsight 应用程序）：了解如何将自定义 HDInsight 应用程序发布到 Azure 应用商店。
- [MSDN: Install an HDInsight application](https://msdn.microsoft.com/zh-cn/library/mt706515.aspx)（MSDN：安装 HDInsight 应用程序）：了解如何定义 HDInsight 应用程序。
- [Customize Linux-based HDInsight clusters using Script Action](/documentation/articles/hdinsight-hadoop-customize-cluster-v1/)（使用脚本操作自定义基于 Linux 的 HDInsight 群集）：了解如何使用脚本操作安装其他应用程序。
- [Create Linux-based Hadoop clusters in HDInsight using Resource Manager templates](/documentation/articles/hdinsight-hadoop-create-linux-clusters-arm-templates/)（使用 Resource Manager 模板在 HDInsight 中创建基于 Linux 的 Hadoop 群集）：了解如何调用 Resource Manager 模板来创建 HDInsight 群集。

<!---HONumber=Mooncake_0926_2016-->