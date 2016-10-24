<properties
   pageTitle="在 Linux、Unix 或 OS X 中将 SSH 密钥与基于 Linux 的 Hadoop 配合使用 | Microsoft Azure"
   description=" 你可以使用安全外壳 (SSH) 访问基于 Linux 的 HDInsight。本文档提供有关在 Linux、Unix 或 OS X 客户端中将 SSH 与 HDInsight 配合使用的信息。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/13/2016"
   ms.author="larryfr"/>

#在 Linux、Unix 或 OS X 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
- [Linux、Unix、OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

[安全外壳 (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) 允许你使用命令行接口在基于 Liux 的 HDInsight 群集上远程执行操作。本文档提供有关在 Linux、Unix 或 OS X 客户端中将 SSH 与 HDInsight 配合使用的信息。

> [AZURE.NOTE] 本文中的步骤假设你使用 Linux、Unix 或 OS X 客户端。如果已安装提供 `ssh` 和 `ssh-keygen` 的包（如 [Windows 上的 Bash Ubuntu](https://msdn.microsoft.com/commandline/wsl/about)），则可能会在基于 Windows 的客户端上执行这些步骤。
>
> 如果没有在基于 Windows 的客户端上安装 SSH，则使用[在 Windows 上配合使用 SSH 和基于 Linux 的 HDInsight (Hadoop)](hdinsight-hadoop-linux-use-ssh-windows.md)中的步骤，用于有关安装和使用 PuTTY 的信息。

##先决条件

* 用于 Linux、Unix 和 OS X 客户端的 **ssh-keygen** 和 **ssh**。这些实用程序通常随同操作系统一起提供或可通过程序包管理系统获得。

* 支持 HTML5 的现代 Web 浏览器。

或

* [Azure CLI](../xplat-cli-install.md)。

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

##什么是 SSH？

SSH 是用于登录远程服务器以及在其上远程执行命令的实用工具。通过基于 Linux 的 HDInsight，SSH 可建立加密连接到群集头节点，并提供用于键入命令的命令行。然后，你便可以在服务器上直接执行命令。

###SSH 用户名

SSH 用户名是用于向 HDInsight 群集进行身份验证的名称。如果在群集创建过程中指定 SSH 用户名，则将在群集中的所有节点上创建此用户。创建群集后，可以使用此用户名连接到 HDInsight 群集头节点。然后，可以从头节点连接到各个辅助节点。

###SSH 密码或公钥

SSH 用户可以使用密码或公钥进行身份验证。密码仅仅是你编造的文本的字符串，而公钥是生成以唯一标识你的加密密钥对的一部分。

密钥比密码更安全，但是，需要额外的步骤才能生成密钥，并且必须在安全位置维护包含密钥的文件。如果任何人可以访问密钥文件，他们就可以访问你的帐户。或者，如果你丢失了密钥文件，你将无法登录到你的帐户。

密钥对由一个公钥（它将发送到 HDInsight 服务器）和一个私钥（它存放在客户端计算机上）组成。 当你使用 SSH 连接到 HDInsight 服务器时，SSH 客户端将使用计算机上的私钥向服务器进行身份验证。

##创建 SSH 密钥

如果你打算在群集上使用 SSH 密钥，请使用以下信息。如果你打算使用密码，则可以跳过本节。

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

	命令完成后，你将获得两个新文件：一个是私钥（例如，**id\_rsa**），一个是公钥（例如，**id\_rsa.pub**）。

##创建基于 Linux 的 HDInsight 群集

创建基于 Linux 的 HDInsight 群集时，你必须提供以前创建的公钥。在 Linux、Unix 或 OS X 客户端中，可通过以下两种方式创建 HDInsight 群集：

* **Azure 门户** - 使用基于 Web 的门户创建群集。

* **适用于 Mac、Linux 和 Windows 的 Azure CLI** - 使用命令行命令来创建群集。

这些方法中的每个方法都需要密码或公钥。有关创建基于 Linux 的 HDInsight 群集的完整信息，请参阅[预配基于 Linux 的 HDInsight 群集](hdinsight-hadoop-provision-linux-clusters.md)。

###Azure 门户

使用 [Azure 门户][preview-portal]创建基于 Linux 的 HDInsight 群集时，必须输入“**SSH 用户名**”，然后选择输入“**密码**”或“**SSH 公钥**”。

如果选择“**SSH 公钥**”，可以将公钥（包含在带有 **.pub** 扩展名的文件中）粘贴到 __SSH PublicKey__ 字段，也可以选择“__选择文件__”浏览并选择公钥文件。

![要求提供公钥的窗体图像](./media/hdinsight-hadoop-linux-use-ssh-unix/ssh-key.png)

> [AZURE.NOTE] 密钥文件只是一个文本文件。内容应如下所示：
> ```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCelfkjrpYHYiks4TM+r1LVsTYQ4jAXXGeOAF9Vv/KGz90pgMk3VRJk4PEUSELfXKxP3NtsVwLVPN1l09utI/tKHQ6WL3qy89WVVVLiwzL7tfJ2B08Gmcw8mC/YoieT/YG+4I4oAgPEmim+6/F9S0lU2I2CuFBX9JzauX8n1Y9kWzTARST+ERx2hysyA5ObLv97Xe4C2CQvGE01LGAXkw2ffP9vI+emUM+VeYrf0q3w/b1o/COKbFVZ2IpEcJ8G2SLlNsHWXofWhOKQRi64TMxT7LLoohD61q2aWNKdaE4oQdiuo8TGnt4zWLEPjzjIYIEIZGk00HiQD+KCB5pxoVtp user@system
> ```

这将通过使用你提供的密码或公钥为指定的用户创建登录名。

###适用于 Mac、Linux 和 Windows 的 Azure 命令行界面

你可以使用[适用于 Mac、Linux 和 Windows 的 Azure CLI](../xplat-cli-install.md) 来通过 `azure hdinsight cluster create` 命令创建新群集。

有关使用此命令的详细信息，请参阅[使用自定义选项在 HDInsight 中预配 Hadoop Linux 群集](hdinsight-hadoop-provision-linux-clusters.md)。

##连接到基于 Linux 的 HDInsight 群集

在终端会话中，使用 SSH 命令通过提供地址和用户名连接到群集头节点：

* **SSH 地址** - 有可用于连接到使用 SSH 的群集的两个地址：

    * **连接到头节点**：群集名称，后接 **-ssh.azurehdinsight.net**。例如，**mycluster-ssh.azurehdinsight.net**。
    
    * **连接到边缘节点**：如果群集是 HDInsight 上的 R 服务器，群集还将包含可以使用 **RServer.CLUSTERNAME.ssh.azurehdinsight.net** 访问的边缘节点，其中 __CLUSTERNAME__ 是该群集的名称。

* **用户名** - 你在创建群集时提供的 SSH 用户名。

以下示例将以用户 **me** 的身份连接到 **mycluster** 的主头节点：

	ssh me@mycluster-ssh.azurehdinsight.net

如果你使用了用户帐户的密码，则系统将提示你输入该密码。

如果你使用了受通行短语保护的 SSH 密钥，则系统将提示你输入该通行短语。否则，SSH 将尝试通过使用客户端上的本地私钥之一自动进行身份验证。

> [AZURE.NOTE] 如果 SSH 未使用正确的私钥自动进行身份验证，请使用 **-i** 参数，并指定该私钥的路径。以下示例将从 `~/.ssh/id_rsa` 加载私钥：
>
> `ssh -i ~/.ssh/id_rsa me@mycluster-ssh.azurehdinsight.net`

如果连接到使用头节点地址，并且未指定任何端口，SSH 将默认为端口 22，它将连接到 HDInsight 群集上的主头节点。如果使用端口 23，将连接到次头节点。有关头节点的详细信息，请参阅 [HDInsight 中的 Hadoop 群集的可用性和可靠性](hdinsight-high-availability-linux.md)。

###连接到从节点

从节点不能直接从 Azure 数据中心外部访问，但是可以通过 SSH 从群集头节点访问。

如果你使用 SSH 密钥对用户帐户进行身份验证，则必须在客户端上完成以下步骤：

1. 使用文本编辑器打开 `~/.ssh/config`。如果此文件不存在，则你可以通过在终端中输入 `touch ~/.ssh/config` 来创建它。

2. 将以下内容添加到该文件中。将 *CLUSTERNAME* 替换为 HDInsight 群集的名称。

        Host CLUSTERNAME-ssh.azurehdinsight.net
          ForwardAgent yes

    这将为你的 HDInsight 群集配置 SSH 代理转发。

3. 在终端中通过使用以下命令测试 SSH 代理转发：

        echo "$SSH_AUTH_SOCK"

    这应该返回如下信息：

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    如果未返回任何内容，这指示 **ssh-agent** 未在运行。有关安装和配置 **ssh-agent** 的具体步骤，请查阅你的操作系统文档，或者请参阅[将 ssh-agent 与 ssh 配合使用](http://mah.everybody.org/docs/ssh)。

4. 验证了 **ssh-agent** 处于运行状态后，请使用以下方式将你的 SSH 私钥添加到代理：

        ssh-add ~/.ssh/id_rsa

    如果你的私钥存储在不同文件中，请将 `~/.ssh/id_rsa` 替换为该文件的路径。

使用以下步骤连接到你的群集从节点。

> [AZURE.IMPORTANT] 如果你使用 SSH 密钥对帐户进行身份验证，则必须完成以前的步骤，以验证代理转发是否正常工作。

1. 如上所述，通过使用 SSH 连接到 HDInsight 群集。

2. 在连接后，使用以下方式检索你的群集节点的列表。将 *ADMINPASSWORD* 替换为你的群集管理员帐户的密码。将 *CLUSTERNAME* 替换为群集名称。

        curl --user admin:ADMINPASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/hosts

    这将返回群集节点的 JSON 格式的信息，包括 `host_name`，其中包含每个节点的完全限定域名 (FQDN)。下面是由 **curl** 命令返回的 `host_name` 条目的示例：

        "host_name" : "workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net"

3. 拥有要连接到的从节点的列表后，在服务器的 SSH 会话中，使用以下命令打开到从节点的连接：

        ssh USERNAME@FQDN

    将 *USERNAME* 替换为你的 SSH 用户名，并将 *FQDN* 替换为辅助节点的 FQDN。例如，`workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net`。

    > [AZURE.NOTE] 如果你使用密码对 SSH 会话进行身份验证，则系统将提示你重新输入该密码。如果你使用 SSH 密钥，则连接应在没有任何提示的情况下完成。

4. 在建立会话后，终端提示将从 `username@hn#-clustername` 更改为 `username@wk#-clustername`，指示你已连接到辅助节点。你此时运行的任何命令都将在从节点上运行。

4. 在辅助节点上执行完操作后，使用 `exit` 命令来关闭辅助节点的会话。这将使你返回到 `username@hn#-clustername` 提示符。

##添加更多帐户

1. 为新的用户帐户生成新的公钥和私钥，如[创建 SSH 密钥](#create-an-ssh-key-optional)部分中所述。

	> [AZURE.NOTE] 私钥应在要用于连接到群集的客户端上生成，或在创建后安全地传输到此类客户端。

1. 在群集的 SSH 会话中，使用以下命令添加新用户：

		sudo adduser --disabled-password <username>

	这将创建一个新的用户帐户，但会禁用密码身份验证。

2. 使用以下命令，创建用于保存密钥的目录和文件：

		sudo mkdir -p /home/<username>/.ssh
		sudo touch /home/<username>/.ssh/authorized_keys
		sudo nano /home/<username>/.ssh/authorized_keys

3. 当 nano 编辑器打开时，请复制并粘贴新用户帐户的公钥内容。最后，使用 **Ctrl-X** 保存文件并退出编辑器。

	![包含示例密钥的 nano 编辑器图像](./media/hdinsight-hadoop-linux-use-ssh-unix/nano.png)

4. 使用以下命令，将 .ssh 文件夹和内容的所有权更改为新用户帐户：

		sudo chown -hR <username>:<username> /home/<username>/.ssh

5. 现在，你应该可以使用新用户帐户和私钥向服务器进行身份验证。

##<a id="tunnel"></a>SSH 隧道

可以使用 SSH 来以隧道方式将本地请求（例如 Web 请求）传送到 HDInsight 群集。然后，请求将路由到请求的资源，就像它是来源于 HDInsight 群集头节点一样。

> [AZURE.IMPORTANT] 访问某些 Hadoop 服务的 Web UI 需要使用 SSH 隧道。例如，作业历史记录 UI 或资源管理器 UI 只能使用 SSH 隧道访问。

有关创建和使用 SSH 隧道的详细信息，请参阅[使用 SSH 隧道访问 Ambari Web UI、ResourceManager、JobHistory、NameNode、Oozie 和其他 Web UI](hdinsight-linux-ambari-ssh-tunnel.md)。

##后续步骤

既然你了解了如何使用 SSH 密钥进行身份验证，就可以学习如何在 HDInsight 上将 MapReduce 与 Hadoop 配合使用。

* [将 Hive 与 HDInsight 配合使用](hdinsight-use-hive.md)

* [将 Pig 与 HDInsight 配合使用](hdinsight-use-pig.md)

* [将 MapReduce 作业与 HDInsight 配合使用](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0921_2016-->