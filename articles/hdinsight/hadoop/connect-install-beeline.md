---
title: 连接到或安装 Apache Beeline-Azure HDInsight
description: 了解如何连接到 Apache Beeline 客户端，以便在 HDInsight 上通过 Hadoop 运行 Hive 查询。 Beeline 是用于通过 JDBC 操作 HiveServer2 的实用工具。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 05/27/2020
ms.openlocfilehash: e93d750dd2feaa70692ab1077ee4333c835417db
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86076770"
---
# <a name="connect-to-apache-beeline-on-hdinsight-or-install-it-locally"></a>连接到 Apache Beeline on HDInsight 或本地安装

[Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell)是一个 Hive 客户端，包含在 HDInsight 群集的头节点上。 本文介绍如何通过不同类型的连接连接到 HDInsight 群集上安装的 Beeline 客户端。 还介绍了如何[在本地安装 Beeline 客户端](#install-beeline-client)。 

## <a name="types-of-connections"></a>连接类型

### <a name="from-an-ssh-session"></a>从 SSH 会话

如果从 SSH 会话连接到群集头节点，则可随后连接到端口 `headnodehost` 上的 `10001` 地址：

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

### <a name="over-an-azure-virtual-network"></a>通过 Azure 虚拟网络

通过 Azure 虚拟网络从客户端连接到 HDInsight 时，必须提供群集头节点的完全限定域名 (FQDN)。 由于直接与群集节点建立此连接，因此此连接使用端口 `10001`：

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

将 `<headnode-FQDN>` 替换为群集头节点的完全限定域名。 若要查找头节点的完全限定域名，请使用[使用 Apache Ambari REST API 管理 HDInsight](../hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes) 文档中的信息。

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>到 HDInsight 企业安全性套餐（ESP）群集使用 Kerberos

将客户端从客户端连接到同一领域的同一领域中的计算机上的企业安全性套餐（ESP） Azure Active Directory 群集时，还必须指定 `<AAD-Domain>` 具有访问群集权限的域名和域用户帐户的名称 `<username>` ：

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

将 `<username>` 替换为域中有权访问群集的帐户的名称。 将 `<AAD-DOMAIN>` 替换为群集加入到的 Azure Active Directory (AAD) 的名称。 将大写字符串用于 `<AAD-DOMAIN>` 值，否则不会找到凭据。 如果需要，请检查 `/etc/krb5.conf` 以找到领域名称。

若要从 Ambari 查找 JDBC URL：

1. 在 Web 浏览器中，导航到 `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary`，其中 `CLUSTERNAME` 是群集的名称。 确保 HiveServer2 正在运行。

1. 使用剪贴板复制 HiveServer2 JDBC URL。

### <a name="over-public-or-private-endpoints"></a>通过公共或专用终结点

使用公共或专用终结点连接到群集时，必须提供群集登录帐户名（默认 `admin` ）和密码。 例如，使用 Beeline 从客户端系统连接到 `clustername.azurehdinsight.net` 地址。 此连接通过端口建立 `443` ，并使用 TLS/SSL 进行加密。

将 `clustername` 替换为 HDInsight 群集的名称。 将 `admin` 替换为群集的群集登录帐户。 对于 ESP 群集，请使用完整的 UPN（例如 user@domain.com）。 将 `password` 替换为群集登录帐户的密码。

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

或对于专用终结点：

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

专用终结点指向一个基本的负载均衡器，后者只能从在同一区域中进行对等互连的 VNET 访问。 有关详细信息，请参阅[对全局 VNet 对等互连和负载均衡器的约束](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)。 在使用 beeline 之前，可以将 `curl` 命令与 `-v` 选项配合使用，以便排查公共或专用终结点的任何连接问题。

### <a name="use-beeline-with-apache-spark"></a>将 Beeline 与 Apache Spark 配合使用

Apache Spark 提供自己的 HiveServer2 实现（有时称为 Spark Thrift 服务器）。 此服务使用 Spark SQL 来解析查询，而不是 Hive。 和可提供更好的性能，具体取决于你的查询。

#### <a name="through-public-or-private-endpoints"></a>通过公共或专用终结点

使用的连接字符串略有不同。 它使用 `httpPath/sparkhive2`，而不包含 `httpPath=/hive2`。 将 `clustername` 替换为 HDInsight 群集的名称。 将 `admin` 替换为群集的群集登录帐户。 对于 ESP 群集，请使用完整的 UPN（例如 user@domain.com）。 将 `password` 替换为群集登录帐户的密码。

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

或对于专用终结点：

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

专用终结点指向一个基本的负载均衡器，后者只能从在同一区域中进行对等互连的 VNET 访问。 有关详细信息，请参阅[对全局 VNet 对等互连和负载均衡器的约束](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)。 在使用 beeline 之前，可以将 `curl` 命令与 `-v` 选项配合使用，以便排查公共或专用终结点的任何连接问题。

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>使用 Apache Spark 从群集头或 Azure 虚拟网络中

当直接从群集头节点或者从 HDInsight 群集所在的 Azure 虚拟网络中的资源进行连接时，应当为 Spark Thrift 服务器使用端口 `10002` 而非 `10001`。 以下示例演示如何直接连接到头节点：

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

## <a name="install-beeline-client"></a>安装 Beeline 客户端

虽然头节点上包含 Beeline，但建议将其安装在本地。  本地计算机的安装步骤基于[适用于 Linux 的 Windows 子系统](https://docs.microsoft.com/windows/wsl/install-win10)。

1. 更新包列表。 在 bash shell 中输入以下命令：

    ```bash
    sudo apt-get update
    ```

1. 安装 Java（如果未安装）。 可以使用 `which java` 命令进行检查。

    1. 如果未安装 java 包，请输入以下命令：

        ```bash
        sudo apt install openjdk-11-jre-headless
        ```

    1. 打开 bashrc 文件（通常位于 ~/.bashrc 中）：`nano ~/.bashrc`。

    1. 修改 bashrc 文件。 在该文件的末尾添加以下行：

        ```bash
        export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
        ```

        依次按 **Ctrl+X**、**Y**、Enter。

1. 下载 Hadoop 和 Beeline 存档，输入以下命令：

    ```bash
    wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz
    wget https://archive.apache.org/dist/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz
    ```

1. 解压缩这些存档，输入以下命令：

    ```bash
    tar -xvzf hadoop-2.7.3.tar.gz
    tar -xvzf apache-hive-1.2.1-bin.tar.gz
    ```

1. 进一步修改 bashrc 文件。 你需要确定存档解压缩到的路径。 如果使用[适用于 Linux 的 Windows 子系统](https://docs.microsoft.com/windows/wsl/install-win10)，并严格按步骤操作，则路径为 `/mnt/c/Users/user/`，其中 `user` 是你的用户名。

    1. 打开文件 `nano ~/.bashrc`

    1. 用适当的路径修改下面的命令，并将其输入到 bashrc 文件的末尾：

        ```bash
        export HADOOP_HOME=/path_where_the_archives_were_unpacked/hadoop-2.7.3
        export HIVE_HOME=/path_where_the_archives_were_unpacked/apache-hive-1.2.1-bin
        PATH=$PATH:$HIVE_HOME/bin
        ```

    1. 依次按 **Ctrl+X**、**Y**、Enter。

1. 关闭并重新打开 bash 会话。

1. 测试连接。 使用上面的[公共或专用终结点](#over-public-or-private-endpoints)的连接格式。

## <a name="next-steps"></a>后续步骤

* 有关将 Beeline 客户端与 Apache Hive 结合使用的示例，请参阅[将 Apache Beeline 与 Apache Hive 配合使用](apache-hadoop-use-hive-beeline.md)
* 有关 HDInsight 中的 Hive 的更多常规信息，请参阅[将 Apache Hive 与 Apache Hadoop on HDInsight 配合使用](hdinsight-use-hive.md)
