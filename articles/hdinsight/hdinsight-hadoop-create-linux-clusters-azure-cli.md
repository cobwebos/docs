---
title: "使用跨平台 Azure CLI 在 HDInsight 中的 Linux 上创建 Hadoop、HBase 或 Storm 群集 | Microsoft Docs"
description: "了解如何使用跨平台 Azure CLI、Azure Resource Manager 模板和 Azure REST API 创建基于 Linux 的 HDInsight 群集。 可以指定群集类型（Hadoop、HBase 或 Storm），或使用脚本来安装自定义组件。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 50b01483-455c-4d87-b754-2229005a8ab9
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/20/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f322b95e3ade4318ccd64f5e2222194bd2fb9361


---
# <a name="create-linux-based-clusters-in-hdinsight-using-the-azure-cli"></a>使用 Azure CLI 在 HDInsight 中创建基于 Linux 的群集
[!INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Azure CLI 是一个跨平台命令行实用工具，可用于管理 Azure 服务。 将它与 Azure 资源管理模板配合使用可以创建 HDInsight 群集，以及关联的存储帐户和其他服务。

Azure 资源管理模板是描述“资源组”及其包含的所有资源（例如 HDInsight）的 JSON 文档。此基于模板的方法可以定义某个模板中 HDInsight 需要的所有资源。 还可以通过“部署”将组更改作为一个整体进行管理，从而将更改应用到整个组。

本文档中的步骤将引导你完成使用 Azure CLI 和模板创建新 HDInsight 群集的过程。

> [!IMPORTANT]
> 本文档中的步骤对 HDInsight 群集使用默认数目（4 个）的辅助角色节点。 如果计划使用 32 个以上的辅助角色节点（在创建群集时或通过扩展群集进行），则必须选择至少具有 8 个核心和 14 GB ram 的头节点大小。
> 
> 有关节点大小和相关费用的详细信息，请参阅 [HDInsight 定价](https://azure.microsoft.com/pricing/details/hdinsight/)。
> 
> 

## <a name="prerequisites"></a>先决条件
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* **Azure CLI**。 本文档中的步骤最近已使用 Azure CLI 版本 0.10.1 进行测试。
  
    [!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

### <a name="access-control-requirements"></a>访问控制要求
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="log-in-to-your-azure-subscription"></a>登录到 Azure 订阅
按照 [从 Azure 命令行接口 (Azure CLI) 连接到 Azure 订阅](../xplat-cli-connect.md) 中所述的步骤，使用 **登录** 方法连接到订阅。

## <a name="create-a-cluster"></a>创建群集
安装并配置 Azure CLI 之后，应从命令提示符、shell 或终端会话中执行以下步骤。

1. 使用以下命令对 Azure 订阅进行身份验证：
   
        azure login
   
    系统将提示你提供用户名和密码。 如果有多个 Azure 订阅，可以使用 `azure account set <subscriptionname>` 来设置 Azure CLI 命令使用的订阅。
2. 使用以下命令切换到 Azure Resource Manager 模式：
   
        azure config mode arm
3. 创建资源组。 此资源组包含 HDInsight 群集和关联的存储帐户。
   
        azure group create groupname location
   
   * 将“groupname”替换为组的唯一名称。 
   * 将“location”替换为要在其中创建该组的地理区域。 
     
       有关有效位置的列表，请使用 `azure location list` 命令，然后使用 **Name** 列中的位置之一。
4. 创建存储帐户。 此存储帐户将用作 HDInsight 群集的默认存储。
   
        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename
   
   * 将“groupname”替换为上一步中创建的组的名称。
   * 将“location”替换为上一步中使用的同一个位置。 
   * 将“storagename”替换为存储帐户的唯一名称。
     
     > [!NOTE]
     > 有关此命令中使用参数的详细信息，请使用 `azure storage account create -h` 查看此命令的帮助。
     > 
     > 
5. 检索用于访问存储帐户的密钥。
   
        azure storage account keys list -g groupname storagename
   
   * 将“groupname”替换为资源组名称。
   * 将“storagename”替换为存储帐户的名称。
     
     在返回的数据中，保存 **key1** 的 **key** 值。
6. 创建 HDInsight 群集。
   
        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 2 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername
   
   * 将“groupname”替换为资源组名称。
   * 用希望创建的群集类型替换 **Hadoop**。 例如，`Hadoop`、`HBase`、`Storm` 或 `Spark`。
     
     > [!IMPORTANT]
     > HDInsight 群集具有各种不同的类型，与该群集进行优化的工作负荷或技术相对应。 不支持在一个群集上创建合并了多个类型（如 Storm 和 HBase）的群集。 
     > 
     > 
   * 将“location”替换为上一步中使用的同一位置。
   * 将“storagename”替换为存储帐户名称。
   * 将“storagekey”替换为上一步中获取的密钥。 
   * 对于 `--defaultStorageContainer` 参数，请使用你为群集使用的同一个名称。
   * 将“admin”和“httppassword”替换为通过 HTTPS 访问群集时要使用的用户名和密码。
   * 将“sshuser”和“sshuserpassword”替换为通过 SSH 访问群集时要使用的用户名和密码
     
     可能需要几分钟时间才能完成群集创建过程。 通常大约为 15 分钟。

## <a name="next-steps"></a>后续步骤
使用 Azure CLI 成功创建 HDInsight 群集后，请参考以下主题来了解如何使用群集：

### <a name="hadoop-clusters"></a>Hadoop 群集
* [将 Hive 与 HDInsight 配合使用](hdinsight-use-hive.md)
* [将 Pig 与 HDInsight 配合使用](hdinsight-use-pig.md)
* [将 MapReduce 与 HDInsight 配合使用](hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>HBase 群集
* [HBase on HDInsight 入门](hdinsight-hbase-tutorial-get-started-linux.md)
* [为 HBase on HDInsight 开发 Java 应用程序](hdinsight-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Storm 群集
* [为 Storm on HDInsight 开发 Java 拓扑](hdinsight-storm-develop-java-topology.md)
* [在 Storm on HDInsight 中使用 Python 组件](hdinsight-storm-develop-python-topology.md)
* [使用 Storm on HDInsight 部署和监视拓扑](hdinsight-storm-deploy-monitor-topology-linux.md)




<!--HONumber=Nov16_HO3-->


