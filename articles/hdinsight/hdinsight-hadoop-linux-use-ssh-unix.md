---
title: "在 Windows、Linux、Unix 或 OS X 中将 SSH 与 HDInsight (Hadoop) 配合使用 | Microsoft 文档"
description: " 可以使用安全外壳 (SSH) 访问 HDInsight。 本文档提供有关在 Windows、Linux、Unix 或 OS X 客户端中将 SSH 与 HDInsight 配合使用的信息。."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: a6a16405-a4a7-4151-9bbf-ab26972216c5
ms.service: hdinsight
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/27/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cfaade8249a643b77f3d7fdf466eb5ba38143f18
ms.openlocfilehash: 4cde035f75bfa3c448f12e9ebf2896b9a54a6873
ms.lasthandoff: 03/01/2017

---
# <a name="use-ssh-with-hdinsight-hadoop-from-bash-on-windows-10-linux-unix-or-os-x"></a>在 Windows 10、Linux、Unix 或 OS X 上的 Bash 中将 SSH 与 HDInsight (Hadoop) 配合使用

> [!div class="op_single_selector"]
> * [PuTTY (Windows)](hdinsight-hadoop-linux-use-ssh-windows.md)
> * [SSH（Windows、Linux、Unix、OS X）](hdinsight-hadoop-linux-use-ssh-unix.md)

在[安全外壳 (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) 中，可以使用命令行接口登录到基于 Linux 的 HDInsight 群集并运行命令。 本文档提供有关 SSH 的基本信息，以及有关在 HDInsight 中使用 SSH 的具体信息。

## <a name="what-is-ssh"></a>什么是 SSH？

SSH 是一种加密网络协议，可用于通过不安全的网络来与远程服务器安全通信。 使用 SSH 可以通过命令行安全登录到远程服务器。 在本例中，远程服务器是 HDInsight 群集的头节点或边缘节点。

还可以使用 SSH 以隧道方式将网络流量从客户端传送到 HDInsight 群集。 使用隧道可以访问 HDInsight 群集中不直接在 Internet 上公开的服务。 有关在 HDInsight 中使用 SSH 隧道的详细信息，请参阅 [Use SSH tunneling with HDInsight](hdinsight-linux-ambari-ssh-tunnel.md)（在 HDInsight 中使用 SSH 隧道）。

## <a name="ssh-clients"></a>SSH 客户端

许多操作系统通过 `ssh` 和 `scp` 命令行实用工具提供 SSH 客户端功能。

* __ssh__：可用于建立远程命令行会话和创建隧道的常规 SSH 客户端。
* __scp__：可以使用 SSH 协议在本地系统与远程系统之间复制文件的实用工具。

Windows 10 周年纪念版提供 Bash 作为开发人员功能。 其中提供 `ssh`、`scp` 及其他 Linux 命令。 有关使用 Bash on Windows 10 的详细信息，请参阅 [Bash on Ubuntu on Windows](https://msdn.microsoft.com/commandline/wsl/about)（Windows 上的 Ubuntu Bash）。

如果你使用的是 Windows 但无法访问 Bash on Windows 10，我们建议使用以下 SSH 客户端：

* [适用于 Windows 的 Git](https://git-for-windows.github.io/)：提供 `ssh` 和 `scp` 命令行实用工具。
* [Cygwin](https://cygwin.com/)：提供 `ssh` 和 `scp` 命令行实用工具。

> [!NOTE]
> 本文档中的步骤假设你可以访问 `ssh` 命令。 如果你使用 puTTY 或 MobaXterm 等客户端，请查阅相应产品的文档，了解等效的命令和参数。

## <a name="ssh-authentication"></a>SSH 身份验证

可以使用密码或[公钥加密 (https://en.wikipedia.org/wiki/Public-key_cryptography)](https://en.wikipedia.org/wiki/Public-key_cryptography) 对 SSH 连接进行身份验证。 最安全的做法是使用密钥，因为它不像密码那样容易受到多种攻击。 但是，与使用密码相比，创建和管理密钥更为复杂。

使用公钥加密涉及到创建_公钥_和_私钥_对。

* **公钥**将载入 HDInsight 群集的节点中，或者载入要对其使用公钥加密的其他任何服务。

* **私钥**是你使用 SSH 客户端登录时，为了验证自己的身份而提供给 HDInsight 群集的凭据。 请保护好私钥， 不要透露给其他人。

    为私钥创建通行短语可以进一步提高安全性。 如果使用密码，则使用 SSH 进行身份验证时必须输入该密码。

### <a name="create-a-public-and-private-key"></a>创建公钥和私钥

创建用于 HDInsight 的公钥和私钥对的最简单方法是使用 `ssh-keygen` 实用工具。 从命令行使用以下命令即可创建用于 HDInsight 的新密钥对：

> [!NOTE]
> 如果使用 MobaXTerm 或 puTTY 等 GUI SSH 客户端，请查阅客户端的文档了解如何生成密钥。

    ssh-keygen -t rsa -b 2048

系统将提示输入以下信息：

* 文件位置：位置默认为 `~/.ssh/id_rsa`。

* 可选的通行短语：如果输入了一个通行短语，在 HDInsight 群集上身份验证时必须重新输入该通行短语。

> [!IMPORTANT]
> 通行短语是私钥的密码。 每当使用私钥进行身份验证时，必须先提供通行短语，然后才能使用该密钥。 如果有人获取了你的私钥，在不知道通行短语的情况下，他们无法使用该私钥。
>
> 但是，如果你忘记了通行短语，就没有办法重置或恢复它。

完成上述命令后，将获得两个新文件：

* __id\_rsa__：此文件包含私钥。

    > [!WARNING]
    > 限制对此文件的访问，防止有人未经授权访问公钥保护的服务。

* __id\_rsa.pub__：此文件包含公钥。 创建 HDInsght 群集时需要用到此文件。

    > [!NOTE]
    > 谁有权访问_公钥_并不重要。 所有公钥的作用无非就是验证私钥。 当你使用私钥进行身份验证时，SSH 服务器等服务使用公钥来验证你的身份。

## <a name="configure-ssh-on-hdinsight"></a>在 HDInsight 上配置 SSH

创建基于 Linux 的 HDInsight 群集时，必须提供 _SSH 用户名_以及_密码_或_公钥_。 在创建群集的过程中，将使用这些信息在 HDInsight 群集节点上创建登录名。 密码或公钥用于保护用户帐户。

有关在创建群集期间配置 SSH 的详细信息，请参阅以下文档之一：

* [Create HDInsight using the Azure portal](hdinsight-hadoop-create-linux-clusters-portal.md)（使用 Azure 门户创建 HDInsight）
* [Create HDInsight using the Azure CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md)（使用 Azure CLI 创建 HDInsight）
* [Create HDInsight using Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)（使用 Azure PowerShell 创建 HDInsight）
* [Create HDInsight using Azure Resource Manager templates](hdinsight-hadoop-create-linux-clusters-arm-templates.md)（使用 Azure Resource Manager 模板创建 HDInsight）
* [Create HDInsight using the .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)（使用 .NET SDK 创建 HDInsight）
* [Create HDInsight using REST](hdinsight-hadoop-create-linux-clusters-curl-rest.md)（使用 REST 创建 HDInsight）

### <a name="additional-ssh-users"></a>其他 SSH 用户

尽管创建群集后可将其他 SSH 用户添加到群集，但我们不建议这样做。

* 新 SSH 用户需要添加到群集中每个节点。

* 新 SSH 用户对 HDInsight 的访问权限与默认用户相同。 无法根据 SSH 用户帐户限制访问 HDInsight 中的数据或作业。

若要基于用户限制访问权限，必须使用已加入域的 HDInsight 群集。 已加入域的 HDInsight 使用 Active Directory 来控制对群集资源的访问。

借助已加入域的 HDInsight 群集，可在通过 SSH 建立连接后，使用 Active Directory 进行身份验证。 多个用户可以使用 SSH 建立连接，然后在其 Active Directory 帐户中进行身份验证。 有关详细信息，请参阅[已加入域的 HDInsight](#domainjoined) 部分。

##<a name="a-idconnecta-connect-to-hdinsight"></a><a id="connect"></a>连接到 HDInsight

尽管 HDInsight 群集中的所有节点都运行 SSH 服务器，但你只能通过公共 Internet 连接到头节点或边缘节点。

* 若要连接到_头节点_，请使用 `CLUSTERNAME-ssh.azurehdinsight.net`，其中，__CLUSTERNAME__ 是 HDInsight 群集的名称。 端口 22（SSH 的默认端口）连接到主头节点。 端口 23 连接到辅助头节点。

* 若要连接到_边缘节点_，请使用 `EDGENAME.CLUSTERNAME-ssh.azurehdinsight.net`，其中，__EDGENAME__ 是边缘节点的名称，__CLUSTERNAME__ 是 HDInsight 群集的名称。 连接到边缘节点时请使用端口 22。

以下示例演示如何使用 SSH 用户名 __sshuser__ 连接到名为 __myhdi__ 的群集的头节点和边缘节点。 边缘节点名为 __myedge__。

| 要执行以下操作... | 请使用以下方法... |
| ----- | ----- |
| 连接到主头节点 | `ssh sshuser@myhdi-ssh.azurehdinsight.net` |
| 连接到辅助头节点 | `ssh -p 23 sshuser@myhdi-ssh.azurehdinsight.net` |
| 连接到边缘节点 | `ssh sshuser@edge.myhdi-ssh.azurehdinsight.net` |

如果使用密码保护 SSH 帐户，系统将提示输入该密码。

如果使用公钥保护 SSH 帐户，可能需要使用 `-i` 开关指定匹配的私钥的路径。 以下示例演示如何使用 `-i` 开关：

    ssh -i /path/to/public.key sshuser@myhdi-ssh.azurehdinsight.net

### <a name="connect-to-other-nodes"></a>连接到其他节点

辅助角色节点和 Zookeeper 节点不能直接从群集外部访问，但可以从群集头节点或边缘节点访问。 以下是连接到其他节点的一般步骤：

1. 使用 SSH 连接到头节点或边缘节点：

        ssh sshuser@myhdi-ssh.azurehdinsight.net

2. 通过 SSH 连接到头节点或边缘节点后，使用 `ssh` 命令连接到群集中的辅助角色节点：

        ssh sshuser@wn0-myhdi

    若要检索群集中辅助角色节点的列表，请参阅 [Manage HDInsight by using the Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes)（使用 Ambari REST API 管理 HDInsight）文档中有关如何检索群集节点完全限定域名的示例。

如果使用密码保护 SSH 帐户，系统会要求输入该密码来建立连接。

如果使用 SSH 密钥对你的用户帐户进行进行身份验证，必须确保为本地环境配置 SSH 代理转发。

> [!IMPORTANT]
> 以下步骤假设在基于 Linux/UNIX 的系统上操作，并且能够使用 Bash on Windows 10。 如果这些步骤不适用于你的系统，你可能需要查阅 SSH 客户端的文档。

1. 使用文本编辑器打开 `~/.ssh/config`。 如果此文件不存在，可以在命令行中输入 `touch ~/.ssh/config` 来创建。

2. 将以下内容添加到该文件中。 将 *CLUSTERNAME* 替换为 HDInsight 群集的名称。

        Host CLUSTERNAME-ssh.azurehdinsight.net
          ForwardAgent yes

    此条目为 HDInsight 群集配置 SSH 代理转发。

3. 在终端中通过使用以下命令测试 SSH 代理转发：

        echo "$SSH_AUTH_SOCK"

    此命令返回类似于以下文本的信息：

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    如未返回任何信息，则 `ssh-agent` 未运行。 请参阅 [Using ssh-agent with ssh (http://mah.everybody.org/docs/ssh)](http://mah.everybody.org/docs/ssh)（将 ssh-agent 与 ssh 配合使用）中的代理启动脚本信息，或者查阅 SSH 客户端文档，了解安装和配置 `ssh-agent` 的具体步骤。

4. 验证了 **ssh-agent** 处于运行状态后，请使用以下方式将你的 SSH 私钥添加到代理：

        ssh-add ~/.ssh/id_rsa

    如果你的私钥存储在不同文件中，请将 `~/.ssh/id_rsa` 替换为该文件的路径。

<a id="domainjoined"></a>
### <a name="domain-joined-hdinsight"></a>已加入域的 HDInsight

[已加入域的 HDInsight](hdinsight-domain-joined-introduction.md) 可将 Kerberos 与 HDInsight 中的 Hadoop 集成。 由于 SSH 用户不是 Active Directory 域用户，因此在使用 Active Directory 进行身份验证之前无法运行 Hadoop 命令。 执行以下步骤，使用 Active Directory 对 SSH 会话进行身份验证：

1. 使用 SSH 连接到已加入域的 HDInsight 群集。 例如，以下命令使用名为 __sshuser__ 的 SSH 帐户连接到名为 __myhdi__ 的 HDInsight 群集。

        ssh sshuser@myhdi-ssh.azurehdinsight.net

2. 运行以下命令，使用域用户和密码进行身份验证：

        kinit

     出现提示时，请输入域用户名以及该域用户的密码。

    有关如何为已加入域的 HDInsight 群集配置域用户的详细信息，请参阅 [Configure Domain-joined HDInisight clusters](hdinsight-domain-joined-configure.md)（配置已加入域的 HDInisight 群集）。

使用 `kinit` 命令进行身份验证后，即可使用 `hdfs dfs -ls /` 或 `hive` 等 Hadoop 命令。

## <a name="a-idtunnelassh-tunneling"></a><a id="tunnel"></a>SSH 隧道

可以使用 SSH 来以隧道方式将本地请求（例如 Web 请求）传送到 HDInsight 群集。 该请求将转发到群集，然后在群集内进行解决。

> [!IMPORTANT]
> 访问某些 Hadoop 服务的 Web UI 需要使用 SSH 隧道。 例如，作业历史记录 UI 或资源管理器 UI 只能使用 SSH 隧道访问。

有关创建和使用 SSH 隧道的详细信息，请参阅[使用 SSH 隧道访问 Ambari Web UI、JobHistory、NameNode、Oozie 和其他 Web UI](hdinsight-linux-ambari-ssh-tunnel.md)。

## <a name="next-steps"></a>后续步骤

既然你了解了如何使用 SSH 密钥进行身份验证，就可以学习如何在 HDInsight 上将 MapReduce 与 Hadoop 配合使用。

* [将 Hive 与 HDInsight 配合使用](hdinsight-use-hive.md)
* [将 Pig 与 HDInsight 配合使用](hdinsight-use-pig.md)
* [将 MapReduce 作业与 HDInsight 配合使用](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/

