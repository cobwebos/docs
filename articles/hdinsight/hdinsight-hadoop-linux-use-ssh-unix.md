---
title: 将 SSH 与 Hadoop 配合使用 - Azure HDInsight
description: 可以使用安全外壳 (SSH) 访问 HDInsight。 本文档介绍如何通过 Windows、Linux、Unix 或 macOS 客户端使用 ssh 和 scp 命令连接到 HDInsight。
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
keywords: linux 中的 hadoop 命令,hadoop linux 命令,hadoop macos,ssh hadoop,ssh hadoop 群集
ms.service: hdinsight
ms.topic: get-started-article
ms.date: 04/26/2018
ms.author: jasonh
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017
ms.openlocfilehash: 589e6d56fca1e987cb9fd086d76dc6fb8a686333
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/07/2018
ms.locfileid: "39594339"
---
# <a name="connect-to-hdinsight-hadoop-using-ssh"></a>使用 SSH 连接到 HDInsight (Hadoop)

了解如何使用[安全外壳 (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) 安全地连接到 Azure HDInsight 上的 Hadoop。 

HDInsight 可以使用 Linux (Ubuntu) 作为 Hadoop 群集中节点的操作系统。 下表包含使用 SSH 客户端连接到基于 Linux 的 HDInsight 时所需的地址和端口信息：

| 地址 | 端口 | 连接到... |
| ----- | ----- | ----- |
| `<clustername>-ed-ssh.azurehdinsight.net` | 22 | 边缘节点 (ML Services on HDInsight) |
| `<edgenodename>.<clustername>-ssh.azurehdinsight.net` | 22 | 边缘节点（如果存在边缘节点，则可以是任何其他群集类型） |
| `<clustername>-ssh.azurehdinsight.net` | 22 | 主头节点 |
| `<clustername>-ssh.azurehdinsight.net` | 23 | 辅助头节点 |

> [!NOTE]
> 将 `<edgenodename>` 替换为边缘节点的名称。
>
> 将 `<clustername>` 替换为群集的名称。
>
> 如果群集包含边缘节点，建议__始终连接到边缘节点__（使用 SSH）。 头节点托管的服务对于 Hadoop 运行状况而言至关重要。 边缘节点只会运行其上放置的软件。
>
> 有关使用边缘节点的详细信息，请参阅 [Use edge nodes in HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node)（在 HDInsight 中使用边缘节点）。

> [!TIP]
> 首先连接到 HDInsight 时，SSH 客户端可能会显示一个警告，指出无法验证主机。 当系统提示时，请选择“是”，将主机添加到 SSH 客户端的受信任服务器列表。
>
> 如果此前已使用同一名称连接到某个服务器，则可能会收到一个警告，指出存储的主机密钥与服务器的主机密钥不匹配。 请参阅 SSH 客户端的文档，了解如何删除服务器名称的现有条目。

## <a name="ssh-clients"></a>SSH 客户端

Linux、Unix 和 macOS 系统提供 `ssh` 和 `scp` 命令。 `ssh` 客户端通常用于在基于 Linux 或 Unix 的系统中创建远程命令行会话。 `scp` 客户端用于在客户端和远程系统之间安全地复制文件。

默认情况下，Microsoft Windows 不安装任何 SSH 客户端。 `ssh` 和 `scp` 客户端通过以下包提供给 Windows 使用：

* OpenSSH 客户端 (Beta)：在 Fall Creators Update 中转到“设置” > “应用和功能” > “管理可选功能” > “添加功能”，选择“OpenSSH 客户端”。 

    > [!NOTE]
    > 如果在启用此功能后 `ssh` 和 `scp` 命令在 PowerShell 中不可用，请注销后再重新登录。

* [基于 Windows 10 的 Ubuntu 上的 Bash](https://msdn.microsoft.com/commandline/wsl/about)：通过 Windows 命令行中的 Bash 提供 `ssh` 和 `scp` 命令。

* [OpenSSH 客户端 (Beta)](https://blogs.msdn.microsoft.com/powershell/2017/12/15/using-the-openssh-beta-in-windows-10-fall-creators-update-and-windows-server-1709/)：这是 Windows 10 Fall Creators Update 中引入的可选功能。

* [Azure Cloud Shell](../cloud-shell/quickstart.md)：此 Cloud Shell 在浏览器中提供 Bash 环境，并提供 `ssh`、`scp` 等常用 Linux 命令。

* [Git (https://git-scm.com/)](https://git-scm.com/)：通过 GitBash 命令行提供 `ssh` 和 `scp` 命令。

还有多个图形 SSH 客户端，如 [PuTTY (http://www.chiark.greenend.org.uk/~sgtatham/putty/)](http://www.chiark.greenend.org.uk/~sgtatham/putty/) 和 [MobaXterm (http://mobaxterm.mobatek.net/)](http://mobaxterm.mobatek.net/)。 尽管可以使用这些客户端连接到 HDInsight，但连接的过程与使用 `ssh` 实用工具时不同。 有关详细信息，请参阅所用图形客户端的文档。

## <a id="sshkey"></a>身份验证：SSH 密钥

SSH 密钥使用[公钥加密](https://en.wikipedia.org/wiki/Public-key_cryptography)对 SSH 会话进行身份验证。 SSH 密钥比密码更安全，使用它可以轻松确保对 Hadoop 群集进行访问时的安全性。

如果使用密钥保护 SSH 帐户，客户端必须在连接时提供匹配的私钥：

* 可将大多数客户端配置为使用__默认密钥__。 例如，在 Linux 和 Unix 环境中，`ssh` 客户端会在 `~/.ssh/id_rsa` 位置查找私钥。

* 可以指定__私钥的路径__。 在 `ssh` 客户端中，可使用 `-i` 参数指定私钥的路径。 例如，`ssh -i ~/.ssh/id_rsa sshuser@myedge.mycluster-ssh.azurehdinsight.net`。

* 如果将__多个私钥__用于不同的服务器，可以考虑使用 [ssh-agent (https://en.wikipedia.org/wiki/Ssh-agent)](https://en.wikipedia.org/wiki/Ssh-agent) 之类的实用工具。 在建立 SSH 会话时，可以通过 `ssh-agent` 实用工具自动选择要使用的密钥。

> [!IMPORTANT]
>
> 如果使用密码保护私钥，使用该密钥时必须输入密码。 为提供方便，`ssh-agent` 等实用工具可以缓存密码。

### <a name="create-an-ssh-key-pair"></a>创建 SSH 密钥对

使用 `ssh-keygen` 命令创建公钥和私钥文件。 以下命令生成可在 HDInsight 中使用的 2048 位 RSA 密钥对：

    ssh-keygen -t rsa -b 2048

在创建密钥的过程中，系统会提示输入信息。 例如，密钥的存储位置，或者是否要使用密码。 完成该过程后，将创建两个文件：一个公钥文件和一个私钥文件。

* __公钥__用于创建 HDInsight 群集。 公钥的扩展名为 `.pub`。

* __私钥__用于在 HDInsight 群集中对客户端进行身份验证。

> [!IMPORTANT]
> 可以使用密码保护密钥。 通行短语其实是基于私钥的密码。 即使有人获取了私钥，但他们必须知道该密码才能使用该私钥。

### <a name="create-hdinsight-using-the-public-key"></a>使用公钥创建 HDInsight

| 创建方法 | 如何使用公钥 |
| ------- | ------- |
| **Azure 门户** | 取消选中“使用与群集登录相同的密码”，并选择“公钥”作为 SSH 身份验证类型。 最后，在“SSH 公钥”字段中选择公钥文件，或粘贴该文件的文本内容。</br>![创建 HDInsight 群集时的 SSH 公钥对话框](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-public-key.png) |
| **Azure PowerShell** | 使用 `New-AzureRmHdinsightCluster` cmdlet 的 `-SshPublicKey` 参数，并以字符串的形式传递公钥内容。|
| **Azure CLI 1.0** | 使用 `azure hdinsight cluster create` 命令的 `--sshPublicKey` 参数，并以字符串的形式传递公钥内容。 |
| **Resource Manager 模板** | 有关在模板中使用 SSH 密钥的示例，请参阅 [Deploy HDInsight on Linux with SSH key](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-publickey/)（使用 SSH 密钥在 Linux 上部署 HDInsight）。 [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-publickey/azuredeploy.json) 文件中的 `publicKeys` 元素用于在创建群集时向 Azure 传递密钥。 |

## <a id="sshpassword"></a>身份验证：密码

可以使用密码保护 SSH 帐户。 使用 SSH 连接到 HDInsight 时，系统会提示输入密码。

> [!WARNING]
> Microsoft 不建议将密码身份验证用于 SSH。 密码可能被猜出，容易受到暴力破解攻击。 我们建议[使用 SSH 密钥进行身份验证](#sshkey)。

> [!IMPORTANT]
> SSH 帐户密码在创建 HDInsight 群集 70 天后过期。 如果密码过期，可以使用[管理 HDInsight](hdinsight-administer-use-portal-linux.md#change-passwords) 文档中的信息更改它。

### <a name="create-hdinsight-using-a-password"></a>使用密码创建 HDInsight

| 创建方法 | 如何指定密码 |
| --------------- | ---------------- |
| **Azure 门户** | 默认情况下，SSH 用户帐户的密码与群集登录帐户的密码相同。 要使用不同的密码，请取消选中“使用与群集登录相同的密码”，并在“SSH 密码”字段中输入密码。</br>![创建 HDInsight 群集时的 SSH 密码对话框](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-password.png)|
| **Azure PowerShell** | 使用 `New-AzureRmHdinsightCluster` cmdlet 的 `--SshCredential` 参数，并传递包含 SSH 用户帐户名和密码的 `PSCredential` 对象。 |
| **Azure CLI 1.0** | 使用 `azure hdinsight cluster create` 命令的 `--sshPassword` 参数，并提供密码值。 |
| **资源管理器模板** | 有关在模板中使用密码的示例，请参阅 [Deploy HDInsight on Linux with SSH password](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/)（使用 SSH 密码在 Linux 上部署 HDInsight）。 [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-password/azuredeploy.json) 文件中的 `linuxOperatingSystemProfile` 元素用于在创建群集时向 Azure 传递 SSH 帐户名和密码。|

### <a name="change-the-ssh-password"></a>更改 SSH 密码

有关更改 SSH 用户帐户密码的信息，请参阅 [Manage HDInsight](hdinsight-administer-use-portal-linux.md#change-passwords)（管理 HDInsight）文档的 __Change passwords__（更改密码）部分。

## <a id="domainjoined"></a>身份验证：已加入域的 HDInsight

如果使用__已加入域的 HDInsight 群集__，必须在通过 SSH 本地用户建立连接后使用 `kinit` 命令。 此命令会提示输入域用户和密码，并在与群集关联的 Azure Active Directory 域中对会话进行身份验证。

还可以在每个加入域的节点（例如头节点、边缘节点）上启用 Kerberos 身份验证，以便通过域帐户使用 ssh。 为此，请编辑 sshd 配置文件：
```bash
sudo vi /etc/ssh/sshd_config
```
取消注释，并将 `KerberosAuthentication` 更改为 `yes`

```bash
sudo service sshd restart
```

在任何时候，为了验证 Kerberos 身份验证是成功还是失败，请使用 `klist` 命令。

有关详细信息，请参阅 [Configure domain-joined HDInsight](./domain-joined/apache-domain-joined-configure.md)（配置已加入域的 HDInsight）。

## <a name="connect-to-nodes"></a>连接到节点

可以通过 Internet 在端口 22 和 23 上访问头节点和边缘节点（如果有）。

* 连接到头节点时，请使用端口 22 连接到主头节点，使用端口 23 连接到辅助头节点。 要使用的完全限定的域名为 `clustername-ssh.azurehdinsight.net`，其中的 `clustername` 为群集的名称。

    ```bash
    # Connect to primary head node
    # port not specified since 22 is the default
    ssh sshuser@clustername-ssh.azurehdinsight.net

    # Connect to secondary head node
    ssh -p 23 sshuser@clustername-ssh.azurehdinsight.net
    ```
    
* 连接到边缘节点时请使用端口 22。 完全限定的域名为 `edgenodename.clustername-ssh.azurehdinsight.net`，其中的 `edgenodename` 是在创建边缘节点时提供的名称。 `clustername` 是群集的名称。

    ```bash
    # Connect to edge node
    ssh sshuser@edgnodename.clustername-ssh.azurehdinsight.net
    ```

> [!IMPORTANT]
> 前面的示例假定你使用的是密码身份验证，或者系统会自动进行证书身份验证。 如果使用 SSH 密钥对进行身份验证，且系统不会自动使用证书，则请使用 `-i` 参数指定私钥。 例如，`ssh -i ~/.ssh/mykey sshuser@clustername-ssh.azurehdinsight.net`。

连接以后，提示符会改为指示 SSH 用户名和连接到的节点。 例如，在以 `sshuser` 身份连接到主头节点时，提示符为 `sshuser@hn0-clustername:~$`。

### <a name="connect-to-worker-and-zookeeper-nodes"></a>连接到工作节点和 Zookeeper 节点

工作节点和 Zookeeper 节点不能从 Internet 直接访问， 但可以从群集头节点或边缘节点访问。 以下是连接到其他节点的一般步骤：

1. 使用 SSH 连接到头节点或边缘节点：

        ssh sshuser@myedge.mycluster-ssh.azurehdinsight.net

2. 通过 SSH 连接到头节点或边缘节点后，使用 `ssh` 命令连接到群集中的工作节点：

        ssh sshuser@wn0-myhdi

    若要检索节点名称列表，请参阅[使用 Ambari REST API 管理 HDInsight](hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) 文档。

如果 SSH 帐户使用某个__密码__进行了保护，请在连接时输入该密码。

如果 SSH 帐户使用 __SSH 密钥__进行了保护，请确保在客户端启用 SSH 转发。

> [!NOTE]
> 直接访问群集中所有节点的另一种方法是在 Azure 虚拟网络中安装 HDInsight。 然后，可将远程计算机加入相同的虚拟网络，这样就可以直接访问群集中的所有节点。
>
> 有关详细信息，请参阅将 [Use a virtual network with HDInsight](hdinsight-extend-hadoop-virtual-network.md)（对 HDInsight 使用虚拟网络）。

### <a name="configure-ssh-agent-forwarding"></a>配置 SSH 代理转发

> [!IMPORTANT]
> 以下步骤假设在基于 Linux 或 UNIX 的系统上操作，并且能够使用基于 Windows 10 的 Bash。 如果这些步骤不适用于系统，可能需要查阅 SSH 客户端的文档。

1. 使用文本编辑器打开 `~/.ssh/config`。 如果此文件不存在，可以在命令行中输入 `touch ~/.ssh/config` 来创建。

2. 将以下文本添加到 `config` 文件。

        Host <edgenodename>.<clustername>-ssh.azurehdinsight.net
          ForwardAgent yes

    将 __Host__ 信息替换为使用 SSH 连接到的节点的地址。 上面的示例使用边缘节点。 此条目为指定的节点配置 SSH 代理转发。

3. 在终端中通过使用以下命令测试 SSH 代理转发：

        echo "$SSH_AUTH_SOCK"

    此命令返回类似于以下文本的信息：

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    如未返回任何信息，则 `ssh-agent` 未运行。 有关详细信息，请参阅 [Using ssh-agent with ssh (http://mah.everybody.org/docs/ssh)](http://mah.everybody.org/docs/ssh)（将 ssh-agent 与 ssh 配合使用）中的代理启动脚本信息，或者查阅 SSH 客户端文档。

4. 验证了 **ssh-agent** 处于运行状态后，请使用以下方式将 SSH 私钥添加到代理：

        ssh-add ~/.ssh/id_rsa

    如果私钥存储在不同文件中，请将 `~/.ssh/id_rsa` 替换为该文件的路径。

5. 使用 SSH 连接到群集边缘节点或头节点。 然后使用 SSH 命令连接到工作节点或 zookeeper 节点。 使用转发的密钥建立连接。

## <a name="copy-files"></a>复制文件

`scp` 实用工具可以用来将文件复制到群集中的单个节点，或者从单个节点进行复制。 例如，以下命令将 `test.txt` 目录从本地系统复制到头主节点：

```bash
scp test.txt sshuser@clustername-ssh.azurehdinsight.net:
```

由于未在 `:` 后指定路径，因此会将文件置于 `sshuser` 主目录。

以下示例将 `test.txt` 文件从头主节点上的 `sshuser` 主目录复制到本地系统：

```bash
scp sshuser@clustername-ssh.azurehdinsight.net:test.txt .
```

> [!IMPORTANT]
> `scp` 只能访问群集中单个节点的文件系统。 它不能用来访问适用于群集且兼容 HDFS 的存储中的数据。
>
> 需要通过 SSH 会话上传要使用的资源时，请使用 `scp`。 例如，上传一个 Python 脚本，然后通过 SSH 会话运行该脚本。
>
> 若要了解如何将数据直接加载到兼容 HDFS 的存储中，请参阅以下文档：
>
> * [使用 Azure 存储的 HDInsight](hdinsight-hadoop-use-blob-storage.md)。
>
> * [使用 Azure Data Lake Store 的 HDInsight](hdinsight-hadoop-use-data-lake-store.md)。

## <a name="next-steps"></a>后续步骤

* [对 HDInsight 使用 SSH 隧道](hdinsight-linux-ambari-ssh-tunnel.md)
* [对 HDInsight 使用虚拟网络](hdinsight-extend-hadoop-virtual-network.md)
* [在 HDInsight 中使用边缘节点](hdinsight-apps-use-edge-node.md#access-an-edge-node)
