<!-- not suitable for Mooncake -->

<properties
	pageTitle="使用 HDInsight 上的 R Server（预览版）安装 RStudio | Azure"
	description="如何使用 HDInsight 上的 R Server（预览版）安装 RStudio。"
	services="hdinsight"
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.date="03/29/2016"
	wacn.date=""/>


# 使用 HDInsight 上的 R Server（预览版）安装 RStudio

当今有许多集成开发环境 (IDE) 适用于 R，包括 Microsoft 最近推出的 [R Tools for Visual Studio](https://www.visualstudio.com/features/rtvs-vs.aspx) (RTVS)、[RStudio](https://www.rstudio.com/products/rstudio-server/) 提供的桌面和服务器工具系列，或 Walware 的基于 Eclipse 的 [StatET](http://www.walware.de/goto/statet)。在 Linux 上使用最广泛的是 [RStudio Server](https://www.rstudio.com/products/rstudio-server/)，它为远程客户端提供基于浏览器的 IDE。在 HDInsight 高级版群集的边缘节点上安装 RStudio Server 可以提供完整的 IDE 体验，以便通过群集上的 R Server 开发和运行 R 脚本，与按默认使用 R 控制台相比，可以大幅提高生产力。

在本文中，你将学习如何使用自定义脚本，在群集的边缘节点上安装 RStudio Server 的社区（免费）版本。如果你偏好 RStudio Server 的商用授权 Pro 版本，则必须遵循 [RStudio Server](https://www.rstudio.com/products/rstudio/download-server/) 的安装说明。

> [AZURE.NOTE] 本文档中的步骤需要使用 HDInsight 群集上的 R Server，如果所用 HDInsight 群集上的 R 是使用[安装 R 脚本操作](/documentation/articles/hdinsight-hadoop-r-scripts-linux)安装的，则无法正常执行这些步骤。

## 先决条件

* 装有 R Server 的 Azure HDInsight 群集。有关说明，请参阅 [Get started with R Server on HDInsight clusters（HDInsight 群集上的 R Server 入门）](hdinsight-hadoop-r-server-get-started.md)。
* SSH 客户端。对于 Linux 和 Unix 分发版或 Macintosh OS X，操作系统已随附 `ssh` 命令。对于 Windows，我们建议使用 [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。 


## 使用自定义脚本在群集上安装 RStudio

1. 识别群集的边缘节点。对于装有 R Server 的 HDInsight 群集，下面是头节点和边缘节点的命名约定。

	* 头节点 - `CLUSTERNAME-ssh.azurehdinsight.cn`
	* 边缘节点 - `rserver.CLUSTERNAME.ssh.azurehdinsight.cn` 

3. 使用上述命名模式通过 SSH 连接到群集的边缘节点。
 
	* 如果从 Linux 客户端连接，请参阅 [Connect to a Linux-based HDInsight cluster（连接到基于 Linux 的 HDInsight 群集）](/documentation/articles/hdinsight-hadoop-linux-use-ssh-unix#connect-to-a-linux-based-hdinsight-cluster)。
	* 如果从 Windows 客户端连接，请参阅 [Connect to a Linux-based HDInsight cluster using PuTTY（使用 PuTTY 连接到基于 Linux 的 HDInsight 群集）](/documentation/articles/hdinsight-hadoop-linux-use-ssh-windows#connect-to-a-linux-based-hdinsight-cluster)。

2. 连接后，你将成为该群集上的 root 用户。在 SSH 会话中输入以下命令。

		sudo su -

3. 下载用于安装 RStudio 的自定义脚本。使用以下命令。

		wget http://mrsactionscripts.blob.core.chinacloudapi.cn/rstudio-server-community-v01/InstallRStudio.sh

4. 更改自定义脚本文件的权限，然后运行该脚本。使用以下命令。

		chmod 755 InstallRStudio.sh
		./InstallRStudio.sh

5. 如果你在创建包含 R Server 的 HDInsight 群集时使用了 SSH 密码，则可以跳过此步骤直接转到下一步。如果创建群集时使用了 SSH 密钥，则必须为 SSH 用户设置密码。连接到 RStudio 时需要此密码。运行以下命令。当系统提示输入**当前 Kerberos 密码**时，只需按 **Enter**。

		passwd remoteuser
		Current Kerberos password:
		New password:
		Retype new password:
		Current Kerberos password:
		
	如果已成功设置密码，你应会看到如下所示的消息。

		passwd: password updated successfully


	退出 SSH 会话。

6. 通过将 HDInsight 群集上的 `localhost:8787` 映射到客户端计算机，来创建通往群集的 SSH 隧道。必须在打开新浏览器会话之前创建 SSH 隧道。

	* 在 Linux 客户端或 Windows 客户端上，（使用 [Cygwin](http://www.redhat.com/services/custom/cygwin/)）打开终端会话并且使用以下命令。

			ssh -L localhost:8787:localhost:8787 USERNAME@rserver.CLUSTERNAME.ssh.azurehdinsight.cn
			
		将 **USERNAME** 替换为 HDInsight 群集的 SSH 用户，并将 **CLUSTERNAME** 替换为 HDInsight 群集的名称

	* 在 Windows 客户端上创建 SSH 隧道 PuTTY。

		1.  打开 PuTTY 并输入你的连接信息。如果你不熟悉 PuTTY，请参阅 [Use SSH with Linux-based Hadoop on HDInsight from Windows（在 Windows 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用）](/documentation/articles/hdinsight-hadoop-linux-use-ssh-windows)，以了解有关如何配合 HDInsight 使用 PuTTY 的信息。
		2.  在对话框左侧的“类别”部分中，依次展开“连接”和“SSH”，然后选择“隧道”。
		3.  提供以下有关“用于控制 SSH 端口转发的选项”窗体的信息：

			* **源端口** - 客户端上要转发的端口。例如 **8787**。
			* **目标** - 必须映射到本地客户端计算机的目标。例如 **localhost:8787**。

			![创建 SSH 隧道](./media/hdinsight-hadoop-r-server-install-r-studio/createsshtunnel.png "创建 SSH 隧道")

		4. 单击“添加”以添加设置，然后单击“打开”以打开 SSH 连接。
		5. 出现提示时，登录到服务器。这将会建立 SSH 会话并启用隧道。

7. 打开 Web 浏览器，并根据你为隧道输入的端口输入以下 URL。

		http://localhost:8787/ 

8. 系统将提示你输入 SSH 用户名和密码以连接到群集。如果在创建群集时使用了 SSH 密钥，则必须输入上述步骤 5 中所创建的密码。

	![连接到 R Studio](./media/hdinsight-hadoop-r-server-install-r-studio/connecttostudio.png "创建 SSH 隧道")

9. 若要测试 RStudio 安装是否成功，可以运行测试脚本，该脚本将在群集上执行基于 R 的 MapReduce 和 Spark 作业。返回到 SSH 控制台，并输入以下命令下载要在 RStudio 中运行的测试脚本。

	* 如果你创建了包含 R 的 Hadoop 群集，请使用此命令。
		
			wget http://mrsactionscripts.blob.core.chinacloudapi.cn/rstudio-server-community-v01/testhdi.r

	* 如果你创建了包含 R 的 Spark 群集，请使用此命令。

			wget http://mrsactionscripts.blob.core.chinacloudapi.cn/rstudio-server-community-v01/testhdi_spark.r

10. 在 RStudio 中，你将看到下载的测试脚本。双击该文件将它打开，选择文件的内容，然后单击“运行”。“控制台”窗格中应会显示输出。
 
	![测试安装](./media/hdinsight-hadoop-r-server-install-r-studio/test-r-script.png "测试安装")

## 另请参阅

- [Compute context options for R Server on HDInsight clusters（适用于 HDInsight 群集上的 R Server 的计算上下文选项）](/documentation/articles/hdinsight-hadoop-r-server-compute-contexts)

- [Azure Storage options for R Server on HDInsight premium（适用于 HDInsight 高级版上的 R Server 的 Azure 存储空间选项）](/documentation/articles/hdinsight-hadoop-r-server-storage)


 

<!---HONumber=Mooncake_0516_2016-->