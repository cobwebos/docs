---
title: 在 Azure 虚拟网络中设置 HBase 群集复制 | Microsoft Docs
description: 了解如何设置从一个 HDInsight 版本到另一个版本的 HBase 复制，以实现负载均衡、高可用性、在不造成停机的情况下进行迁移和更新，以及灾难恢复。
services: hdinsight,virtual-network
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: jgao
ms.openlocfilehash: 56b2b5ae9d3e4a0e682ec3dd47cd5cc30ebf6d58
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2018
---
# <a name="set-up-hbase-cluster-replication-in-azure-virtual-networks"></a>在 Azure 虚拟网络中设置 HBase 群集复制

了解如何在 Azure 中的一个虚拟网络内部或者在两个虚拟网络之间设置 HBase 复制。

群集复制使用源推送方法。 HBase 群集可以是一个源或一个目标，也可以同时充当这两个角色。 复制是异步的。 复制的目标是保持最终一致性。 在启用复制的情况下，当源接收到对列系列的编辑时，该编辑将传播到所有目标群集。 当数据从一个群集复制到另一个群集，会跟踪源群集和所有已使用数据的群集，防止复制循环。

本教程介绍如何设置源-目标复制。 对于其他群集拓扑，请参阅 [Apache HBase 参考指南](http://hbase.apache.org/book.html#_cluster_replication)。

下面是单个虚拟网络的 HBase 复制用例：

* 负载均衡。 例如，可以在目标群集上运行扫描或 MapReduce 作业，在源群集上引入数据。
* 添加高可用性。
* 在不同的 HBase 群集之间迁移数据。
* 将 Azure HDInsight 群集从一个版本升级到另一个版本。

下面是两个虚拟网络的 HBase 复制用例：

* 设置灾难恢复。
* 对应用程序进行负载均衡和分区。
* 添加高可用性。

可以使用 [GitHub](https://github.com/Azure/hbase-utils/tree/master/replication) 中的[脚本操作](../hdinsight-hadoop-customize-cluster-linux.md)脚本复制群集。

## <a name="prerequisites"></a>先决条件
在开始学习本教程之前，必须有一个 Azure 订阅。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

## <a name="set-up-the-environments"></a>设置环境

有三个配置选项：

- 两个 HBase 群集位于一个 Azure 虚拟网络中。
- 两个 HBase 群集位于同一区域的两个不同虚拟网络中。
- 两个 HBase 群集位于两个不同区域的两个不同虚拟网络中（异地复制）。

本文介绍异地复制方案。

为了帮助设置环境，我们创建了一些 [Azure 资源管理器模板](../../azure-resource-manager/resource-group-overview.md)。 如果想要使用其他方法设置环境，请参阅：

- [在 HDInsight 中创建 Hadoop 群集](../hdinsight-hadoop-provision-linux-clusters.md)
- [在 Azure 虚拟网络中创建 HBase 群集](apache-hbase-provision-vnet.md)

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>在两个不同的区域中设置两个虚拟网络

若要在两个不同区域创建两个虚拟网络并在 VNet 之间创建 VPN 连接，请选择下图来创建。 模板存储在[公共 Blob 存储]](https://hditutorialdata.blob.core.windows.net/hbaseha/azuredeploy.json)中。

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

模板中的某些硬编码值：

**VNet 1**

| 属性 | 值 |
|----------|-------|
| Location | 美国西部 |
| VNet 名称 | &lt;ClusterNamePrevix>-vnet1 |
| 地址空间前缀 | 10.1.0.0/16 |
| 子网名称 | 子网 1 |
| 子网前缀 | 10.1.0.0/24 |
| 子网（网关）名称 | GatewaySubnet（不能更改） |
| 子网（网关）前缀 | 10.1.255.0/27 |
| 网关名称 | vnet1gw |
| 网关类型 | Vpn |
| 网关 VPN 类型 | RouteBased |
| 网关 SKU | 基本 |
| 网关 IP | vnet1gwip |

**VNet 2**

| 属性 | 值 |
|----------|-------|
| Location | 美国东部 |
| VNet 名称 | &lt;ClusterNamePrevix>-vnet2 |
| 地址空间前缀 | 10.2.0.0/16 |
| 子网名称 | 子网 1 |
| 子网前缀 | 10.2.0.0/24 |
| 子网（网关）名称 | GatewaySubnet（不能更改） |
| 子网（网关）前缀 | 10.2.255.0/27 |
| 网关名称 | vnet2gw |
| 网关类型 | Vpn |
| 网关 VPN 类型 | RouteBased |
| 网关 SKU | 基本 |
| 网关 IP | vnet1gwip |

## <a name="setup-dns"></a>设置 DNS

在上一部分，模板在两个虚拟网络的每一个中创建 Ubuntu 虚拟机。  在此部分，请先在两个 DNS 虚拟机上安装 Bind，然后在两个虚拟机上配置 DNS 转发。

若要安装 Bind，需找到两个 DNS 虚拟机的公共 IP 地址。

1. 打开 [Azure 门户](https://portal.azure.com)。
2. 打开 DNS 虚拟机，方法是选择“资源组”> [资源组名称] > [vnet1DNS]。  资源组名称是在上一过程中创建的。 默认的 DNS 虚拟机名称为 *vnet1DNS* 和 *vnet2NDS*。
3. 选择“属性”，打开虚拟网络的属性页。
4. 记下“公共 IP 地址”，并验证“专用 IP 地址”。  vnet1DNS 的专用 IP 地址应该是 **10.1.0.4**，vnet2DNS 的专用 IP 地址应该是 **10.2.0.4**。  

若要安装 Bind，请执行以下过程：

1. 使用 SSH 连接到 DNS 虚拟机的公共 IP 地址。 以下示例将在 40.68.254.142 连接到虚拟机：

    ```bash
    ssh sshuser@40.68.254.142
    ```

    将 `sshuser` 替换为创建 DNS 虚拟机时指定的 SSH 用户帐户。

    > [!NOTE]
    > 可通过多种方法获取 `ssh` 实用工具。 在 Linux、Unix 和 macOS 上，操作系统会附带此实用工具。 如果使用的是 Windows，请考虑以下选项之一：
    >
    > * [Azure Cloud Shell](../../cloud-shell/quickstart.md)
    > * [Windows 10 版 Bash on Ubuntu](https://msdn.microsoft.com/commandline/wsl/about)
    > * [Git (https://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSH (https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. 若要安装 Bind，请从 SSH 会话中使用以下命令：

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. 若要配置 Bind 将名称解析请求转发至本地 DNS 服务器，请使用以下文本作为 `/etc/bind/named.conf.options` 文件的内容：

    ```
    acl goodclients {
        10.1.0.0/16; # Replace with the IP address range of the virtual network 1
        10.2.0.0/16; # Replace with the IP address range of the virtual network 2
        localhost;
        localhost;
    };
    
    options {
        directory "/var/cache/bind";
        recursion yes;
        allow-query { goodclients; };

        forwarders {
            168.63.129.16 #This is the Azure DNS server
        };

        dnssec-validation auto;

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
    };
    ```
    
    > [!IMPORTANT]
    > 将 `goodclients` 节中的值替换为两个虚拟网络的 IP 地址范围。 本部分定义此 DNS 服务器从其中接受请求的地址。

    若要编辑此文件，请使用以下命令：

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    若要保存文件，请使用 Ctrl+X、Ctrl+Y，然后按 Enter。

4. 在 SSH 会话中，请使用以下命令：

    ```bash
    hostname -f
    ```

    此命令会返回类似于以下文本的值：

        vnet1DNS.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net

    `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` 文本是该虚拟网络的 DNS 后缀。 请保存该值，因为稍后会使用它。

    还必须从其他 DNS 服务器中找出 DNS 后缀。 因为下一步骤需要用到。

5. 若要配置 Bind 以在虚拟网络中解析资源的 DNS 名称，请使用以下文本作为 `/etc/bind/named.conf.local` 文件的内容：

    ```
    // Replace the following with the DNS suffix for your virtual network
    zone "v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net" {
            type forward;
            forwarders {10.2.0.4;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]
    > 必须将 `v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` 替换为另一个虚拟网络的 DNS 后缀。 转发器 IP 是另一虚拟网络中 DNS 服务器的专用 IP 地址。

    若要编辑此文件，请使用以下命令：

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    若要保存文件，请使用 Ctrl+X、Ctrl+Y，然后按 Enter。

6. 若要启动 Bind，请使用以下命令：

    ```bash
    sudo service bind9 restart
    ```

7. 若要验证绑定是否可以解析另一虚拟网络中的资源名称，请使用以下命令：

    ```bash
    sudo apt install dnsutils
    nslookup vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net 10.2.0.4
    ```

    > [!IMPORTANT]
    > 将 `vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net` 替换为另一网络中 DNS 虚拟机的完全限定的域名 (FQDN)。
    >
    > 将 `10.2.0.4` 替换为另一虚拟网络中自定义 DNS 服务器的内部 IP 地址。

    显示的响应如下文所示：

    ```
    Server:         10.2.0.4
    Address:        10.2.0.4#53
    
    Non-authoritative answer:
    Name:   vnet2dns.v5ant3az2hbe1edzthhvwwkcse.bx.internal.cloudapp.net
    Address: 10.2.0.4
    ```

    到目前为止，如果不指定 DNS 服务器 IP 地址，则无法查找另一网络中的 IP 地址。

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>配置虚拟网络以使用自定义 DNS 服务器

若要配置虚拟网络以使用自定义 DNS 服务器，而不是 Azure 递归解析程序，请使用以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中，选择“虚拟网络”，然后选择“DNS 服务器”。

2. 选择“自定义”，然后输入自定义 DNS 服务器的内部 IP 地址。 最后，选择“保存”。

6. 打开 vnet1 中的 DNS 服务器虚拟机，然后单击“重启”。  必须重启虚拟网络中的所有虚拟机才能使 DNS 配置生效。
7. 重复这些步骤即可为 vnet2 配置自定义 DNS 服务器。

若要测试 DNS 配置，可以使用 SSH 连接到这两个 DNS 虚拟机，然后使用其主机名称 ping 另一虚拟网络的 DNS 服务器。 如果不起作用，请使用以下命令来检查 DNS 状态：

```bash
sudo service bind9 status
```

## <a name="create-hbase-clusters"></a>创建 HBase 群集

使用以下配置在这两个虚拟网络的每一个中创建 HBase 群集：

- **资源组名称**：使用的资源组名称与创建虚拟网络时所用的相同。
- **群集类型**：HBase
- **版本**：HBase 1.1.2 (HDI 3.6)
- **位置**：与虚拟网络使用同一位置。  默认情况下，vnet1 为“美国西部”，vnet2 为“美国东部”。
- **存储**：为群集创建新的存储帐户。
- **虚拟网络**（在门户的“高级”设置中）：选择在上一过程中创建的 vnet1。
- **子网**：模板中所用的默认名称为 **subnet1**。

若要确保正确配置环境，必须能够在两个群集之间 ping 头节点的 FQDN。

## <a name="load-test-data"></a>加载测试数据

复制群集时，必须指定要复制的表。 在本节中，要将一些数据载入源群集。 在下一部分，会在两个群集之间启用复制。

若要创建一个“联系人”表并在其中插入一些数据，请遵照 [HBase 教程：开始在 HDInsight 中使用 Apache HBase](apache-hbase-tutorial-get-started-linux.md) 中的说明。

## <a name="enable-replication"></a>启用复制

以下步骤说明如何从 Azure 门户调用脚本操作脚本。 有关使用 Azure PowerShell 和 Azure 命令行工具 (Azure CLI) 运行脚本操作的信息，请参阅[使用脚本操作自定义 HDInsight 群集](../hdinsight-hadoop-customize-cluster-linux.md)。

**从 Azure 门户启用 HBase 复制**

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 打开源 HBase 群集。
3. 在群集菜单中，选择“脚本操作”。
4. 在页面顶部，选择“提交新项”。
5. 选择或输入以下信息：

  1. **名称**：输入“启用复制”。
  2. **Bash 脚本 URL**：输入 **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh**。
  3.  **头**：确保已选定。 清除其他节点类型。
  4. **参数**：以下示例参数将对所有现有表启用复制，并将源群集中的所有数据复制到目标群集：

          -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata
    
    >[!note]
    >
    > 对源和目标群集 DNS 名称使用主机名而不是 FQDN。

6. 选择**创建**。 该脚本可能会运行一段时间，尤其是在使用 **-copydata** 参数的情况下。

必需参数：

|名称|说明|
|----|-----------|
|-s、--src-cluster | 指定源 HBase 群集的 DNS 名称。 例如：-s hbsrccluster、--src-cluster=hbsrccluster |
|-d、--dst-cluster | 指定目标（副本）HBase 群集的 DNS 名称。 例如：-s dsthbcluster、--src-cluster=dsthbcluster |
|-sp、--src-ambari-password | 指定源 HBase 群集的 Ambari 管理员密码。 |
|-dp、--dst-ambari-password | 指定目标 HBase 群集的 Ambari 管理员密码。|

可选参数：

|名称|说明|
|----|-----------|
|-su、--src-ambari-user | 指定源 HBase 群集的 Ambari 管理员用户名。 默认值为 **admin**。 |
|-du、--dst-ambari-user | 指定目标 HBase 群集的 Ambari 管理员用户名。 默认值为 **admin**。 |
|-t、--table-list | 指定要复制的表。 例如：--table-list="table1;table2;table3"。 如果不指定表，将复制所有现有的 HBase 表。|
|-m、--machine | 指定要在其中运行脚本操作的头节点。 值为 **hn1** 或 **hn0**。 由于 **hn0** 头节点通常较为繁忙，因此我们建议使用 **hn1**。 在 HDInsight 门户或 Azure PowerShell 中以脚本操作的形式运行 $0 脚本时，可使用此选项。|
|-cp、-copydata | 在启用复制的情况下，允许迁移表中的现有数据。 |
|-rpm、-replicate-phoenix-meta | 针对 Phoenix 系统表启用复制。 <br><br>*请谨慎使用此选项。* 建议在使用此脚本之前，在副本群集上重新创建 Phoenix 表。 |
|-h、--help | 显示用法信息。 |

该[脚本](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh)的 `print_usage()` 节中提供了详细的参数说明。

成功部署脚本操作后，可以使用 SSH 连接到目标 HBase 群集，并验证是否已复制数据。

### <a name="replication-scenarios"></a>复制方案

以下列表显示了一些普通用例及其参数设置：

- **针对两个群集之间的所有表启用复制**。 此方案不需要复制或迁移表中的现有数据，也不使用 Phoenix 表。 使用以下参数：

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **针对特定的表启用复制**。 若要针对 table1、table2 和 table3 启用复制，请使用以下参数：

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **针对特定的表启用复制并复制现有数据**。 若要针对 table1、table2 和 table3 启用复制，请使用以下参数：

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **针对所有表启用复制，并将 Phoenix 元数据从源复制到目标**。 Phoenix 元数据复制并不完善， 请谨慎使用。 使用以下参数：

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## <a name="copy-and-migrate-data"></a>复制并迁移数据

启用复制后，可以使用两个单独的脚本操作脚本来复制或迁移数据：

- [针对小型表的脚本](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh)（大小只有几个 GB，有望在不到 1 小时内完成整个复制的表）

- [针对大型表的脚本](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh)（预计需要一小时以上才能完成复制的表）

可以遵循[启用复制](#enable-replication)中所述的相同过程来调用脚本操作。 使用以下参数：

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

该[脚本](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh)的 `print_usage()` 节中提供了详细的参数说明。

### <a name="scenarios"></a>方案

- **复制特定表（test1、test2 和 test3）中到目前（当前时间戳）为止编辑的所有行**：

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  或：

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **复制指定时间范围内的特定表**：

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## <a name="disable-replication"></a>禁用复制

若要禁用复制，可以使用 [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) 中的另一个脚本操作脚本。 可以遵循[启用复制](#enable-replication)中所述的相同过程来调用脚本操作。 使用以下参数：

    -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">  

该[脚本](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh)的 `print_usage()` 节中提供了详细的参数说明。

### <a name="scenarios"></a>方案

- **对所有表禁用复制**：

        -m hn1 -s <source cluster DNS name> -sp Mypassword\!789 -all
  或

        --src-cluster=<source cluster DNS name> --dst-cluster=<destination cluster DNS name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>

- **对指定的表（table1、table2 和 table3）禁用复制**：

        -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> -t "table1;table2;table3"

## <a name="next-steps"></a>后续步骤

本教程已介绍如何在一个虚拟网络内部或者在两个虚拟网络之间设置 HBase 复制。 若要了解有关 HDInsight 和 HBase 的详细信息，请参阅以下文章：

* [HDInsight 中的 Apache HBase 入门](./apache-hbase-tutorial-get-started-linux.md)
* [HDInsight HBase 概述](./apache-hbase-overview.md)
* [在 Azure 虚拟网络中创建 HBase 群集](./apache-hbase-provision-vnet.md)

