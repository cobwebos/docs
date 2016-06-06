<!-- not suitable for Mooncake -->

<properties
   	pageTitle="在 HDInsight 上安装 Hadoop 应用程序 | Azure"
   	description="了解如何在 HDInsight 上安装 HDInsight 应用程序。"
   	services="hdinsight"
   	documentationCenter=""
   	authors="mumian"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.date="05/16/2016"
	wacn.date=""/>

# 安装自定义 HDInsight 应用程序

HDInsight 应用程序是用户可以在基于 Linux 的 HDInsight 群集上安装的应用程序。这些应用程序可能是 Microsoft、独立软件供应商 (ISV) 或你自己开发的。在本文中，你将了解如何在 HDInsight 上安装尚未发布到 Azure 门户的 HDInsight 应用程序。要安装的应用程序是 [Hue](http://gethue.com/)。

其他相关文章：

- [Publish HDInsight applications（发布 HDInsight 应用程序）](/documentation/articles/hdinsight-apps-publish-applications)：了解如何将自定义 HDInsight 应用程序发布到 Azure 库。
- [MSDN: Install an HDInsight application（MSDN：安装 HDInsight 应用程序）](https://msdn.microsoft.com/zh-cn/library/mt706515.aspx)：了解如何定义 HDInsight 应用程序。

 
## 先决条件

如果想要在现有的 HDInsight 群集上安装 HDInsight 应用程序，必须有一个 HDInsight 群集。若要创建群集，请参阅 [Create clusters（创建群集）](/documentation/articles/hdinsight-hadoop-tutorial-get-started-windows-v1#create-cluster)。也可以在创建 HDInsight 群集时安装 HDInsight 应用程序。


## 安装 HDInsight 应用程序

可以在创建群集时安装 HDInsight 应用程序，也可以将它安装到现有的 HDInsight 群集。有关如何定义 ARM 模板，请参阅 [MSDN: Install an HDInsight application（MSDN：安装 HDInsight 应用程序）](https://msdn.microsoft.com/zh-cn/library/mt706515.aspx)。

部署此应用程序 (Hue) 时所需的文件：

- [azuredeploy.json](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json)：用于安装 HDInsight 应用程序的 ARM 模板。有关如何开发自己的 ARM 模板，请参阅 [MSDN: Install an HDInsight application（MSDN：安装 HDInsight 应用程序）](https://msdn.microsoft.com/zh-cn/library/mt706515.aspx)。
- [hue-install\_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh)：ARM 模板为了配置边缘节点而调用的脚本操作。 
- [hue-binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz)：从 hui-install\_v0.sh 调用的 hue 二进制文件。 
- [hue-binaries-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz)：从 hui-install\_v0.sh 调用的 hue 二进制文件。 
- [webwasb-tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz)：从 hui-install\_v0.sh 调用的示例 Web 应用程序 (Tomcat)。

**将 Hue 安装到现有的 HDInsight 群集**

1. 单击以下图像以登录到 Azure，然后在 Azure 门户中打开 ARM 模板。 

    <a href="https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/documentation/articles/hdinsight-hbase-tutorial-get-started-v1/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

    单击此按钮可在 Azure 门户上打开 ARM 模板。ARM 模板位于 [https://github.com/hdinsight/Iaas-Applications/tree/master/Hue](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue) 中。若要了解如何编写此 ARM 模板，请参阅 [MSDN: Install an HDInsight application（MSDN：安装 HDInsight 应用程序）](https://msdn.microsoft.com/zh-cn/library/mt706515.aspx)。
    
2. 在“参数”边栏选项卡中，输入以下内容：

    - **ClusterName**：输入要在其中安装应用程序的群集的名称。此群集必须是现有的群集。
    
3. 单击“确定”以保存参数。
4. 在“自定义部署”边栏选项卡中输入“资源组”。资源组是对群集、依赖存储帐户和其他资源进行分组的容器。必须使用与群集相同的资源组。
5. 单击“法律条款”，然后单击“创建”。
6. 确认已选中“固定到仪表板”复选框，然后单击“创建”。可以从固定到门户仪表板的磁贴和门户通知查看安装状态（单击门户顶部的铃铛图标）。安装此应用程序大约需要 10 分钟。

**若要创建群集的同时安装色调**

1. 单击以下图像以登录到 Azure，然后在 Azure 门户中打开 ARM 模板。 

    <a href="https://portal.azure.cn/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/documentation/articles/hdinsight-hbase-tutorial-get-started-v1/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

    单击此按钮可在 Azure 门户上打开 ARM 模板。ARM 模板位于 [https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json) 中。若要了解如何编写此 ARM 模板，请参阅 [MSDN: Install an HDInsight application（MSDN：安装 HDInsight 应用程序）](https://msdn.microsoft.com/zh-cn/library/mt706515.aspx)。

2. 根据说明来创建群集并安装 Hue。有关创建 HDInsight 群集的详细信息，请参阅 [Create Linux-based Hadoop clusters in HDInsight（在 HDInsight 中创建基于 Linux 的 Hadoop 群集）](/documentation/articles/hdinsight-provision-clusters-v1)。

## 验证安装

可以在 Azure 门户中检查应用程序状态，以验证应用程序安装。此外，还可以验证所有 HTTP 终结点和网页（如果有）是否按预期出现：

**打开 Hue 门户**

1. 登录到 [Azure 门户](https://portal.azure.cn)。
2. 在左侧菜单中单击“HDInsight 群集”。如果未看到该菜单，请单击“浏览”，然后单击“HDInsight 群集”。
3. 单击已安装应用程序的群集。
4. 在“设置”边栏选项卡中，单击“常规”类别下面的“应用程序”。你应会看到 **hue** 已列在“已安装的应用”边栏选项卡中。
5. 单击列表中的 **hue** 以列出属性。  
6. 单击“网页”链接以验证网站；在浏览器中打开 HTTP 终结点以验证 Hue Web UI，并使用 [PuTTY](/documentation/articles/hdinsight-hadoop-linux-use-ssh-windows) 或其他 [SSH 客户端](/documentation/articles/hdinsight-hadoop-linux-use-ssh-unix)打开 SSH 终结点。
 
## 后续步骤

- [MSDN: Install an HDInsight application（MSDN：安装 HDInsight 应用程序）](https://msdn.microsoft.com/zh-cn/library/mt706515.aspx)：了解如何开发用于部署 HDInsight 应用程序的 ARM 模板。
- [Publish HDInsight applications（发布 HDInsight 应用程序）](/documentation/articles/hdinsight-apps-publish-applications)：了解如何将自定义 HDInsight 应用程序发布到 Azure 库。
- [Customize Linux-based HDInsight clusters using Script Action（使用脚本操作自定义基于 Linux 的 HDInsight 群集）](/documentation/articles/hdinsight-hadoop-customize-cluster-v1)：了解如何使用脚本操作来安装其他应用程序。
- [Create Linux-based Hadoop clusters in HDInsight using ARM templates（使用 ARM 模板在 HDInsight 中创建基于 Linux 的 Hadoop 群集）](/documentation/articles/hdinsight-hadoop-create-linux-clusters-arm-templates)：了解如何调用 ARM 模板来创建 HDInsight 群集。

<!---HONumber=Mooncake_0530_2016-->