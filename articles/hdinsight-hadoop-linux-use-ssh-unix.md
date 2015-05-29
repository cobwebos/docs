<properties
   pageTitle="在 Linux、Unix 或 OS X 中的基于 Linux 的 HDInsight 上将 SSH 密钥与 Hadoop 配合使用 | Azure"
   description="了解如何创建和使用 SSH 密钥，以便向基于 Linux 的 HDInsight 群集进行身份验证。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight" 
   ms.devlang="na" 
   ms.topic="article" 
   ms.tgt_pltfrm="na" 
   ms.workload="big-data" 
   ms.date="03/20/2015" 
   wacn.date="" 
   ms.author="larryfr"/>

# 在 Linux、Unix 或 OS X 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用（预览版）

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-linux-use-ssh-windows)
- [Linux、Unix、OS X](hdinsight-hadoop-linux-use-ssh-unix)

基于 Linux 的 Azure HDInsight 群集提供通过密码或 SSH 密钥使用 Secure Shell (SSH) 访问权限的选项。本文档提供有关在 Linux、Unix 或 OS X 客户端中将 SSH 与 HDInsight 配合使用的信息。

> [AZURE.NOTE] 本文中的步骤假设你使用 Linux、Unix 或 OS X 客户端。虽然可以对基于 Windows 的客户端执行这些步骤，但是，如果你已安装提供了  `ssh` 和  `ssh-keygen`（例如 Git for Windows）的程序包，则我们建议基于 Windows 的客户端遵循[在 Windows 中将 SSH 与基于 Linux 的 HDInsight (Hadoop) 配合使用](hdinsight-hadoop-linux-use-ssh-windows)。

## 先决条件

* 用于 Linux、Unix 和 OS X 客户端的 **ssh-keygen** 和 **ssh**。这些实用程序通常随同操作系统一起提供或可通过程序包管理系统获得。

* 支持 HTML5 的现代 Web 浏览器。

或者

* <a href="/documentation/articles/xplat-cli/" target="_blank">Azure 跨平台命令行界面</a>。

## 什么是 SSH？

SSH 是用于登录远程服务器以及在其上远程执行命令的实用工具。通过基于 Linux 的 HDInsight，SSH 可建立与群集头节点的加密连接，并提供用于键入命令的命令行。然后，你便可以在服务器上直接执行命令。

## 创建 SSH 密钥（可选）

创建基于 Linux 的 HDInsight 群集时，你可以选择使用密码或 SSH 密钥向服务器进行身份验证（如果使用 SSH）。SSH 密钥基于证书，因此被认为更安全。如果你打算在群集上使用 SSH 密钥，请使用以下信息。

1. 打开终端会话，并使用以下命令来了解你是否拥有任何现有的 SSH 密钥：

		ls -al ~/.ssh

	在目录列表中查找以下文件。这是公共 SSH 密钥的公用名。

	* id\_dsa.pub
	* id\_ecdsa.pub
	* id\_ed25519.pub
	* id\_rsa.pub

2. 如果你不希望使用现有文件，或者你没有现有的 SSH 密钥，请使用以下方式生成新文件：

		ssh-keygen -t rsa

	系统将提示你输入以下信息：

	* 文件位置 - 位置默认为 ~/.ssh/id\_rsa。
	* 通行短语 - 系统将提示你重新输入此通行短语。
	
		> [AZURE.NOTE] 强烈建议你为密钥使用安全的通行短语。但是，如果你忘记了通行短语，将没有办法恢复它。

	在命令完成后，你将拥有两个新文件：私钥（例如 **id\_rsa**）和公钥（例如 **id\_rsa.pub**）。

## 创建基于 Linux 的 HDInsight 群集

创建基于 Linux 的 HDInsight 群集时，你必须提供以前创建的公钥。在 Linux、Unix 或 OS X 客户端中，可通过以下两种方式创建 HDInsight 群集：

* **Azure 门户** - 使用基于 Web 的门户创建群集。

* **Azure 跨平台命令行界面 (xplat-cli)** - 使用命令行命令创建群集。

这些方法中的每个方法都需要密码或公钥。有关创建基于 Linux 的 HDInsight 群集的完整信息，请参阅<a href="/documentation/articles/hdinsight-hadoop-provision-linux-clusters/" target="_blank">设置基于 Linux 的 HDInsight 群集</a>。

### Azure 门户

使用该门户创建基于 Linux 的 HDInsight 群集时，你必须输入**"SSH 用户名"**，然后选择输入**"密码"**或**"SSH 公钥"**。如果你选择**"SSH 公钥"**，则必须将公钥（包含在扩展名为 **.pub** 的文件中）粘贴为以下形式：

![Image of form asking for public key](./media/hdinsight-hadoop-linux-use-ssh-unix/ssh-key.png)

> [AZURE.NOTE] 密钥文件只是一个文本文件。内容应如下所示：
> ```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCelfkjrpYHYiks4TM+r1LVsTYQ4jAXXGeOAF9Vv/KGz90pgMk3VRJk4PEUSELfXKxP3NtsVwLVPN1l09utI/tKHQ6WL3qy89WVVVLiwzL7tfJ2B08Gmcw8mC/YoieT/YG+4I4oAgPEmim+6/F9S0lU2I2CuFBX9JzauX8n1Y9kWzTARST+ERx2hysyA5ObLv97Xe4C2CQvGE01LGAXkw2ffP9vI+emUM+VeYrf0q3w/b1o/COKbFVZ2IpEcJ8G2SLlNsHWXofWhOKQRi64TMxT7LLoohD61q2aWNKdaE4oQdiuo8TGnt4zWLEPjzjIYIEIZGk00HiQD+KCB5pxoVtp user@system
> ```

这将通过使用你提供的密码或公钥为指定的用户创建登录名。

### Azure 跨平台命令行界面

你可以使用 <a href="/documentation/articles/xplat-cli/" target="_brad">Azure 跨平台命令行界面</a>来通过  `azure hdinsight cluster create` 命令创建新群集。

有关使用此命令的详细信息，请参阅<a href="/documentation/articles/hdinsight-hadoop-provision-linux-clusters/" target="_blank">使用自定义选项在 HDInsight 中设置 Hadoop Linux 群集</a>。

## 连接到基于 Linux 的 HDInsight 群集

在终端会话中，使用 SSH 命令通过提供地址和用户名连接到群集头节点：

* **SSH 地址** - 群集名称，后面跟 **-ssh.azurehdinsight.cn**。例如，**mycluster-ssh.azurehdinsight.cn**。

* **用户名** - 你在创建群集时提供的 SSH 用户名。

以下示例将以用户 **me** 的身份连接到群集 **mycluster**：

	ssh me@mycluster-ssh.azurehdinsight.cn

如果你使用了用户帐户的密码，则系统将提示你输入该密码。

如果你使用了受通行短语保护的 SSH 密钥，则系统将提示你输入该通行短语。否则，SSH 将尝试通过使用客户端上的本地私钥之一自动进行身份验证。

> [AZURE.NOTE] 如果 SSH 未使用正确的私钥自动进行身份验证，请使用 **-i** 参数，并指定该私钥的路径。以下示例将从 `~/.ssh/id_rsa` 加载私钥：
> 
> `ssh -i ~/.ssh/id_rsa me@mycluster-ssh.azurehdinsight.cn`

### 连接到从节点

从节点不能直接从 Azure 数据中心外部访问，但是可以通过 SSH 从群集头节点访问。

如果你使用 SSH 密钥对用户帐户进行身份验证，则必须在客户端上完成以下步骤：

1. 使用文本编辑器，打开 `~/.ssh/config`。如果此文件不存在，则你可以通过在终端中输入  `touch ~/.ssh/config` 来创建它。

2. 将以下内容添加到该文件中。将  *CLUSTERNAME* 替换为你的 HDInsight 群集名称。

        Host CLUSTERNAME-ssh.azurehdinsight.cn
          ForwardAgent yes

    这将为你的 HDInsight 群集配置 SSH 代理转发。

3. 在终端中通过使用以下命令测试 SSH 代理转发：

        echo "$SSH_AUTH_SOCK"

    这应该返回如下信息：

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    如果未返回任何内容，这表示 **ssh-agent** 未在运行。有关安装和配置 **ssh-agent** 的特定步骤，请查阅你的操作系统文档，否则，请参阅<a href="http://mah.everybody.org/docs/ssh" target="_blank">将 ssh-agent 与 ssh 配合使用</a>。

4. 验证了 **ssh-agent** 处于运行状态后，使用以下方式将你的 SSH 私钥添加到代理：

        ssh-add ~/.ssh/id_rsa

    如果你的私钥存储在不同文件中，请将 `~/.ssh/id_rsa` 替换为该文件的路径。

使用以下步骤连接到你的群集从节点。

> [AZURE.IMPORTANT] 如果你使用 SSH 密钥对帐户进行身份验证，则必须完成以前的步骤，以验证代理转发是否正常工作。

1. 如上所述，通过使用 SSH 连接到 HDInsight 群集。

2. 在连接后，使用以下方式检索你的群集节点的列表。将  *ADMINPASSWORD* 替换为你的群集管理员帐户的密码。将  *CLUSTERNAME* 替换为你的群集名称。

        curl --user admin:ADMINPASSWORD https://CLUSTERNAME.azurehdinsight.cn/api/v1/hosts

    这将返回群集节点的 JSON 格式的信息，包括  `host_name`，其中包含每个节点的完全限定域名 (FQDN)。下面是由 **curl** 命令返回的  `host_name` 条目的示例：

        "host_name" : "workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net"

3. 拥有要连接到的从节点的列表后，在服务器的 SSH 会话中，使用以下命令打开到从节点的连接：

        ssh USERNAME@FQDN

    将  *USERNAME* 替换为你的 SSH 用户名，并将  *FQDN* 替换为从节点的 FQDN。例如 `workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net`。

    > [AZURE.NOTE] 如果你使用密码对 SSH 会话进行身份验证，则系统将提示你重新输入该密码。如果你使用 SSH 密钥，则连接应在没有任何提示的情况下完成。

4. 在建立会话后，终端提示将从  `username@headnode` 更改为  `username@workernode`，指示你已连接到从节点。你此时运行的任何命令都将在从节点上运行。

4. 在从节点上执行完操作后，使用  `exit` 命令来关闭从节点的会话。这将使你返回到  `username@headnode` 提示符。

## 添加更多帐户

1. 为新的用户帐户生成新的公钥和私钥，如[创建 SSH 密钥](#create-an-ssh-key-optional) 部分中所述。

	> [AZURE.NOTE] 私钥应在要用于连接到群集的客户端上生成，或在创建后安全地传输到此类客户端。

1. 在群集的 SSH 会话中，使用以下命令添加新用户：

		sudo adduser --disabled-password <username> 

	这将创建一个新的用户帐户，但会禁用密码身份验证。

2. 使用以下命令，创建用于保存密钥的目录和文件：

		sudo mkdir -p /home/<username>/.ssh
		sudo touch /home/<username>/.ssh/authorized_keys
		sudo nano /home/<username>/.ssh/authorized_keys

3. 当 nano 编辑器打开时，请复制并粘贴新用户帐户的公钥内容。最后，使用 **Ctrl-X** 保存文件并退出编辑器。

	![image of nano editor with example key](./media/hdinsight-hadoop-linux-use-ssh-unix/nano.png)

4. 使用以下命令，将 .ssh 文件夹和内容的所有权更改为新用户帐户：

		sudo chown -hR <username>:<username> /home/<username>/.ssh

5. 现在，你应该可以使用新用户帐户和**私钥**向服务器进行身份验证。

## <a id="tunnel"></a>SSH 隧道

还可以使用 SSH 来以隧道方式将本地请求（例如 Web 请求）传送到 HDInsight 群集。然后，请求将路由到请求的资源，就像它是来源于 HDInsight 群集头节点一样。

这最适用于访问 HDInsight 群集上将内部域名用于群集中的头节点或从节点的 Web 服务。例如，Ambari 网页上的某些部分使用类似于 **headnode0.mycluster.d1.internal.chinacloudapp.cn** 的内部域名。这些名称无法从群集外部解析，但是，通过 SSH 传送的请求将源自于群集内部，并可正确解析。

使用以下步骤创建 SSH 隧道，并将浏览器配置为使用 SSH 隧道连接到群集。

1. 以下命令可用于创建到群集头节点的 SSH 隧道：

		ssh -C2qTnNf -D 9876 username@clustername-ssh.azurehdinsight.cn

	这会创建一个通过 SSH 将流量路由到群集本地端口 9876 的连接。选项包括：

	* **D 8080** - 将通过隧道路由流量的本地端口。

	* **C** - 压缩所有数据，因为 Web 流量大都是文本。

	* **2** - 强制 SSH 仅试用协议版本 2。

	* **q** - 安静模式。

	* **T** - 禁用 pseudo-tty 分配，因为我们将仅转发端口。
	
	* **n** - 防止读取 STDIN，因为我们将仅转发端口。

	* **N** - 不执行远程命令，因为我们将仅转发端口。

	* **f** - 在后台运行。

	如果使用 SSH 密钥配置了群集，则可能需要使用 `-i` 参数，并指定 SSH 私钥的路径。

	在命令完成后，发送到本地计算机上的端口 9876 的流量将通过安全套接字层 (SSL) 路由到群集头节点，并显示为源于此处。

2. 将客户端程序（例如 Firefox）配置为使用 **localhost:9876** 作为 **SOCKS v5** 代理。下面是 Firefox 设置的外观：

	![image of Firefox settings](./media/hdinsight-hadoop-linux-use-ssh-unix/socks.png)

	> [AZURE.NOTE] 选择**"远程 DNS"**将通过使用 HDInsight 群集解析域名系统 (DNS) 请求。如果未将其选中，则将在本地解析 DNS。

	<!--You can verify that traffic is being routed through the tunnel by vising a site such as <a href="http://www.whatismyip.com/" target="_blank">http://www.whatismyip.com/</a> with the proxy settings enabled and disabled in Firefox. While enabled, the IP address will be for a machine in the Microsoft Azure datacenter.-->

### 浏览器扩展

虽然你可以将浏览器配置为使用隧道，但是，你通常不想要通过隧道路由所有流量。浏览器扩展，例如 <a href="http://getfoxyproxy.org/" target="_blank">FoxyProxy</a>，支持 URL 请求的模式匹配（仅限 FoxyProxy Standard 或 Plus 版），以便只有特定 URL 的请求才通过隧道发送。

如果你已安装 FoxyProxy Standard 版，请使用以下步骤将其配置为仅通过隧道转发 HDInsight 的流量。

1. 在浏览器中打开 FoxyProxy 扩展。例如，在 Firefox 中，选择地址字段旁边的 FoxyProxy 图标。

	![foxyproxy icon](./media/hdinsight-hadoop-linux-use-ssh-unix/foxyproxy.png)

2. 选择**"添加新代理"**，再选择**"常规"**选项卡，然后输入代理名称 **HDInsightProxy**。

	![foxyproxy general](./media/hdinsight-hadoop-linux-use-ssh-unix/foxygeneral.png)

3. 选择**"代理详细信息"**选项卡，并填充以下字段：

	* **主机或 IP 地址** - 这是 localhost，因为我们要在本地计算机上使用 SSH 隧道。

	* **端口** - 这是用于 SSH 隧道的端口。

	* **SOCKS 代理** - 选择此项可让浏览器使用隧道作为代理。

	* **SOCKS v5** - 选择此项可设置代理所需的版本。

	![foxyproxy proxy](./media/hdinsight-hadoop-linux-use-ssh-unix/foxyproxyproxy.png)

4. 选择**"URL 模式"**选项卡，然后选择**"添加新模式"**。使用以下设置来定义模式，然后单击**"确定"**：

	* **模式名称** - **headnode** - 这就是模式的友好名称。

	* **URL 模式** - **\*headnode\*** - 这可以定义将任何 URL 与其中的单词 **headnode** 匹配的模式。

	![foxyproxy pattern](./media/hdinsight-hadoop-linux-use-ssh-unix/foxypattern.png)

4. 单击**"确定"**以添加代理，并关闭**"代理设置"**。

5. 在"FoxyProxy"对话框的顶部，将**"选择模式"**更改为**"根据其预定义模式和优先级使用代理"**，然后单击**"关闭"**。

	![foxyproxy select mode](./media/hdinsight-hadoop-linux-use-ssh-unix/selectmode.png)

执行这些步骤后，只会通过 SSL 隧道路由包含字符串 **headnode** 的 URL 的请求。

## 后续步骤

既然你了解了如何使用 SSH 密钥进行身份验证，就可以学习如何在 HDInsight 上将 MapReduce 与 Hadoop 配合使用。

* [将 Hive 与 HDInsight 配合使用](/documentation/articles/hdinsight-use-hive/)

* [将 Pig 与 HDInsight 配合使用](/documentation/articles/hdinsight-use-pig/)

* [将 MapReduce 作业与 HDInsight 配合使用](/documentation/articles/hdinsight-use-mapreduce/)
 

<!---HONumber=56-->