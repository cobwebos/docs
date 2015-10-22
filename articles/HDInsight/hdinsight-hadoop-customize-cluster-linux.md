<properties
	pageTitle="使用脚本操作自定义 HDInsight 群集 | Azure"
	description="了解如何使用脚本操作自定义 HDInsight 群集。"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.date="08/21/2015"
	wacn.date=""/>

# 使用脚本操作自定义 HDInsight 群集

HDInsight 提供了一个称为**脚本操作**的配置选项，该脚本操作可调用自定义脚本，以定义设置过程中要在群集上执行自定义。这些脚本可用于在群集上安装额外的软件或更改群集上的应用程序配置。

> [AZURE.NOTE]本文中的信息特定于基于 Linux 的 HDInsight 群集。有关特定于基于 Windows 的群集的本文版本，请参阅[使用脚本操作自定义 HDInsight 群集 (Windows)](/documentation/articles/hdinsight-hadoop-customize-cluster)

## 群集设置过程中的脚本操作

只能在创建群集过程中使用脚本操作。下图演示了在设置过程中执行脚本操作的时间：

![群集设置过程中的 HDInsight 群集自定义和阶段][img-hdi-cluster-states]

在配置 HDInsight 时运行脚本。在此阶段，脚本在群集中的所有指定节点上并行运行，并且在节点上使用根权限运行。

> [AZURE.NOTE]由于在运行脚本时，你对群集节点具有根权限，因此可以执行停止和启动服务（包括与 Hadoop 相关的服务）等操作。如果你停止服务，则必须在脚本完成运行之前，确保 Ambari 服务及其他与 Hadoop 相关的服务都已启动且正在运行。这些服务必须在群集创建时，成功地确定群集的运行状况和状态。

每个群集可接受多个脚本操作，这些脚本依其指定顺序被调用。脚本可在头节点和/或辅助节点上运行。

> [AZURE.IMPORTANT]脚本操作必须在 15 分钟内完成，否则将会超时。

## 脚本操作脚本示例

可以从 Azure 门户、Azure PowerShell 或 HDInsight .NET SDK 使用脚本操作脚本。本文介绍如何从门户使用脚本操作。若要了解如何通过 PowerShell 和 .NET SDK 使用脚本操作，请查看下表中列出的示例。

HDInsight 提供了多个脚本用于在 HDInsight 群集上安装以下组件：

Name | 脚本
----- | -----
**安装 Hue** | https://hdiconfigactions.blob.core.chinacloudapi.cn/linuxhueconfigactionv01/install-hue-uber-v01.sh 。请参阅[在 HDInsight 群集上安装并使用 Hue](/documentation/articles/hdinsight-hadoop-hue-linux)。
**安装 Spark** | https://hdiconfigactions.blob.core.chinacloudapi.cn/linuxsparkconfigactionv01/spark-installer-v01.sh 。请参阅[在 HDInsight 群集上安装并使用 Spark](/documentation/articles/hdinsight-hadoop-spark-install-linux)。
**安装 R** | https://hdiconfigactions.blob.core.chinacloudapi.cn/linuxrconfigactionv01/r-installer-v01.sh 。请参阅[在 HDInsight 群集上安装并使用 R](/documentation/articles/hdinsight-hadoop-r-scripts-linux)。
**安装 Solr** | https://hdiconfigactions.blob.core.chinacloudapi.cn/linuxsolrconfigactionv01/solr-installer-v01.sh 。请参阅[在 HDInsight 群集上安装并使用 Solr](/documentation/articles/hdinsight-hadoop-solr-install-linux)。
**安装 Giraph** | https://hdiconfigactions.blob.core.chinacloudapi.cn/linuxgiraphconfigactionv01/giraph-installer-v01.sh 。请参阅[在 HDInsight 群集上安装并使用 Giraph](/documentation/articles/hdinsight-hadoop-giraph-install-linux)。

##从 Azure 门户使用脚本操作

1. 按照[使用自定义选项预配群集](/documentation/articles/hdinsight-provision-clusters#portal)中的说明开始预配群集。

2. 在“脚本操作”边栏选项卡的“可选配置”下，单击“添加脚本操作”可提供有关脚本操作的详细信息，如下所示：

	![使用脚本操作自定义群集](./media/hdinsight-hadoop-customize-cluster-linux/HDI.CreateCluster.8.png "使用脚本操作自定义群集")

	| 属性 | 值 |
	| -------- | ----- |
	| Name | 指定脚本操作的名称。 |
	| 脚本 URI | 指定要调用来自定义群集的脚本的 URI。 |
	| 头节点/辅助节点 | 指定在其上运行自定义脚本的节点（**头**节点、**辅助**节点或 **ZooKeeper** 节点）。 |
	| Parameters | 根据脚本的需要，指定参数。 |

	按 ENTER 可添加多个脚本操作，以在群集上安装多个组件。

3. 单击“选择”可保存脚本操作配置并继续执行群集预配。

##从 Azure 资源管理器模板使用脚本操作

在此部分中，我们将使用 Azure 资源管理器 (ARM) 模板来预配 HDInsight 群集，还使用脚本操作在群集上安装自定义组件（在此示例中为 R）。本部分提供示例 ARM 模板，以便使用脚本操作预配群集。

### 开始之前

* 有关配置工作站以运行 HDInsight Powershell cmdlet 的信息，请参阅[安装和配置 Azure PowerShell](/documentation/articles/powershell-install-configure)。
* 有关如何创建 ARM 模板的说明，请参阅 [创作 Azure 资源管理器模板](/documentation/articles/resource-group-authoring-templates)。
* 如果你以前没有对资源管理器使用过 Azure PowerShell，请参阅[将 Azure PowerShell 与 Azure 资源管理器配合使用](powershell-azure-resource-manager)。

### 使用脚本操作预配群集

1. 将以下模板复制到你计算机上的某个位置。此模板将在群集中的头节点和辅助节点上安装 R。你还可以验证 JSON 模板是否有效。将模板内容粘贴到 [JSONLint](http://jsonlint.com/) 中，后者是一款在线 JSON 验证程序工具。

			{
		    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
		    "contentVersion": "1.0.0.0",
		    "parameters": {
		        "clusterLocation": {
		            "type": "string",
		            "defaultValue": "West US",
		            "allowedValues": [ "West US" ]
		        },
		        "clusterName": {
		            "type": "string"
		        },
		        "clusterUserName": {
		            "type": "string",
		            "defaultValue": "admin"
		        },
		        "clusterUserPassword": {
		            "type": "securestring"
		        },
		        "sshUserName": {
		            "type": "string",
		            "defaultValue": "hdiuser"
		        },
		        "sshPassword": {
		            "type": "securestring"
		        },
		        "clusterStorageAccountName": {
		            "type": "string"
		        },
		        "clusterStorageAccountResourceGroup": {
		            "type": "string"
		        },
		        "clusterStorageType": {
		            "type": "string",
		            "defaultValue": "Standard_LRS",
		            "allowedValues": [
		                "Standard_LRS",
		                "Standard_GRS",
		                "Standard_ZRS"
		            ]
		        },
		        "clusterStorageAccountContainer": {
		            "type": "string"
		        },
		        "clusterHeadNodeCount": {
		            "type": "int",
		            "defaultValue": 1
		        },
		        "clusterWorkerNodeCount": {
		            "type": "int",
		            "defaultValue": 2
		        }
		    },
		    "variables": {
		    },
		    "resources": [
		        {
		            "name": "[parameters('clusterStorageAccountName')]",
		            "type": "Microsoft.Storage/storageAccounts",
		            "location": "[parameters('clusterLocation')]",
		            "apiVersion": "2015-05-01-preview",
		            "dependsOn": [ ],
		            "tags": { },
		            "properties": {
		                "accountType": "[parameters('clusterStorageType')]"
		            }
		        },
		        {
		            "name": "[parameters('clusterName')]",
		            "type": "Microsoft.HDInsight/clusters",
		            "location": "[parameters('clusterLocation')]",
		            "apiVersion": "2015-03-01-preview",
		            "dependsOn": [
		                "[concat('Microsoft.Storage/storageAccounts/', parameters('clusterStorageAccountName'))]"
		            ],
		            "tags": { },
		            "properties": {
		                "clusterVersion": "3.2",
		                "osType": "Linux",
		                "clusterDefinition": {
		                    "kind": "hadoop",

		                    "configurations": {
		                        "gateway": {
		                            "restAuthCredential.isEnabled": true,
		                            "restAuthCredential.username": "[parameters('clusterUserName')]",
		                            "restAuthCredential.password": "[parameters('clusterUserPassword')]"
		                        }
		                    }
		                },
		                "storageProfile": {
		                    "storageaccounts": [
		                        {
		                            "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.chinacloudapi.cn')]",
		                            "isDefault": true,
		                            "container": "[parameters('clusterStorageAccountContainer')]",
		                            "key": "[listKeys(resourceId(parameters('clusterStorageAccountResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).key1]"
		                        }
		                    ]
		                },
		                "computeProfile": {
		                    "roles": [
		                        {
		                            "name": "headnode",
		                            "targetInstanceCount": "[parameters('clusterHeadNodeCount')]",
		                            "hardwareProfile": {
		                                "vmSize": "Large"
		                            },
		                            "osProfile": {
		                                "linuxOperatingSystemProfile": {
		                                    "username": "[parameters('sshUserName')]",
		                                    "password": "[parameters('sshPassword')]"
		                                }
		                            },
		                            "scriptActions": [
		                                {
		                                    "name": "installR",
		                                    "uri": "https://hdiconfigactions.blob.core.chinacloudapi.cn/linuxrconfigactionv01/r-installer-v01.sh",
		                                    "parameters": ""
		                                }
		                            ]
		                        },
		                        {
		                            "name": "workernode",
		                            "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
		                            "hardwareProfile": {
		                                "vmSize": "Large"
		                            },
		                            "osProfile": {
		                                "linuxOperatingSystemProfile": {
		                                    "username": "[parameters('sshUserName')]",
		                                    "password": "[parameters('sshPassword')]"
		                                }
		                            },
		                            "scriptActions": [
		                                {
		                                    "name": "installR",
		                                    "uri": "https://hdiconfigactions.blob.core.chinacloudapi.cn/linuxrconfigactionv01/r-installer-v01.sh",
		                                    "parameters": ""
		                                }
		                            ]
		                        }
		                    ]
		                }
		            }
		        }
		    ],
		    "outputs": {
		        "cluster":{
		            "type" : "object",
		            "value" : "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
		        }
		    }
		}



2. 启动 Azure PowerShell 并登录到你的 Azure 帐户。提供凭据后，该命令将返回有关你的帐户的信息。

		Add-AzureAccount

		Id                             Type       ...
		--                             ----
		someone@example.com            User       ...

3. 如果你有多个订阅，请提供要用于部署的订阅 ID。

		Select-AzureSubscription -SubscriptionID <YourSubscriptionId>

4. 切换到 Azure 资源管理器模块。

		Switch-AzureMode AzureResourceManager

5. 如果目前没有资源组，请创建新的资源组。提供资源组的名称，以及解决方案所需的位置。将返回新资源组的摘要。

		New-AzureResourceGroup -Name myresourcegroup -Location "West US"

		ResourceGroupName : myresourcegroup
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
		            		Actions  NotActions
		            		=======  ==========
		            		*
		ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup


6. 若要为资源组创建新部署，请运行 **New-AzureResourceGroupDeployment** 命令并提供所需的参数。参数包括部署的名称、资源组的名称、所创建模板的路径或 URL。如果你的模板需要任何参数，则也必须传递这些参数。在此示例中，用于在群集上安装 R 的脚本操作不需要任何参数。


		New-AzureResourceGroupDeployment -Name mydeployment -ResourceGroupName myresourcegroup -TemplateFile <PathOrLinkToTemplate>


	系统将提示你为模板中定义的参数提供值。

7. 部署资源组后，你将看到部署摘要。

		  DeploymentName    : mydeployment
		  ResourceGroupName : myresourcegroup
		  ProvisioningState : Succeeded
		  Timestamp         : 8/17/2015 7:00:27 PM
		  Mode              : Incremental
		  ...

8. 如果你的部署失败，则可以使用以下 cmdlet 获取有关故障的信息。

		Get-AzureResourceGroupLog -ResourceGroup myresourcegroup -Status Failed

	如需有关部署失败的详细信息，请使用以下 cmdlet。

		Get-AzureResourceGroupLog -ResourceGroup myresourcegroup -Status Failed -DetailedOutput

##从 Azure PowerShell 使用脚本操作

在本部分中，我们使用 **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** cmdlet 通过脚本操作来调用脚本，以自定义群集。在继续前，确保你已安装并配置 Azure PowerShell。有关配置工作站以运行 HDInsight Powershell cmdlet 的信息，请参阅[安装和配置 Azure PowerShell](/documentation/articles/powershell-install-configure)。

执行以下步骤：

1. 打开 Azure PowerShell 控制台，并声明以下变量：

		# PROVIDE VALUES FOR THESE VARIABLES
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# The number of nodes in the HDInsight cluster.
		$version = "<HDInsightClusterVersion>"          # HDInsight version, for example "3.1"

2. 指定配置值（如群集中的节点）和要使用的默认存储。

		# SPECIFY THE CONFIGURATION OPTIONS
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.chinacloudapi.cn"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName

3. 使用 **Add-AzureHDInsightScriptAction** cmdlet 可调用该脚本。以下示例使用脚本在群集上安装 R：

		# INVOKE THE SCRIPT USING THE SCRIPT ACTION
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install R"  -ClusterRoleCollection HeadNode,WorkerNode,ZookeeperNode -Uri https://hdiconfigactions.blob.core.chinacloudapi.cn/linuxrconfigactionv01/r-installer-v01.sh


	**Add-AzureHDInsightScriptAction** cmdlet 采用以下参数：

	| 参数 | 定义 |
	| --------- | ---------- |
	| Config | 脚本操作信息添加到的配置对象。 |
	| Name | 脚本操作的名称。 |
	| ClusterRoleCollection | 指定在其上运行自定义脚本的节点。有效值是 **HeadNode**（在头节点上安装）、**WorkerNode**（在所有数据节点上安装）或**ZookeeperNode**（在 zookeeper 节点上安装）。你可以使用任一值或所有值。 |
	| Parameters | 脚本所需的参数。 |
	| Uri | 指定执行的脚本的 URI。 |

4. 最后，预配群集：

		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version

出现提示时，请输入群集的凭据。创建群集可能需要几分钟时间。

##从 HDInsight .NET SDK 使用脚本操作

HDInsight .NET SDK 提供客户端库，可简化从 .NET 应用程序中使用 HDInsight 的操作。以下步骤演示如何使用脚本从 HDInsight .NET SDK 自定义群集。


> [AZURE.IMPORTANT]必须先创建自签名证书，将其安装到工作站上，然后将其上载到你的 Azure 订阅。有关说明，请参阅[创建自签名证书](http://go.microsoft.com/fwlink/?LinkId=511138)。


###创建 Visual Studio 项目

1. 打开 Visual Studio 2013 或 2015。

2. 在“文件”菜单中，单击“新建”，然后单击“项目”。

3. 在“新建项目”中，键入或选择以下值：

	| 属性 | 值 |
	| -------- | ----- |
	| 类别 | 模板/Visual C#/Windows |
	| 模板 | 控制台应用程序 |
	| Name | ScriptActionCluster |

4. 单击“确定”以创建该项目。

5. 在“工具”菜单中，单击“Nuget Package Manager”，然后单击“Package Manager Console”。

6. 在控制台中运行下列命令以安装程序包。

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	此命令将从当前 Visual Studio 项目添加 .NET 库以及对这些库的引用。

7. 在“解决方案资源管理器”中，双击 **Program.cs** 将其打开。

8. 将下列 **using** 语句添加到文件顶部：

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;
		using Microsoft.WindowsAzure.Management.HDInsight.Framework.Logging;

9. 在 **Main()** 函数中，粘贴以下代码，并为变量提供值：

        var clusterName = args[0];

        // PROVIDE VALUES FOR THE VARIABLES
        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionId = "<AzureSubscriptionID>";
        string location = "<MicrosoftDataCenterLocation>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.chinacloudapi.cn";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // PROVIDE THE CERTIFICATE THUMBPRINT TO RETRIEVE THE CERTIFICATE FROM THE CERTIFICATE STORE
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // CREATE AN HDINSIGHT CLIENT OBJECT
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionId), cert);
        var client = HDInsightClient.Connect(creds);
		client.IgnoreSslErrors = true;

        // PROVIDE THE CLUSTER INFORMATION
		var clusterInfo = new ClusterCreateParameters()
        {
            Name = clusterName,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = clusterName,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize,
            Version = "3.1"
        };

10. 将以下代码追加到 **Main()** 函数中。此代码调用脚本操作；在此示例中，为用于在群集上安装 R 的脚本：

		// ADD THE SCRIPT ACTION TO INSTALL R

        clusterInfo.ConfigActions.Add(new ScriptAction(
            "Install R",
            new ClusterNodeType[] { ClusterNodeType.HeadNode, ClusterNodeType.DataNode },
            new Uri("https://hdiconfigactions.blob.core.chinacloudapi.cn/linuxrconfigactionv01/r-installer-v01.sh"), null
            ));

11. 最后，创建群集：

		client.CreateCluster(clusterInfo);

11. 将更改保存到应用程序并构建解决方案。

###运行应用程序

打开 Azure PowerShell 控制台，导航到项目保存到的位置，导航到项目中的 \\bin\\debug 目录，然后运行以下命令：

	.\ScriptActionCluster <cluster-name>

提供群集名称，并按 ENTER 以预配群集。

## 支持 HDInsight 群集上使用的开放源代码软件

Microsoft Azure HDInsight 服务是一个弹性平台，可让你使用围绕着 Hadoop 形成的开放源代码技术生态系统，在云中生成大数据应用程序。Microsoft Azure 为开放源代码技术提供一般级别的支持，如 [Azure 支持常见问题网站](http://azure.microsoft.com/support/faq/)上的**支持范围**部分中所述。HDInsight 服务为如下所述的某些组件提供附加的支持级别。

HDInsight 服务中有两种类型的开放源代码组件：

- **内置组件** - 这些组件预先安装在 HDInsight 群集上，并提供在群集的核心功能。例如，Yarn ResourceManager、Hive 查询语言 (HiveQL) 及 Mahout 库均属于此类别。[HDInsight 提供的 Hadoop 群集版本有哪些新功能？](/documentation/articles/hdinsight-component-versioning)中提供了群集组件的完整列表。

- **自定义组件** - 作为群集用户，你可以安装，或者在工作负荷中使用由社区提供的或你自己创建的任何组件。

完全支持内置组件，Microsoft 支持部门将帮助你找出并解决与这些组件相关的问题。

自定义组件可获得合理范围的支持，以帮助你进一步排查问题。这可能导致问题解决，或要求你参与可用的开放源代码技术渠道，在该处可找到该技术的深入专业知识。例如，有许多可以使用的社区站点，例如：

* [针对 HDInsight 的 MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)

* [堆栈溢出](https://stackoverflow.com)

此外，Apache 项目在 [Apache.org](https://apache.org) 上提供了项目站点，例如 [Hadoop](http://hadoop.apache.org/) 和 [Spark](http://spark.apache.org/)。

HDInsight 服务提供多种方式来使用自定义组件。不论在群集上使用组件或安装组件的方式为何，均适用相同级别的支持。以下是可以在 HDInsight 群集上使用的自定义组件最常见方式的列表：

1. 作业提交 - Hadoop 或其他类型的作业可以提交到执行或使用自定义组件的群集。

2. 群集自定义 - 在群集创建期间，你可以指定将安装在群集节点的其他设置和自定义组件。

3. 示例 - 对于常见的自定义组件，Microsoft 和其他人可能会提供演示如何在 HDInsight 群集上使用这些组件的示例。我们不针对这些示例提供支持。

##故障排除

可使用 Ambari web UI 查看脚本在群集预配期间记录的信息。

1. 在浏览器中，导航到 https://CLUSTERNAME.azurehdinsight.cn。将 CLUSTERNAME 替换为 HDInsight 群集的名称。

	出现提示时，为群集输入管理员帐户名 (admin) 和密码。你可能需要在 Web 窗体中重新输入管理员凭据。

2. 从页面顶部栏中选择“操作”条目。这将显示通过 Ambari 在群集上执行的当前操作和以前操作的列表。

	![选中了“操作”的 Ambari Web UI 栏](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. 查找“操作”列中包含 __run\_customscriptaction__ 的条目。这些条目是在运行脚本操作时创建的。

	![操作的屏幕截图](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

	选择此条目，并通过链接向下钻取以查看在群集上运行该脚本时生成的 STDOUT 和 STDERR 输出。

## 后续步骤

请参阅以下内容以获取有关创建和使用脚本自定义群集的信息和示例：

- [为 HDInsight 开发脚本操作脚本](/documentation/articles/hdinsight-hadoop-script-actions-linux)
- [在 HDInsight 群集上安装并使用 Spark](/documentation/articles/hdinsight-hadoop-spark-install-linux)
- [在 HDInsight 群集上安装并使用 R](/documentation/articles/hdinsight-hadoop-r-scripts-linux)
- [在 HDInsight 群集上安装并使用 Solr](/documentation/articles/hdinsight-hadoop-solr-install-linux)
- [在 HDInsight 群集上安装并使用 Giraph](/documentation/articles/hdinsight-hadoop-giraph-install-linux)



[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "群集设置过程中的阶段"

<!---HONumber=74-->